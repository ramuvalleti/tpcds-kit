# tpcds-kit

The official TPC-DS tools can be found at [tpc.org](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp).

This version is based on v2.4 and has been modified to:

* Allow compilation under macOS (commit [2ec45c5](https://github.com/gregrahn/tpcds-kit/commit/2ec45c5ed97cc860819ee630770231eac738097c))
* Address obvious query template bugs like 
  * https://github.com/gregrahn/tpcds-kit/issues/30
  * https://github.com/gregrahn/tpcds-kit/issues/31
  * https://github.com/gregrahn/tpcds-kit/issues/33

## Setup

### Linux

Make sure the required development tools are installed:

Ubuntu: 
```
sudo apt-get install gcc make flex bison byacc git
```

CentOS/RHEL: 
```
sudo yum install gcc make flex bison byacc git
```

Then run the following commands to clone the repo and build the tools:

```
git clone https://github.com/databricks/tpcds-kit.git
cd tpcds-kit/tools
make OS=LINUX
```

### macOS

Make sure the required development tools are installed:

```
xcode-select --install
```
 
Then run the following commands to clone the repo and build the tools:

```
git clone https://github.com/databricks/tpcds-kit.git
cd tpcds-kit/tools
make OS=MACOS
```

## Using the TPC-DS tools

### Data generation

Data generation is done via `dsdgen`.  See `dsdgen --help` for all options.  If you do not run `dsdgen` from the `tools/` directory then you will need to use the option `-DISTRIBUTIONS /.../tpcds-kit/tools/tpcds.idx`.

### Query generation

Query generation is done via `dsqgen`.   See `dsqgen --help` for all options.

The following command can be used to generate all 99 queries in numerical order (`-QUALIFY`) for the 10TB scale factor (`-SCALE`) using the Netezza dialect template (`-DIALECT`) with the output going to `/tmp/query_0.sql` (`-OUTPUT_DIR`).

```
cd tpcds-kit/tools
mkdir ../sql_queries

./dsqgen -VERBOSE "Y" \
-DIALECT netezza \
-DIRECTORY ../query_templates \
-INPUT ../query_templates/templates.lst \
-SCALE 10000 \
-QUALIFY "Y" \
-OUTPUT_DIR ../sql_queries

```

### Known issues
If there are _END errors like (ERROR: Substitution'_END' is used before being initialized at line 63 in ../query_templates/query1.tpl), add below line to end of ../query_templates/netezza.tpl
define _END = "";

If only one query file is generated instead of 99 sql files, use below bash code to generate 99 sql files -
```
cd tpcds-kit/tools/sql_queries

IFS=$(echo -en "\n\b")
CNTR=1
for T in `cat query_0.sql`
do
  if [[ $T != "----" ]]; then 
    echo $T >> query_${CNTR}.sql
  else
    echo "Done: $CNTR"
    ls -l query_${CNTR}.sql
    cat query_${CNTR}.sql
    CNTR=$((CNTR+1)) 
  fi
done
```
