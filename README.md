# Oracle Graph Shell Helper Tools

Since Oracle Graph 20.3, using the PGX server requires authentication. This is achieved by requesting an authorization token from the PGX server, then passing this token when creating the `ServerInstance` object. Unfortunately, the authentication is not fully integrated with the core Graph APIs and tools. This means that you need to get the token yourself first (using `curl` or `wget`) then pass it to the graph shell when it prompts you for it.

For example:
```
$ curl -X POST -H 'Content-Type: application/json' -d '{"username": "scott", "password": "tiger"}' http://localhost:7007/auth/token
```
which returns the following
```
{"access_token":"eyJ...a7A","token_type":"bearer","expires_in":3600}
```
Then cut the token proper from this output, invoke the graph shell and paste the token when prompted:

```
$ ./opt/graph/bin/opg-jshell -b http://localhost:7007

enter authentication token (press Enter for no token): ******************************
```

This is a cumbersome and error prone process. The tools offered here are wrapper shell scripts that make authentication fully transparent. All you need is to pass username and password to the script. The main wrapper then takes care of getting the token and passing it to the actual graph shell.

The following scripts are included

- `opg`: the wrapper script proper that does all the work and is used by the other scripts
- `opg-jshell`: invokes the jshell-based shell via the `opg` script.
- `opg-groovy`: invokes the groovy-based shell via `opg` script
- `get-token`: just fetches the token using the `opg` script

Using the scripts
-----------------

Call the proper graph shell script above, passing the server URL together with username and password. For example:

```
$ ./opg-jshell -b http://localhost:7007 -u scott -p tiger
[INFO] Running java version 11.0.6
[INFO] Fetching security token from PGX server http://localhost:7007
[INFO] Starting /opt/oracle/graph/bin/opg-jshell
For an introduction type: /help intro
Oracle Graph Server Shell 20.3.0
PGX server version: 20.1.1 type: SM
PGX server API version: 3.8.1
PGQL version: 1.3
Variables instance, session, and analyst ready to use.
```
Parameters are
- -b/--base_url
- -u/--username
- -p/--password

User name and/or password will be prompted if not passed.

```
$ ./opg-jshell --base_url http://localhost:7007 --username scott
[INFO] Running java version 11.0.6
Password: *****
[INFO] Fetching security token from PGX server http://localhost:7007
[INFO] Starting /opt/oracle/graph/bin/opg-jshell
For an introduction type: /help intro
Oracle Graph Server Shell 20.3.0
PGX server version: 20.1.1 type: SM
PGX server API version: 3.8.1
PGQL version: 1.3
Variables instance, session, and analyst ready to use.
opg-jshell>
```

Passing invalid credentials cause a failure:

```
$ opg-jshell -b http://localhost:7007 -u scott -p xxxxxx
[INFO] Running java version 11.0.6
[INFO] Fetching security token from PGX server http://localhost:7007
[ERROR] Unable to get security token with provided credentials: {"error_type":"UNAUTHORIZED","error_message":"invalid username/password"}
```
Important note: that error can have a number of causes
- the username or password are invalid
- the account may be locked
- the database may be down
- the listener may be down
- the jdbc URL in pgx.conf is malformed

The `-v` / `--verbose` parameter lets you see details about the token:

```
$ ./opg-jshell -b http://localhost:7007 -u scott -p tiger -v
[INFO] Running java version 11.0.6
[INFO] Fetching security token from PGX server http://localhost:7007
[DEBUG] Token type: bearer
[DEBUG] Token value: eyJraWQiOiJEYXRhYmFzZVJlYWxtIiwiYWxnIjoiUlMyNTYifQ.eyJzdWIiOiJzY290dCIsInJvbGVzIjpbIkdSQVBIX0RFVkVMT1BFUiIsIkdSQVBIX1VTRVIiLCJDT05ORUNUIiwiRVhQX0ZVTExfREFUQUJBU0UiLCJQTFVTVFJBQ0UiLCJHUkFQSF9BRE1JTklTVFJBVE9SIiwiUkVTT1VSQ0UiXSwiaXNzIjoib3JhY2xlLnBnLmlkZW50aXR5LnJlc3QuQXV0aGVudGljYXRpb25TZXJ2aWNlIiwiZXhwIjoxNTk4MzE4NDgyfQ.V0oyLo33GcGpwihpQ1_eovuQya086ZmpHT-tXmPfpzQ0xK-_P0FbU5wUAjP-xXohwpDk71laNciDlutEvKPZoGpPqveLtK3Numz9sqzFu8S7D9LE1Bj-NJi3dytV8gjzJ-IHlrwWKudYoEgSvDz5FZ_-LgP_mjdUTzJbE8moVeKSKllQ-cfYLVtl8_17Tr2KyFbY4USlVpzt3ZfUvN3fKr5j4F2hgeFVb-fY6xzeJBe4pVKcXSybaOEoV_UAlnZdIwAzMcAESvR7y4CQXqwIczzE3wsE_-0eaylZt4Hjmxvd2_sIX4M0b9SaBZIrE-NfZTKWUVSmUc8oS6Y6CBGU1Q
[DEBUG] Token header: {"kid":"DatabaseRealm","alg":"RS256"}
[DEBUG] Token payload: {"sub":"scott","roles":["GRAPH_DEVELOPER","GRAPH_USER","CONNECT","EXP_FULL_DATABASE","PLUSTRACE","GRAPH_ADMINISTRATOR","RESOURCE"],"iss":"oracle.pg.identity.rest.AuthenticationService","exp":1598318482}
[DEBUG] Token expires in: 14400 seconds
[INFO] Starting /opt/oracle/graph/bin/opg-jshell
For an introduction type: /help intro
Oracle Graph Server Shell 20.3.0
PGX server version: 20.1.1 type: SM
PGX server API version: 3.8.1
PGQL version: 1.3
Variables instance, session, and analyst ready to use.
opg-jshell>
```

To use the shell with the graph engine in embedded mode, just pass no parameter:

```
$ ./opg-jshell
[INFO] Running java version 11.0.6
[INFO] Starting /opt/oracle/graph/bin/opg-jshell in local mode
16:22:57,545 INFO Ctrl$1 - >>> start engine
For an introduction type: /help intro
Oracle Graph Server Shell 20.3.0
PGX server version: 20.1.1 type: SM running in embedded mode.
PGX server API version: 3.8.1
PGQL version: 1.3
Variables instance, session, and analyst ready to use.
opg-jshell> instance
instance ==> ServerInstance[embedded=true,version=20.1.1]
opg-jshell>
```

To use the shell without any connection to a PGX server and no embedded engine, pass the `--no_connect` parameter

```
$ ./opg-jshell --no_connect
[INFO] Running java version 11.0.6
[INFO] Starting /opt/oracle/graph/bin/opg-jshell in local mode
For an introduction type: /help intro
Oracle Graph Server Shell 20.3.0
opg-jshell>
```

Use `get-token` to just fetch a token

```
$ ./get-token -b http://localhost:7007 -u scott -p tiger
[INFO] Running java version 11.0.6
[INFO] Fetching security token from PGX server http://localhost:7007
eyJraWQiOiJEYXRhYmFzZVJlYWxtIiwiYWxnIjoiUlMyNTYifQ.eyJzdWIiOiJzY290dCIsInJvbGVzIjpbIkdSQVBIX0RFVkVMT1BFUiIsIkdSQVBIX1VTRVIiLCJDT05ORUNUIiwiRVhQX0ZVTExfREFUQUJBU0UiLCJQTFVTVFJBQ0UiLCJHUkFQSF9BRE1JTklTVFJBVE9SIiwiUkVTT1VSQ0UiXSwiaXNzIjoib3JhY2xlLnBnLmlkZW50aXR5LnJlc3QuQXV0aGVudGljYXRpb25TZXJ2aWNlIiwiZXhwIjoxNTk5MTQ2NzA2fQ.eatJcJ85lVYad62SR_jmSAFDfsPMCNiWq_FQWGgK4cMJUvHoAFPt7P1ZGTcu7i04phFgdEHebzxxHWhKzUjZnRZtQp3pbvgUFAclUg1-xi2MjaXspVjCY7nOQl7t6P7O4__6awFHR_mos8PJErZ4ocxYpE_PA2BNfXrEgs1kS7mXG0zmUXDLAqb7D60UQeIzzM0xXwIpBXQjHIGOTTE1tqoK0KOxoXBNaatQtXk7NctpK52pGWXHAkvuBfvGcP6c6oREj3eRYCNAad5tHIZyK15EOJDy2cEWxZugUsivybsg1viPW_TVWlapMip-zmfE8nMyk6ei5V5LG97n5GiFiQ
```
Use this to extract the token in a file or in environment variable:

```
$ TOKEN=`./get-token -b http://localhost:7007 -u scott -p tiger`
```
```
$ ./get-token -b http://localhost:7007 -u scott -p tiger >token.txt
```

How does it all work ?
----------------------

1. The parameters are read, checked and prompted if missing
2. The token is fetched using `curl`
```
PGX_RESPONSE=`curl -X POST -H 'Content-Type: application/json' -d '{"username": "scott", "password": "tiger"}' http://localhost:7007/auth/token`
```
3. The JSON response is parsed and the token is extracted
```
PGX_ACCESS_TOKEN=`echo $PGX_RESPONSE | sed -e 's/[{}]/''/g' | sed s/\"//g | awk -v RS=',' -F: '$1=="access_token"{print $2}'`
PGX_TOKEN_TYPE=`echo $PGX_RESPONSE | sed -e 's/[{}]/''/g' | sed s/\"//g | awk -v RS=',' -F: '$1=="token_type"{print $2}'`
PGX_EXPIRES_IN=`echo $PGX_RESPONSE | sed -e 's/[{}]/''/g' | sed s/\"//g | awk -v RS=',' -F: '$1=="expires_in"{print $2}'`
```
4. It is saved in a keystore. If the keystore does not exist, it is created.
```
echo $PGX_ACCESS_TOKEN | keytool -importpass -alias AuthorizationToken -keystore $PGX_KEYSTORE -storepass ""
```
5. Finally the graph shell script proper is invoked
```
$PG_HOME/bin/$PG_COMMAND  --keystore_auth_token -k $PGX_KEYSTORE -b $BASE_URL $OTHER_PARAMS
```

Configuration
-------------

The actual graph shell scripts are expected to be in `/opt/oracle/graph/bin` - the default installation location of Oracle Graph. To use the wrapper scripts with the graph client, just set `PG_HOME` to point to your graph client installation. For example:

```
$ export PG_HOME=/usr/local/oracle/graph/oracle-graph-client-20.3.0
$ ./opg-jshell -b http://localhost:7007 -u scott -p tiger
[INFO] Running java version 11.0.6
[INFO] Fetching security token from PGX server http://localhost:7007
[INFO] Starting /usr/local/oracle/graph/oracle-graph-client-20.3.0/bin/opg-jshell
For an introduction type: /help intro
Oracle Graph Client Shell 20.3.0
PGX server version: 20.1.1 type: SM
PGX server API version: 3.8.1
PGQL version: 1.3
Variables instance, session, and analyst ready to use.
opg-jshell>
```