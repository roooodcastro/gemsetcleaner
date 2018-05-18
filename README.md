# Gemset Cleaner

This is a simple script library for Linux & Ruby users, whose main goal is to
remove unused gems from the system to save disk space. Saving disk space might
be a concern if you're using Linux on a small SDD or a small disk partition,
for example.

Unused gems are considered to be gems which are not being used by any configured
Ruby project. Unused gems are usually the result of upgrading a single gem's
version, or upgrading the Ruby interpreter version as a whole, in which case a
whole new *gemset* is created. Both situations leave behind a trail of old and
unused gems installed on the system.

## Requirements

1. Linux. This script was not tested and may not work on MacOS systems.
2. [RVM](https://rvm.io/). There is no Support for [rbenv](http://rbenv.org/)
(yet).
3. Valid Ruby projects with `Gemfile`, `.ruby-version` and `.ruby-gemset` files
   (some are optional depending on the script).

## Usage

There are 3 separate scripts which should be used individually, and have
increasing effects on space savings. The first one, `bundlecleaner`, will remove
unused **gems** from a single project. The second, `workspacecleaner`, will
remove unused **gems** from all projects within a *workspace* directory. The
third will remove entire unused **gemsets** from your system, based on what
gemsets are the projects inside a *workspace* directory using.

### bundlecleaner

The `bundlecleaner` script must be executed inside the project directory, and
it can take an optional `-q` argument. If executed without `-q`, it will print
some progress updates and the space saved in the end. If executed with `-q`,
it will only print out the raw number of kilobytes saved. This is intended to
be used on the `workspacecleaner` script, to be able to add the total space
saved.

**Warning**: This will uninstall **all** the local gems which are not present in
the `Gemfile.lock` file, so if you have a standalone gem that you use locally
but should not be in the Gemfile (for example: the `nexus` gem which is used to
upload new gems to a Nexus gem repository), these will also be removed.
Dependencies, however, will remain as expected.

Usage:

    $ cd ~/workspace/my-project
    $ /path_to_gemsetcleaner/bundlecleaner
    
For the script to successfully execute, it needs 3 things:

1. A valid installation of **RVM**
2. The **Gemfile** file to be present
3. A **.ruby-version** file to be present
4. (optional) a **.ruby-gemset** file to be present

If one of the first previous items is not present, it will not try to clean the
gems, and print an error message instead. If `-q` was specified, it will
silently return "0" as output.

Example output:

    $ ~/bundlecleaner
    Trying to remove unused gems...
    
    Removing tilt (2.0.8)
    Removing sinatra (2.0.1)
    Removing rack-protection (2.0.1)
    Removing mustermann (1.0.2)
    
    2,088kb cleared!
    
### workspacecleaner

The `workspacecleaner` script can be executed from anywhere, as it will take as
argument the path to the *workspace* directory. This argument is required and
may be a relative path, such as `.`.

The *workspace* directory is the directory where you store all of your projects,
or at least most of them.

This script will then look for all directories inside the *workspace* directory,
assume they're all Ruby projects, and run `bundlecleaner`. If one or more
directories are not Ruby projects, don't worry, as nothing will be executed and
they will just count as having saved 0kb each.

To execute it:

    $ /path_to_gemsetcleaner/workspacecleaner path_to_my_workspace
    
Example output:

    $ ~/workspacecleaner ~/workspace
    Cleaning ununsed gems in "project_1/"
    Cleaning ununsed gems in "rails/"
    Cleaning ununsed gems in "twitter-bootstrap/"
    Cleaning ununsed gems in "twitter-react/"
    Cleaning ununsed gems in "project_2/"

    Cleanup complete! A total of 15,957kb was saved!

### gemsetcleaner

The `gemsetcleaner` script takes the same parameter as `workspacecleaner`, and
also assumes that the provided *workspace* directory is where **all** your Ruby
projects are located. It will check which *gemsets* are being used by each
project, and compare that with the existing *gemsets* inside the RVM home
(assumed to be `~/.rvm/gems`). If a *gemset* is found that is not being used by
any project, it will be removed. The script will ask for confirmation before
actually deleting anything.

**Warning**: This script assumes that **all** your Ruby projects reside directly
under a specific, single *workspace* directory. If you have Ruby projects on
separate *workspace* directories, you shouldn't run this script, as it will
only check for Ruby projects inside one of the directories, not all of them.

To execute it:

    $ /path_to_gemsetcleaner/gemsetcleaner path_to_my_workspace

Example output:

    $ ~/gemsetcleaner ~/workspace
    A total of 1 unused gemsets were found:

      01) /home/rodrigo/.rvm/gems/ruby-2.5.1@useless_gemset/

    Are you sure you want to remove these 1 gemsets? (yes/no) yes

    Removing unused gemsets:
    
    Removing gemset useless_gemset......

    Done! 166,692kb cleared!

## Author

Rodrigo Castro
