source 'https://rubygems.org'
ruby '2.6.5'

gem 'rails'
gem 'puma'
gem 'sass-rails'
gem 'uglifier'
gem "haml-rails"
gem "susy" , "2.2.12"

gem 'high_voltage'

gem 'opal', '~> 0.11.0'
gem 'opal-rails'
gem 'opal-jquery'
gem 'opal-browser' , "0.2.0"

gem "rubyx" , "0.6" , git: "https://github.com/ruby-x/rubyx" , require: false
gem "rubyx-debugger" , "0.3" , git: "https://github.com/ruby-x/rubyx-debugger" , require: false
gem "rx-file" , git: "https://github.com/ruby-x/rx-file"

gem "haml-coderay"
gem 'mini_racer'

group :development, :test do
  gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
  gem 'rspec-rails'
end

group :test do
  gem 'capybara'
  gem 'capybara-screenshot'
end

group :development do
  gem 'web-console', '>= 3.3.0'
  gem 'listen', '>= 3.0.5', '< 3.2'
  gem 'spring'
  gem 'spring-watcher-listen', '~> 2.0.0'

  gem 'capistrano'
  gem 'capistrano-bundler'
  gem 'capistrano-rails'
  gem 'capistrano-rails-console'
  gem 'capistrano-passenger'

end
