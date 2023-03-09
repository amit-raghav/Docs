# Basic Authentication

###### Note: In order to use both authentication methods we need 
# Authentication
User authentication at the HTTP API
- A user who calls the HTTP API can be authenticated using two mechanisms:
Pre-authentication by an HTTP reverse proxy in front of Ditto, e.g. doing HTTP BASIC Authentication by providing username and password.
- A JWT issued by Google or other OpenID Connect providers.
# Pre-authentication
HTTP API calls to Ditto may be authenticated with a reverse proxy (e.g. a nginx) which:
- authenticates a user/subject
- passes the authenticated username as HTTP header
- ensures that this HTTP header can never be written by the end-user

By default, pre-authentication is disabled in the Ditto [gateway](https://www.eclipse.org/ditto/architecture-services-gateway.html) services. It can however be enabled by configuring the environment variable  ENABLE_PRE_AUTHENTICATION  to the value true.

When it is enabled, the reverse proxy has to set the HTTP header x-ditto-pre-authenticated.

The format of the “pre-authenticated” string is: &#42;issuer&#42;:&#42;subject&#42;. The issuer defines which system authenticated the user and the subject contains e.g. the user-id or -name.
  
This string must then be used in [policies](https://www.eclipse.org/ditto/basic-policy.html#subjects) as “Subject ID”.
  
Example for a nginx “proxy” configuration:
auth_basic                    "Authentication required";
  
auth_basic_user_file          nginx.htpasswd;
  
...
  
proxy_set_header              x-ditto-pre-authenticated "nginx:${remote_user}";
  

Subjects in a policy define who gets permissions granted/revoked on the resources of a policy entry.
  
Each subject ID contains a prefix defining the subject “issuer” (so which party issued the authentication) and an actual subject, separated with a colon:
  
&#42;subject-issuer&#42;:&#42;subject&#42;

The subject can be one of the following ones:
  
- nginx:&#42;nginx-username&#42; - when using nginx as [pre-authentication provider](https://www.eclipse.org/ditto/installation-operating.html#pre-authentication) - by default enabled in the Ditto installation’s nginx
- &#42;other-pre-auth-provider&#42;:&#42;username&#42; - when using another custom provider as [pre-authentication provider](https://www.eclipse.org/ditto/installation-operating.html#pre-authentication) which sets the x-ditto-pre-authenticated HTTP header
