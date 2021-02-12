# lib_LDAP
This is the LDAP library for Convertigo platform. Install this library to execute LDAP requests for your Convertigo applications.

# Installation

* In your Convertigo Studio use File->Import->Convertigo->Convertigo Project and hit the 'Next' button

* In the Dialog 'Project remote URL' field Paste :

        lib_LDAP=https://github.com/convertigo/c8oprj-lib-ldap.git

* And click the 'Finish' button
* Create all 'Undefined Global Symbols' when prompted

# Usage

## Configuring Convertigo Symbols

__lib_LDAP__ needs some symbols to be configured. You configure them through the Web Console: **https://&lt;your site&gt;.convertigo.net/admin**, hit the ___symbols___ button to get to the symbol configuration page.

Symbol  | value
------| ------
lib_LDAP.adminUser | LDAP **Administrator user** having administrator rights
lib_LDAP.adminPassword.secret | LDAP **Administrator password**
lib_LDAP.ldapBasePath | LDAP **Search Base** for the queries (for example: DC=TWINSOFT,DC=FR)
lib_LDAP.ldapServer | LDAP **Server URL** formatted as ldap://example.com:389

## Sequences

____lib_LDAP____ provides sequences you can call in your projects

Sequence  | Action
------| ------
searchLDAP | Return an array of results. <br>Takes 7 variables :<br> - **adminUser** : LDAP **Administrator user** which have administrator rights. Default value is **lib_LDAP.adminUser** symbol<br >- **adminPassword** : LDAP **Administrator password**. Default value is **lib_LDAP.adminPassword** symbol<br >- **ldapBasePath** : LDAP **Search Base** for the queries (for example: DC=TWINSOFT,DC=FR). Default value is **lib_LDAP.ldapBasePath** symbol<br >- **ldapServer** : LDAP **Server URL** formatted as ldap://example.com:389. Default value is **lib_LDAP.ldapServer** symbol<br >- **searchFilter** : LDAP **search Filter** to be applied. Default value is (objectClass=*)<br >- **returnedAttributes** : LDAP **Attributes** to be returned. Default value is ["displayName", "mail"]<br >- **includeRDN** : Defines if Relative Distinguished Name must be retunred . Default value is false<br >
