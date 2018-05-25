# mysql-file-sync
## What is mysql-file-sync?
It syncs a set of MySQL tables with a set of files on disk.

## Installation
```
npm install mysql-file-sync -g
```
This will install mysql-file-sync globally so it can be run from the command line.

## Usage
* Create a file `.mysql-file-sync.yml`  
The structure should have this structure:
```yaml
---
database:
  host: localhost
  username: my_username
  password: my_password
  schema: wordpress
 
tables:
  - table_name: wp_options
    file_name: src/database/wp-options.sql
    order_by: option_name
    ignore_columns:
      - option_id
    exclude_rows:
      - column: option_name
        value_to_exclude: "^_transient_.*$"
```

* In the same folder as `.mysql-file-sync.yml` simply run:
```
mysql-file-sync
```

## What does it do? / what are the options?
* If there are changes in the database table, they get copied to the file
* If there are changes to the file, they get copied to the database

It's about as simple as that!

Actually, there's one more bit:

* First, it checks if any of the files have un-committed changes in git.
  * When the program first starts, it doesn't know whether you consider the database or the file to be most up-to-date (the timestamp might not represent what you consider to be the latest change)
  * So, we insist that you put the .sql files into git and that they are committed in git before we start
  * Then, the first thing we do is to sync from the database to the file - now, git knows about both sets of data - the file (committed) and the database (the new, uncommitted changes)