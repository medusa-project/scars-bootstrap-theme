# SCARS Bootstrap Theme

This repo consists of Bootstrap, an SCSS theme, images, and SCARS header and
footer components, packaged as a gem that is convenient for SCARS applications
to consume. The goal is to reduce the burden of maintaining visual consistency
across SCARS' Rails applications.

A typical SCARS Rails project would declare a dependency on Bootstrap in its
Gemfile, and then pull the JavaScript and SCSS from that into its asset
pipeline. This gem aims to be a swap-in replacement of a Bootstrap gem. It's
not quite that simple (see below), but that's the idea, anyway.

This gem is compatible with Rails through version 6 and Sprockets through
version 4. It is  not compatible with Webpacker, which is a completely
different way of managing assets that all of our apps are currently
incompatible with.

# Branching & versioning

The master branch is only used to host this readme. Instead there are release
branches named after a Bootstrap major-minor version. There are also tags which
point to a specific Bootstrap version and theme version.

# Incorporating the theme into a Rails app

1. Remove Bootstrap from the project, whether it is by removing it from the
   Gemfile, or the `vendor` directory, or whatever.
2. Add the following to the Gemfile:
   ```ruby
   gem 'autoprefixer-rails'
   # specifying a branch automatically pulls updates
   gem 'scars-bootstrap-theme', github: 'medusa-project/scars-bootstrap-theme', branch: 'release/bootstrap-4.4'
   # specifying a tag ensures stability
   gem 'scars-bootstrap-theme', github: 'medusa-project/scars-bootstrap-theme', tag:  'v4.4.1_1.0'
   ```
3. Run `bundle install`
4. Ensure that `app/javascripts/application.js` contains the following:
   ```javascript
   //= require popper
   //= require bootstrap
   ```
5. Ensure that `app/stylesheets/application.scss` contains
   `@import "bootstrap";`
6. Ensure that `config/environments/development.rb` and `test.rb` contains
   `config.assets.check_precompiled_asset = false`
7. Restart the app

Most likely, things will look quite broken. You'll need to yank out a lot of
custom baseline styles that are now provided by this theme. Don't forget to
delete obsolete images.

The theme tries to respect the [Illinois Identity Standards](https://brand.illinois.edu/logos-and-colors.html) and offers some variables containing
official U of I colors:

```scss
$uofi-blue
$uofi-blue-lighter-1
$uofi-blue-lighter-2
$uofi-blue-lighter-3
$uofi-blue-lighter-4
$uofi-orange
```

The [official Bootstrap documentation](https://getbootstrap.com/docs/) still
applies. To obtain the markup for the custom header and footer, either copy it
out of `docs/index.html`, or proceed to the Development section below, which
provides a convenient way to copy it from your web browser.

See [IDEALS](https://github.com/medusa-project/ideals) for a working app that
uses this gem.

# Development

1. Clone the repo
2. In your application's Gemfile, temporarily change the `gem` line to:
   ```ruby
   gem 'scars-bootstrap-theme', path: '../scars-bootstrap-theme' # or whatever
   ```
3. `$ npm install`
4. `$ npm install -g grunt-cli`
5. `$ grunt`

The last command will start a web server and open the sample page in a web
browser. The server will watch for any changes to the SCSS files and
automatically rebuild the theme and reload the page on change. After
rebuilding, restart your application to see changes.

You are basically editing only three files:

* `docs/index.html`
* `docs/theme/_variables.scss` (variables that customize Bootstrap)
* `docs/theme/_scars-bootstrap-theme.scss` (customizations on top of Bootstrap)

(Don't edit anything in `app/assets`. All of that stuff gets overwritten.)

Images are located in `docs/theme/images`.

When you are done, run `grunt build` to populate `app/assets`, which supplies
Rails' asset path.

## Creating a new theme version

1. `git checkout release/<bootstrap version>` (branch to start from)
2. `git checkout -b release/<new version>` (only if updating Bootstrap)
3. Make edits
4. Update the version in `package.json`
5. `grunt build`
6. Commit changes
7. `git tag -a v<bootstrap version>_<theme version>`
8. `git push --tags`
