# eset_efs

Ansible role to install ESET Server Sercurity

## Example playbook


```yaml
#!/usr/bin/env ansible-playbook
---

- name: ESET Antivirus
  hosts: all
  become: true
  gather_facts: true

  tasks:
    - name: Set proxy environment
      ansible.builtin.set_fact:
        proxy_env:
          HTTPS_PROXY: "{{ https_proxy | default(omit) }}"
          PROXY_USER: "{{ proxy_user | default(omit) }}"
          PROXY_PASSWORD: "{{ proxy_password | default(omit) }}"
      no_log: true

    - name: Update all packages
      ansible.builtin.yum:
        name: '*'
        state: latest  # noqa: package-latest
        update_cache: true

    - name: Check if reboot_required
      ansible.builtin.command:
        cmd: "needs-restarting -r"
      changed_when: false
      failed_when: reboot_required.rc != 0 and reboot_required.rc != 1
      register: reboot_required

    - name: Reboot if required
      when: reboot_required.rc == 1
      ansible.builtin.reboot:

    - name: Install ESET Antivirus
      ansible.builtin.include_role:
        name: eset_efs
...
```
