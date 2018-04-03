# ansible_sosreport

### What is it?
These playbooks allow you to run sosreports on defined inventory and optionally upload the reports to a specified Red Hat support case.

### Variables to define

1) **case_id**: Red Hat support case number
2) **run_id**: Run number (you make this up and iterate as additional runs for the same case are executed)
3) **rhn_user**: Red Hat Customer Portal user (required to upload reports to case)
4) **rhn_pass**: Red Hat Customer Portal password (required to upload reports to case)

**NOTE:** On the initial run of `redhat-support-tool`, you are prompted to enter your Red Hat Customer Portal user id and password and asked if you would like to save it to ~/.redhat-support-tool/redhat-support-tool.conf. If this file is available and populated correctly on a node, the sosreport_upload playbook will attempt to use that file for authentication and specifying the `{{ rhn_user }}` and `{{ rhn_pass }}` variables explicitly is not necessary. It is advised to have use the redhat-support-tool.conf method as the password is stored in a hashed form within the file and safer than passing rhn_pass as a variable on the command line.

### Examples

Run sosreports on specified inventory and fetch reports to cetralized location:
- `$ ansible-playbook sosreport.yml -e case_id=01234567 -e run_id=1`

Same as above, but upload to specified Red Hat support case (using file-based authentication):
- `$ ansible-playbook sosreport.yml sosreport_upload.yml -e case_id=01234567 -e run_id=1`

Same as above, but specify Red Hat Customer Portal username and password variables for upload purposes:
- `$ ansible-playbook sosreport.yml -e case_id=01234567 -e run_id=1 -e rhn_user=username -e rhn_pass=secret`

### TODO

- Make the sosreport destination root path a variable - maybe: `{{ sos_dest_root }}`
- Add an encrypted secrets file (Ansible Vault?) to pass Red Hat Customer Portal passwords securely.
- Other stuff
