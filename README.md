
williamyeh.oracle-java for Ansible Galaxy
============

[![Build Status](https://travis-ci.org/William-Yeh/ansible-oracle-java.svg?branch=master)](https://travis-ci.org/William-Yeh/ansible-oracle-java) [![Circle CI](https://circleci.com/gh/William-Yeh/ansible-oracle-java.svg?style=shield)](https://circleci.com/gh/William-Yeh/ansible-oracle-java)

## Summary

Role name in Ansible Galaxy: **[williamyeh.oracle-java](https://galaxy.ansible.com/williamyeh/oracle-java/)**

This Ansible role has the following features for Oracle JDK:

 - Install JDK 7 or 8 version.
 - Install optional Java Cryptography Extensions (JCE)
 - Install for CentOS, Debian/Ubuntu, SUSE, and Mac OS X families.

If you prefer OpenJDK, try alternatives such as [geerlingguy.java](https://galaxy.ansible.com/geerlingguy/java/) or [smola.java](https://galaxy.ansible.com/smola/java/).


## Role Variables

### Mandatory variables

None.

### Optional variables


User-configurable defaults:

```yaml
# which version?
java_version: 8

# which subversion?
java_subversion: 111

# which directory to put the download file?
java_download_path: /tmp

# rpm/tar.gz file location:
#   - true: download from Oracle on-the-fly;
#   - false: copy from `{{ playbook_dir }}/files` on the control machine.
java_download_from_oracle: true

# remove temporary downloaded/copied files?
java_remove_download: true

# set $JAVA_HOME?
java_set_javahome: false

# install JCE?
java_install_jce: false
```

For other configurable internals, read `tasks/set-role-variables.yml` file; for example, supported `java_version`/`java_subversion` combinations.

If you want to install a Java release which is not supported out-of-the-box, you have to specify the corresponding Java build number in the variable `java_build` in addition to `java_version` and `java_subversion`, e.g.
```yaml
---
- hosts: all

  roles:
    - gikoluo.oracle-java

  vars:
    java_version: 8
    java_subversion: 111
    java_build: 14
```


### Customized variables, if absolutely necessary

If you have a pre-downloaded `jdk_tarball_file` whose filename cannot be inferred successfully by `tasks/set-role-variables.yml`, you may specify it explicitly: 

```yaml
# Specify the pre-fetch filename (without tailing .tar.gz or .rpm or .dmg);
# used in conjunction with `java_download_from_oracle: false`.

jdk_tarball_file

# For example, if you have a `files/jdk-7u79-linux-x64.tar.gz` locally,
# put it on the playbook files directory, not this role files directory.
# and specify the following variables in your playbook:
#
#    java_version:    7
#    java_subversion: 79
#    java_download_from_oracle: false
#    jdk_tarball_file: jdk-7u79-linux-x64
#
```


## Usage


### Step 1: add role

Add role name `gikoluo.oracle-java` to your playbook file.
```
ansible-galaxy install gikoluo.oracle-java --roles-path=./roles
```


### Step 2: pre-fetch .rpm and .tar.gz files

For some reasons, you may want to pre-fetch .rpm and .tar.gz files *before the execution of this role*, instead of downloading from Oracle on-the-fly.

To do this, put the file on the `{{ playbook_dir }}/files` directory in advance, and then set the `java_download_from_oracle` variable to `false`:

```yaml
---
# file: prefetch-playbook.yml

- hosts: all

  roles:
    - gikoluo.oracle-java

  vars:
    java_version: 8
    java_download_from_oracle: false
```


### Step 3: add variables to use local file

```yaml
---
- hosts: "{{ hosts|default('jdk8') }}"
  become: True
  become_user: root
  roles:
    - gikoluo.oracle-java
  vars:
    java_version: 8
    java_subversion: 111
    java_download_from_oracle: false
    jdk_tarball_file: jdk-{{java_version}}u{{java_subversion}}-linux-x64
    java_install_dir: "/usr/lib/jvm/java-1.8.0/default"
    java_default_link_name: default
```

### (Options): The simplest way. 
### If you like to install on-the-fly, you can ignore the step 1-3
### and create this simplest playbook example, then RUN it!

```yaml
---
# file: simple-playbook.yml

- hosts: all

  roles:
    - gikoluo.oracle-java

  vars:
    java_version: 8
```





## Dependencies


## License

Licensed under the Apache License V2.0. See the [LICENSE file](LICENSE) for details.


## History

Rewritten from my pre-Galaxy version: [server-config-template](https://github.com/William-Yeh/server-config-template).
