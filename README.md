# oxd-perl
oxd-perl is a client library for the Gluu oxd server implemented in JAVA. Using it you can integrate oxd server in your perl applications. For information about oxd, visit http://oxd.gluu.org

The README is used to introduce the module and provide instructions on
how to install the module, any machine dependencies it may have (for
example C compilers and installed libraries) and any other information
that should be provided before the module is installed.

A README file is required for CPAN modules since CPAN extracts the README
file from a module distribution so that people browsing the archive
can use it to get an idea of the module's uses. It is usually a good idea
to provide version information here so that people can decide whether
fixes for the module are worth downloading.


INSTALLATION

To install this module, run the following commands:

	perl Makefile.PL
	make
	make test
	make install

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

Copyright (C) 2016 Ourdesignz

This program is free software; you can redistribute it and/or modify it
under the terms of the the Artistic License (2.0). You may obtain a
copy of the full license at:

L<http://www.perlfoundation.org/artistic_license_2_0>

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

Configuration For Example Setup

The oxd-perl configuration file is located in 'oxd-settings.json'. The values here are used during registration. For a full list of supported oxd configuration parameters, see the oxd documentation Below is a typical configuration data set for registration:

	{
	  "op_host": "https://ce-dev2.gluu.org",
	  "oxd_host_port":8099,
	  "authorization_redirect_uri" : "https://oxd-perl-example.com/login.cgi",
	  "post_logout_redirect_uri" : "https://oxd-perl-example.com/logout.cgi",
	  "scope" : [ "openid", "profile","uma_protection","uma_authorization" ],
	  "application_type" : "web",
	  "response_types" : ["code"],
	  "grant_types":["authorization_code"],
	  "acr_values" : [ "basic" ]
	}

    oxd_host_port - oxd port or socket

Sample code

OxdConfig.pm

    Class description.
    Oxd RP config.

Example

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

OxdRegister.pm

    Class description.
    OxdRegister protocol description.

Example

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


UpdateRegistration.pm

    Class description.
    UpdateRegistration protocol description.

Example

    $update_site_registration = new UpdateRegistration();
				
	$update_site_registration->setRequestAcrValues($acrValues);
	$update_site_registration->setRequestOxdId($oxd_id);
	$update_site_registration->setRequestAuthorizationRedirectUri($authorizationRedirectUrl);
	$update_site_registration->setRequestPostLogoutRedirectUri($postLogoutRedirectUrl);
	$update_site_registration->setRequestContacts([$emal]);
	$update_site_registration->setRequestGrantTypes($grantType);
	$update_site_registration->setRequestResponseTypes($responseType);
	$update_site_registration->setRequestScope($scope);
	$update_site_registration->request();

	$session->param('oxd_id', $update_site_registration->getResponseOxdId());


GetAuthorizationUrl.pm

    Class description.
    GetAuthorizationUrl protocol description.

Example

	$get_authorization_url = new GetAuthorizationUrl( );
	$get_authorization_url->setRequestOxdId($session->param('oxd_id'));
	$get_authorization_url->setRequestScope($scope);
	$get_authorization_url->setRequestAcrValues($acrValues);
	$get_authorization_url->request();
	my $oxdurl = $get_authorization_url->getResponseAuthorizationUrl();

GetTokenByCode.pm

    Class description.
    Get_tokens_by_code protocol description.

Example

	$$get_tokens_by_code = new GetTokenByCode();
	$get_tokens_by_code->setRequestOxdId($oxd_id);
	$get_tokens_by_code->setRequestCode($code);
	$get_tokens_by_code->setRequestState($state);
	$get_tokens_by_code->request();
	#store values in sessions
	$session->param('user_oxd_id_token', $get_tokens_by_code->getResponseIdToken());
	$session->param('state', $state);
	$session->param('session_state', $cgi->escapeHTML($cgi->param("session_state")));

GetUserInfo.pm

    Class description.
    Get_user_info protocol description.

Example

	$get_user_info = new GetUserInfo();
	$get_user_info->setRequestOxdId($oxd_id);
	$get_user_info->setRequestAccessToken($get_tokens_by_code->getResponseAccessToken());
	$get_user_info->request();

	print Dumper( $get_user_info->getResponseObject() );

OxdLogout.pm

    Class description.
    Get_logout_uri protocol description.

Example

	$logout = new OxdLogout();
    $logout->setRequestOxdId($oxd_id);
    $logout->setRequestPostLogoutRedirectUri($postLogoutRedirectUrl);
    $logout->setRequestIdToken($user_oxd_id_token);
    $logout->setRequestSessionState($session_state);
    $logout->setRequestState($state);
    $logout->request();

	$session->delete();
    $logoutUrl = $logout->getResponseObject()->{data}->{uri};


