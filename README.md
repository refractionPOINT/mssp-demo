# LimaCharlie MSSP Demo Repo
Example repository demonstrating an MSSP setup with LimaCharlie.

If you are not an MSSP, this structure still describes a recommended setup
that is scalable and efficient using LimaCharlie. The main changes is that
you can simplify the configuration (repo here) to be a single tree.

## Configuration

***Current Deployment Status:***

![deploy](https://github.com/refractionPOINT/mssp-demo/workflows/deploy/badge.svg)

### Prerequisite Steps
1. Create a special CI/CD User on LC to use for orchestrating.
1. Create a User API key for the CI/CD User.

### Repo Structure
```
configs
configs/global
configs/global/all.yaml
configs/global/rules.yaml
configs/global/outputs.yaml
configs/global/fim.yaml
configs/global/artifact.yaml
configs/global/exfil.yaml
configs/customers/
configs/customers/cus_1
configs/customers/cus_1/main.yaml
configs/customers/cus_1/custom_rules.yaml
configs/customers/cus_2
configs/customers/cus_2/main.yaml
configs/customers/cus_2/custom_rules.yaml
configs/customers/cus_2/custom_fim.yaml
configs/customers/cus_3
configs/customers/cus_3/main.yaml
configs/customers/cus_3/custom_rules.yaml
```

The `global` directory contains all configs that are global to all customers.

Each `customers` subdirectory contains any specific customizations needed per customer. The `main.yaml` file is the core file used in the `limacharlie configs` CLI invocation.

### Adding a New Customer
1. Add the CI/CD User to the new organization.
1. Edit the following files and add the relevant commands based on previous customers:
    1. `.github/workflows/deploy.yaml`
    1. `.github/workflows/dry_run.yaml`

### Future Expansion
1. Add Replay jobs on new D&R rules PR to test rule changes.

## Permissions
The best way to scale for access control as an MSSP is to make use of a root account and Organization Groups.

### Root Account
A root account is simply a normal user account that is dedicated to be the ultimate authority for all Organizations the MSSP manages, while also being a user account that nobody actually logs in with other than to perform high level management tasks.

Having this root account be a member (and even the creator) of all Organizations simplifies the management of API keys. Instead of using per-Organization API keys, you can create User-level API keys that inherently have access across all your Organizations.

Obviously this also implies this account has a high level of privilege, so make sure to safeguard the credentials for it and to avoid logging in with it whenever possible, delegating specific permissions through Organization Groups instead.

### Organization Group
An Organization Group (OG) is an access unit composed of the following components:

1. Owners: Users with the permission to modify the OG. Note that an Owner does not automatically get the permissions on the Organizations that the OG itself provides its Members.
1. Members: Users who will gain permissions on a set of Organizations through this OG.
1. Organizations: Organizations onto which permissions will be granted by this OG.
1. Permissions: permissions that will be granted to all Members of the OG to all the Organizations that are part of this OG.

This means an OG is a great way to maintain a given role (set of permissions) on a large number of Organizations without having to modify each Organization every time a new User needs this role.

### Sample Setup
Here is an example access setup an MSSP may opt for:

#### Root Account
A special root account User, `limacharlie-root@corp.com`. This root account is used to create new MSSP customer Organizations as needed. Being the creator of these Organizations, the root account will automatically receive "owner" permissions (all) on the new Organizations.

#### Organization Groups
When a new Organization is created, it is added to the following OGs. When a new User (employee) joins the MSSP, that User is added to the relevant OGs.

##### Administrators
The Administrators OG is the top level of delegation for people within the MSSP that need full admin access to all features, billing and user access control. It holds all permissions.

##### Analysts
The Analysts OG is meant for most operators and analysts in the MSSP. It has all permissions except:

* `apikey.ctrl` (create and delete API keys)
* `billing.ctrl` (subscribe or make changes that will change billing in significant ways)
* `user.ctrl` (add and remove Users from Organizations)

##### Viewers
The Viewers OG is designed for Users who require visibility into the activity within each Organization, but do not require the ability to modify or task sensors.

##### Auditors
The Auditors OG is designed for high level auditing and reporting. It provides permissions to view audit logs and list sensors within each Organization.

#### Break-Glass
Some permissions may be considered more sensitive, like `sensor.task` and you may wish to restrict their access.

The recommended solution in LimaCharlie is to use Organization Groups (OG) as a break-glass mechanism. This has the advantage of limiting access to the permissions in general, while limiting the friction users who need to "upgrade" to those permissions encounter.

A classic example would be where you want to restrict `sensor.task` only to active incident responders. To accomplish this, you would create an OG named `active-responders` which holds the `sensor.task` permissions and has all active Organization as part of the group. By default, none of your analyst are Members of the group. When an incident occurs, an Owner of the group can add a User to the group to grant the tasking permission. By doing so, it also creates an audit trail (from the OG's audit log). When the User is done, simply remove them from the group.

Organization Groups also have [an API](https://api.limacharlie.io/static/swagger/#/Groups). This makes it easy to extend them to accomplish more complex and integrated workflows for your users.