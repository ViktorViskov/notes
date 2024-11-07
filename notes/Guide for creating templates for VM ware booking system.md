### Naming
This booking system requires specific naming conventions to function properly.

All templates need to include the word `_template` in their name. Only templates with `_template` in their name will be shown on the frontend and will allow bookings to be created using them.

**Examples:**
- `ubuntu_2404_template` < This will allow the creation of bookings.
- `ubuntu_2204` < This will not allow the creation of bookings.

#### Server templates
If you need to create a server OS template, you must add `server` to the name. You also need to specify the `username` for accessing the VM.

**Examples:**
- `debian_12_server_template` > Indicates that this is a server, and the default user is `root`.
- `windows_22_server_template` > Indicates that this is a Windows server, and the default user is `Administrator`.

#### General Templates
For general templates, there is no need to use any specific word. These templates use the default user `user`.

**Examples:**
- `windows_10_template`
- `debian_12_gnome_template`

### Creating template
When you start creating a template, you need to choose the correct type for the VM (Windows or Linux). The password must be changed after the VM is created. We use different scripts for different OS types.
The password should be the same as the one in the `.env` file under the variable `VM_ROOT_PASSWORD`. This password is used to change the default password to a custom one.

**Important:** *When you create a template that is not for a server OS, you must use the username `user`. After installation, the password for this username will be changed to a custom one.*
#### Template for linux machines
When creating a template for a Linux OS, after installation, you need to create a password for the root user. 

**Important:** *Ensure that the OpenSSH server is installed on the Linux template and that the server is configured to allow SSH connections for the root user.*

```shell
sudo bash # get super user previligious
passwd # to create or change password for current user
```