grails-spring-security-oauth
============================

Adds OAuth-based authentication to the [Spring Security plugin][spring-security-plugin] using the [OAuth plugin][oauth-plugin].

This plugin provides an OAuth realm that can easily be integrated into existing applications and a host of utility functions to make things like "log in with Twitter" almost trivial.

**This README refers to the code currently in the master branch, maybe not yet released.**

Please, see [Grails plugin portal][s2oauth-grails-website] for instructions about the actual released version.


Changelog
---------

**2.0.1.2 (for spring-security-core:1.2.7.3)**

This version is maintain support for old spring-security-core 1.2.7.3, fork from [Spring Security OAuth][spring-security-oauth-origin] current support version 2.0.2

Version provider's service and token are moved into separate plugin, example:

    ':spring-security-oauth-google:0.1'


Installation
------------

In `BuildConfig.groovy`, add the dependency to "plugins" section:

```groovy

    plugins {
        //...
        compile ':spring-security-oauth:2.0.1.2'

        // and also you need add at least one of extensions:
        compile ':spring-security-oauth-google:0.2'
        //...
    }
```

Change the version to reflect the actual version you would like to use.


Usage
-----

Install the plugin as described above by adding a dependency in BuildConfig.groovy. Then follow Spring Security Core and OAuth plugins documentation.

Sample configuration for OAuth plugin (each provider needs the proper spring-security-oauth-* plugin):

```groovy
def appName = grails.util.Metadata.current.'app.name'
def baseURL = grails.serverURL ?: "http://localhost:${System.getProperty('server.port', '8080')}/${appName}"
oauth {
    debug = true
    providers {
        // for Google OAuth 1.0 DEPRECATED
        google {
            api = org.scribe.builder.api.GoogleApi
            key = 'oauth_google_key'
            secret = 'oauth_google_secret'
            successUri = '/oauth/google/success'
            failureUri = '/oauth/google/error'
            callback = "${baseURL}/oauth/google/callback"
            scope = 'https://www.googleapis.com/auth/userinfo.email'
        }

        // for Google OAuth 2.0
        google {
            api = org.grails.plugin.springsecurity.oauth.GoogleApi20
            key = 'oauth_google_key'
            secret = 'oauth_google_secret'
            successUri = '/oauth/google/success'
            failureUri = '/oauth/google/error'
            callback = "${baseURL}/oauth/google/callback"
            scope = 'https://www.googleapis.com/auth/userinfo.profile https://www.googleapis.com/auth/userinfo.email'
        }
    }
}
```

Other configuration keys you can use, are (with their default values):

```groovy
grails.plugin.springsecurity.oauth.active = true
grails.plugin.springsecurity.oauth.domainClass = 'OAuthID'
grails.plugin.springsecurity.oauth.userLookup.oAuthIdsPropertyName = 'oAuthIDs'
grails.plugin.springsecurity.oauth.registration.askToLinkOrCreateAccountUri = '/oauth/askToLinkOrCreateAccount'
grails.plugin.springsecurity.oauth.registration.roleNames = ['ROLE_USER']
```

Once you have an user domain and configured provider names, go with:

    grails s2-init-oauth [domain-class-package] [oauthid-class-name]

Example:

    grails s2-init-oauth com.yourapp OAuthID

that creates the domain class `com.yourapp.OAuthID`

Finally, add:

```groovy
static hasMany = [oAuthIDs: OAuthID]
```

to you user domain class.

In your view you can use the taglib exposed from this plugin and from OAuth plugin to create links and to know if the user is authenticated with a given provider:

```xml
<oauth:connect provider="google" id="google-connect-link">Google</oauth:connect>

Logged with google? <s2o:ifLoggedInWith provider="google">yes</s2o:ifLoggedInWith><s2o:ifNotLoggedInWith provider="google">no</s2o:ifNotLoggedInWith>
```

Extensions
----------

* [Google][spring-security-oauth-google-plugin]

That's it!

[s2oauth-grails-website]: http://www.grails.org/plugin/spring-security-oauth
[spring-security-plugin]: http://grails.org/plugin/spring-security-core
[oauth-plugin]: http://grails.org/plugin/oauth
[spring-security-oauth-google-plugin]: https://github.com/nolifelover/grails-spring-security-oauth-google
[spring-security-oauth-origin]: https://github.com/enr/grails-spring-security-oauth
