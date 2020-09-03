=========================================================================
Oracle Graph
Shell helper tools
Graph Version 20.3
Last updated by albert.godfrind@oracle.com
On 27-Aug-2020
=========================================================================

This directory contains a set of shell scripts that simplifies the use of graphs with the security mechanism introduced in version 20.3

Using the graph shells
======================

Use the graph shell passing the server URL with username and password

  $ ./opg-jshell -b http://localhost:7007 -u scott -p tiger
  [INFO] Running java version 11.0.6
  [INFO] Fetching security token from PGX server http://localhost:7007
  [INFO] Starting opg-jshell
  For an introduction type: /help intro
  Oracle Graph Server Shell 20.3.0
  PGX server version: 20.1.1 type: SM
  PGX server API version: 3.8.1
  PGQL version: 1.3
  Variables instance, session, and analyst ready to use.

User name and/or password will be prompted if not passed.

  $ ./opg-jshell --base_url http://localhost:7007 --username scott
  [INFO] Running java version 11.0.6
  Password: [INFO] Fetching security token from PGX server http://localhost:7007
  [INFO] Starting opg-jshell
  For an introduction type: /help intro
  Oracle Graph Server Shell 20.3.0
  PGX server version: 20.1.1 type: SM
  PGX server API version: 3.8.1
  PGQL version: 1.3
  Variables instance, session, and analyst ready to use.
  opg-jshell>

The -s / --show parameter lets you see details about the token:

  $ ./opg-jshell -b http://localhost:7007 -u scott -p tiger -s
  [INFO] Running java version 11.0.6
  [INFO] Fetching security token from PGX server http://localhost:7007
  [DEBUG] Token type: bearer
  [DEBUG] Token value: eyJraWQiOiJEYXRhYmFzZVJlYWxtIiwiYWxnIjoiUlMyNTYifQ.eyJzdWIiOiJzY290dCIsInJvbGVzIjpbIkdSQVBIX0RFVkVMT1BFUiIsIkdSQVBIX1VTRVIiLCJDT05ORUNUIiwiRVhQX0ZVTExfREFUQUJBU0UiLCJQTFVTVFJBQ0UiLCJHUkFQSF9BRE1JTklTVFJBVE9SIiwiUkVTT1VSQ0UiXSwiaXNzIjoib3JhY2xlLnBnLmlkZW50aXR5LnJlc3QuQXV0aGVudGljYXRpb25TZXJ2aWNlIiwiZXhwIjoxNTk4MzE4NDgyfQ.V0oyLo33GcGpwihpQ1_eovuQya086ZmpHT-tXmPfpzQ0xK-_P0FbU5wUAjP-xXohwpDk71laNciDlutEvKPZoGpPqveLtK3Numz9sqzFu8S7D9LE1Bj-NJi3dytV8gjzJ-IHlrwWKudYoEgSvDz5FZ_-LgP_mjdUTzJbE8moVeKSKllQ-cfYLVtl8_17Tr2KyFbY4USlVpzt3ZfUvN3fKr5j4F2hgeFVb-fY6xzeJBe4pVKcXSybaOEoV_UAlnZdIwAzMcAESvR7y4CQXqwIczzE3wsE_-0eaylZt4Hjmxvd2_sIX4M0b9SaBZIrE-NfZTKWUVSmUc8oS6Y6CBGU1Q
  [DEBUG] Token header: {"kid":"DatabaseRealm","alg":"RS256"}
  [DEBUG] Token payload: {"sub":"scott","roles":["GRAPH_DEVELOPER","GRAPH_USER","CONNECT","EXP_FULL_DATABASE","PLUSTRACE","GRAPH_ADMINISTRATOR","RESOURCE"],"iss":"oracle.pg.identity.rest.AuthenticationService","exp":1598318482}
  [DEBUG] Token expires in: 14400 seconds
  [INFO] Starting opg-jshell
  For an introduction type: /help intro
  Oracle Graph Server Shell 20.3.0
  PGX server version: 20.1.1 type: SM
  PGX server API version: 3.8.1
  PGQL version: 1.3
  Variables instance, session, and analyst ready to use.
  opg-jshell>

To use the shell with the graph engine in embedded mode, just pass no parameter:

  $ ./opg-jshell
  [INFO] Running java version 11.0.6
  [INFO] Starting opg-jshell in local mode
  23:23:04,348 INFO Ctrl$1 - >>> start engine
  For an introduction type: /help intro
  Oracle Graph Server Shell 20.3.0
  PGX server version: 20.1.1 type: SM running in embedded mode.
  PGX server API version: 3.8.1
  PGQL version: 1.3
  Variables instance, session, and analyst ready to use.
  opg-jshell>

To use the shell without any connection to a PGX server and no embedded engine, pass the "--no_connect" parameter

  $ ./opg-jshell --no_connect
  [INFO] Running java version 11.0.6
  [INFO] Starting opg-jshell in local mode
  For an introduction type: /help intro
  Oracle Graph Server Shell 20.3.0
  opg-jshell>

Use get-token to just fetch a token

  $ ./get-token -b http://localhost:7007 -u scott -p tiger
  [INFO] Running java version 11.0.6
  [INFO] Fetching security token from PGX server http://localhost:7007
  [DEBUG] Token type: bearer
  [DEBUG] Token value: eyJraWQiOiJEYXRhYmFzZVJlYWxtIiwiYWxnIjoiUlMyNTYifQ.eyJzdWIiOiJzY290dCIsInJvbGVzIjpbIkdSQVBIX0RFVkVMT1BFUiIsIkdSQVBIX1VTRVIiLCJDT05ORUNUIiwiRVhQX0ZVTExfREFUQUJBU0UiLCJQTFVTVFJBQ0UiLCJHUkFQSF9BRE1JTklTVFJBVE9SIiwiUkVTT1VSQ0UiXSwiaXNzIjoib3JhY2xlLnBnLmlkZW50aXR5LnJlc3QuQXV0aGVudGljYXRpb25TZXJ2aWNlIiwiZXhwIjoxNTk4MzE4NzAyfQ.ZvKsy_7hK4Lm0KAOQRY7cjBah_4j8IH_iUUjnYnun0jHO067wkPY_PGbv8VFoaj09ar-z87mDD_NOeqtwXjIqYRZHIYBm5OuowpsvFd7XjXyYHIv0PkbL7C0QDV_QwvUQVkeIRe_Ru7dUz80MYMhnGDmx-1ZDaGLqJAHzGuJm1YxGAzztAxZ5aaMWh9TAu9FiSoEv7ICmR36WgMhYdpdJJ1PBudxiBhcJ09uSrnm-HACQ_4QpGQ6nTCrMEJhzKIZMceFcrQxvfpxbkymTTMEHk9OVieVhmon4xzlea7zNtNppAlfH5mcHPEnme78qsD4Dvgs1LT8AUnhlUyQJi6M4Q
  [DEBUG] Token header: {"kid":"DatabaseRealm","alg":"RS256"}
  [DEBUG] Token payload: {"sub":"scott","roles":["GRAPH_DEVELOPER","GRAPH_USER","CONNECT","EXP_FULL_DATABASE","PLUSTRACE","GRAPH_ADMINISTRATOR","RESOURCE"],"iss":"oracle.pg.identity.rest.AuthenticationService","exp":1598318702}
  [DEBUG] Token expires in: 14400 seconds

