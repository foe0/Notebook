

Jan 21 11:23:29 localhost su[28722]: pam_unix(su:auth): authentication failure; logname=ubuntu uid=500 euid=0 tty=/dev/pts/1 ruser=ubuntu rhost=  user=root
Jan 21 11:23:32 localhost su[28722]: pam_authenticate: Authentication failure
Jan 21 11:23:32 localhost su[28722]: FAILED su for root by ubuntu
Jan 21 11:23:32 localhost su[28722]: - /dev/pts/1 ubuntu:root





ubuntu18

 普通用户切换root失败记录

cat /var/log/auth.log｜grep su



redhat enterprise linux 7

时间 priority=150，severity_label=informational，logsource=DVCCXT-IPLY733,facility_label=local2,program=audispd,logtypename=linux_syslog_rsylog,facility=19,message=node=DVCCXT-IPLY733 type=user_CHAUTHTPOK msg=audit(1611196942.104:2898):pid=6060 uid=1000 auid=1000 set=3 msg='op=PAM:chauthtok grantors=? acct=\"root\" exe=\"/usr/bin.su\" hostname=DVCCXT-IPLY733 addr=? terminal=pts/0 res=failed\',"host"='10.35.25.82',"security=6}"}}



nmap -p 873 --script rsync-brute --script-args 'rsync-brute.module=www' 192.168.1.4 　　破解rsync 



验证NFS未授权漏洞：showmount -e ip




