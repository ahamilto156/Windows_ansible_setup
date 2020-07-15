# Windows_ansible_setup

## Prerequisites
### Inventory File
e.g. 
[windows]
## This is the windows server
ad1.${GUID}.internal ssh_host=${PUBLIC_ACCESSIBLE_HOSTNAME} ansible_password=${ADMINISTRATOR_PASWORD}

[windows:vars]
ansible_connection=winrm
ansible_user=Administrator
ansible_winrm_server_cert_validation=ignore
ansible_become=false

### Windows Server Provisioning
Windows Remote Management (WinRM) is already configured on the Windows Server 2012 node. It needs to be pre-configured by running the following PowerShell script on the Windows servers:
"<powershell>\n",
"$admin = [adsi]('WinNT://./administrator, user')\n",
"$admin.PSBase.Invoke('SetPassword', '{{ windows_password | default(generated_windows_password) }}')\n",
"$scriptPath=((New-Object System.Net.Webclient).DownloadString('https://raw.githubusercontent.com/ansible/ansible/devel/examples/scripts/ConfigureRemotingForAnsible.ps1'))\n",
"Invoke-Command -ScriptBlock ([scriptblock]::Create($scriptPath)) -ArgumentList '-skipNetworkProfileCheck'\n",
"</powershell>"

## Execution
- To create the users in Windows AD:
   ansible-playbook ad_user_group_create.yml
   
- To install OpenSSH
  ansible-playbook win_ssh_server.yml
  
- To install windows AD
  ansible-playbook setup_ad.yml
