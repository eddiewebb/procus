# Product Focus Requirement Doc

Product Focus is an online application that helps product teams prioritize their backlog by aggregating customer feedback via connection from CRM tool like Hubspot.

## Main Concepts

The applicaiton has a few primary concerns:

- Desire: Desires represent the individual unit of a customer ask. A desire might be a feature, a fix, minor UI change or comprehensive change in product approach.
- Organization: Organizations represent the legal entity that holds or prospectively holds a contract with our platform. They are associated with a `Company` in the CRM as a 1:1 relationship. ORganizations represent existing or potential contract value measured in ARR.
- Contact: Contracts are indivudal users or prospepctive users of our platorm. They may express and vote for desires, but do not represent any contract value themselves.
- Work Item: Work Items are the tickets used by product and engineering team to track work. These work items exist outside this applicaiton typically in GitHub Issues or Jira Work. A single Desire has a 0-many relationship with Work Items.
- User: Users are the internal members of our company, consisting of customer facing and itnernal roles. Users will have access to create and report on Desires, and can also associate Desires which Organizations, Contacts, and Work Items.
- Report: Reports aggregate and summarize information for multiple audiences, and will consistent of different types support product team and customer facing roles.


## Role Based Access Control

The application uses a reasonable RBAC model that controls who can View, Create, Update, and Delete objects within the platform.  Roles may be defined to have different levels of access across the main concepts outlined above.
For instance, an Admin role might have full access across all objects, while a contributor role might have read access to reports, but can create and associate Desires.

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
  
  
## Object Model

### Desire

- Summary
- Problem it solves
- Alternate approaches
- Why it matters
- Total Lost or Blocked Revenue
- Total Current Revenue
- Total Contact Votes
