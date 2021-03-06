# CentOS Linux 7 VM Baseline Hardening

 A collection of scripts that will help to harden operating system baseline configuration supported by Cloudneeti as defined in CIS CentOS Linux 7 benchmark v2.2.0. This remediates policies, compliance status can be validated for below  [policies listed here.](../../osBaseline/centOSQuickWins/#csbp-centos-linux-7-version-220)

Note: The scripts are designed to harden the operating system baseline configurations, Please test it on the test/staging system before applying to the production system.


| **Configuration script**        | **Number of policies remediated** | **Cloud Account Type**|
|--------------------------------|-------------------------------|-----------------------------------|
| [CentOS Linux 7 VM baseline policies for Cloud Security Best Practices](https://raw.githubusercontent.com/Cloudneeti/os-harderning-scripts/master/CentOS7/Azure_CSBP_CentOS_Linux7_Remediation.sh){target=_blank}             | 26                               | Azure|
| [CentOS Linux 7 VM baseline policies for CIS Benchmark CentOS Linux 7 Version 2.2.0](https://raw.githubusercontent.com/Cloudneeti/os-harderning-scripts/master/CentOS7/CIS_CentOS_Linux7_Benchmark_v2_2_0_Remediation.sh){target=_blank}   | 151                               | Azure|


#### Prerequisites 
The below steps are required for executing script to harden operating system baseline configuration

| Activity             | Description                |
|----------------------|----------------------------|
| 1.	Download and review **Bash script** to harden operating system baseline configuration | The PowerShell script is used to harden operating system baseline configuration: <br> [Azure - CentOS Linux 7 VM baseline policies for CSBP](https://raw.githubusercontent.com/Cloudneeti/os-harderning-scripts/master/CentOS7/Azure_CSBP_CentOS_Linux7_Remediation.sh){target=_blank} <br> [Azure - CentOS Linux 7 VM baseline policies for CIS Benchmark CentOS Linux 7 Version 2.2.0](https://raw.githubusercontent.com/Cloudneeti/os-harderning-scripts/master/CentOS7/CIS_CentOS_Linux7_Benchmark_v2_2_0_Remediation.sh){target=_blank}|

Execute OS Baseline Hardening script
-------------------------------------

### CentOS Linux 7 VM baseline policies for Cloud Security Best Practices

Below steps are performed on Virtual Machine, as a root user

1. Open bash and switch user to root

        sudo su

2. Download script

        wget https://raw.githubusercontent.com/Cloudneeti/os-harderning-scripts/master/CentOS7/Azure_CSBP_CentOS_Linux7_Remediation.sh -O Azure_CSBP_CentOS_Linux7_Remediation.sh

3. Execute the script as a root user  

        bash Azure_CSBP_CentOS_Linux7_Remediation.sh

       ![Compliance score](../../images/osBaselineQuickWIns/CentOS_QuickWins_CSBP.png#thumbnail_1)

4. Script will update baseline configuration to harden operating system.

       ![Compliance score](../../images/osBaselineQuickWIns/CentOS_QuickWins_CSBP_1.png#thumbnail_1)

5. Scan related Cloud Account in Cloudneeti or wait for scheduled scan

6. Verify policy results in CSBP Benchmark on Cloudneeti portal

    ![Compliance score](../../images/osBaselineQuickWIns/CentOS_QuickWins.png#thumbnail_1)

### CentOS Linux 7 VM baseline policies for CIS Benchmark CentOS Linux 7 Version 2.2.0

Below steps are performed on Virtual Machine as a root user

1. Open bash and switch user to root

        sudo su

2. Download script

        wget https://raw.githubusercontent.com/Cloudneeti/os-harderning-scripts/master/CentOS7/CIS_CentOS_Linux7_Benchmark_v2_2_0_Remediation.sh -O CIS_CentOS_Linux7_Benchmark_v2_2_0_Remediation.sh

3. Execute the script as a root user  

        bash CIS_CentOS_Linux7_Benchmark_v2_2_0_Remediation.sh

       ![Compliance score](../../images/osBaselineQuickWIns/CentOS_QuickWins_CIS.png#thumbnail_1)

4. Script will update baseline configuration to harden operating system for 121 policies.

       ![Compliance score](../../images/osBaselineQuickWIns/CentOS_QuickWins_CIS_1.png#thumbnail_1)


## Remediation policy list

### CSBP CentOS Linux 7 Version 2.2.0

| Category Name                                       | Policy Name                                                            |
|-----------------------------------------------------|------------------------------------------------------------------------|
| CentOS 7 - Network Configuration                    | CentOS 7 - Ensure wireless interfaces are disabled                     |
| CentOS 7 - Network Configuration                    | CentOS 7 - Ensure IP forwarding is disabled                            |
| CentOS 7 - Network Configuration                    | CentOS 7 - Ensure broadcast ICMP requests are ignored                  |
| CentOS 7 - Network Configuration                    | CentOS 7 - Ensure bogus ICMP responses are ignored                     |
| CentOS 7 - Network Configuration                    | CentOS 7 - Ensure Reverse Path Filtering is enabled                    |
| CentOS 7 - Network Configuration                    | CentOS 7 - Ensure TCP SYN Cookies is enabled                           |
| CentOS 7 - Logging and Auditing                     | CentOS 7 - Ensure logrotate is configured                              |
| CentOS 7 - Logging and Auditing                     | CentOS 7 - Ensure rsyslog Service is enabled                           |
| CentOS 7 - Logging and Auditing                     | CentOS 7 - Ensure rsyslog or syslog-ng is installed                    |
| CentOS 7 - Initial Setup                            | CentOS 7 - Ensure nodev option set on removable media partitions       |
| CentOS 7 - Initial Setup                            | CentOS 7 - Ensure nosuid option set on removable media partitions      |
| CentOS 7 - Initial Setup                            | CentOS 7 - Ensure noexec option set on removable media partitions      |
| CentOS 7 - Initial Setup                            | CentOS 7 - Ensure XD/NX support is enabled                             |
| CentOS 7 - Initial Setup                            | CentOS 7 - Ensure address space layout randomization (ASLR) is enabled |
| CentOS 7 - Services                                 | CentOS 7 - Ensure rsh server is not enabled                            |
| CentOS 7 - Services                                 | CentOS 7 - Ensure telnet server is not enabled                         |
| CentOS 7 - Services                                 | CentOS 7 - Ensure Avahi Server is not enabled                          |
| CentOS 7 - Services                                 | CentOS 7 - Ensure CUPS is not enabled                                  |
| CentOS 7 - Services                                 | CentOS 7 - Ensure DHCP Server is not enabled                           |
| CentOS 7 - Services                                 | CentOS 7 - Ensure rsh client is not installed                          |
| CentOS 7 - Services                                 | CentOS 7 - Ensure telnet client is not installed                       |
| CentOS 7 - Access, Authentication and Authorization | CentOS 7 - Ensure cron daemon is enabled                               |
| CentOS 7 - Access, Authentication and Authorization | CentOS 7 - Ensure SSH PermitUserEnvironment is disabled                |
| CentOS 7 - System Maintenance                       | CentOS 7 - Ensure permissions on /etc/passwd are configured            |
| CentOS 7 - System Maintenance                       | CentOS 7 - Ensure permissions on /etc/group are configured             |
| CentOS 7 - System Maintenance                       | CentOS 7 - Ensure root is the only UID 0 account                       |
