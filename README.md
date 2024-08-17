# Ansible Role: Apache Flex Config
Ansible Role - Apache (httpd) Flexible Configuration Template

***

Note: This role does *not* install Apache server. It is meant to be used in tandem with any of the many existing httpd
installing roles which do not have much in the way of config options. (e.g. [buluma.http](https://galaxy.ansible.com/ui/standalone/roles/buluma/httpd/),
[geerlingguy.apache](https://galaxy.ansible.com/ui/standalone/roles/geerlingguy/apache/), etc.)

This role exists to write /etc/httpd/conf.d configuration files. The "parameter" dict at each level is a rudimentary but
more flexible approach to maintaining configurability without creating individual variables for each of apache and its
mods' many many options. This also allows configurability for custom mods.

If you have any suggestions for subsections to add, please create a PR or otherwise make them known.

The examples host vars in example_vars.yml result in the following .conf file(s):
```
#
# Ansible managed
#

LoadModule ssl_module modules/mod_ssl.so

Listen  443 https
<VirtualHost *:443>
    ServerName example.com
    SSLEngine on
    SSLCertificateFile /etc/pki/server.cert
    SSLCertificateKeyFile /etc/pki/server.key
    SSLVerifyClient optional
    SSLVerifyDepth 2
    SSLOptions +FakeBasicAuth +StrictRequire

    <Location "/">
        <RequireAll>
            Require ssl-verify-client
            Require expr %{SSL_CLIENT_I_DN_O} == "Company Inc."
            Require expr %{SSL_CLIENT_I_DN_OU} == "Client CertAuth"
        </RequireAll>

        AuthBasicProvider ldap
        AuthType Basic
        AuthName "Company Stuff"
        AuthLDAPURL "ldap://company.local:389/DC=comp,DC=local?sAMAccountName?sub?(objectClass=*)" NONE
        AuthLDAPBindDN "comp\linuxLDAP"
        AuthLDAPBindPassword ex26498
        Require valid-user
        Require group compEx
    </Location>
</VirtualHost>
```
and
```
#
# Ansible managed
#


Alias /test /var/www/test


<Directory "/var/www/test">
    Options +Indexes
    IndexOptions NameWidth=*,DescriptionWidth=*
</Directory>
```
