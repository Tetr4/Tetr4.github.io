source "https://rubygems.org"

gem "jekyll-theme-hydeout", "~> 4.1"
gem "github-pages", "~> 227", group: :jekyll_plugins

# Plugins
# See https://pages.github.com/versions/
group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.15.1"
end

# Windows and JRuby does not include zoneinfo files, so bundle the tzinfo-data gem
# and associated library.
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo", "~> 1.2"
  gem "tzinfo-data"
end

# Performance-booster for watching directories on Windows
gem "wdm", "~> 0.1.1", :platforms => [:mingw, :x64_mingw, :mswin]

# Lock `http_parser.rb` gem to `v0.6.x` on JRuby builds since newer versions of the gem
# do not have a Java counterpart.
gem "http_parser.rb", "~> 0.6.0", :platforms => [:jruby]

# github-pages still uses ruby 2.7.4, which contains webrick. For using Ruby 3.x we need to include it manually.
# See https://github.com/github/pages-gem/issues/752
gem "webrick", "~> 1.7"
