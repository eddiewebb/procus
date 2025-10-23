# Product Focus - Requirement Doc

Product Focus ("Procus") is an online application that helps product teams prioritize their backlog by aggregating customer feedback via connection from CRM tool like Hubspot.

## Main Concepts

The application will correlate customer accounts with our internal product backlog through an intermediate object dubbed a 'Desire.'  The application will need to maintain these objects, manage user access, and run regular syncs jobs with the ultimate goal to generate easily consumable reports for our product teams and customer facing roles. 

### Application Owned Objects 

The application will maintain a few objects that are exclusive to the control of our application, managed directly via our UI and or API.

- Desire: Desires represent the individual unit of a customer ask. A desire might be a feature, a fix, minor UI change or comprehensive change in product approach.
- User: Users are the internal members of our company, consisting of customer facing and itnernal roles. Users will have access to create and report on Desires, and can also associate Desires which Organizations, Contacts, and Work Items.
- Report: Reports aggregate and summarize information for multiple audiences, and will consistent of different types support product team and customer facing roles.

### Externally Linked Objects

The application will also maintain awareness of some linked objects where the source of truth is pulled or synced from an external system. Our application will maintain a primary ID for each that will be used to sync the relevant summary fields of interest. But all edits will be made to the upstream system. 

- Organization: Organizations represent the legal entity that holds or prospectively holds a contract with our platform. They are associated with a `Company` in the CRM as a 1:1 relationship. ORganizations represent legal entity holding existing or potential contracts. An existing company will have an ARR value of recurring revenue paid to our company.
- Deal: Deals represent individual opportunities to win or expand contracts with organizations. Deals will have a contract value representing potential ARR paid to the company.
- Contact: Contracts are indivudal users or prospepctive users of our platorm. They may express and vote for desires, but do not represent any contract value themselves. These from from the configured CRM like Hubspot
- Work Item: Work Items are the tickets used by product and engineering team to track work. These work items exist outside this applicaiton typically in GitHub Issues or Jira Work. A single Desire has a 0-many relationship with Work Items.

#### External Syncing

- Connections to external systems will be read-only. Pulling data into Procus.
- Procus will expose a page to configure the API Endpoints, Authentication Tokens, and FIeld Mappings needed to external systems.
- Summary data for Deals, Organizations, and Contacts should be pulled nightly so they are searchable from Procus. This only needs to include name fields.
- Details Deal, Organization, and Contact data should be pulled when associated to a Desire
- Nightly sync job should pull any updates to external object fields mapped to Procus
  
## Integrations

### CRM

The system must connect to an external Customer Relationship Management tool. The first required integration will be Hubspot CRM.

https://developers.hubspot.com/docs/api-reference/overview 

### Work Item Management

The system must connect to an external work item management tool. The first required integration will be GitHub.

- https://docs.github.com/en/rest/projects/projects?apiVersion=2022-11-28
- https://docs.github.com/en/rest/issues?apiVersion=2022-11-28

## Role Based Access Control

The application uses a reasonable RBAC model that controls who can View, Create, Update, and Delete objects within the platform.  Roles may be defined to have different levels of access across the main concepts outlined above.
For instance, an Admin role might have full access across all objects, while a contributor role might have read access to reports, but can create and associate Desires.

Procus will offer 3 default roles:

- Admin: Full system permissions
- Contributor: Ability to create desires and link external objects
- Consumer: Ability to read reports and objects, but not modify data

Each of these roles may be modified for the permissions below. And new custom roles may be created.

### Assignable Desire Permissions 
- Create
- Update
- Delete
- Associate with Organization
- Associate with Contact
- Associate with Work Item

### Assignable Organization Permissions

Since organizations are synced from the CRM, minimal management should occur in the application.

- List Organizations
- View Organization Details & Desires

### Assignable Contact Permissions

Since contacts are synced from the CRM, minimal management should occur in the application.

- List Contacts
- View Contact Details & Desires
  
### Assignable Work Item Permissions

Since work items are merely links to an outside planning tool, minimal management should occur in the application.

- List Linked Work Items
- View Work Item Associated Desires

### Assignable Role Permissions

- Create Roles
- Assign User to Roles
- Assign default Role for new users
- Delete Roles
  
### Assignable User Permissions

- Invite Users
- Edit Users
- Delete Users

### Assignable Report Permissions

- View Reports

## Application Navigation

The application will be divided into key sections.

### Dashboard

The dashboard will summarize Desires, and allow searching and filtering of desires.  New Desires can be created from this page as well.  

### CRM Entities

The CRM section will allow searching across Organizations, Contacts, and Deals based on name field.  A detailed view of the objects will summarize associated desires.  This page might be used in customer facing conversations to ensure we are aware of their interests.  All associations will be editable to add, remove, or adjust the Need_Level.

### Work Items

The WOrk Item section will allow searching across all known work items by name. A detailed view of the objects will summarize associated desires.  This page might be used in product tean facing conversations to ensure they are aware of  interests associated with their backlog.  All associations will be editable to add, remove, or adjust the Need_Level.

### Settings

This page will be for Admins only. It will include severak sub-sections listed below.

#### Integrations

Here admins will configure API endpoints, Authorization, and Field Mappins for the CRM and Work Item tooling.

#### Statuses

Admins come here to define the `STATUS` objects used in Desires, marking which represent closed and delivered states.  For instance we could define a "Won't Do" status that would be considered isClosed true, but isDelivered as false.

## Reporting

The system shall allow reports to summarize the relationships and make insightful analysis.

### Top Desires by Deal Revenue

This report shall list Desires, ordered by the total ARR of associated Deals.  This report can be filtered by Need Level of the associating Deal_vote. The report can be filtered on the Deal isWon status.

### Top Desires by Existing Customers

This report shall list Desires, ordered by the total ARR of associated Organizations where the ARR is positive, is in active standing. This report can be filtered by Need Level of the votes.

### Top Desires by Votes

This report shall list Desires, ordered by the total votes of associated Contacts regardless of their Deal or Organization status. This report can be filtered by Need Level of the votes.
  
## Object Model (ERD)

This sections defines the Entity Relationship Model including key fields and object relationships.

```mermaid
erDiagram
    ORGANIZATION ||--o{ ORGANIZATION_DESIRE : expresses
    ORGANIZATION {
        string externalId pk
        string name
        boolean isActive
        boolean isChurned
        float arr
    }
    ORGANIZATION_DESIRE }o--|| DESIRE : references
    ORGANIZATION_DESIRE {
        string organizationId fk
        string desireId fk
        string needLevelId fk
    }
    ORGANIZATION_DESIRE }o--|| NEED_LEVEL: references
    DESIRE {
        string id pk
        string title
        string problemStatment
        string idealSolution
        string alternatives
        string statusId
    }
    DESIRE }o--|| STATUS: assigned

    STATUS {
        string id pk
        string label
        string description
        boolean isClosed
        boolean isDelivered
    }
    
    ORGANIZATION ||--o{ CONTACT : employs
    CONTACT {
        string externalId pk
        string organizationId fk
        string name
        string email
    }

    ORGANIZATION ||--o{ DEAL : owns
    DEAL {
        string externalId pk
        string organizationId fk
        string name
        boolean isWon
        string dealStage 
        float contractValue
    }

    CONTACT ||--o{ CONTACT_VOTE : asks_for
    CONTACT_VOTE {
        string contactId fk
        string desireId fk
        string needLevelId fk
    }
    CONTACT_VOTE }o--|| DESIRE: references
    CONTACT_VOTE }o--|| NEED_LEVEL: references
    NEED_LEVEL {
        string id pk
        string label "ENUM:Must Have, Important, Nice to Have"
    }

    DEAL ||--o{ DEAL_VOTE : asks_for
    DEAL_VOTE {
        string dealId fk
        string desireId fk
        string needLevelId fk
    }
    DEAL_VOTE }o--|| DESIRE: references
    DEAL_VOTE }o--|| NEED_LEVEL: references

    DESIRE ||--o{ DESIRE_WORK_ITEM : delivered_by
    DESIRE_WORK_ITEM }o--|| WORK_ITEM: references
    WORK_ITEM{
        string externalId pk
        string externalKey
        string externalTitle
        string externalStatus
        boolean isDelivered
    }
    DESIRE_WORK_ITEM {
        string desireId fk
        string workItemId fk
    }
```


