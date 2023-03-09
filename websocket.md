    Basic Authentication

Note: In order to use both authentication methods we need 
Authentication
User authentication at the HTTP API
A user who calls the HTTP API can be authenticated using two mechanisms:
Pre-authentication by an HTTP reverse proxy in front of Ditto, e.g. doing HTTP BASIC Authentication by providing username and password.
A JWT issued by Google or other OpenID Connect providers.
Pre-authentication
HTTP API calls to Ditto may be authenticated with a reverse proxy (e.g. a nginx) which:
authenticates a user/subject
passes the authenticated username as HTTP header
ensures that this HTTP header can never be written by the end-user
By default, pre-authentication is disabled in the Ditto gateway services. It can however be enabled by configuring the environment variable ENABLE_PRE_AUTHENTICATION to the value true.
When it is enabled, the reverse proxy has to set the HTTP header x-ditto-pre-authenticated.
The format of the “pre-authenticated” string is: <issuer>:<subject>. The issuer defines which system authenticated the user and the subject contains e.g. the user-id or -name.
This string must then be used in policies as “Subject ID”.
Example for a nginx “proxy” configuration:
auth_basic                    "Authentication required";
auth_basic_user_file          nginx.htpasswd;
...
proxy_set_header              x-ditto-pre-authenticated "nginx:${remote_user}";
