## Running a Local DPN Cluster

You can run a local DPN REST cluster using the run_cluster script in the script
directory. If you have never run the cluster before, you'll need to set up the
SQLite databases for the cluster by running this command from the top-level directory
of the project:

```
./script/setup_cluster.rb
```

If you have run the cluster before, and you have new database migrations to run, run
this from the top-level directory of the prject:

```
./script/migrate_cluster.rb
```

When the databases are ready, run the cluster with this command:

```
./script/run_cluster.rb -f
```

The -f option loads all of the fixtures under test/fixtures/integration.
As long as your migrations are up to date, you can set up and run the cluster
with a single command, like this:

```
./script/setup_cluster.rb && ./script/run_cluster.rb -f
```

This will run five local DPN nodes on five different ports, each
impersonating one of the actual DPN nodes. The run as follows:

1. APTrust on port 3001
2. Chronopolis on port 3002
3. Hathi Trust on port 3003
4. Stanford on port 3004
5. Texas on port 3005

All of these nodes will have a set of pre-loaded data for testing, and each time
you run run_cluster.sh, it resets the data in all the nodes. In the pre-load data,
each node has bag entries and replication requests for _its own_ six bags, and no
node knows about the bags in the other nodes.

You can access the REST API of each of these local nodes using one of the following
API keys:

1. APTrust: aptrust_token
2. Chronopolis: chron_token
3. Hathi Trust: hathi_token
4. Stanford: sdr_token
5. Texas: tdr_token

You should be able to connect to any node using any of these tokens. Connecting
with aptrust_token will make you admin at APTrust, and a the APTrust user at
every other node. The same goes for all the other tokens. chron_token makes you
admin when connecting to Chronopolis, and the Chron user when connecting to
any other node.

To test whether you can connect to the cluster, run the following curl command,
substiting the token and port number as necessary. Note the format of the token
header.

```
curl -H "Authorization: Token token=sdr_token" -L http://localhost:3001/api-v2/bag/
```

You should see a JSON response with a list of bags. If you see a response that says
"HTTP Token: Access denied" make sure your Authorization header is formatted
correctly.

### Test Data for the Cluster

The test data for the local DPN cluster is in test/fixtures/integration. The YAML
fixtures are reloaded each time the cluster starts, wiping out any data from previous
tests.

There are also six test bags in test/fixtures/integration/testbags/. The bag entries
for each node refer to these six bags, and the bag sizes and tag manifest checksums
match the actual bags. There are notes in the YAML files explaining that final two
digits of each bag registry entry match the final two digits of the test bag names.
So a bag UUID ending in 01 matches the bag IntTestValidBag01.tar. The UUID ending in
02 refers to the bag IntTestValidBag02.tar, etc.

Also note that the first digit of each bag UUID matches the bag's admin node. So all
APTrust bag UUIDs start with 1, matching APTrust port 3001. Chronopolis bag UUIDs
start with 2, matching Chronopolis port 3002, etc. This should provide some cues
to help remember what's what when you are visually reviewing test results and log
file entries.
