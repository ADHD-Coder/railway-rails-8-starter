# 🚂 Railway + Rails 8 Starter Template (Solid Stack Edition)

This is my production-ready Rails 8 template optimized for [Railway](https://railway.app). It ships with:

* ⚙️ **Solid Stack**: `solid_cache`, `solid_queue`, `solid_cable`
* 📦 Four separate PostgreSQL services (no Redis)
* 📫 Postmark for production email & Letter Opener in development
* 🔐 Devise for authentication (I usually need more advanced flows than what Rails 8 provides out of the box)
* 🐳 Dockerfile build without Thruster

If you’re tired of copy-pasting the same setup every time you spin up a new project, or just want to see how Rails 8 works with Railway and Solid Stack, this template saves you the headache.

**CREDITS:**
* Thank you to this video tutorial on [How to Configure Multiple Databases with Rails 8 by GoRails](https://youtu.be/aOsffm22T8I?si=wU3Le7Yr11ElfZ4I)
* See me troubleshooting the Docker / Thruster issue [here in Railway Station](https://station.railway.com/questions/rails-8-app-successful-deploy-but-contai-28123617)


## 🔥 Fast Start: Use the Template

Want everything done for you?

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/deploy/z3hsm9?referralCode=oePuom)

Then just:

1. Add your `RAILS_MASTER_KEY` to Railway (from `config/master.key`)
2. Generate a domain in the App Service settings
3. Update your production `action_mailer.default_url_options` with that domain
4. (Optional) Add your Postmark API token
5. (Optional) Change storage to S3 instead

Boom. You’re deployed.

Scroll down if you want to understand what’s under the hood, or how to do this all from scratch.


## 🛠 Want to DIY It? Here’s How to Build It from Scratch

If you’d rather understand each moving piece (or use this in an existing app), here’s the build-your-own-guide:

### 1. Create a new Rails 8 app

```bash
rails new myapp --css=tailwind --database=postgresql
cd myapp
```

### 2. Add the following gems

In your `Gemfile`, add:

```ruby
# Auth
gem 'devise'

# Job Monitoring
gem 'mission_control-jobs'

# Email
gem 'postmark-rails'

# Dev
group :development do
  gem 'letter_opener'
end
```

Then run:

```bash
bundle install
```

Set up your models/routes accordingly.

### 3. Configure Solid Stack

Create 4 databases on Railway:

* `PRIMARY_DATABASE_URL`
* `CACHE_DATABASE_URL`
* `QUEUE_DATABASE_URL`
* `CABLE_DATABASE_URL`

Update your `config/database.yml` to map them properly (template shows how).

### 4. Update the following files

Make sure to double check that these files match the template: 

* `application.rb` - adds MissionControl auth, you can set this to use credentials or environment variables instead
* `cable.yml` - sets up solid cable in dev
* `database.yml` - sets up development & production databases for solid stack
* `development.rb` - adds letter_opener and solid stack
* `puma.rb` - sets up solid queue in development
* `routes.rb` - adds /jobs route
* `Dockerfile` - replaces default `CMD ["./bin/thrust", "./bin/rails", "server"]` with `CMD bin/rails server -b 0.0.0.0 -p ${PORT}` to work with Railway
* `Gemfile` - adds Mission Control, Letter Opener, Devise, and Postmark gems

You can focus on just production changes and ignore the development changes, but I recommend setting it up like the template so it's easy to catch issues before pushing to production

### 5. Configure Active Storage

**Default**: Volume-mounted local storage

**Optional**: Switch to S3 in production

To keep it simple, on Railway, add a persistent volume to the App service and mount it to /rails/storage. This ensures uploaded files persist between deploys when using local disk Active Storage.

### 6. Set up email

For dev, letter_opener is already set up. Emails open in your browser.

(Optional) Use Postmark by following instructions in your Postmark Dashboard.

You can generate a public domain in Railway under App Service → Settings → Networking.

## 🧪 Local Development Tips

Run everything with:

```bash
bin/dev
```

This runs Rails + Tailwind in parallel.

Visit `/up` to check your app’s health (Rails 8 ships with this endpoint by default for uptime checks).

Don’t forget:

* Set up your credentials: `EDITOR="code --wait" bin/rails credentials:edit`
* Add a `master.key` if you didn’t already
* Check `/jobs` to monitor queues in dev

## 🧾 Environment Variables (cheat sheet)

Minimum required:

* `RAILS_ENV = production`
* `RAILS_MASTER_KEY = your_local_key_here`
* `PRIMARY_DATABASE_URL = created_by_template`
* `CACHE_DATABASE_URL = created_by_template`
* `QUEUE_DATABASE_URL = created_by_template`
* `CABLE_DATABASE_URL = created_by_template`

Optional:

* `POSTMARK_API_TOKEN`

Any questions, feel free to email me at [ADHDCoder.com](https://www.adhdcoder.com/)