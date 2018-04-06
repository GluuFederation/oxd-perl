# oxd-Perl

oxd-Perl is perl modules to interact with Gluu's oxd Server. The oxd Server must be running in your machine to make use of this library. 


# INSTALLATION

To install this module, run the following commands on following path:
    
    Path : /var/www/html/oxd-perl/oxdPerl/
    
	perl Build.PL
	./Build
	./Build test
	./Build install

SUPPORT AND DOCUMENTATION

After installing, you can find documentation for this module with the
perldoc command.

    perldoc oxdPerl

You can also look for information at:

    RT, CPAN's request tracker (report bugs here)
        http://rt.cpan.org/NoAuth/Bugs.html?Dist=oxdPerl

    AnnoCPAN, Annotated CPAN documentation
        http://annocpan.org/dist/oxdPerl

    CPAN Ratings
        http://cpanratings.perl.org/d/oxdPerl

    Search CPAN
        http://search.cpan.org/dist/oxdPerl/


LICENSE AND COPYRIGHT

Copyright (C) 2018 Gluu Inc

This program is free software; you can redistribute it and/or modify it
under the terms of the the Artistic License (2.0). You may obtain a
copy of the full license at:

<http://www.perlfoundation.org/artistic_license_2_0>

Any use, modification, and distribution of the Standard or Modified
Versions is governed by this Artistic License. By using, modifying or
distributing the Package, you accept this license. Do not use, modify,
or distribute the Package, if you do not accept this license.

If your Modified Version has been derived from a Modified Version made
by someone other than you, you are nevertheless required to ensure that
your Modified Version complies with the requirements of this license.

This license does not grant you the right to use any trademark, service
mark, tradename, or logo of the Copyright Holder.

This license includes the non-exclusive, worldwide, free-of-charge
patent license to make, have made, use, offer to sell, sell, import and
otherwise transfer the Package with respect to any patent claims
licensable by the Copyright Holder that are necessarily infringed by the
Package. If you institute patent litigation (including a cross-claim or
counterclaim) against any party alleging that the Package constitutes
direct or contributory patent infringement, then this Artistic License
to you shall terminate on the date that such litigation is filed.

Disclaimer of Warranty: THE PACKAGE IS PROVIDED BY THE COPYRIGHT HOLDER
AND CONTRIBUTORS "AS IS' AND WITHOUT ANY EXPRESS OR IMPLIED WARRANTIES.
THE IMPLIED WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR
PURPOSE, OR NON-INFRINGEMENT ARE DISCLAIMED TO THE EXTENT PERMITTED BY
YOUR LOCAL LAW. UNLESS REQUIRED BY LAW, NO COPYRIGHT HOLDER OR
CONTRIBUTOR WILL BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, OR
CONSEQUENTIAL DAMAGES ARISING IN ANY WAY OUT OF THE USE OF THE PACKAGE,
EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

# Configuration For Example Setup

# OXD Perl Demo site

This is a demo site for oxd-perl written using Perl (CGI) to demonstrate how to use oxd-perl to perform authorization with an OpenID Provider and fetch information.

## Deployment

### Prerequisites

Ubuntu 14.04 with some basic utilities listed below

```bash
$ sudo apt-get update
$ sudo apt-get install apache2
$ a2enmod ssl
```

### Installing and configuring the oxd-server
You can download the oxd-server and follow the installation instructions from [here](https://www.gluu.org/docs-oxd)

## Demosite deployment

OpenID Connect works only with HTTPS connections. So let us get the ssl certs ready.
```bash
$ mkdir /etc/certs
$ cd /etc/certs
$ openssl genrsa -des3 -out demosite.key 2048
$ openssl rsa -in demosite.key -out demosite.key.insecure
$ mv demosite.key.insecure demosite.key
$ openssl req -new -key demosite.key -out demosite.csr
$ openssl x509 -req -days 365 -in demosite.csr -signkey demosite.key -out demosite.crt
```

### Install Perl on ubuntu

```bash
$ sudo apt-get install perl
$ sudo apt-get install libapache2-mod-perl2 

```
Then create virtual host of oxd-perl ""odx-perl.conf" under /etc/apache2/sites-available/  file and add these lines :

```bash
$ cd /etc/apache2/sites-available
$ vim oxd-perl-example.conf

```
add below mention lines on  virtual host file

```
<IfModule mod_ssl.c>
    <VirtualHost _default_:443>

        DocumentRoot /var/www/html/oxd-perl/example/
        ServerName www.oxd-perl-example.com
        ServerAlias oxd-perl-example.com

        <Directory /var/www/html/oxd-perl/example/>
                        AllowOverride All
        </Directory>

        ErrorLog /var/www/html/oxd-perl/example/logs/error.log
        CustomLog /var/www/html/oxd-perl/example/logs/access.log combined

        AddType application/x-httpd-php .php
           <Files 'xmlrpc.php'>
                   Order Allow,Deny
                   deny from all
           </Files>

        SSLEngine on
        SSLCertificateFile  /etc/certs/demosite.crt
        SSLCertificateKeyFile /etc/certs/demosite.key

                <FilesMatch "\.(cgi|shtml|phtml|php)$">
                        SSLOptions +StdEnvVars
                </FilesMatch>

                # processes .cgi and .pl as CGI scripts

                ScriptAlias /cgi-bin/ /var/www/html/oxd-perl/
                <Directory "/var/www/html/oxd-perl">
                        Options +ExecCGI
                        SSLOptions +StdEnvVars
                        AddHandler cgi-script .cgi .pl
                </Directory>

                BrowserMatch "MSIE [2-6]" \
            nokeepalive ssl-unclean-shutdown \
            downgrade-1.0 force-response-1.0
                BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown

        </VirtualHost>
</IfModule>
```

Then enable `oxd-perl-example.conf` virtual host by running:

```bash

$ sudo a2ensite oxd-perl-example.conf 

```

After that add domain name in virtual host file.
In console:

```bash

$ sudo nano /etc/hosts

```

Add these lines in virtual host file:
```
127.0.0.1 www.oxd-perl-example.com
127.0.0.1  oxd-perl-example.com

```

Reload the apache server

```bash
$ sudo service apache2 restart

```
### Setting up and running demo app

Navigate to perl app root:

```bash

Copy example folder from oxdPerl directory and placed on root folder

cd /var/www/html/oxd-perl/example



```
## Configuration 

The oxd-perl configuration file is located in 'oxd-settings.json'. The values here are used during registration. For a full list of supported oxd configuration parameters, see the oxd documentation Below is a typical configuration data set for registration:
``` {.code }
{
	"acr_values": [""],
	"client_secret": "85a5db45-8f53-4611-abe5-35394a8099e7",
	"oxd_host_port": "8099",
	"claims_redirect_uri": "https://client.example.com:8090/uma.cgi",
	"dynamic_registration": "true",
	"scope": ["openid", "profile", "email", "uma_authorization", "uma_protection"],
	"post_logout_redirect_uri": "https://client.example.com:8090/index.cgi",
	"application_type": "web",
	"client_id": "@!A531.9F38.8347.F83D!0001!3806.151C!0008!CF0E.739F.56AC.5507",
	"op_host": "https://idp.example.com",
	"rest_service_url": "",
	"resource_end_point": "https://client.example.com:44300/api/values",
	"oxd_id": "c2964e5c-9b29-4c2b-b085-56d32511c00f",
	"grant_types": ["authorization_code", "client_credentials", "uma_ticket"],
	"response_types": ["code"],
	"connection_type": "local",
	"client_name": "Perl_App",
	"client_frontchannel_logout_uris": "https://client.example.com:8090/logout.cgi",
	"authorization_redirect_uri": "https://client.example.com:8090/login.cgi"
}
```        
-    oxd_host_port - oxd port or socket

### Sample code

### OxdConfig.pm

    Class description.
    Oxd RP config.

**Example**
``` {.code}

OxdConfig:

	Configuration Values from oxd-settings.json

	$object = new OxdConfig();
	my $opHost = $object->getOpHost();
	my $oxdHostPort = $object->getOxdHostPort();
	my $authorizationRedirectUrl = $object->getAuthorizationRedirectUrl();
	my $postLogoutRedirectUrl = $object->setPostLogoutRedirectUrl();
	my $scope = $object->getScope();
	my $applicationType = $object->getApplicationType();
	my $responseType = $object->getResponseType();
	my $grantType = $object->getGrantTypes();
	my $acrValues = $object->getAcrValues();

```


### OxdSetupClient.pm

- [Setup_client protocol description](https://gluu.org/docs/oxd/3.1.2/api/#setup-client).

**Example**

``` {.code}
OxdSetupClient:

my $setup_client = new OxdSetupClient( );
$setup_client->setRequestOpHost($opHost);
$setup_client->setRequestAuthorizationRedirectUri($authorizationRedirectUrl);
$setup_client->setRequestGrantTypes($grantType);
$setup_client->setRequestScope($scope);
$setup_client->request();

my $oxd_id = $setup_client->getResponseOxdId();
my $client_id = $setup_client->getResponseClientId();
my $client_secret = $setup_client->getResponseClientSecret();
print Dumper($setup_client->getResponseObject());
```


### GetClientToken.pm

- [Get_client_token protocol description](https://gluu.org/docs/oxd/3.1.2/api/#get-client-token).

**Example**

``` {.code}
GetClientToken:

my $get_client_token = new GetClientToken( );
$get_client_token->setRequestClientId($client_id);
$get_client_token->setRequestClientSecret($client_secret);
$get_client_token->setRequestOpHost($opHost);
$get_client_token->request();

my $protection_access_token = $get_client_token->getResponseAccessToken();
print Dumper($get_client_token->getResponseObject());
```

### IntrospectAccessToken.pm

- [Introspect_access_token protocol description](https://gluu.org/docs/oxd/3.1.2/api/#introspect-access-token).

**Example**

``` {.code}
IntrospectAccessToken:

my $introspect_access_token = new IntrospectAccessToken( );
$introspect_access_token->setRequestOxdId($oxd_id);
$introspect_access_token->setRequestAccessToken($access_token);
$introspect_access_token->request();

print Dumper($introspect_access_token->getResponseData());
```


### OxdRegister.pm

- [Register_site protocol description](https://gluu.org/docs/oxd/3.1.2/api/#register-site).

**Example**

``` {.code}
OxdRegister:

my $register_site = new OxdRegister( );
$register_site->setRequestOpHost($gluu_server_url);
$register_site->setRequestAcrValues($acrValues);
$register_site->setRequestAuthorizationRedirectUri($authorizationRedirectUrl);
$register_site->setRequestPostLogoutRedirectUri($postLogoutRedirectUrl);
$register_site->setRequestContacts([$emal]);
$register_site->setRequestGrantTypes($grantType);
$register_site->setRequestResponseTypes($responseType);
$register_site->setRequestScope($scope);
$register_site->setRequestApplicationType($applicationType);
$register_site->request();

# storing data in the session
$session->param('oxd_id', $register_site->getResponseOxdId());
	
```

### UpdateRegistration.pm

- [Update_site protocol description](https://gluu.org/docs/oxd/3.1.2/api/#update-site).

**Example**

``` {.code}
UpdateRegistration:

$update_site = new UpdateRegistration();
$update_site->setRequestAcrValues($acrValues);
$update_site->setRequestOxdId($oxd_id);
$update_site->setRequestAuthorizationRedirectUri($authorizationRedirectUrl);
$update_site->setRequestPostLogoutRedirectUri($postLogoutRedirectUrl);
$update_site->setRequestContacts([$emal]);
$update_site->setRequestGrantTypes($grantType);
$update_site->setRequestResponseTypes($responseType);
$update_site->setRequestScope($scope);
$update_site->request();

$session->param('oxd_id', $update_site->getResponseOxdId());
```


### OxdRemove.pm

- [Remove_site protocol description](https://gluu.org/docs/oxd/3.1.2/api/#remove-site)

**Example**

``` {.code}
OxdRemove:

my $oxd_remove = new OxdRemove();
$oxd_remove->setRequestOxdId($oxd_id);
$oxd_remove->setRequestProtectionAccessToken($protection_access_token);
$oxd_remove->request();

print Dumper($oxd_remove->getResponseObject());
```


### GetAuthorizationUrl.pm

- [Get_authorization_url protocol description](https://gluu.org/docs/oxd/3.1.2/api/#get-authorization-url).

**Example**

``` {.code}
GetAuthorizationUrl:

$get_authorization_url = new GetAuthorizationUrl( );
$get_authorization_url->setRequestOxdId($session->param('oxd_id'));
$get_authorization_url->setRequestScope($scope);
$get_authorization_url->setRequestAcrValues($acrValues);
$get_authorization_url->request();
my $oxdurl = $get_authorization_url->getResponseAuthorizationUrl();
```

### GetTokenByCode.pm

- [Get_tokens_by_code protocol description](https://gluu.org/docs/oxd/3.1.2/api/#get-tokens-id-access-by-code).

**Example**

``` {.code}
GetTokenByCode:

my $oxd_id = $cgi->escapeHTML($session->param('oxd_id'));
my $code = $cgi->escapeHTML($cgi->param("code"));
my $state = $cgi->escapeHTML($cgi->param("state"));

$get_tokens_by_code = new GetTokenByCode();
$get_tokens_by_code->setRequestOxdId($oxd_id);
$get_tokens_by_code->setRequestCode($code);
$get_tokens_by_code->setRequestState($state);
$get_tokens_by_code->request();
#store values in sessions
$session->param('user_oxd_id_token', $get_tokens_by_code->getResponseIdToken());
$session->param('state', $state);
$session->param('session_state', $cgi->escapeHTML($cgi->param("session_state")));

print Dumper( $get_user_info->getResponseObject() );        
```

### GetAccessTokenByRefreshToken.pm

- [Get_access_tokens_by_refresh_token](https://gluu.org/docs/oxd/3.1.2/api/#get-access-token-by-refresh-token).

**Example**

``` {.code}
GetAccessTokenByRefreshToken:

my $get_access_token_by_refresh_token = new GetAccessTokenByRefreshToken();
$get_access_token_by_refresh_token->setRequestOxdId($oxd_id);
$get_access_token_by_refresh_token->setRequestRefreshToken($refresh_token);
$get_access_token_by_refresh_token->setRequestProtectionAccessToken($protection_access_token);
$get_access_token_by_refresh_token->request();

$new_access_token = $get_access_token_by_refresh_token->getResponseAccessToken();
$new_refresh_token = $get_access_token_by_refresh_token->getResponseRefreshToken();
print Dumper($get_access_token_by_refresh_token->getResponseObject());
```


### GetUserInfo.pm

- [Get_user_info protocol description](https://gluu.org/docs/oxd/3.1.2/api/#get-user-info).

**Example**

``` {.code}
GetUserInfo:

my $oxd_id = $cgi->escapeHTML($session->param('oxd_id'));

$get_user_info = new GetUserInfo();
$get_user_info->setRequestOxdId($oxd_id);
$get_user_info->setRequestAccessToken($get_tokens_by_code->getResponseAccessToken());
$get_user_info->request();

print Dumper( $get_user_info->getResponseObject() );
                        
```


### OxdLogout.pm

- [Get_logout_uri protocol description](https://gluu.org/docs/oxd/3.1.2/api/#get-logout-uri).

**Example**

``` {.code}
OxdLogout:

my $oxd_id = $cgi->escapeHTML($session->param('oxd_id'));
my $user_oxd_id_token = $cgi->escapeHTML($session->param("user_oxd_id_token"));
my $session_state = $cgi->escapeHTML($session->param("session_state"));
my $state = $cgi->escapeHTML($session->param("state"));

$logout = new OxdLogout();
$logout->setRequestOxdId($oxd_id);
$logout->setRequestPostLogoutRedirectUri($postLogoutRedirectUrl);
$logout->setRequestIdToken($user_oxd_id_token);
$logout->setRequestSessionState($session_state);
$logout->setRequestState($state);
$logout->request();

$session->delete();
$logoutUrl = $logout->getResponseObject()->{data}->{uri};
                        
```


### For UMA authentications open this url in browser
- [https://client.example.com/uma.cgi](https://client.example.com/uma.cgi).

### UmaRsProtect.pm

- [Uma_rs_protect protocol description](https://gluu.org/docs/oxd/3.1.2/api/#uma-rs-protect-resources).


**Example**

``` {.code}
UmaRsProtect:

my $oxdId = $session->param('oxd_id');

$uma_rs_protect = new UmaRsProtect();
$uma_rs_protect->setRequestOxdId($oxdId);

$uma_rs_protect->addConditionForPath(["GET"],["https://photoz.example.com/dev/actions/view"], ["https://photoz.example.com/dev/actions/view"]);
$uma_rs_protect->addConditionForPath(["POST"],[ "https://photoz.example.com/dev/actions/add"],[ "https://photoz.example.com/dev/actions/add"]);
$uma_rs_protect->addConditionForPath(["DELETE"],["https://photoz.example.com/dev/actions/remove"], ["https://photoz.example.com/dev/actions/remove"]);
$uma_rs_protect->addResource('/photo');

$uma_rs_protect->request();
print Dumper( $uma_rs_protect->getResponseObject() );
```

***RS Protect with scope_expression***

```perl
my $uma_rs_protect = new UmaRsProtect();
$uma_rs_protect->setRequestOxdId($oxdId);

%rule = ('and' => [{'or' => [{'var' => 0},{'var' => 1}]},{'var' => 2}]);
$data = ["http://photoz.example.com/dev/actions/all", "http://photoz.example.com/dev/actions/add", "http://photoz.example.com/dev/actions/internalClient"];

$uma_rs_protect->addConditionForPath(["GET"],["https://client.example.com:44300/api"], ["https://client.example.com:44300/api"], $uma_rs_protect->getScopeExpression(\%rule, $data));
$uma_rs_protect->addResource('/values');
$uma_rs_protect->setRequestProtectionAccessToken($protection_access_token);
$uma_rs_protect->request();

print Dumper( $uma_rs_protect->getResponseObject() );
```

### UmaRsCheckAccess.pm

- [Uma_rs_check_access protocol description](https://gluu.org/docs/oxd/3.1.2/api/#uma-rs-check-access).

**Example**

``` {.code}
UmaRsCheckAccess:

my $oxdId = $session->param('oxd_id');
my $umaRpt = $session->param('uma_rpt');

$uma_rs_authorize_rpt = new UmaRsCheckAccess();
$uma_rs_authorize_rpt->setRequestOxdId($oxdId);
$uma_rs_authorize_rpt->setRequestRpt($umaRpt);
$uma_rs_authorize_rpt->setRequestPath("/photo");
$uma_rs_authorize_rpt->setRequestHttpMethod("GET");
$uma_rs_authorize_rpt->request();

print Dumper($uma_rs_authorize_rpt->getResponseObject());
my $uma_ticket= $uma_rs_authorize_rpt->getResponseTicket();
$session->param('uma_ticket', $uma_ticket);
```


### UmaIntrospectRpt.pm

- [Introspect_rpt protocol description](https://gluu.org/docs/oxd/3.1.2/api/#uma-2-introspect-rpt).

**Example**

``` {.code}
UmaIntrospectRpt:

my $introspect_rpt = new UmaIntrospectRpt();
$introspect_rpt->setRequestOxdId($oxdId);
$introspect_rpt->setRequestRPT($rpt);
$introspect_rpt->request();

print Dumper($introspect_rpt->getResponseObject());
```


### UmaRpGetRpt.pm

- [Uma_rp_get_rpt protocol description](https://gluu.org/docs/oxd/3.1.2/api/#uma-rp-get-rpt).

**Example**

``` {.code}
UmaRpGetRpt:

my $uma_rp_get_rpt = new UmaRpGetRpt();
$uma_rp_get_rpt->setRequestOxdId($oxd_id);
$uma_rp_get_rpt->setRequestTicket($ticket);
$uma_rp_get_rpt->setRequestProtectionAccessToken($protection_access_token);
$uma_rp_get_rpt->request();

my $uma_rpt= $uma_rp_get_rpt->getResponseRpt();
print Dumper($uma_rp_get_rpt->getResponseObject());
```


### UmaRpGetClaimsGatheringUrl.pm

- [Uma_rp_get_claims_gathering_url protocol description](https://gluu.org/docs/oxd/3.1.2/api/#uma-rp-get-claims-gathering-url).

**Example**

``` {.code}
UmaRpGetClaimsGatheringUrl:

my $uma_rp_get_claims_gathering_url = new UmaRpGetClaimsGatheringUrl();
$uma_rp_get_claims_gathering_url->setRequestOxdId($oxd_id);
$uma_rp_get_claims_gathering_url->setRequestTicket($ticket);
$uma_rp_get_claims_gathering_url->setRequestClaimsRedirectUri($claims_redirect_Uri);
$uma_rp_get_claims_gathering_url->setRequestProtectionAccessToken($protection_access_token);
$uma_rp_get_claims_gathering_url->request();

print Dumper($uma_rp_get_claims_gathering_url->getResponseObject());
```


Now your perl app should work from https://client.example.com/

