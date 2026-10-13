


# lib_LDAP

Synchronizes the users of an LDAP or Active Directory directory into C8Oforms: one Forms user profile and one personal group per user, plus the memberships of selected directory groups. Optionally hides the profiles of users who left the directory.

The synchronization is the `syncDirectoryUsersToForms` sequence. It is Private: call it from another sequence, for example a scheduled one, and preview it first with `dryRun=true`.

## Configuration

The connection settings are the ones of the C8Oforms LDAP login (lib_UserManager symbols). Everything else is driven by `lib_LDAP.*` symbols, or by the sequence variables when called with parameters.

| symbol | default | usage |
|--------|---------|-------|
| lib_UserManager.ldapServer | | `ldap://host:port` or `ldaps://host:port` |
| lib_UserManager.adminUser | | service account DN used to bind; its password is the ciphered `adminPassword` variable, aligned with `lib_UserManager.adminPassword.secret` |
| lib_UserManager.ldapBasePath | | base DN of the users |
| lib_UserManager.LDAP_USER_ID_ATTRIBUTE | uid | login attribute (`sAMAccountName` for Active Directory) |
| lib_UserManager.ldapBasePathGroup | ldapBasePath | base DN of the groups, also used by the login |
| lib_UserManager.LDAP_GROUP_OBJECT_CLASS | group | object class of the groups when `lib_LDAP.groupSearchFilter` is empty |
| lib_UserManager.LDAP_GROUP_DISPLAY_ATTRIBUTE | cn | group attribute giving the Forms group name (`ad:<name>`) |
| C8Oforms.LDAP_AD_Casesentsitive | true | `false` lowercases logins and personal groups |
| lib_LDAP.userSearchFilter | `(&(objectCategory=person)(objectClass=user))` | LDAP filter of the users to synchronize |
| lib_LDAP.userExclusionFilter | | LDAP filter of the users to leave out |
| lib_LDAP.userAccountNamePattern | | regular expression the login must match (case-insensitive) |
| lib_LDAP.userDistinguishedNameMarkers | | only keep users whose DN contains one of these comma-separated values |
| lib_LDAP.userExpiryAttribute | | exit date attribute; users whose date is before today are left out |
| lib_LDAP.userExpiryDateFormat | dd/MM/yyyy | Java SimpleDateFormat pattern of the exit date |
| lib_LDAP.userExpiryGraceDays | 0 | days a user is kept after the exit date |
| lib_LDAP.groupSearchFilter | | filter of the groups to map, e.g. `(&(objectClass=groupOfUniqueNames)(\|(cn=GROUP_A)(cn=GROUP_B)))` |
| lib_LDAP.groupMemberAttribute | uniqueMember | `member` for groupOfNames or Active Directory |
| lib_LDAP.groupLookupMode | preload | `perUser` runs one LDAP search per user (slow on large directories) |
| lib_LDAP.reconcileAbsentUsers | false | hide the profiles of users who are no longer selected |
| lib_LDAP.userMailAttributes, userDisplayNameAttributes, userGivenNameAttributes, userSurnameAttributes | mail, displayName..., givenName, sn | attributes used to fill the Forms profile |
| lib_LDAP.debugUserCreationLogs | false | per-user logs |

In the user and group filters, `{{today}}` (yyyyMMdd) or `{{today:FORMAT}}` is replaced by the current date. In a symbols file, prefer `[0-9]` to `\d` in regular expressions, since backslashes must be doubled there.

Example for an OpenLDAP directory whose exit dates are stored as text:

```
lib_UserManager.LDAP_USER_ID_ATTRIBUTE=uid
lib_UserManager.ldapBasePathGroup=ou=MyApp,ou=applications,dc=example,dc=org
lib_LDAP.userSearchFilter=(&(objectClass=inetOrgPerson)(mail=*))
lib_LDAP.userAccountNamePattern=^(AB[0-9]{3})[^TG]
lib_LDAP.userExpiryAttribute=exitDate
lib_LDAP.userExpiryDateFormat=dd/MM/yyyy
lib_LDAP.groupSearchFilter=(&(objectClass=groupOfUniqueNames)(|(cn=APP_USERS)(cn=APP_ADMINS)))
lib_LDAP.groupMemberAttribute=uniqueMember
```

## How the synchronization works

1. The users are read once, with LDAP paged results on one connection (`pageSize`, 500 by default). The read is flagged incomplete when the server stops it (size or time limit, missing paged response, referrals).
2. The DN markers, the login pattern and the exit date are applied to each entry.
3. The groups matching `groupSearchFilter` are read once and their members are matched in memory, including Active Directory ranged attributes (`member;range=...`).
4. For each batch of users, the profiles `C8Oreserved_<login>` (provider `ad`, mail, display name, owner) are written to `c8oforms_fs`, then the personal group and the `ad:<group>` memberships through `lib_FullSyncGrp.SetUserInGroupBulk`.
5. When `reconcileAbsentUsers=true`, the reconciliation runs (see below).

The response holds a `summary` and a `syncSummaryJSONSTR` with the counters: directory entries, read completeness, filtered, expired and unparseable dates, written documents and errors, preloaded groups, membership errors.

## Reconciliation

Disabled by default. When enabled, every profile of the configured provider that is not selected by the directory read is hidden (`c8o_view_type_users=false`, `ldap_sync_disabled=true`) and its group memberships are removed; the profile document is kept. A profile selected again is reactivated and its directory groups are recreated, manual Forms groups are not.

It is refused, without any change, when the directory read is incomplete or empty, when `countLimit` is set, or when users have no login. Profiles whose id only differs by case from a directory user, or whose identity is inconsistent, are left untouched and reported. Run it with `dryRun=true` first and check the `disabled` list.

## Compatibility

Works with lib_UserManager and lib_FullSyncGrp 8.0.0.19 or later, public or security builds. When the lib_FullSyncGrp group delegation is enabled (`lib.fullsyncgrp.delegation.adminGroups`), an unauthenticated synchronization skips the memberships of protected groups and the reconciliation never removes them; both are reported.

`exportDirectoryAccountsAndGroups` and `searchLDAP` are Private helper sequences.


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
<td>userAutoPage</td><td>Set to true to walk LDAP pages on the same connection before returning one bounded user page. Use with userPageOffset for cookie-safe pagination.</td>
</tr>
<tr>
<td>userExclusionFilter</td><td>Optional LDAP filter for user accounts to exclude, for example (|(sAMAccountName=krbtgt)(sAMAccountName=svc_*))</td>
</tr>
<tr>
<td>userPageCookie</td><td>Opaque Base64 cookie returned by the previous user page. Leave empty for the first page.</td>
</tr>
<tr>
<td>userPageOffset</td><td>Number of LDAP user entries to skip before collecting the returned page when userAutoPage is true.</td>
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
<td>userSearchFilter</td><td>LDAP filter applied to user or account entries. {{today}} or {{today:FORMAT}} (FORMAT being a Java SimpleDateFormat pattern) is replaced by the current date.</td>
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

Synchronizes directory users into Forms user documents and reserved groups. Works with lib_UserManager and lib_FullSyncGrp 8.0.0.19 or later, public or security builds, and honors lib_FullSyncGrp group delegation.

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
<td>groupLookupCountLimit</td><td>Maximum number of LDAP groups looked up per user in perUser group lookup mode. Use 0 for no limit.</td>
</tr>
<tr>
<td>groupLookupMode</td><td>preload (default) reads the groups matching groupSearchFilter once and resolves memberships in memory; perUser runs one LDAP group search per user, slow for large directories. Default can be driven by symbol lib_LDAP.groupLookupMode.</td>
</tr>
<tr>
<td>groupLookupTimeLimit</td><td>LDAP group lookup time limit per user in milliseconds, in perUser group lookup mode. Use 0 for no limit.</td>
</tr>
<tr>
<td>groupMemberAttribute</td><td>LDAP group attribute that contains the user distinguishedName (uniqueMember for groupOfUniqueNames, member for groupOfNames or Active Directory). lib_UserManager currently uses uniqueMember in its group lookup. Default can be driven by symbol lib_LDAP.groupMemberAttribute.</td>
</tr>
<tr>
<td>groupObjectClass</td><td>LDAP objectClass value identifying directory groups. Uses the same symbol as lib_UserManager.</td>
</tr>
<tr>
<td>groupsBasePath</td><td>LDAP base DN dedicated to group lookup. Uses the same symbol as lib_UserManager.</td>
</tr>
<tr>
<td>groupSearchFilter</td><td>Advanced override for the LDAP group filter, e.g. (&(objectClass=groupOfUniqueNames)(|(cn=GROUP_A)(cn=GROUP_B))). Leave empty to use lib_UserManager.LDAP_GROUP_OBJECT_CLASS. Supports the {{today}} token. Default can be driven by symbol lib_LDAP.groupSearchFilter.</td>
</tr>
<tr>
<td>includeDirectoryGroups</td><td>Directory group lookup is mandatory. This variable is kept for compatibility but is forced to true by the sequence.</td>
</tr>
<tr>
<td>includeRDN</td><td>Kept for compatibility, not used by the synchronization.</td>
</tr>
<tr>
<td>ldapBasePath</td><td>Default LDAP base DN used when usersBasePath is not provided</td>
</tr>
<tr>
<td>ldapServer</td><td>LDAP server URL in the form ldap(s)://server:port</td>
</tr>
<tr>
<td>pageSize</td><td>LDAP paged results size and number of users written to Forms per batch. Use a value greater than 0. Default 500.</td>
</tr>
<tr>
<td>reconcileAbsentUsers</td><td>Set to true to hide Forms profiles of this provider that are missing from a complete directory snapshot, remove their group memberships and reactivate returning users. Refused when the snapshot is incomplete, empty, limited by countLimit or contains users without account identifier. Preview with dryRun=true. Default can be driven by symbol lib_LDAP.reconcileAbsentUsers.</td>
</tr>
<tr>
<td>timeLimit</td><td>LDAP time limit passed to the search. Use 0 for no limit.</td>
</tr>
<tr>
<td>userAccountNameAttributes</td><td>Comma/newline-separated candidate attributes used to build the Forms login, reserved group id and ACL. Defaults to lib_UserManager.LDAP_USER_ID_ATTRIBUTE.</td>
</tr>
<tr>
<td>userAccountNamePattern</td><td>Optional regular expression, matched case-insensitively against the account identifier (before lowercase normalization). Users that do not match are not synchronized, e.g. ^(AC750|UR[0-9]{3})[^TG]. Prefer [0-9] to backslash escapes, which must be doubled in symbol files. Default can be driven by symbol lib_LDAP.userAccountNamePattern.</td>
</tr>
<tr>
<td>userDisplayNameAttributes</td><td>Comma/newline-separated candidate attributes used as displayName fallback after a computed givenName/surname display name. Custom attributes must also be present in userReturnedAttributes.</td>
</tr>
<tr>
<td>userDistinguishedNameMarkers</td><td>Optional comma/newline-separated distinguishedName markers. When provided, only users whose distinguishedName contains at least one marker are synchronized. Default can be driven by symbol lib_LDAP.userDistinguishedNameMarkers.</td>
</tr>
<tr>
<td>userExclusionFilter</td><td>Optional LDAP filter for directory accounts to exclude from the Forms synchronization. Supports the {{today}} token. Default can be driven by symbol lib_LDAP.userExclusionFilter.</td>
</tr>
<tr>
<td>userExpiryAttribute</td><td>Optional attribute holding the user exit date. Users whose date is strictly before today (minus userExpiryGraceDays) are not synchronized and are hidden by the reconciliation; users without a date are kept; unparseable dates are kept and reported in the summary. The date is evaluated at each run. Default can be driven by symbol lib_LDAP.userExpiryAttribute.</td>
</tr>
<tr>
<td>userExpiryDateFormat</td><td>Java SimpleDateFormat pattern of userExpiryAttribute, parsed strictly, e.g. dd/MM/yyyy or yyyyMMddHHmmss. Default can be driven by symbol lib_LDAP.userExpiryDateFormat.</td>
</tr>
<tr>
<td>userExpiryGraceDays</td><td>Number of days a user is still synchronized after the exit date. 0 keeps the user up to and including the exit day. Default can be driven by symbol lib_LDAP.userExpiryGraceDays.</td>
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
<td>userSearchFilter</td><td>LDAP filter applied to the directory accounts to import into Forms. {{today}} or {{today:FORMAT}} (FORMAT being a Java SimpleDateFormat pattern) is replaced by the current date, e.g. (|(!(expiry=*))(expiry>={{today:yyyyMMdd}})). Default can be driven by symbol lib_LDAP.userSearchFilter.</td>
</tr>
<tr>
<td>userSurnameAttributes</td><td>Comma/newline-separated candidate attributes used as surname for the computed Forms displayName. Custom attributes must also be present in userReturnedAttributes.</td>
</tr>
</table>



