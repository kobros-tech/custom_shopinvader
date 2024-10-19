# Keycloak docker compose configuration file:


## Create this docker compose configuration file:
    
```yml
version: '3'
services:
  keycloak:
    image: quay.io/keycloak/keycloak:26.0.0
    container_name: keycloak
    environment:
      - KEYCLOAK_ADMIN=admin
      - KEYCLOAK_ADMIN_PASSWORD=admin
    ports:
      - "8080:8080"
    command:
      - start-dev
    restart: always
```

## Open keycloak admin console ``http://localhost:8080/admin/master/console/`` and configure it

+ Create new realm and set ``Realm name`` ``myrealm``
+ Click on ``Clients`` tab on the sidebar and click on ``Create client`` button
    + ``Client type `` ``OpenID Connect``
    + ``Client ID`` ``public-client``
    + ``Valid redirect URIs`` ``http://localhost:8044/*``
    + ``Web origins`` ``http://localhost:8044``
    + Authentication flow
        + ``Standard flow`` ``True``
        + ``Direct access grants`` ``True``
        + ``Implicit flow`` ``True``
+ Click on Users tab on the sidebar and click on ``Add user`` button
    + ``Email verified`` ``True``
    + ``Username`` ``portal``
    + ``Email`` ``joel.willis63@example.com``
    + ``First name`` ``Joel``
    + ``Last name`` ``Willis``
    + Click on ``Save`` button
    + Click on ``Credentials`` tab in the horizontal bar and create a new password
        + ``Password`` ``portal``
        + ``New password confirmation`` ``portal``
        + ``Temporary`` ``False``


## Test connection to odoo API through the keycloak authentication

+ In the frontend app call this url when pressing on Signin button
    + ``http://localhost:8080/realms/myrealm/protocol/openid-connect/auth?client_id=public-client&response_type=code&redirect_uri=http://localhost:8044&scope=openid``
    + Add your username and password, you will be redirected back to the frontend app with authentication code in the URL
    + Exchange the authentication code with the access token in the backend of the app
        ```bash
        curl --location --request POST 'http://localhost:8080/realms/myrealm/protocol/openid-connect/token' \
        --header 'Content-Type: application/x-www-form-urlencoded' \
        --data-urlencode 'grant_type=authorization_code' \
        --data-urlencode 'client_id=public-client' \
        --data-urlencode 'code=8eb32038-6d3f-4aed-ba3f-02670843f017.e728c449-60a0-41d7-9038-ede3f4c9177c.d8553aeb-4871-40cf-8625-d0abbfd8fdf6' \
        --data-urlencode 'redirect_uri=http://localhost:8044' \
        ```
    + Use the access token to communicate with odoo API endpoint in the backend of the app
        ```bash
        curl --location --request GET 'http://localhost:8069//shopinvader_demo/sales' \
        --header 'Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJDUFpXUGdvWEl2MUJLbE5BSXg5VWRIamktbHlHNGRualFTYl8yWVFhVVBBIn0.eyJleHAiOjE3MjkzNTMxMTMsImlhdCI6MTcyOTM1MjgxMywiYXV0aF90aW1lIjoxNzI5MzUyODEzLCJqdGkiOiI3YjQ0YzhmYS0xYzUxLTQ1ODUtYjdmNy1jNjIzYWYwY2ZlZDAiLCJpc3MiOiJodHRwOi8vbG9jYWxob3N0OjgwODAvcmVhbG1zL215cmVhbG0iLCJhdWQiOiJhY2NvdW50Iiwic3ViIjoiMjEwYWE5NTItMmI5Mi00NzQyLTgxMzYtOTZjMDNmMTVjMGMwIiwidHlwIjoiQmVhcmVyIiwiYXpwIjoicHVibGljLWNsaWVudCIsInNpZCI6IjdiZmNiN2FjLTg1NWQtNGE5Zi1hYzRmLWEwOGZkMDg1YWJiMSIsImFjciI6IjEiLCJhbGxvd2VkLW9yaWdpbnMiOlsiaHR0cDovL2xvY2FsaG9zdDo4MDQ0Il0sInJlYWxtX2FjY2VzcyI6eyJyb2xlcyI6WyJkZWZhdWx0LXJvbGVzLW15cmVhbG0iLCJvZmZsaW5lX2FjY2VzcyIsInVtYV9hdXRob3JpemF0aW9uIl19LCJyZXNvdXJjZV9hY2Nlc3MiOnsiYWNjb3VudCI6eyJyb2xlcyI6WyJtYW5hZ2UtYWNjb3VudCIsIm1hbmFnZS1hY2NvdW50LWxpbmtzIiwidmlldy1wcm9maWxlIl19fSwic2NvcGUiOiJvcGVuaWQgcHJvZmlsZSBlbWFpbCIsImVtYWlsX3ZlcmlmaWVkIjp0cnVlLCJuYW1lIjoiSm9lbCBXaWxsaXMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJwb3J0YWwiLCJnaXZlbl9uYW1lIjoiSm9lbCIsImZhbWlseV9uYW1lIjoiV2lsbGlzIiwiZW1haWwiOiJqb2VsLndpbGxpczYzQGV4YW1wbGUuY29tIn0.pH1H-AcPMDdqtEyNNg8o1qXmRJOktwPO8j_p8A7PvxkwPGsTGx26AGK_mut8GAi5s0ptyxERB3tRjGXEpoGq7Te9UxABXehE6rS7idwBf8tgadrtbNb01TzBugMtU5VhyWlMufx_LFtH7bidSJM3dSWuGW9MapcJUImhfc7L2mt-01aKNFLLXeUNEHux9p_fYDGWfHdwzHJjBOh68pTsXk78NqhWBx3Gj48ChOZ8NMqkJGqB5IaPNxAEdzikrPWbjzDiHfXRfjSg4d1taPdqCV0Pe35G4QpT10n70ZfffGVLPBIQUoG7QZaUUa6oIcOmxOiuzliXgRAmkprEZcf2pw' \
        ```
