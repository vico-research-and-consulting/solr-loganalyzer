solr-loganalyzer
=================

A query analyzer that parses Solr's log file to get some basic query statistics 

Note that you need to enable logging at the INFO level for this to
work and you need to have cores enabled. You also need python3 installed.

Basic usage
===========

# Modify solr log4j configuration to use bufferend io
```
log4j.appender.file.bufferedIO=true
log4j.appender.file.bufferSize=10000 # default is 1024
```
Restart server

# Usage:
```
# reading from stdin
cat file1 file2 | ./solr_loganalyzer.py --max 20
tail -1000  file | ./solr_loganalyzer.py --max 20

# create statistics and a siege urls file by continously reading the solr.log 
tail -F solr.log | ./solr_loganalyzer.py --max 20 --write_file urls.txt http://127.0.0.1:10080/solr4/ | tee stats.txt

# perform a siege loadtest by using the url file
apt-get install siege
siege -f urls.txt --concurrent=5 --reps=5 --delay=0.5 --no-parser
# poor mans loadtest
(for i in `seq 1 5`;do curl -K urls.txt >/dev/null & done; wait)

# reading one or more files
./solr_loganalyzer.py solr.log solr.log.1 solr.log.2
```

The analyzer outputs statistics grouped by Solr core. Here is an example:

```
Top Endpoints for core1
========================================
1) "/mlt" 3 times
   
Top Searh URLs for core1
========================================
1) "mlt.count=16&&q=qrows=16" 1 times
2) "mlt.count=16&start=0&q=&rows=16" 1 times
   
Slowest Searches for core1
========================================
1) "mlt.count=16&&q=qrows=16" 30 times
2) "mlt.count=16&start=0&q=&rows=16" 11 times
   
Top Factet Fields for core1
========================================
ITEM 1    : 3384 times      "language"
ITEM 2    : 1578 times      "authorName"
   
Search Time for core1
========================================
Median     2
75%       11
90%       11
99%       30
```

# Testing

```
cd tests
./tests.py
```

Open issues
===========
- Is it possible to log addtional 

Notes and Copyright
===================

This tool is inspired by [redis-faina](https://github.com/Instagram/redis-faina).
The tool was forked from [solr-loganalyzer](https://github.com/dfdesho/solr-loganalyzer).

This tool is distributed with the [BSD-2 Licence](LICENCE).

Authors
=======

* Didier Deshommes <dfdeshom@gmail.com>
* Marc Schoechlin <marc.schoechlin@vico-research.com>

