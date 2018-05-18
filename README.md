# Gemset Cleaner

This is a simple script to uninstall unused gems in your Ruby projects.
The main goal of this script is to save disk space, as this might be a concern
if you have installed Linux on a small SSD, for example.

Unused gems are gems which are not being used by the application's Gemfile.
When you update a gem using bundler, it will only install the new version, but
not uninstall the old one, so you'll end up with both versions of the gem
installed on your system/gemset. In the long run, you'll end up with many
unused gems just hogging up disk space.

**Warning**: This will uninstall **all** the gems which are not present in
the `Gemfile.lock` file, so if you have a standalone gem that you use locally
but should not be in the Gemfile (for example: the `nexus` gem which is used to
upload new gems to a Nexus gem repository), these will also be removed.
Dependencies, however, will remain.

## Requirements

1. Linux. This script was not tested and may not work on MacOS systems.
2. [RVM](https://rvm.io/). There is no Support for [rbenv](http://rbenv.org/)
(yet).
3. Having a valid Ruby project with both `Gemfile` and `.ruby-version` files

## Usage

There are two separate scripts, `bundlecleaner` and `workspacecleaner`. The
first will basically only run the command `bundle clean --force`, while also
printing some nice things like the amount of space saved. The `workspacecleaner`
will run `bundlecleaner` for each project in your *workspace* directory, in
order to clean them all in one go.

### bundlecleaner

The `bundlecleaner` script must be executed inside the project directory, and
it can take an optional `-q` argument. If executed without `-q`, it will print
some progress updates and the space saved in the end. If executed with `-q`,
it will only print out the raw number of kilobytes saved. This is intended to
be used on the `workspacecleaner` script, to be able to add the total space
saved.

    $ cd ~/workspace/my-project
    $ /path_to_bundlecleaner/bundlecleaner
    
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

    $ /path_to_workspacecleaner/workspacecleaner path_to_my_workspace
    
Example output:

    $ ~/workspacecleaner ~/workspace
    Cleaning ununsed gems in "project_1/"
    Cleaning ununsed gems in "rails/"
    Cleaning ununsed gems in "twitter-bootstrap/"
    Cleaning ununsed gems in "twitter-react/"
    Cleaning ununsed gems in "project_2/"

    Cleanup complete! A total of 15,957kb was saved!

## Author

Rodrigo Castro
