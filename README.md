Dandelion
=========
Incremental Git repository deployment.

Install
-------
Ensure that Ruby and RubyGems are installed, then run:

    $ gem install dandelion
    
Alternatively, you can build the gem yourself:

    $ git clone git://github.com/scttnlsn/dandelion.git
    $ cd dandelion
    $ rake install
    
Config
------
Configuration options are specified in a YAML file (by default, the root of your
Git repository is searched for a file named `dandelion.yml`). Example:

    # Required
    scheme: sftp
    host: example.com
    username: user
    password: pass
    
    # Optional
    path: path/to/deployment
    exclude:
        - .gitignore
        - dandelion.yml
    revision_file: .revision

Passwords
---------
If you set up your ssh config to log in to the specified server using an ssh key, you do not need to specify the password in the configuration file. You must use `ssh-add` to add the appropriate key to your ssh agent first.

If SSH logins are not supported by your remote server, and you are using OSX, you can store the login credentials in your keychain and dandelion will search for them when deploying. Do this by first logging in to the server through an (S)FTP client (like Cyberduck) that will save the password in your keychain.

It is recommended that you **_do not_** select **"Allow all"** when you are asked if ruby can access your keychain--this would give **all ruby applications access** to these credentials.

Schemes
-------
There is support for multiple backend file transfer schemes.  The configuration
must specify one of these schemes and the set of additional parameters required
by the given scheme.

**SFTP**: `scheme: sftp`

Required:

 * `host`
 * `username`
 * `password`

Optional:

 * `path`
 * `exclude`
 * `port`
 * `revision_file` (defaults to .revision)
 * `preserve_permissions` (defaults to true)

**FTP**: `scheme: ftp`

Required:

 * `host`
 * `username`
 * `password`

Optional:

 * `path`
 * `exclude`
 * `port`
 * `revision_file` (defaults to .revision)
 * `passive` (defaults to true)
    
**Amazon S3**: `scheme: s3`

Required:

 * `access_key_id`
 * `secret_access_key`
 * `bucket_name`

Optional:

 * `path`
 * `exclude`
 * `revision_file` (defaults to .revision)

Usage
-----
From within your Git repository, run:

    $ dandelion deploy
    
This will deploy the local `HEAD` revision to the location specified in the config
file.  Dandelion keeps track of the currently deployed revision so that only files
which have been added/changed/deleted need to be transferred.

You can specify the revision you wish to deploy and Dandelion will determine which
files need to be transferred:

    $ dandelion deploy <revision>

For a more complete summary of usage options, run:

    $ dandelion -h
    Usage: dandelion [options] <command> [<args>]
        -v, --version                    Display the current version
        -h, --help                       Display this screen
            --repo=[REPO]                Use the given repository
            --config=[CONFIG]            Use the given configuration file

    Available commands:
        deploy
        status
        
Note that when specifying the repository or configuration file, the given paths
are relative to the current working directory (not the repository root).  To see
the options for a particular command, run:

    $ dandelion <command> -h
