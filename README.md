User-Init
=========

Check (otherwise create) that given users and groups are present on the system.

Requirements
------------

Python installed on the server.

Role Variables
--------------

Available customizations:

 - `users_init_users`: dictionnary of users to be created
   - `comment`: description of user in `passwd` file (see [GEOS field](https://en.wikipedia.org/wiki/Gecos_field)) (defaut none)
   - `create_home`: create a home directory if it doesn’t exist (default according to `users_init_create_home_default`)
   - `expires`: epoch time when user will expire.  `-1` means never (default according to `users_init_expires_default`)
   - `generate_ssh_key`: whether to generate SSH key for the user (default according to `users_init_generate_ssh_key_default`)
   - `group`: primary group of the user (default none).  Must be created manually
   - `groups`: list of groups the user will be member (default none).  It will overrides previous settings (no append).  Must be created manually
   - `home`: home directory for the user (default according to system)
   - `move_home`: move user old home to a new one (default according to `users_init_move_home_default`)
   - `non_unique`: allows changing _uid_ (when used with option `uid`) to a non unique one (default `users_init_non_unique_default`)
   - `password`: the user’s password to this crypted value. (default none)
   - `password_lock`: disables login with a password.  The user is not disabled, but cannot login by password methods (might be `sudo su` or `ssh`, etc.) (default according to `users_init_password_lock_default`)
   - `seuser`: sets the seuser type (=user_u=) on [selinux](https://www.digitalocean.com/community/tutorial_series/an-introduction-to-selinux-on-centos-7) enabled systems (default none)
   - `shell`: shell used when logging-in (default according to `users_init_shell_default`)
   - `skeleton`: sets the home skeleton (template) used to create the home directory (default according to system)
   - `uid`: sets the _uid_ of the user (default omitted)
   - `ssh_key`: SSH key generation configuration
	 - `force`: overwrite old SSH key with a new one (default according to `users_init_ssh_key_force_default`)
	 - `ssh_key_bits`: size of the key in bits (default according to `users_init_ssh_key_bits_default`)
	 - `ssh_key_comment`: comment for the SSH key (default omitted)
	 - `ssh_key_file`: custom user SSH key file path (default omitted)
	 - `ssh_key_passphrase`: passphrase for accessing the key (default omitted)
	 - `ssh_key_type`: SSH key algorithm type.  This must be provided in order to create the key.  Possible values are: `rsa`, `ed25519` or others depending on the system
	 
 - `users_init_system_users`: dictionnary of system users to be created.  This has the same structure as `users_init_users`, except default values don’t have the `users_init_` prefix, but `users_init_sysuser_`
 - `users_init_user_groups`: list of user groups to create.  Because groups provided to `users_init_users` won’t be created automatically
 - `users_init_system_groups`: list of system groups to create.  Because groups provided to `users_init_system_users` won’t be created automatically

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - role: notetiene.users-init
		   users_init_system_users:
			   dummy:
				   create_home: no
				   authorized_keys:
				   groups: 
					   - dummy
				   shell: "/bin/sh"users_init_users:
			   foo:
				   password: "{{ lookup('passwordstore', 'hostname/foo') | password_hash('sha512', 2645282 | random(seed=inventory_hostname) | string, rounds=165536) }}"
				   authorized_keys:
				   ssh_key:
				   ssh_key_bits: 4096
				   ssh_key_type: "rsa"
				   expires: 1422403387
				   home: "/home/bar/foo"
				   group: foo
				   groups:
					   - bar
				   shell: "/bin/bash"
		   users_init_system_groups:
			   - dummy
		   users_init_user_groups:
			   - foo
			   - bar

License
-------

MIT

Author Information
------------------

This role was created by [Etienne Prud’homme](https://www.etienne.cc/).
