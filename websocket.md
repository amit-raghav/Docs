# Basic Authentication

#### Note: In order to use both authentication methods we need 
# Authentication
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


Subjects in a policy define who gets permissions granted/revoked on the resources of a policy entry.
Each subject ID contains a prefix defining the subject “issuer” (so which party issued the authentication) and an actual subject, separated with a colon:
<subject-issuer>:<subject>

The subject can be one of the following ones:
nginx:<nginx-username> - when using nginx as pre-authentication provider - by default enabled in the Ditto installation’s nginx
<other-pre-auth-provider>:<username> - when using another custom provider as pre-authentication provider which sets the x-ditto-pre-authenticated HTTP header




                                  













# JWT Authentication

# OpenID Connect
The authentication provider must be added to the ditto-gateway configuration with a unique configuration key (e.g. myprovier in the example below).
Either issuer as a single supported JWT "iss" claim or issuers (as a list of supported JWT "iss" claims) has to be configured. If the issuer is configured, this list has priority and the value configured in issuer will be ignored.
The configured auth-subjects, an optional field, takes a list of placeholders that will be evaluated against incoming JWTs.
For each entry in auth-subjects an authorization subject will be generated. If the entry contains unresolvable placeholders, it will be ignored in full. When auth-subjects is not provided, the "sub" claim ({{ jwt:sub }}) is used by default.
Please read more details on the OpenId Connect configuration placeholder to find out what is possible when defining the auth-subjects.
ditto.gateway.authentication {
    oauth {
      openid-connect-issuers = {
        myprovider = {
          issuer = "localhost:9000"
          #issuers = [
          #  "localhost:9000/one"
          #  "localhost:9000/two"
          #]
          auth-subjects = [
            "{{ jwt:sub }}",
            "{{ jwt:sub }}/{{ jwt:scp }}",
            "{{ jwt:sub }}/{{ jwt:scp }}@{{ jwt:client_id }}",
            "{{ jwt:sub }}/{{ jwt:scp }}@{{ jwt:non_existing }}",
            "{{ jwt:roles/support }}"
          ]
        }
      }
    }
}

Note: The issuer must not include the http:// or https:// prefix as this is added based on the configuration value of ditto.gateway.authentication.oauth.protocol.
The configured subject-issuer will be used to prefix the value of each individual auth-subject.
{
  "subjects": {
    "<provider>:<auth-subject-0>": {
      "type": "generated"
    },
    ...
    "<provider>:<auth-subject-n>": {
      "type": "generated"
    }
  }
}

As of the OAuth2.0 and OpenID Connect standards Ditto expects the headers Authorization: Bearer <JWT> and Content-Type: application/json, containing the issued token of the provider.
The token has to be issued beforehand. The required logic is not provided by Ditto
