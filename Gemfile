source 'https://rubygems.org'

gem 'rake', '~> 10.4'

platforms :ruby do
  gem 'sqlite3', '= 1.3.13'
end

platforms :jruby do
  gem 'jruby-openssl'
  gem 'activerecord-jdbcsqlite3-adapter'
end

platforms :rbx do
  gem 'psych'
end

group :test do
  gem 'minitest', '~> 4.7.5'
  if ENV['RAILS_VERSION'] == 'edge'
    gem 'activerecord', :github => 'rails/rails'
    gem 'actionmailer', :github => 'rails/rails'
  else
    gem 'activerecord', (ENV['RAILS_VERSION'] || ['>= 3.0', '< 5.0'])
    gem 'actionmailer', (ENV['RAILS_VERSION'] || ['>= 3.0', '< 5.0'])
  end

  gem 'coveralls', :require => false
  gem 'rspec', '>= 3'
  gem 'rubocop', '>= 0.25'
  gem 'simplecov', '>= 0.9'
end

gemspec
