## 1.0.0.rc.6

Features:

  - Much better documentation for most of the commands and Gemfile
    format

Bugfixes:

  - Don't attempt to create directories if they already exist
  - Fix the capistrano task so that it actually runs
  - Update the Gemfile template to reference rubygems.org instead
    of :gemcutter
  - bundle exec should exit with a non zero exit code when the gem
    binary does not exist or the file is not executable.
  - Expand paths in Gemfile relative to the Gemfile and not the current
    working directory.

## 1.0.0.rc.5

Features:

  - Make the Capistrano task more concise.

Bugfixes:

  - Fix a regression with determining whether or not to use sudo
  - Allow using the --gemfile flag with the --deployment flag

## 1.0.0.rc.4

Features:

  - `bundle gem NAME` command to generate a new gem with Gemfile
  - Bundle config file location can be specified by BUNDLE_APP_CONFIG
  - Add --frozen to disable updating the Gemfile.lock at runtime
    (default with --deployment)
  - Basic Capistrano task now added as 'bundler/capistrano'

Bugfixes:

  - Multiple bundler process no longer share a tmp directory
  - `bundle update GEM` always updates dependencies of GEM as well
  - Deleting the cache directory no longer causes errors
  - Moving the bundle after installation no longer causes git errors
  - Bundle path is now correctly remembered on a read-only filesystem
  - Gem binaries are installed to Gem.bindir, not #{Gem.dir}/bin
  - Fetch gems from vendor/cache, even without --local
  - Sort lockfile by platform as well as spec

## 1.0.0.rc.3 (August 3, 2010)

Features:

  - Deprecate --production flag for --deployment, since the former
    was causing confusion with the :production group
  - Add --gemfile option to `bundle check`
  - Reduce memory usage of `bundle install` by 2-4x
  - Improve message from `bundle check` under various conditions
  - Better error when a changed Gemfile conflicts with Gemfile.lock

Bugfixes:

  - Create bin/ directory if it is missing, then install binstubs
  - Error nicely on the edge case of a pinned gem with no spec
  - Do not require gems for other platforms
  - Update git sources along with the gems they contain

## 1.0.0.rc.2 (July 29, 2010)

  - `bundle install path` was causing confusion, so we now print
    a clarifying warning. The preferred way to install to a path
    (which will not print the warning) is
    `bundle install --path path/to/install`.
  - `bundle install --system` installs to the default system
    location ($BUNDLE_PATH or $GEM_HOME) even if you previously
    used `bundle install --path`
  - completely remove `--disable-shared-gems`. If you install to
    system, you will not be isolated, while if you install to
    another path, you will be isolated from gems installed to
    the system. This was mostly an internal option whose naming
    and semantics were extremely confusing.
  - Add a `--production` option to `bundle install`:
    - by default, installs to `vendor/bundle`. This can be
      overridden with the `--path` option
    - uses `--local` if `vendor/cache` is found. This will
      guarantee that Bundler does not attempt to connect to
      Rubygems and will use the gems cached in `vendor/cache`
      instead
    - Raises an exception if a Gemfile.lock is not found
    - Raises an exception if you modify your Gemfile in development
      but do not check in an updated Gemfile.lock
  - Fixes a bug where switching a source from Rubygems to git
    would always say "the git source is not checked out" when
    running `bundle install`

NOTE: We received several reports of "the git source has not
been checked out. Please run bundle install". As far as we
can tell, these problems have two possible causes:

1. `bundle install ~/.bundle` in one user, but actually running
   the application as another user. Never install gems to a
   directory scoped to a user (`~` or `$HOME`) in deployment.
2. A bug that happened when changing a gem to a git source.

To mitigate several common causes of `(1)`, please use the
new `--production` flag. This flag is simply a roll-up of
the best practices we have been encouraging people to use
for deployment.

If you want to share gems across deployments, and you use
Capistrano, symlink release_path/current/vendor/bundle to
release_path/shared/bundled_gems. This will keep deployments
snappy while maintaining the benefits of clean, deploy-time
isolation.

## 1.0.0.rc.1 (July 26, 2010)

  - Fixed a bug with `bundle install` on multiple machines and git

## 1.0.0.beta.10 (July 25, 2010)

  - Last release before 1.0.0.rc.1
  - Added :mri as a valid platform (platforms :mri { gem "ruby-debug" })
  - Fix `bundle install` immediately after modifying the :submodule option
  - Don't write to Gemfile.lock if nothing has changed, fixing situations
    where bundle install was run with a different user than the app
    itself
  - Fix a bug where other platforms were being wiped on `bundle update`
  - Don't ask for root password on `bundle install` if not needed
  - Avoid setting `$GEM_HOME` where not needed
  - First solid pass of `bundle config`
  - Add build options
    - `bundle config build.mysql --with-mysql-config=/path/to/config`

## 1.0.0.beta.9 (July 21, 2010)

  - Fix install failure when switching from a path to git source
  - Fix `bundle exec bundle *` in a bundle with --disable-shared-gems
  - Fix `bundle *` from inside a bundle with --disable-shared-gem
  - Shim Gem.refresh. This is used by Unicorn
  - Fix install failure when a path's dependencies changed

## 1.0.0.beta.8 (July 20, 2010)

  - Fix a Beta 7 bug involving Ruby 1.9

## 1.0.0.beta.7 (July 20, 2010, yanked)

  - Running `bundle install` twice in a row with a git source always crashed

## 1.0.0.beta.6 (July 20, 2010, yanked)

  - Create executables with bundle install --binstubs
  - You can customize the location (default is app/bin) with --binstubs other/location
  - Fix a bug where the Gemfile.lock would be deleted even if the update was exited
  - Fix a bug where cached gems for other platforms were sometimes deleted
  - Clean up output when nothing was deleted from cache (it previously said
    "Removing outdated gems ...")
  - Improve performance of bundle install if the git gem was already checked out,
    and the revision being used already exists locally
  - Fix bundle show bundler in some cases
  - Fix bugs with bundle update
  - Don't ever run git commands at runtime (fixes a number of common passenger issues)
  - Fixes an obscure bug where switching the source of a gem could fail to correctly
    change the source of its dependencies
  - Support multiple version dependencies in the Gemfile
    (gem "rails", ">= 3.0.0.beta1", "<= 3.0.0")
  - Raise an exception for ambiguous uses of multiple declarations of the same gem
    (for instance, with different versions or sources).
  - Fix cases where the same dependency appeared several times in the Gemfile.lock
  - Fix a bug where require errors were being swallowed during Bundler.require

## BACKFILL COMING

## 1.0.0.beta.1

  - No `bundle lock` command. Locking happens automatically on install or update
  - No .bundle/environment.rb. Require 'bundler/setup' instead.
  - $BUNDLE_HOME defaults to $GEM_HOME instead of ~/.bundle
  - Remove lockfiles generated by 0.9

## 0.9.26

Features:

  - error nicely on incompatible 0.10 lockfiles

## 0.9.25 (May 3, 2010)

Bugfixes:

  - explicitly coerce Pathname objects to Strings for Ruby 1.9
  - fix some newline weirdness in output from install command

## 0.9.24 (April 22, 2010)

Features:

  - fetch submodules for git sources
  - limit the bundled version of bundler to the same as the one installing
  - force relative paths in git gemspecs to avoid raising Gem::NameTooLong
  - serialize GemCache sources correctly, so locking works
  - raise Bundler::GemNotFound instead of calling exit! inside library code
  - Rubygems 1.3.5 compatibility for the adventurous, not supported by me :)

Bugfixes:

  - don't try to regenerate environment.rb if it is read-only
  - prune outdated gems with the platform "ruby"
  - prune cache without errors when there are directories or non-gem files
  - don't re-write environment.rb if running after it has been loaded
  - do not monkeypatch Specification#load_paths twice when inside a bundle

## 0.9.23 (April 20, 2010)

Bugfixes:

  - cache command no longer prunes gems created by an older rubygems version
  - cache command no longer prunes gems that are for other platforms

## 0.9.22 (April 20, 2010)

Features:

  - cache command now prunes stale .gem files from vendor/cache
  - init --gemspec command now generates development dependencies
  - handle Polyglot's changes to Kernel#require with Bundler::ENV_LOADED (#287)
  - remove .gem files generated after installing a gem from a :path (#286)
  - improve install/lock messaging (#284)

Bugfixes:

  - ignore cached gems that are for another platform (#288)
  - install Windows gems that have no architecture set, like rcov (#277)
  - exec command while locked now includes the bundler lib in $LOAD_PATH (#293)
  - fix the `rake install` task
  - add GemspecError so it can be raised without (further) error (#292)
  - create a parent directory before cloning for git 1.5 compatibility (#285)

## 0.9.21 (April 16, 2010)

Bugfixes:

  - don't raise 'omg wtf' when lockfile is outdated

## 0.9.20 (April 15, 2010)

Features:

  - load YAML format gemspecs
  - no backtraces when calling Bundler.setup if gems are missing
  - no backtraces when trying to exec a file without the executable bit

Bugfixes:

  - fix infinite recursion in Bundler.setup after loading a bundled Bundler gem
  - request install instead of lock when env.rb is out of sync with Gemfile.lock

## 0.9.19 (April 12, 2010)

Features:

  - suggest `bundle install --relock` when the Gemfile has changed (#272)
  - source support for Rubygems servers without prerelease gem indexes (#262)

Bugfixes:

  - don't set up all groups every time Bundler.setup is called while locked (#263)
  - fix #full_gem_path for git gems while locked (#268)
  - eval gemspecs at the top level, not inside the Bundler class (#269)


## 0.9.18 (April 8, 2010)

Features:

  - console command that runs irb with bundle (and optional group) already loaded

Bugfixes:

  - Bundler.setup now fully disables system gems, even when unlocked (#266, #246)
    - fixes Yard, which found plugins in Gem.source_index that it could not load
    - makes behaviour of `Bundler.require` consistent between locked and unlocked loads

## 0.9.17 (April 7, 2010)

Features:

  - Bundler.require now calls Bundler.setup automatically
  - Gem::Specification#add_bundler_dependencies added for gemspecs

Bugfixes:

  - Gem paths are not longer duplicated while loading bundler
  - exec no longer duplicates RUBYOPT if it is already set correctly

## 0.9.16 (April 3, 2010)

Features:

  - exit gracefully on INT signal
  - resolver output now indicates whether remote sources were checked
  - print error instead of backtrace when exec cannot find a binary (#241)

Bugfixes:

  - show, check, and open commands work again while locked (oops)
  - show command for git gems
    - outputs branch names other than master
    - gets the correct sha from the checkout
    - doesn't print sha twice if :ref is set
  - report errors from bundler/setup.rb without backtraces (#243)
  - fix Gem::Spec#git_version to not error on unloaded specs
  - improve deprecation, Gemfile, and command error messages (#242)

## 0.9.15 (April 1, 2010)

Features:

  - use the env_file if possible instead of doing a runtime resolve
     - huge speedup when calling Bundler.setup while locked
     - ensures bundle exec is fast while locked
     - regenerates env_file if it was generated by an older version
  - update cached/packed gems when you update gems via bundle install

Bugfixes:

  - prep for Rubygems 1.3.7 changes
  - install command now pulls git branches correctly (#211)
  - raise errors on invalid options in the Gemfile

## 0.9.14 (March 30, 2010)

Features:

  - install command output vastly improved
    - installation message now accurate, with 'using' and 'installing'
    - bundler gems no longer listed as 'system gems'
  - show command output now includes sha and branch name for git gems
  - init command now takes --gemspec option for bootstrapping gem Gemfiles
  - Bundler.with_clean_env for shelling out to ruby scripts
  - show command now aliased as 'list'
  - VISUAL env var respected for GUI editors

Bugfixes:

  - exec command now finds binaries from gems with no gemspec
  - note source of Gemfile resolver errors
  - don't blow up if git urls are changed

## 0.9.13 (March 23, 2010)

Bugfixes:

  - exec command now finds binaries from gems installed via :path
  - gem dependencies are pulled in even if their type is nil
  - paths with spaces have double-quotes to work on Windows
  - set GEM_PATH in environment.rb so generators work with Rails 2

## 0.9.12 (March 17, 2010)

  - refactoring, internal cleanup, more solid specs

Features:

  - check command takes a --without option
  - check command exits 1 if the check fails

Bugfixes:

  - perform a topological sort on resolved gems (#191)
  - gems from git work even when paths or repos have spaces (#196)
  - Specification#loaded_from returns a String, like Gem::Specification (#197)
  - specs eval from inside the gem directory, even when locked
  - virtual gemspecs are now saved in environment.rb for use when loading
  - unify the Installer's local index and the runtime index (#204)

## 0.9.11 (March 9, 2010)

  - added roadmap with future development plans

Features:

  - install command can take the path to the gemfile with --gemfile (#125)
  - unknown command line options are now rejected (#163)
  - exec command hugely sped up while locked (#177)
  - show command prints the install path if you pass it a gem name (#148)
  - open command edits an installed gem with $EDITOR (#148)
  - Gemfile allows assigning an array of groups to a gem (#114)
  - Gemfile allows :tag option on :git sources
  - improve backtraces when a gemspec is invalid
  - improve performance by installing gems from the cache if present

Bugfixes:

  - normalize parameters to Bundler.require (#153)
  - check now checks installed gems rather than cached gems (#162)
  - don't update the gem index when installing after locking (#169)
  - bundle parenthesises arguments for 1.8.6 (#179)
  - gems can now be assigned to multiple groups without problems (#135)
  - fix the warning when building extensions for a gem from git with Rubygems 1.3.6
  - fix a Dependency.to_yaml error due to accidentally including sources and groups
  - don't reinstall packed gems
  - fix gems with git sources that are private repositories

## 0.9.10 (March 1, 2010)

  - depends on Rubygems 1.3.6

Bugfixes:

  - support locking after install --without
  - don't reinstall gems from the cache if they're already in the bundle
  - fixes for Ruby 1.8.7 and 1.9

## 0.9.9 (February 25, 2010)

Bugfixes:

  - don't die if GEM_HOME is an empty string
  - fixes for Ruby 1.8.6 and 1.9

## 0.9.8 (February 23, 2010)

Features:

  - pack command which both caches and locks
  - descriptive error if a cached gem is missing
  - remember the --without option after installing
  - expand paths given in the Gemfile via the :path option
  - add block syntax to the git and group options in the Gemfile
  - support gems with extensions that don't admit they depend on rake
  - generate gems using gem build gemspec so git gems can have native extensions
  - print a useful warning if building a gem fails
  - allow manual configuration via BUNDLE_PATH

Bugfixes:

  - eval gemspecs in the gem directory so relative paths work
  - make default spec for git sources valid
  - don't reinstall gems that are already packed

## 0.9.7 (February 17, 2010)

Bugfixes:

  - don't say that a gem from an excluded group is "installing"
  - improve crippling rubygems in locked scenarios

## 0.9.6 (February 16, 2010)

Features:

  - allow String group names
  - a number of improvements in the documentation and error messages

Bugfixes:

  - set SourceIndex#spec_dirs to solve a problem involving Rails 2.3 in unlocked mode
  - ensure Rubygems is fully loaded in Ruby 1.9 before patching it
  - fix `bundle install` for a locked app without a .bundle directory
  - require gems in the order that the resolver determines
  - make the tests platform agnostic so we can confirm that they're green on JRuby
  - fixes for Ruby 1.9

## 0.9.5 (Feburary 12, 2010)

Features:

  - added support for :path => "relative/path"
  - added support for older versions of git
  - added `bundle install --disable-shared-gems`
  - Bundler.require fails silently if a library does not have a file on the load path with its name
  - Basic support for multiple rubies by namespacing the default bundle path using the version and engine

Bugfixes:

  - if the bundle is locked and .bundle/environment.rb is not present when Bundler.setup is called, generate it
  - same if it's not present with `bundle check`
  - same if it's not present with `bundle install`
