# Creating a Grails 4 app

## Baseline

To make a grails app, first run the `grails create-app` command passing in the full package name to your application's name.

```shell script
grails create-app com.locator.eimpound.eimpound2
```
From this point forward we'll use the Grails Wrapper while in the project's `eimpound2` directory.

Use the `./grailsw list-plugins` command to see all the plugins we have available to us. We want to install the spring security-related plugins, so we'll use the `./grailsw plugin-info` command to find out how.

<pre>
<code>./grailsw plugin-info spring-security-core
| Plugin Info: spring-security-core
| Latest Version: 4.0.2
| All Versions: 3.0.0.M2,3.0.0,3.0.1,3.0.2,3.0.3,3.0.4,3.1.0,3.1.1,3.1.2,3.2.0.M1,3.2.0,3.2.1,3.2.2,3.2.3,3.3.0,3.3.1,4.0.0.M1,4.0.0.M2,4.0.0.RC1,4.0.0.RC2,4.0.0.RC3,4.0.0,4.0.1,4.0.2
| Title: Spring Security Core Plugin

Spring Security Core plugin

* License: APACHE
* Documentation: http://grails-plugins.github.io/grails-spring-security-core/
* Issue Tracker: https://github.com/grails-plugins/grails-spring-security-core/issues
* Source: https://github.com/grails-plugins/grails-spring-security-core
* Definition:

dependencies {
    <b>compile "org.grails.plugins:spring-security-core:4.0.2"</b>
}</code>
</pre>

We get similar output when we execute `./grailsw plugin-info spring-security-ui`. We add the documented spring-security references to the dependency block of the `build.gradle`.
While we're at it, let's also add dependencies for the Grails Mail plugin, and the PostgreSQL driver. Note that the PostgreSQL driver is a runtime dependency, not a compile dependency.

<pre>
<code>dependencies {
    ...
    compile "org.grails.plugins:hibernate5"
    compile "org.hibernate:hibernate-core:5.4.18.Final"
    compile "org.grails.plugins:gsp"
<b>
    compile "org.grails.plugins:spring-security-core:4.0.2"
    compile "org.grails.plugins:spring-security-ui:4.0.0.TEST"
    compile "org.grails.plugins:mail:3.0.0"
    runtime "org.postgresql:postgresql:42.2.14"
</b>
    compileOnly "io.micronaut:micronaut-inject-groovy"
    console "org.grails:grails-console"
    profile "org.grails.profiles:web"
}</code>
</pre>

Since we're going to utilize PostgreSQL in production, we'll update the `application.yml` file in the `grails-app/conf` directory to configure that.
We'll also indicate that while in the development environment, all emails should go to the developer.
And, while we're at it, let's specify different ports for production vs. development.

<pre>
<code>---
environments:
    development:
        <b>server:
            port: 8888
        grails:
            mail:
                overrideAddress: jneuhaus@locatortechnologies.com</b>
        dataSource:
            dbCreate: create-drop
            url: jdbc:h2:mem:devDb;MVCC=TRUE;LOCK_TIMEOUT=10000;DB_CLOSE_ON_EXIT=FALSE
    test:
        <b>server:
            port: 8888
        grails:
            mail:
                overrideAddress: jneuhaus@locatortechnologies.com</b>
        dataSource:
            dbCreate: update
            url: jdbc:h2:mem:testDb;MVCC=TRUE;LOCK_TIMEOUT=10000;DB_CLOSE_ON_EXIT=FALSE
    production:
        <b>server:
            port: 5942</b>
        dataSource:
            <b>dbCreate: validate
            dialect: org.hibernate.dialect.PostgreSQLDialect
            driverClassName: org.postgresql.Driver
            url: jdbc:postgresql://localhost:5432/eimpound
            username: eimpound
            password: ********</b>
            properties:
                jmxEnabled: true
                ...</code>
</pre>

Next, we run the `s2-quickstart` command provided by the `spring-security-core` plugin to create our User and Role classes, as well as the class that joins them together. Note the message telling us that the file
`grails-app/conf/applications.groovy` was updated with configuration informaiton. We'll be looking at that file in a bit.

<pre>
<code>./grailsw s2-quickstart com.locator.eimpound User Role
| Creating User class 'User' and Role class 'Role' in package 'com.locator.eimpound'
| Rendered template PersonWithoutInjection.groovy.template to destination grails-app/domain/com/locator/eimpound/User.groovy
| Rendered template PersonPasswordEncoderListener.groovy.template to destination src/main/groovy/com/locator/eimpound/UserPasswordEncoderListener.groovy
| Rendered template Authority.groovy.template to destination grails-app/domain/com/locator/eimpound/Role.groovy
| Rendered template PersonAuthority.groovy.template to destination grails-app/domain/com/locator/eimpound/UserRole.groovy
************************************************************
* Created security-related domain classes. Your            *
* <b>grails-app/conf/application.groovy has been updated with</b> *
* the class names of the configured domain classes;        *
* please verify that the values are correct.               *
************************************************************</code>
</pre>

The User class that's been created will not work as-is with PostgreSQL because `user` is a reserved word in the Postgres server. To fix that, we'll add
a static mapping in the User class to tell Grails that we want the actual table to be named `users` instead of `user`.

<pre>
<code>class User implements Serializable {

    private static final long serialVersionUID = 1
    <b>
    static mapping = {
        table 'users'
        password column: '`password`'
    }
    </b>
}</code>
</pre>

## The application.groovy file

Installing the spring-security-core plugin modified our `conf/application.groovy` file with security-related configuration. We add some
configuration so that we can create a simple link to logout (the `postOnly = false` setting) and add a bunch of paths with ROLE_ADMIN access,
otherwise we wouldn't be able to access those endpoints.

<pre>
<code>// Added by the Spring Security Core plugin:
grails.plugin.springsecurity.userLookup.userDomainClassName = 'com.locator.eimpound.User'
grails.plugin.springsecurity.userLookup.authorityJoinClassName = 'com.locator.eimpound.UserRole'
grails.plugin.springsecurity.authority.className = 'com.locator.eimpound.Role'
<b>grails.plugin.springsecurity.logout.postOnly = false</b>

grails.plugin.springsecurity.controllerAnnotations.staticRules = [
    [pattern: '/',               access: ['permitAll']],
    [pattern: '/error',          access: ['permitAll']],
    [pattern: '/index',          access: ['permitAll']],
    [pattern: '/index.gsp',      access: ['permitAll']],
    [pattern: '/shutdown',       access: ['permitAll']],
    [pattern: '/assets/**',      access: ['permitAll']],
    [pattern: '/**/js/**',       access: ['permitAll']],
    [pattern: '/**/css/**',      access: ['permitAll']],
    [pattern: '/**/images/**',   access: ['permitAll']],
    [pattern: '/**/favicon.ico', access: ['permitAll']],
    [pattern: '/register/**',    access: ['permitAll']],<b>
    [pattern: '/user/**',              access: ['ROLE_ADMIN']],
    [pattern: '/role/**',              access: ['ROLE_ADMIN']],
    [pattern: '/userRole/**',          access: ['ROLE_ADMIN']],
    [pattern: '/securityInfo/**',      access: ['ROLE_ADMIN']],
    [pattern: '/registrationCode/**',  access: ['ROLE_ADMIN']],
    [pattern: '/metadata/**',          access: ['ROLE_ADMIN']],
    [pattern: '/h2-console/**',        access: ['ROLE_ADMIN']]</b>
]</code>
</pre>

## Running the s2ui-override scripts

Running the s2ui-override scripts will generate Controllers and GSPs that we can modify for our own UI implementation.

<pre>
<code>./grailsw s2ui-override auth
./grailsw s2ui-override register com.locator.eimpound</code>
</pre>

We'd like to allow users to login using either their `username` or their `email` and to allow this we must extend the Grails Spring Security plugin files.
First, we create a CustomUserDetails class that extends `GrailsUser` and includes our added `email` field. We create the following file:
`src/main/groovy/com/locator/eimpound/CustomUserDetails.groovy` with the contents:

```groovy
package com.locator.eimpound

import grails.plugin.springsecurity.userdetails.GrailsUser
import org.springframework.security.core.GrantedAuthority

class CustomUserDetails extends GrailsUser {

    final String email

    CustomUserDetails(String username, String password, boolean enabled,
                      boolean accountNonExpired, boolean credentialsNonExpired,
                      boolean accountNonLocked,
                      Collection<GrantedAuthority> authorities,
                      long id, String email) {
        super(username, password, enabled, accountNonExpired,
                credentialsNonExpired, accountNonLocked, authorities, id)

        this.email = email
    }

}
```

Next, we create the companion service using `./grailsw create-service CustomUserDetails` and replace the contents with the following:

```groovy
package com.locator.eimpound

import grails.gorm.transactions.Transactional
import grails.plugin.springsecurity.SpringSecurityUtils
import grails.plugin.springsecurity.userdetails.GrailsUserDetailsService
import grails.plugin.springsecurity.userdetails.NoStackUsernameNotFoundException
import groovy.util.logging.Slf4j
import org.springframework.security.core.authority.SimpleGrantedAuthority
import org.springframework.security.core.userdetails.UserDetails
import org.springframework.security.core.userdetails.UsernameNotFoundException

@Slf4j
class CustomUserDetailsService implements GrailsUserDetailsService {

    /**
     * Some Spring Security classes (e.g. RoleHierarchyVoter) expect at least
     * one role, so we give a user with no granted roles this one which gets
     * past that restriction but doesn't grant anything.
     */
    static final List NO_ROLES = [new SimpleGrantedAuthority(SpringSecurityUtils.NO_ROLE)]

    UserDetails loadUserByUsername(String username, boolean loadRoles)
            throws UsernameNotFoundException {
        return loadUserByUsername(username)
    }

    @Transactional(readOnly = true, noRollbackFor = [IllegalArgumentException, UsernameNotFoundException])
    UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {

        User user = User.findByUsername(username)

        if (user == null) {
            user = User.findByEmail(username)
            log.debug("loadUserByUsername(username): user = ${user}")
        }

        if (!user) throw new NoStackUsernameNotFoundException()

        def roles = user.authorities

        // or if you are using role groups:
        // def roles = user.authorities.collect { it.authorities }.flatten().unique()

        def authorities = roles.collect {
            new SimpleGrantedAuthority(it.authority)
        }

        return new CustomUserDetails(user.username, user.password, user.enabled,
                !user.accountExpired, !user.passwordExpired,
                !user.accountLocked, authorities ?: NO_ROLES, user.id,
                user.email)
    }
}

```

The last thing we need to do is make sure Spring will use our extended implementation by specifying that in the `grails-app/conf/spring/resources.groovy` file.

<pre>
<code>import com.locator.eimpound.UserPasswordEncoderListener
<b>import com.locator.eimpound.CustomUserDetailsService</b>
// Place your Spring DSL code here
beans = {
  userPasswordEncoderListener(UserPasswordEncoderListener)
  <b>userDetailsService(CustomUserDetailsService)</b>
}
</code>
</pre>
