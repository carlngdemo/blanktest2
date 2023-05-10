# disk_cleanup_tools
## Run cleanup tools (commands)
Supported OS's/Distributions:
  - Windows 2008R2 and later
  - RHEL 6/7/8/9

Requirements:
  - Create a playbook to call this role.
  - Include the role in roles/requirements.yml. See usage.
  - Provide an inventory of hosts.
  - Provide list of cleanup tools (commands) to run.
  - Optionally, provide email address(es) to receive activity report.

# Usage
```
# roles/requirements.yml
# either, if using master/main
- name: disk_cleanup_tools
  src: https://github.com/carlngdemo/disk_cleanup_tools.git
  scm: git
  version: origin/main
# or, if using branch
- name: disk_cleanup_tools
  src: https://github.com/carlngdemo/some_repo.git
  scm: git
  version: origin/disk_cleanup_tools


# playbook.yml
- ansible.builtin.include_role:
    name: disk_cleanup_tools
```

# Parameters
-------
| Variables | Required | Default | Comments |
| :---: | :---: | :---: | :---: |
| MAIL_TO | No | None | Email address(es) to receive activity report. Either a simple variable with one address, or a list with one or more addresses |
|a_lin_cleanup_tools_local (Linux) a_win_cleanup_tools_local (Windows) | No | [] | Tools that know how to cleanup and what to delete |

# Examples
## MAIL_TO
```
#if specified/defined, send email report
#Optional
#Default: none
#Examples
MAIL_TO:
  - '{{ tower_user_email | default("no_such_user@please-use-valid-email-address-{{ random_string }}.com") }}'
  - some_user@please-use-valid-email-address-{{ random_string }}.com
  - some_more_user@please-use-valid-email-address-{{ random_string }}.com
MAIL_TO: '{{ tower_user_email | default("no_such_user@please-use-valid-email-address-{{ random_string }}.com") }}'
MAIL_TO: 'no_such_user@please-use-valid-email-address-{{ random_string }}.com"'
MAIL_TO: some_user@please-use-valid-email-address-{{ random_string }}.com
```


## a_lin_cleanup_tools_local (Linux)
## a_win_cleanup_tools_local (Windows)
```
#Optional
#Default: []
#Examples

##
## WARNING: This runs arbitrary commands
##
## Tools that know how to cleanup and what to delete
## cmd is required
## arg(ument) is optional
a_lin_cleanup_tools_local:
  - cmd: nosuch_a_cleanuptool
    #arg:
  - cmd: hostname
    #arg:
  - cmd: date
    arg: +%Y%m%d_%H%M%S
  ## Find files that are older than 366 days and match patter "nosuch_file*", then delete
  - cmd: find /nosuch_a_dir_to_find -mtime +366 -name "nosuch_file*" -exec rm {} \;
    #arg:
  - cmd: find /var/log -mtime +3660 -name "spooler-*" | wc
    #arg:
  ## Delete 10 year old "spooler-*" log files
  - cmd: find /var/log -mtime +3660 -name "spooler-*" -exec rm {} \;
    #arg:
a_win_cleanup_tools_local:
  - cmd: nosuch_a_cleanuptool
    #arg:
  - cmd: hostname
    #arg:
  - cmd: Get-Date
    arg: ""
  ## Find files that are older than 366 days and match patter "nosuch_file*", then delete
  - cmd: (Get-ChildItem -Path C:\nosuch_a_dir_to_find\ -Filter nosuch_file* | ? {$_.LastWriteTime -lt (Get-Date).AddDays(-366) -and -not $_.PSIsContainer} | Remove-Item | Out-String).trim()
    #arg:
  - cmd: (Get-ChildItem C:\Windows\Temp\ -Filter vmware-vmusr-*.log | ? {$_.LastWriteTime -lt (Get-Date).AddDays(-3660) -and -not $_.PSIsContainer} | Measure-Object).Count
    #arg:
  ## Delete 10 year old "-vmware-vmusr*" log files
  - cmd: (Get-ChildItem C:\Windows\Temp\ -Filter vmware-vmusr-*.log | ? {$_.LastWriteTime -lt (Get-Date).AddDays(-3660) -and -not $_.PSIsContainer} | Remove-Item | Out-String).trim()
    #arg:
```

