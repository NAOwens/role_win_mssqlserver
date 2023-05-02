role_win_mssqlserver 
=========

This role uses a template to build a file with the information needed to install MS SQL Server on Windows. The template file is placed on the server as SQLServer.ini and that file is used by the SQL Server install file to install SQL Server.

Requirements
------------

Template file - SQLServer.ini.j2

Role Variables
--------------

Dependencies
------------

Example Playbook
----------------
- name: Create a dba group
  ansible.windows.win_group:
    name: "{{ dba_local_group }}"
    description: Local DBA Group
    state: present

- name: Create db users 
  ansible.windows.win_user:
    name: "{{ item }}"
    password: "{{ db_pw }}"
    state: present
    groups: 
      - Administrators
      - "{{ dba_local_group }}"
  loop: "{{ db_admins }}"

- name: Add domain users to local Admin group - skip
  win_group_membership:
    name: Administrators
    members: '{{ db_admins }}'
    state: present

- name: Add the SQL engine account to Lock pages in memory
  ansible.windows.win_user_right:
    name: SeLockMemoryPrivilege
    users:
    - '{{ db_engine_user }}'
    action: add

- name: Add the SQL engine account to Perform volume maintenance task
  ansible.windows.win_user_right:
    name: SeManageVolumePrivilege
    users:
    - '{{ db_engine_user }}'
    action: add

License
-------

GPL

Author Information
------------------

Norman Owens
Norman.Owens@redhat.com