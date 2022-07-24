source 'https://rubygems.org'

#gemspec

group :test do
  gem "html-proofer", "~> 3.18"
end

# Windows and JRuby does not include zoneinfo files, so bundle the tzinfo-data gem
# and associated library.
install_if -> { RUBY_PLATFORM =~ %r!mingw|mswin|java! } do
  gem "tzinfo", "~> 1.2"
  gem "tzinfo-data"
end

gem 'nokogiri'
gem 'rack', '~> 2.0.1'
gem 'rspec'
gem "jekyll"
gem 'jemoji'

group :jekyll_plugins do
  gem "jekyll-feed"
  gem "jekyll-seo-tag"
  gem 'jekyll-avatar'
  gem 'jekyll-coffeescript'
  gem 'jekyll-default-layout'
  gem 'jekyll-gist'
  gem 'jekyll-paginate'
  gem 'jekyll-mentions'
  gem 'jekyll-optional-front-matter'
  gem 'jekyll-readme-index'
  gem 'jekyll-redirect-from'
  gem 'jekyll-remote-theme'
  gem 'jekyll-relative-links'
  gem 'jekyll-sitemap'
  gem 'jekyll-titles-from-headings'
end
#gem 'github-pages', group: :jekyll_plugins

# Performance-booster for watching directories on Windows
gem "wdm", "~> 0.1.1", :install_if => Gem.win_platform?

# Jekyll <= 4.2.0 compatibility with Ruby 3.0
gem "webrick", "~> 1.7"

