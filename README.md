


# lib_LDAP

Mashup Sequencer project


For more technical informations : [documentation](./project.md)

- [Installation](#installation)
- [Sequences](#sequences)
    - [exportDirectoryAccountsAndGroups](#exportdirectoryaccountsandgroups)
    - [searchLDAP](#searchldap)
    - [syncDirectoryUsersToForms](#syncdirectoryuserstoforms)


## Installation

1. In your Convertigo Studio click on ![](https://github.com/convertigo/convertigo/blob/develop/eclipse-plugin-studio/icons/studio/project_import.gif?raw=true "Import a project in treeview") to import a project in the treeview
2. In the import wizard

   ![](https://github.com/convertigo/convertigo/blob/develop/eclipse-plugin-studio/tomcat/webapps/convertigo/templates/ftl/project_import_wzd.png?raw=true "Import Project")
   
   paste the text below into the `Project remote URL` field:
   <table>
     <tr><td>Usage</td><td>Click the copy button at the end of the line</td></tr>
     <tr><td>To contribute</td><td>

     ```
     lib_LDAP=git@github.com:convertigo/c8oprj-lib-ldap.git:branch=7.6.0
     ```
     </td></tr>
     <tr><td>To simply use</td><td>

     ```
     lib_LDAP=git@github.com:convertigo/c8oprj-lib-ldap/archive/7.6.0.zip
     ```
     </td></tr>
    </table>
3. Click the `Finish` button. This will automatically import the __lib_LDAP__ project


## Sequences

### exportDirectoryAccountsAndGroups

Exports LDAP or Active Directory user accounts and groups with caller-provided filters, bases and returned attributes

**variables**

<table>
<tr>
<th>name</th><th>comment</th>
</tr>
<tr>
<td>adminPassword</td><td></td>
</tr>
<tr>
<td>adminUser</td><td>LDAP or Active Directory account used to bind and execute the searches</td>
</tr>
<tr>
<td>countLimit</td><td>LDAP size limit. Use 0 for no limit.</td>
</tr>
<tr>
<td>emitAttributesJSON</td><td>Set to false to skip populating per-entry attributesJSON in XML items</td>
</tr>
<tr>
<td>emitDirectoryJSON</td><td>Set to false to skip building directoryJSONSTR</td>
</tr>
<tr>
<td>emitGroupsJSON</td><td>Set to false to skip building groupsJSONSTR</td>
</tr>
<tr>
<td>emitGroupsXML</td><td>Set to false to skip populating the groups XML items</td>
</tr>
<tr>
<td>emitSummaryXML</td><td>Set to false to skip populating the summary XML details</td>
</tr>
<tr>
<td>emitUsersJSON</td><td>Set to false to skip building usersJSONSTR</td>
</tr>
<tr>
<td>emitUsersXML</td><td>Set to false to skip populating the users XML items</td>
</tr>
<tr>
<td>groupPageCookie</td><td>Opaque Base64 cookie returned by the previous group page. Leave empty for the first page.</td>
</tr>
<tr>
<td>groupPageSize</td><td>LDAP paged-results size for groups. Defaults to 500 to keep direct calls memory-bounded. Use 0 only to disable group pagination.</td>
</tr>
<tr>
<td>groupReturnedAttributes</td><td></td>
</tr>
<tr>
<td>groupsBasePath</td><td>Optional LDAP base DN dedicated to group extraction. Falls back to ldapBasePath when empty.</td>
</tr>
<tr>
<td>groupSearchFilter</td><td>LDAP filter applied to group entries</td>
</tr>
<tr>
<td>includeGroups</td><td>Set to true to extract group entries. Defaults to false to avoid large group exports on direct calls.</td>
</tr>
<tr>
<td>includeRDN</td><td>Set to true to expose parsed RDN components for each entry</td>
</tr>
<tr>
<td>includeUsers</td><td>Set to true to extract user or account entries</td>
</tr>
<tr>
<td>ldapBasePath</td><td>Default LDAP base DN used when usersBasePath or groupsBasePath are not provided</td>
</tr>
<tr>
<td>ldapServer</td><td>LDAP server URL in the form ldap(s)://server:port</td>
</tr>
<tr>
<td>timeLimit</td><td>LDAP time limit passed to the search. Use 0 for no limit.</td>
</tr>
<tr>
<td>userExclusionFilter</td><td>Optional LDAP filter for user accounts to exclude, for example (|(sAMAccountName=krbtgt)(sAMAccountName=svc_*))</td>
</tr>
<tr>
<td>userPageCookie</td><td>Opaque Base64 cookie returned by the previous user page. Leave empty for the first page.</td>
</tr>
<tr>
<td>userPageSize</td><td>LDAP paged-results size for users. Defaults to 500 to keep direct calls memory-bounded. Use 0 only to disable user pagination.</td>
</tr>
<tr>
<td>userReturnedAttributes</td><td></td>
</tr>
<tr>
<td>usersBasePath</td><td>Optional LDAP base DN dedicated to user or account extraction. Falls back to ldapBasePath when empty.</td>
</tr>
<tr>
<td>userSearchFilter</td><td>LDAP filter applied to user or account entries</td>
</tr>
</table>

### searchLDAP

**variables**

<table>
<tr>
<th>name</th><th>comment</th>
</tr>
<tr>
<td>adminPassword</td><td></td>
</tr>
<tr>
<td>adminUser</td><td>A valid ActiveDirectory Admin Account with read privileges used to search for the user attributes. If this is not provided, the group membership information will not be returned.</td>
</tr>
<tr>
<td>includeRDN</td><td></td>
</tr>
<tr>
<td>ldapBasePath</td><td>The active directory user user to sign in. May be a valid domain account ex: 'DOMAIN\user' or the user's email ex: 'user@domain.com' or just the users full name ex:  'John doe'</td>
</tr>
<tr>
<td>ldapServer</td><td>The Active Directory server ldap url as ldap(s)://server:port. Mandatory.</td>
</tr>
<tr>
<td>returnedAttributes</td><td></td>
</tr>
<tr>
<td>searchFilter</td><td></td>
</tr>
</table>

### syncDirectoryUsersToForms

Synchronizes directory users into Forms user documents and reserved groups.

**variables**

<table>
<tr>
<th>name</th><th>comment</th>
</tr>
<tr>
<td>adminPassword</td><td></td>
</tr>
<tr>
<td>adminUser</td><td>LDAP or Active Directory account used to bind and execute the searches</td>
</tr>
<tr>
<td>bulkMergeRules</td><td>Merge rules applied when upserting Forms user documents</td>
</tr>
<tr>
<td>casesensitive</td><td>If false, Forms user identifiers and individual reserved groups are normalized to lowercase. LDAP learned group names are preserved.</td>
</tr>
<tr>
<td>clearCacheAfterSync</td><td>Set to true to clear Convertigo cache entries after the Forms synchronization</td>
</tr>
<tr>
<td>countLimit</td><td>Maximum number of directory users to synchronize across all pages. Use 0 for no limit.</td>
</tr>
<tr>
<td>debugUserCreationLogs</td><td>Set to true to log per-user preparation, lowercase normalization and PostBulkDocuments responses during the Forms synchronization</td>
</tr>
<tr>
<td>dryRun</td><td>Set to true to prepare the Forms payloads without writing to FullSync databases</td>
</tr>
<tr>
<td>formsDirectoryGroupPrefix</td><td>Prefix added to LDAP directory group names before SetUserInGroupBulk. Same behavior as lib_UserManager group mappings.</td>
</tr>
<tr>
<td>formsEditingRights</td><td>editing_rights value written in synchronized Forms user documents</td>
</tr>
<tr>
<td>formsFavorites</td><td>Favorites value written in synchronized Forms user documents</td>
</tr>
<tr>
<td>formsGroupPrefix</td><td>Prefix used to build the reserved Forms group identifier for each synchronized user</td>
</tr>
<tr>
<td>formsLanguage</td><td>Language value written in synchronized Forms user documents</td>
</tr>
<tr>
<td>formsProvider</td><td>Provider value written in synchronized Forms user documents</td>
</tr>
<tr>
<td>formsPublishedFirst</td><td>published_First value written in synchronized Forms user documents</td>
</tr>
<tr>
<td>groupDisplayAttributes</td><td>Comma/newline-separated group attributes used to build the Forms directory group name. Uses the same symbol as lib_UserManager.</td>
</tr>
<tr>
<td>groupLookupCountLimit</td><td>Maximum number of LDAP groups looked up per user. Use 0 for no limit.</td>
</tr>
<tr>
<td>groupLookupTimeLimit</td><td>LDAP group lookup time limit per user in milliseconds. Use 0 for no limit.</td>
</tr>
<tr>
<td>groupMemberAttribute</td><td>LDAP group attribute that contains the user distinguishedName. lib_UserManager currently uses uniqueMember in its group lookup.</td>
</tr>
<tr>
<td>groupObjectClass</td><td>LDAP objectClass value identifying directory groups. Uses the same symbol as lib_UserManager.</td>
</tr>
<tr>
<td>groupsBasePath</td><td>LDAP base DN dedicated to group lookup. Uses the same symbol as lib_UserManager.</td>
</tr>
<tr>
<td>groupSearchFilter</td><td>Advanced override for the LDAP group filter. Leave empty to use lib_UserManager.LDAP_GROUP_OBJECT_CLASS.</td>
</tr>
<tr>
<td>includeDirectoryGroups</td><td>Directory group lookup is mandatory. This variable is kept for compatibility but is forced to true by the sequence.</td>
</tr>
<tr>
<td>includeRDN</td><td>Set to true to expose parsed RDN components for each directory entry</td>
</tr>
<tr>
<td>ldapBasePath</td><td>Default LDAP base DN used when usersBasePath is not provided</td>
</tr>
<tr>
<td>ldapServer</td><td>LDAP server URL in the form ldap(s)://server:port</td>
</tr>
<tr>
<td>pageSize</td><td>Number of directory users requested per export page. Use a value greater than 0. Default 500.</td>
</tr>
<tr>
<td>timeLimit</td><td>LDAP time limit passed to the search. Use 0 for no limit.</td>
</tr>
<tr>
<td>userAccountNameAttributes</td><td>Comma/newline-separated candidate attributes used to build the Forms login, reserved group id and ACL. Defaults to lib_UserManager.LDAP_USER_ID_ATTRIBUTE.</td>
</tr>
<tr>
<td>userDisplayNameAttributes</td><td>Comma/newline-separated candidate attributes used as displayName fallback after a computed givenName/surname display name. Custom attributes must also be present in userReturnedAttributes.</td>
</tr>
<tr>
<td>userDistinguishedNameMarkers</td><td>Optional comma/newline-separated distinguishedName markers. When provided, only users whose distinguishedName contains at least one marker are synchronized. Default can be driven by symbol lib_LDAP.userDistinguishedNameMarkers.</td>
</tr>
<tr>
<td>userExclusionFilter</td><td>Optional LDAP filter for directory accounts to exclude from the Forms synchronization. Default can be driven by symbol lib_LDAP.userExclusionFilter.</td>
</tr>
<tr>
<td>userGivenNameAttributes</td><td>Comma/newline-separated candidate attributes used as first name for the computed Forms displayName. Custom attributes must also be present in userReturnedAttributes.</td>
</tr>
<tr>
<td>userMailAttributes</td><td>Comma/newline-separated candidate attributes used to fill the Forms user mail field. Custom attributes must also be present in userReturnedAttributes.</td>
</tr>
<tr>
<td>userReturnedAttributes</td><td>LDAP attributes returned for each user. Include every attribute referenced by userAccountNameAttributes, userMailAttributes, userDisplayNameAttributes, userGivenNameAttributes and userSurnameAttributes.</td>
</tr>
<tr>
<td>usersBasePath</td><td>Optional LDAP base DN dedicated to user extraction. Falls back to ldapBasePath when empty.</td>
</tr>
<tr>
<td>userSearchFilter</td><td>LDAP filter applied to the directory accounts to import into Forms. Default can be driven by symbol lib_LDAP.userSearchFilter.</td>
</tr>
<tr>
<td>userSurnameAttributes</td><td>Comma/newline-separated candidate attributes used as surname for the computed Forms displayName. Custom attributes must also be present in userReturnedAttributes.</td>
</tr>
</table>



