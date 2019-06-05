# angular-oauth2-oidc-msar
[![Build Status](https://travis-ci.org/ahdis/angular-oauth2-smart.svg?branch=master)](https://travis-ci.org/ahdis/angular-oauth2-smart)


angular-oauth2-oidc-smart is an OAuth2 and OpenId Connect (OIDC) client for Angular.
The library is a 
  1. Github fork of [bechhansen/angular-oauth2-oidc](https://github.com/bechhansen/angular-oauth2-oidc) which is a
  2. Github fork of [manfredsteyer/angular-oauth2-oidc](https://github.com/manfredsteyer/angular-oauth2-oidc).
The code of this library is found at [ahdis/angular-oauth2-oidc](https://github.com/ahdis/angular-oauth2-oidc).

The 1. fork extends the existing library so it do also support:
- [Authorization Code Grant flow](https://tools.ietf.org/html/rfc6749#page-24)

The 2. fork extends the existing library so it do also support smart-on-fhir launch sequences:
- [SMART App Launch Framework](http://www.hl7.org/fhir/smart-app-launch/)


## Installing

```
npm i angular-oauth2-oidc-smart --save
```

## Importing the NgModule

```TypeScript
import { OAuthModule } from 'angular-oauth2-oidc-smart';
[...]

@NgModule({
  imports: [ 
    [...]
    HttpModule,
    OAuthModule.forRoot()
  ],
  declarations: [
    AppComponent,
    HomeComponent,
    [...]
  ],
  bootstrap: [
    AppComponent 
  ]
})
export class AppModule {
}

``` 

## Configuring for Code Grant Flow

To configure the library the following sample uses the new configuration API introduced with Version 2.1.
Hence, The original API is still supported.

```TypeScript
import { AuthConfig } from 'angular-oauth2-oidc-smart';

export const authConfig: AuthConfig = {

  // Url of the Identity Provider
  issuer: 'https://steyer-identity-server.azurewebsites.net/identity',

  // URL of the SPA to redirect the user to after login
  redirectUri: window.location.origin + '/index.html',

  // The SPA's id. The SPA is registerd with this id at the auth-server
  clientId: 'spa-demo',

  // set the scope for the permissions the client should request
  // The first three are defined by OIDC. The 4th is a usecase-specific one
  scope: 'openid profile email voucher',
}
```

Configure the OAuthService with this config object when the application starts up:

```TypeScript
import { OAuthService } from 'angular-oauth2-oidc';
import { JwksValidationHandler } from 'angular-oauth2-oidc';
import { authConfig } from './auth.config';
import { Component } from '@angular/core';

@Component({
    selector: 'flight-app',
    templateUrl: './app.component.html'
})
export class AppComponent {

    constructor(private oauthService: OAuthService) {
      this.configureWithNewConfigApi();
    }

    private configureWithNewConfigApi() {
      this.oauthService.configure(authConfig);
      this.oauthService.tokenValidationHandler = new JwksValidationHandler();
       
      this.oauthService.loadDiscoveryDocumentAndTryLogin().then(_ => {
        console.log("Logged in");
      }).catch(err => {
        console.log("Unable to login");
      })
      
      
      // Call this.oauthService.tryLogin() if discovery document is not used.
      // All configurations must be set manually.
    }
}
```

To initialize the login login flow, make a button call 'initAuthorizationCodeFlow()':
```TypeScript
this.oauthService.initAuthorizationCodeFlow();
```





