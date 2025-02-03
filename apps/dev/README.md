# Some tests to confirm

Check out the realm definition

    curl http://dev.k8s/auth/realms/dev/.well-known/openid-configuration

Login to get access_token jwt

    curl -d 'client_id=abc' -d 'client_secret=txhhHvocC2lxlf5qKJDS9rx3SSHD0hQA' -d 'username=johnsnow' -d 'password=wdORXy9GxO' -d 'grant_type=client_credentials' 'http://dev.k8s/auth/realms/dev/protocol/openid-connect/token'

Ensure client has direct access
Ensure user password is correct
Ensure no user first login action needed

Some useful curl commands: https://www.janua.fr/examples-of-offline-token-usage-in-keycloak/

    curl -d 'client_id=app-ui' -d 'username=johnsnow' -d 'password=johnsnowpw' -d 'grant_type=password' 'http://dev.k8s/auth/realms/dev/protocol/openid-connect/token'
    refresh_token=$(curl -d 'client_id=app0' -d 'username=johnsnow' -d 'password=johnsnowpw' -d 'grant_type=password' 'http://dev.k8s/auth/realms/dev/protocol/openid-connect/token' | jq -r '.refresh_token')
    echo $refresh_token
    curl -d 'client_id=app-ui' -d "refresh_token=${refresh_token}" -d 'grant_type=refresh_token' 'http://dev.k8s/auth/realms/dev/protocol/openid-connect/token'


Consider this diagram: https://github.com/keycloak/keycloak/discussions/9239#discussioncomment-1846726

    +--------+                                           +---------------+
    |        |--(A)------- Authorization Grant --------->|               |
    |        |                                           |               |
    |        |<-(B)----------- Access Token -------------|               |
    |        |               & Refresh Token             |               |
    |        |                                           |               |
    |        |                            +----------+   |               |
    |        |--(C)---- Access Token ---->|          |   |               |
    |        |                            |          |   |               |
    |        |<-(D)- Protected Resource --| Resource |   | Authorization |
    | Client |                            |  Server  |   |     Server    |
    |        |--(E)---- Access Token ---->|          |   |               |
    |        |                            |          |   |               |
    |        |<-(F)- Invalid Token Error -|          |   |               |
    |        |                            +----------+   |               |
    |        |                                           |               |
    |        |--(G)----------- Refresh Token ----------->|               |
    |        |                                           |               |
    |        |<-(H)----------- Access Token -------------|               |
    +--------+           & Optional Refresh Token        +---------------+

                Figure 2: Refreshing an Expired Access Token

Get the jwks uri

    curl http://dev.k8s/auth/realms/dev/protocol/openid-connect/certs

# Curling into the pie-k8s-python

Make a curl against an app endpoing

    curl http://dev.k8s/pie/v0/chunks

Or from within the cluster

    curl http://pie-k8s-python/pie/v0/chunks

Now add auth via access_token

    access_token=$(curl -d 'client_id=app-ui' -d 'username=johnsnow' -d 'password=johnsnowpw' -d 'grant_type=password' 'http://dev.k8s/auth/realms/dev/protocol/openid-connect/token' | jq -r '.access_token')
    curl -H "Authorization: Bearer ${access_token}" http://pie-k8s-python/pie/v0/chunks
    curl -H "Authorization: Bearer ${access_token}" -X POST http://k8s-python/pie/v0/chunks -H "Content-Type: application/json" -d '{"name": "example", "num_chunks": 25}'

How to setup client scopes to map from client to audience: https://dev.to/metacosmos/how-to-configure-audience-in-keycloak-kp4
