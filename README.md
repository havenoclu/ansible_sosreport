# ansible_sosreport

### What is it?

These Ansible playbooks allow you to run sosreports on defined inventory and optionally upload the reports to a specified Red Hat support case. The current focus and testing is performed on major Red Hat Enterprise Linux releases, but should work on other distributions that support `sos` (likely with tweaking based on things like the package manager).

### Variables to define

1) **case_id**: Red Hat support case number
2) **run_id**: Run number (you make this up and iterate as additional runs for the same case are executed)
3) **sos_dest_root**: The directory you wish to copy sosreports to (directories will be created under this root according to specified `case_id` and `run_id`).
4) **rhn_user**: Red Hat Customer Portal user (required to upload reports to case)
5) **rhn_pass**: Red Hat Customer Portal password (required to upload reports to case)

**NOTE:** On the initial run of `redhat-support-tool`, you are prompted to enter your Red Hat Customer Portal user id and password and asked if you would like to save it to ~/.redhat-support-tool/redhat-support-tool.conf. If this file is available and populated correctly on the node set to upload the reports, the sosreport_upload playbook will attempt to use that file for authentication and specifying the `{{ rhn_user }}` and `{{ rhn_pass }}` variables explicitly is not necessary. It is advised to use the redhat-support-tool.conf method as the password is stored in a hashed form within the file and safer than passing `rhn_pass` as a variable on the command line. Another option may be to set the variables in a file and use ansible-vault to secure them.

### Examples

Run sosreports on specified inventory and fetch reports to centralized location:
- `$ ansible-playbook sosreport.yml -e case_id=01234567 -e run_id=1 -e sos_dest_root=/root/sosreports`

Same as above, but upload to specified Red Hat support case (using file-based authentication):
- `$ ansible-playbook sosreport.yml sosreport_upload.yml -e case_id=01234567 -e run_id=1 -e sos_dest_root=/root/sosreports`

Same as above, but specify Red Hat Customer Portal username and password variables for upload purposes:
- `$ ansible-playbook sosreport.yml -e case_id=01234567 -e run_id=1 -e rhn_user=username -e rhn_pass=secret -e sos_dest_root=/root/sosreports`

### Basic Flow

                        +--------------+
                        |              |
                        |              |
                        | Red Hat Case |
                        |              |
                        |              |
                        |              |
                        +------+-------+
                               ^
                               |
                               |3. Upload
                               |Sosreports
                               |
                               |
                        +------+-------+
                        |              |
                        |  Ansible     |
                        |  Controller  |
                        |              |
              +---------+              +--------+
              |         |              |        |
              |         +--+-------+---+        |
              |            ^       ^            |
     1. Run   |            |       |            |1. Run
     Sosreport|            |2. Fetch            |Sosreport
              |            |Sosreports          |
              |            |       |            |
              |            |       |            |
              |            |       |            |
              v            |       |            v
                           |       |
             +-------------++     ++-------------+
             |              |     |              |
             |  Ansible     |     |  Ansible     |
             |  Client      |     |  Client      |
             |              |     |              |
             |              |     |              |
             |              |     |              |
             +--------------+     +--------------+
