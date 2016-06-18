# Ansible AutoFS Role

Ansible role which can install autofs, as well as manage 'master' and 'map' files.

### Requirements

  * Ansible 2.0 or later
  * sudo/root privileges

Tested on Linux, but may also work on other platforms.

### Usage

Once the role is in your role path, it will setup the service by default. Masters and maps can be created by passing the dictionaries shown below as variables.

#### Service

The following options are available to configure the service:

  * `autofs_package_name: 'autofs'` - the package name to use for autofs
  * `autofs_package_state: 'present'` - the package state; mainly included to allow both 'latest' and 'present'
  * `autofs_service_name: 'autofs'` - the service name for autofs
  * `autofs_allow_browse: True` - when 'True' (default), ensures the `browse_mode` option is enabled
  * `autofs_conf_file: '/etc/autofs.conf'` - the location of the main configuration file
  * `autofs_master_file: '/etc/auto.master'` - the location of the main master file
  * `autofs_dyn_dir: '/etc/auto.master.d'` - the location of the 'dynamic' configuration directory

#### Master Files

The role is designed to place master files in the autofs dynamic directory, to try and prevent writing to the main 'auto.master' file. It will try to ensure the following line is present though:

`+dir:/path/to/dynamic/directory`

By default, a backup is created, but it can be disabled with the `autofs_create_master_backup` option.

To create master files, the dictionary `autofs_masters:` is used with the following syntax:

```
autofs_masters:
  auto.master.name1:
    mount_point: /some/path
    map_type: file
    map_format: sun
    map_name: name.map
    options: 'option1 option2 option3'
  auto.master.name2:
    mount_point: /another/one
    map_name: some.map
```

The master file will be created with the key of each entry as the name, in the defined dynamic directory. The user, group, and permissions of these (as well as the map files) can be adjusted with the `autofs_owner`, `autofs_group`, and `autofs_mode` options respectively.

#### Map Files

To create map files, the dictionary `autofs_maps` is used. This has the below expected syntax:

```
autofs_maps:
  map.name:
    path: /some/path
    mounts:
      - 'dir1 192.168.1.10:/export1'
      - 'dir2 -rw,soft,intr,rsize=8192,wsize=8192 192.168.1.20:/export2'
```

The map files will be created with the key as their name, in the path specified in the 'path' value. The files are created 'freeform', using a list item per line in desired map file.

### Known Issues

When using the role, but not defining any master or map files, you'll get a warning:

```
[DEPRECATION WARNING]: Skipping task due to undefined Error, in the future this will be a fatal error.: 'autofs_maps' is undefined.
This feature will be removed in a future release. Deprecation warnings can be disabled by setting 
deprecation_warnings=False in ansible.cfg.
```

The jinja check doesn't appear to be working as intended. Since it works as intended still, this will be addressed in a later release.

### License

This role is released under the MIT license. See LICENSE file for copyright, and full details.

