# Winkk Passport Node for Authentication Trees

[Winkk Passport](https://passport.winkk.com/partner) is a service that acts as password-free secured online identity provider utilizing patented optical label recognition technology. Winkk Passport supports generic scenarios including OAuth and SAML authentication.
Winkk Passport mobile application is required to follow the authentication process. It is available at [AppStore](https://itunes.apple.com/us/app/winkkpass-fast-secure-login/id1166258885) and [Play Market](https://play.google.com/store/apps/details?id=com.winkk.winkkpass).
Winkk Passport can be configured as external Access Manager OAuth2 identity provider utilizing Authentication Trees model. The step-by-step manual is outlined below.

## Integration manual
The steps of reference integration constructing are listed below.

* Deploy Access Manager 6 as described in the [ForgeRock manual](https://backstage.forgerock.com/docs/am/6/quick-start-guide).
* Create OAuth2 interface at [Winkk Passport Partner Tool](https://passport.winkk.com/partner) as described in the [Winkk Passport manual](https://passport.winkk.com/partner/#oauth2-manual). Remember its application #id and secret.
* Go to AM realm dashboard. Open “Authentication - Trees”.
* Click “(+) Create Tree”.

* Fill the form.

    + Tree Name: ```“Winkk"```

* Click “Create”.

The GUI tree builder will appear. Initially it contains two connected nodes - ```“Start”``` and ```“Failure”```.

* In the “Components” section at the left, find “OAuth 2.0” one and drag it to the working area. Do the same for “Anonymous User Mapping” and “Success” components. You should have the following components at the working area:
    + “Start”
    + “OAuth 2.0”
    + “Anonymous User Mapping”
    + “Success URL”
    + “Success”
    + “Failure”


* Click the “OAuth 2.0” component. Properties list will appear at the right.

* Fill the form.

    + Node Name: ```“Winkk”```
    + Client Id: ```The OAuth2 application # from Partner Tool```
    + Client Secret: ```The OAuth2 application secret from Partner Tool```
    + Authentication Endpoint URL: ```“https://passport.winkk.com/oauth2a/auth”```
    + Access Token Endpoint URL: ```“https://passport.winkk.com/oauth2a/token”```
    + User Profile Service URL: ```“https://passport.winkk.com/oauth2a/userinfo”```
    + OAuth Scope: ```“profile email”```
    + Scope Delimiter: ```“ ”``` (single space character)
    + Redirect URL: ```The URL where AM is deployed```
    + Social Provider: ```“Winkk”```
    + Auth ID Key: ```“id”```
    + Use Basic Auth: ```Off```
    + Account Provider: ```“org.forgerock.openam.authentication.modules.common.mapping.DefaultAccountProvider”```
    + Account Mapper: ```“org.forgerock.openam.authentication.modules.common.mapping.JsonAttributeMapper|*|winkk-”```
    + Attribute Mapper: ```“org.forgerock.openam.authentication.modules.common.mapping.JsonAttributeMapper|iplanet-am-user-alias-list|winkk-”```
    + Add the following entry to Account Mapper Configuration. You have to click “Add” button first for subform to popup and click “+” button after finished:
        + ```“id”```: ```“iplanet-am-user-alias-list”```
    + Add the following entries to Attribute Mapper Configuration. You have to click “Add” button first for subform to popup and click “+” button after finished:
        + ```“email_email”```: ```“email”```
        + ```“personal_first_name”```: ```“givenName”```
        + ```“personal_last_name”```: ```“sn”```
        + ```“personal_nickname”```: ```“cn”```
    + Save Attributes in the Session: ```Off```
    + OAuth 2.0 Mix-Up Mitigation Enabled: ```Off```


* Click the “Success URL” component. Properties list will appear at the right.

* Fill the form.

    + Node Name: ```“Success URL”```
    + Success URL: ```The url of the protected resource```


* Now nodes should be connected in the given way. Node input is located at the left, and output(s) at the right. Connection is performed by dragging some output to a required input. 
    + Connect “Start” node output to “Winkk” node input.
    + Connect “Winkk” node output saying “No account exists” to “Anonymous User Mapping” node input.
    + Connect “Anonymous User Mapping” node output to “Set Success URL” node input.
    + Connect “Set Success URL” node output to “Success” node input.

![ScreenShot](./forgerock_authentication_tree.png)

* Click “Save”.


## Test the integration

* Go to AM realm dashboard. Open “Authentication - Settings”.

* Go to “Core” tab.

    + Select Organization Authentication Configuration: ```“Winkk”```

* Click “Save Changes”.
    + Once you change that, the default log in to AM will be ```“Winkk”``` tree. If you want to go back to the administrator window to make changes to the configuration go to AM URL by appending ```“/console”``` (for example, ```“http://openam.partner.com:8080/openam/console”```). This will use the administrator service to log in to AM (which should be the ```“ldapService”```).


* Logout from Access Manager and try to login again.
* AM should automatically redirect you to the Winkk identity provider.



# Disclaimer
The sample integration setup described herein is provided on an "as is" basis, without warranty of any kind, to the fullest extent permitted by law. Winkk does not warrant or guarantee the individual success developers may have when following the integration manual on their development platforms or in production configurations.
Winkk does not warrant, guarantee or make any representations regarding the use, results of use, accuracy, timeliness or completeness of any data or information relating to the integration manual. Winkk disclaims all warranties, expressed or implied, and in particular, disclaims all warranties of merchantability, and warranties related to the integration manual, or any service or software related thereto.
Winkk shall not be liable for any direct, indirect or consequential damages or costs of any type arising out of any action taken by you or others related to the integration manual.
