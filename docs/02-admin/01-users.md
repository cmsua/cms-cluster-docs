# User Management

## User Creation

Users should be able to sign up via the [Account Console](https://auth.cms.physics.ua.edu/realms/ua-cms/account/). If not, add them yourselves via the [Admin Console](https://auth.cms.physics.ua.edu/admin/ua-cms/console/).

Once a user has registered, in the Admin Console, you may assign them a temporary password if they have not set one on their own.

Additionally, a user should be assigned the HPC, Admin, or Sysadmin roles. The HPC role allows access to all our cluster's services. The Admin role (you) allows access to administrator features of apps (eg. opening other people's jupyter environments, editing other docker libraries), as well as the user admin console itself.

The Sysadmin role allows access to administrate the entire cluster, and is generally something you shouldn't touch. You can assign it to yourself or others in an emergency if nessecary.