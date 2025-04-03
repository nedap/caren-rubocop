# caren-rubocop

A RuboCop configuration focused on enforcing best practices and coding conventions used by Team Caren.
The library contains default settings and overrides for Ruby and Rails projects. This gem also
requires default plugins, for example `rubocop-performance`, `rubocop-rails` and `rubocop-factory_bot`.

## Installation

Add this line to your application's `.rubocop.yml`:

```yaml
inherit_from: https://raw.githubusercontent.com/nedap/caren-rubocop/main/.rubocop.yml
```

## Usage

The default configuration of this project targets Ruby 3.2. You can specify a different target in the project's `.rubocop.yml`:

```yaml
inherit_from: https://raw.githubusercontent.com/nedap/caren-rubocop/main/.rubocop.yml

AllCops:
  TargetRubyVersion: 3.0.2
```
