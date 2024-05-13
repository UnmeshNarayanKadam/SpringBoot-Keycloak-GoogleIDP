# SpringBoot-Keycloak-GoogleIDP
This repository contains the code for integrating Keycloak and Google Identity Providers (IDPs) for role-based authentication and authorization in a Java Spring Boot application.

Install java-17:

  1. sudo apt install openjdk-17-jdk openjdk-17-jre

Install Keycloak:

	1. Go to https://www.keycloak.org/downloads and download latest tar.gz file
	2. Run cmd : sudo tar -xvzf keycloak-24.0.3.tar.gz
	3. Go to bin folder and run cmd : sudo ./kc.sh start-dev --http-port=8080
	4. Keycloak is start running on http://0.0.0.0:8080

Keycloak setup:

	1. Create admin account and sign in.
	2. Create realm (A realm manages a set of users, credentials, roles, and groups. A user belongs to and logs into a realm. 
     Realms are isolated from one another and can only manage and authenticate the users that they control.) 
		 Realm name: SpringBootKeycloakRoleMapping
	3. Create Clients (Clients are applications and services that can request authentication of a user.) 
		 a) Client type: OIDC
		 b) client ID: login-app
		 c) Root URL: Where spring boot app run (http://localhost:8081)
		 d) Home URL: http://localhost:8081
		 e) Valid redirect URIs: http://localhost:8081/* 
		 f) Valid post logout redirect URIs: http://localhost:8081
	4. Create client roles(specific to individual client applications within the realm)
		 for ex: client_admin and client_user
	5. Create realm roles(available across all clients within that realm. Realm roles are typically used for global permissions 
     that apply to all applications within the realm)
		 for ex: admin and user
	6. Go to realm role and assign associated client role
		 for ex: admin is associated with client_admin and user is associated with client_user
	7. Create user
		 a) Username: admin_user
		 b) Must enter first name and Last name otherwise it will give you the "Account is not fully set up" error
		 c) Go to role mapping and assign role(admin) to current user
		 d) Go to credentials and set password
		 e) Similarly create another user app_user and assign role(user)
	8. Now to see the configuration details of realm hit this url 
     http://localhost:8080/realms/SpringBootKeycloakRoleMapping/.well-known/openid-configuration
	9. To get the token hit the token_endpoint which is given in above configuration 
  		Endpoint: "http://localhost:8080/realms/SpringBootKeycloakRoleMapping/protocol/openid-connect/token"
  		Method: POST
  		Body -> x-www-form-urlencoded -> 
  			client_id: login-app
  			username: admin_user
  			password: admin_user
  			grant_type: password

Integrating google as IDP: 

	1. Keycloak setting:
		 a) Open keycloak and Click "Identity Providers" in the menu. 
		 b) Select Google as social IDP 
		 c) Copy the Redirect URI value to your clipboard. 

	2. Google IDP setting:
		 a) Go to the Google Cloud Platform console(https://console.cloud.google.com/) 
		 b) Create new project. 
		 c) Navigate to "APIs & Services" -> "OAuth consent screen" and select External and click the Create button. 
		    Now, on the “OAuth consent screen”  
		 	  1) first step, in the “App information” section, provide APP NAME and EMAIL ID 
		  	2) second step, click ADD AND REMOVE SCOPES button. Select email, profile and openid and click the UPDATE button. 
			  3) To finalize this step, click SAVE AND CONTINUE button. 
		 d) After creating new project go to the Credentials menu item on the left. Then, click "+ CREATE CREDENTIALS" button and 
        select OAuth client ID. 
  			1) For the Application type, select Web application and give some name also in the “Authorized redirect URIs”, add the 
           Redirect URI provided by Keycloak 
  			2) To complete the setup, click the CREATE button. 
  			3) On the next page, you will see the Client ID and Client Secret for your app. 
		e) Enter the Client ID and Client Secret values provided by Google into the corresponding fields on the Add Google 
       provider screen in Keycloak 

