DNF Repositories
================

Role to manage package repositories of the DNF package manager.

Dependencies
------------

This role depends on collection `community.general`.

Role Variables
--------------

* `dnf_repositories_managed`: Instruct DNF to use the managed repos directory instead of default directory. (`true`)
* `rpm_keys`: List of RPM signing keys to manage the state of.
              Review documentation of module `ansible.builtin.rpm_key` for more details
  * `key` (required): Key that will be modified. Can be a url, a file on the managed node, or a
                      keyid if the key already exists in the database.
  * `state`: If the key will be imported or removed from the rpm db. (`true`)
  * `fingerprint: The long-form fingerprint of the key being imported.
  * `validate_certs:`: If `false' and the `key' is a url starting with https, SSL certificates will not be validated.
* `dnf_repositories`: A list of repositories to ensure on Red Hat systems. Every list item requires
                      at least two embedded dictionaries (`name` and `baseurl` xor `metalink` xor
                      `mirrorlist`). Optional additions are:
  * `description`
  * `enabled`
  * `gpgcheck`
  * `gpgkey`
  * `metadata_expire`
  * `metalink`
  * `repo_gpgcheck`
  * `skip_if_unavailable`
  * `excludepkgs`: list of package specifiers (name or glob) to exclude from this repository.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: rpm_boxes
      vars:
        rpm_keys:
          - key: RPM-GPG-KEY-redhat-release  # as a file in files/ relative to path of the playbook
            state: present
        dnf_repositories:
          - name: rhel-8-for-x86_64-baseos-rpms
            description: RHEL 8 RPM Mirror BaseOS Channel
            baseurl: http://my.repo.server.example/rhel8/baseOS
          - name: rpmfusion-free
            description: RPM Fusion for Fedora $releasever - Free
            metalink: https://mirrors.rpmfusion.org/metalink?repo=free-fedora-$releasever&arch=$basearch
          - name: fedora
            description: Fedora $releasever - $basearch
            # location
            metalink: https://mirrors.fedoraproject.org/metalink?repo=fedora-$releasever&arch=$basearch
            # signature verification
            repo_gpgcheck: false
            gpgcheck: true
            gpgkey: file:///etc/pki/rpm-gpg/RPM-GPG-KEY-fedora-$releasever-$basearch
            # options
            enabled: true
            metadata_expire: 7d
            skip_if_unavailable: false
            excludepkgs:
              - 'java-17-openjdk*'


      roles:
         - role: repositories
           tags: ['packages','repositories','software']

License
-------

EUPL-1.2

Author Information
------------------

- Stefan Joosten <stefan@atcomputing.nl>
