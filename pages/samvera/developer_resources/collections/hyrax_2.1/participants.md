---
title: "FAQ - Collection Sharing Participants"
keywords: Participants, Collection, FAQ, Sharing
categories: How to Do All the Things
permalink: collection_extensions_faq_participants.html
folder: samvera/how-to/collection_extensions/hyrax_2.1/faq_participants.md
sidebar: home_sidebar
tags: [development_resources]
a-z: ['FAQ - Collection Sharing Participants', 'Collection Extensions - Sharing Participants', 'Sharing Participants for Collections', 'Participants for Collections']
version:
  label: 'Hyrax v2.1.0'
  branch:
    label: 'collections-sprint'
    link: 'https://github.com/samvera/hyrax/tree/collections-sprint'
---

## Understanding Collection Type Participants

NOTE: Only admins can create, edit, and delete collection types.  Participants set for a collection type effect how users can interact with collections of this type.

### How to set participants for a collection type?

* Dashboard -> Settings -> Collection Types
* Click Edit beside the collection type you want to update
* Select Participants tab
* Search for user/group
* Set access level to Manager or Creator
* Click Add button

### Manager

**Description:** 

Managers for a collection type can edit collections other users created, including adding to and removing works from a collection, modifying collection metadata, and deleting collections, only for collections of the type they manage.

**Default:**  Repository Administrators

**Implementation:**

* When a manager is added, an entry is added to `collection_type_participants` database table granting the user/group :manage access.
* When a manager is removed, the entry in `collection_type_participants` database table for this manager is removed.
* When the create new collection process is initiated, the user is allowed to create collections of types for which they have manage access.
* When a collection is created of a type, each collection type manager is made a manager of the collection which grants them `edit_access` to the new collection regardless of which user creates the collection.  This `edit_access` grant is what grants managers the abilities to edit the collection, collection items, and metadata.
  
NOTE: Access to a collection is granted when the collection is created.  Changes to managers after a collection is created do not change the access grants of the collection.

Special Note on Admin Sets:
* Admin Set collection type is pre-defined and has default managers assigned.
* **Default:**  Administrators
* A site can assign managers to the Admin Set collection type.  This grants them :manage access to admin sets created after they became a manager.  At the collection type level, assigning managers has the same effect as for other collection types.


### Creator

**Description:** 

Creators for a collection type can create collections of this type.  They are given manage acceess only to collections they create.

**Default:**  Registered Users (i.e., users that have logged into the system)

Impact:

* When a creator is added, an entry is added to `collection_type_participants` database table granting the user/group :create access.
* When a creator is removed, the entry in `collection_type_participants` database table for this creator is removed.
* When the create new collection process is initiated, the user is allowed to create collections of types for which they have create access.
* When a collection type creator creates a new collection, the creator is made a manager of only that collection which grants them `edit_access` to the new collection.  This `edit_access` grant is what grants the creator the abilities to edit the collection, collection items, and metadata.

NOTE: Access to a collection is granted when the collection is created.  Changes to creators after a collection is created do not change the access grants of the collection.  These changes only effect who can create new collections in the future.

NOTE: A site can set creators of Admin Set collection type, which gives these creators the ability to create new admin sets.

### Configuring collection sharing

The collection type controls whether collections of that type can be shared.  If Sharable? is true, then collections of this type can be shared; otherwise, they can not.  Collections that can be shared have a Sharing tab in the collection edit form.

*Proposed, but not yet implemented:* For backward compatibility, the sharable setting should have additional configuration that determines how participants are applied to works created directly in the collection.

- [x] Collection managers are given `edit access` to the collection (cannot be turned off)
- [ ] Collection viewers are given `read access` to the collection which allows them to see works in the collection regardless of the works visibility.
- [ ] Works are assigned participant access where managers are given edit_access and viewers are given read_access to any work created directly in the collection.


These additional settings allow for backward compatibility for pre-defined collection types.

* User Collection type shares permissions for the collection only, but does not apply permissions to works.
* Admin Set type does not share view permissions to the admin set, but does apply permissions to works.
* The default is for all to be checked.


----

## Understanding Collection Participants

### How to set participants for a collection?

* Dashboard -> Collections
* Click Edit collection in action menu beside the collection you want to update
* Select Sharing tab
* Search for user/group
* Set access level to Manager or Depositor or Viewer
* Click Add button


### Manager

Managers of a collection can add to and remove works from the collection, modify collection metadata, and delete the collection.  If configured to set work permissions, the managers are given 'edit_access' to any work created directly in the collection.

**Default:**  user creating the collection, Collection Type managers for this collection's type

**Implementation:**

* When a manager is added
  * an entry is added to `permission_template_accesses` database table granting the user/group :manage access 
  * `edit_access_group_ssim` or `edit_access_person_ssim` is set in the solr document
  * a `Hydra::AccessControls::Permission` is added in Fedora
* When a manager is removed
  * the entry in `permission_template_accesses` database table is removed for this manager 
  * `edit_access_group_ssim` or `edit_access_person_ssim` is updated to remove the group/user id from the solr document
  * the `Hydra::AccessControls::Permission` is removed from Fedora
* When the create new work actor stack is run, if this is the only collection assigned, then the work is considered to be created directly in the collection.  When a work is created directly in a collection 
  * each manager is granted `edit_access` to the new work
  
NOTE: Access to a work is granted when the work is created.  Changes to managers after a work is created do not change the access grants of any existing works.  Work access grants can be updated in the work edit form to remove/add access for any group/user including those set by the collection.

Special Note on Admin Sets:
* A site can set managers on an admin set, which grants them :manage access to that admin set.  This creates the solr edit access and Fedora AccessControl entries as well, same as for other collections.
* All works created with this admin set assigned at create time give the admin set's managers `edit_access` to the new work.



### Depositor

Depositors of a collection can view the collection's admin show page and add/remove works to it, even if the visibility permissions of the collection otherwise would not permit them to view it.

**Default:**  *assigned only, no defaults*

**Implementation:**

* When a depositor is added
  * an entry is added to `permission_template_accesses` database table granting the user/group :deposit access
  * ### TODO: Trying to determine what happens with Sipity Roles...   assigned the role Hyrax::RoleRegistry::DEPOSITING in the workflow for the work

* When a depositor is removed
  * the entry in `permission_template_accesses` database table is removed for this depositor 
* When the create new work actor stack is run, no special access is granted to depositors.  The depositor who created the collection is assigned edit access to the work as the creator of the work following the rules governing access for works.  This is the same whether the user is a depositor or not.


### Viewer

Viewers of a collection can add to and remove works from the collection, modify collection metadata, and delete the collection.  If configured to set work permissions, the viewers are given 'read_access' to any work created directly in the collection.

**Default:**  *assigned only, no defaults*

**Implementation:**

* When a viewer is added
  * an entry is added to `permission_template_accesses` database table granting the user/group :view access 
  * `read_access_group_ssim` or `read_access_person_ssim` is set in the solr document
  * a `Hydra::AccessControls::Permission` is added in Fedora
* When a viewer is removed
  * the entry in `permission_template_accesses` database table is removed for this viewer 
  * `read_access_group_ssim` or `read_access_person_ssim` is updated to remove the group/user id from the solr document
  * the `Hydra::AccessControls::Permission` is removed from Fedora
* When the create new work actor stack is run, if this is the only collection assigned, then the work is considered to be created directly in the collection.  When a work is created directly in a collection 
  * each viewer is granted `read_access` to the new work
  
NOTE: Access to a work is granted when the work is created.  Changes to viewers after a work is created do not change the access grants of any existing works.  Work access grants can be updated in the work read form to remove/add access for any group/user including those set by the collection.

Special Note on Admin Sets:
* A site can set viewers on an admin set, which grants them :view access to that admin set.  This creates the solr read access and Fedora AccessControl entries as well, same as for other collections.
* All works created with this admin set assigned at create time grant the admin set's viewers `read_access` to the new work.


## Setting groups vs. users as participants

### Why should you care about this?

Works that are assigned access grants through a collection or admin set are not updated if the roles in the collection or admin set are changed after the work already exists.  

For example, 
* if user-1 is a manager of collection-1 when work-1 is created 
  * user-1 is granted `edit_access` to work-1
* if collection-1 participants is changed to remove user-1 as a manager and add user-2 as a manager
  * work-1 continues to have user-1 with `edit_access`  
  * user-2 has no special access grants to work-1  
* when work-2 is created after the manager change 
  * user-2 is granted `edit_access` to work-2, and all other works going forward as long as user-2 is a manager of the collection/admin_set

### Why might you want to set user participants?

Some sites has preservation concerns with allowing the assigned role changes to impact existing collections and works.  In that case, you may want to set participants as specific users.  Any changes to existing collections and works will require an edit of each one to change those assigned to roles.
 
### What are the benefits of setting groups as participants?

The access grant for a work is either granted to a single user or to a group of users.  If you set a group as a participant, you can move users in and out of the group to give or remove grants to a work.

For example, 
* if group-1 includes user-1 AND group-1 is a manager of collection-1 when work-1 is created
  * group-1 is granted `edit_access` to work-1
  * user-1 has `edit_access` to work-1 through their membership in group-1
* if group-1 membership is changed to remove user-1 and add user-2
  * user-1 no longer has `edit_access` to work-1 since user-1 is no longer a member of group-1
  * user-2 has `edit_access` to work-2 through their membership in group-1
