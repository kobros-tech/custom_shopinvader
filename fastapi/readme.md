# Setup FastAPI to connect frontend app with odoo

1. Install these ``auth_jwt``, ``shopinvader_v2_app_demo`` modules
2. Open ``FastAPI ==> FastAPI Endpoint`` view and create a new ``fastapi.endpoint`` record and assign fields the suitable values:
    + ``Name:`` ``Shopinvader Demo Endpoint``
    + ``App`` ``Shopinvader Demo Endpoint``
    + ``Root Path`` ``/shopinvader_demo``
    +  ``User`` ``Shopinvader App Demo User``
    + ``Company`` ``My Company``
    + ``Description`` ``# A Dummy Shopinvader Demo``
    + ``Auth Jwt Validator`` ``shopinvader_demo``, create a new auth.jwt.validator record
        + Create a new ``auth.jwt.validator`` record:
            + ``Name`` ``shopinvader_demo``
            + ``User Id Strategy`` ``Static``
            + ``Static User`` ``Shopinvader App Demo User``
            + ``Cookie Enabled`` ``True``
            + ``Cookie Name`` ``authorization``
            + ``Cookie Path`` ``/``
            + ``Cookie Max Age`` ``31,536,000``
            + ``Audience`` ``shopinvader_jwt,account``
            + ``Issuer`` ``http://localhost:8080/realms/myrealm``
            + ``Signature Type`` ``Public Key``
            + ``JWK URI`` ``http://localhost:8080/realms/myrealm/protocol/openid-connect/certs``
            + ``Algorithm`` ``RS256``
            + ``Partner Id Strategy`` ``From email claim``
            + ``Partner Id Required`` ``True``
3. Make sure to install [keycloak docker image](./keycloak.md).
