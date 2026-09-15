# caren-rubocop

A shared RuboCop configuration focused on enforcing best practices and coding conventions used by
Team Caren. It contains default settings and overrides for Ruby and Rails projects.

## Installation

Add this line to your application's `.rubocop.yml`:

```yaml
inherit_from: https://raw.githubusercontent.com/nedap/caren-rubocop/main/rubocop.yml
```

This configuration declares plugins but cannot install them for you, so your application must
carry them in its own Gemfile:

```ruby
group :development, :test do
  gem "rubocop-factory_bot", require: false
  gem "rubocop-performance", require: false
  gem "rubocop-rails", require: false
end
```

This follows from distributing configuration over a URL: when a plugin is added here, every
consuming application needs a Gemfile change before it can lint again. That is a known
limitation, and it is accepted.

## Usage

This configuration targets Ruby 3.4. Specify a different target in your project's
`.rubocop.yml` when your application runs another version:

```yaml
inherit_from: https://raw.githubusercontent.com/nedap/caren-rubocop/main/rubocop.yml

AllCops:
  TargetRubyVersion: 3.3
```

## Staying up to date

Inheriting from `main` means your configuration updates on its own. RuboCop caches the remote
file it fetches over `inherit_from` and refreshes that cache once every 24 hours.

CI already lints against the newest configuration, as long as your workflow does not cache
`~/.cache/rubocop_cache`. Caching that directory would make CI lint against a configuration up
to a day old.

Locally, that 24 hour window can lag behind `main`. Closing it is optional, and belongs in your
own git hooks rather than in this repository. With lefthook, backdate the cached file right
before RuboCop runs, so a stale cache is always refetched:

```yaml
pre-push:
  parallel: true
  commands:
    rubocop:
      tags: linter
      glob: "*.rb"
      run: |
        for cache_dir in "$RUBOCOP_CACHE_ROOT" "${XDG_CACHE_HOME:+$XDG_CACHE_HOME/$(id -u)}" "$HOME/.cache"; do
          [ -n "$cache_dir" ] && touch -t 200001010000 "$cache_dir"/rubocop_cache/rubocop-*.yml 2>/dev/null
        done
        bundle exec rubocop --force-exclusion {push_files}
```

The `run` command has to include the refresh itself, not just sit beside it: `pre-push` runs its
commands in parallel, so a separate command would race the linter. If your repository uses
lefthook's newer `jobs:` syntax, keep that syntax and move the same `run` body across.

The snippet backdates the cached file instead of deleting it, so that a push without a network
connection still lints against the last known configuration: RuboCop swallows the connection
error and falls back to the cached file, whereas a deleted file would fail with a configuration
error.
