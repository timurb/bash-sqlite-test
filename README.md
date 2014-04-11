## bash-sqlite-test

A simple proof-of-concept for realtime logs processing using Bash and SQLite3.
It produces median response size for a typical Apache access.log but can be
quickly and easily adapted to similar puproses.

Don't use it in production.

* *Note:* Odd-sized collection formula is used for both odd-sized and even-sized collections 
(see [Median](http://en.wikipedia.org/wiki/Median#Easy_explanation_of_the_sample_median) 
for more info) thus producing sane and acceptable results which are though slightly incorrect
from mathematical point of view.

### Requirements

SQLite3 is required for the script to work.

In Ubuntu you can install it by running `apt-get install sqlite3`

### Description

There are 4 scripts in the set:
* `filldb`: main script that does most of processing
* `process_apache_log`: extracts response code and response size from Apache log
* `json_median_size`: formats the argument as JSON file
* `do_processing`: binds all these together to make testing easier

### Usage

You'll want to point your Apache/nginx root to `response_codes/` dir.

#### do_processing

Watches the file passed as a param and calculates median response size from it.

Example:
```
do_processing /var/log/apache.log "tail -f"
```

You can omit any of params:
 * filename defaults to `apache.log`
 * command defaults to `cat`

#### process_apache_log

Extracts response code and response size from Apache log.

Usage is the same as for `do_processing` (see above).
No default filename in this case.

The processed output is dumped to STDOUT.

#### filldb

Reads the data from STDIN, calculates the median per code and passes the 
result to its param.

Example:
```
cat processed_data.log | DB=mydb.db ./filldb echo
```

In this case `processed_data.log` should contain lines in the format:
```
200 123
401 444
...
```
The data will be stored in `mydb.db` and the result will be dumped to STDOUT
with each new line read.

#### json_median_size

It is a simple formatter.
It dumps the second param to the filename passed as a first param.

Example:
```
OUTPUT_PATH=/var/www json_median_size 200 1234
```
This command will create `/var/www/200` with the content `{"median_size": 1234}`

## License and Author

* Author:: Timur Batyrshin <erthad@gmail.com>
* License:: MIT
