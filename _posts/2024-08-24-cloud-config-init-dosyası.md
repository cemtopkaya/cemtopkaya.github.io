---
layout: post
title:  "cloud-[config|init] Dosyası"
date:   2024-08-24 11:57:24 +0000
categories: cloud-init openstack ubuntu
tags: cloud-init openstack ubuntu
---
Openstack üzerinde VM oluştururken aşağıdaki ekran görüntüsünde olduğu gibi sunucu açıldıktan sonra çalıştırılmak üzere YAML biçiminde ayarları içeren bir metin gireriz. İşte bu metnin doğru olup olmadığını görmek için VM’in oluşturulup, günlüklerine bakmamıza gerek yok.

`sudo apt install cloud-init` kurulumunu yaptığınızda aşağıdaki komutları kullanarak oluşturduğunuz metnin geçerli olup olmadığını görebilirsiniz.

Eğer `cloud-init --version` çıktısı 22 sürümünden eskiyse `devel schema` değilse `schema` anahtarını kullanarak doğrulayabilirsiniz. Hatayı daha ayrıntılı görmek için `--annotate` anahtarını kullanabilirsiniz.

cloud-init devel schema --config-file ./cloud.txt

cloud-init schema --config-file ./cc1.txt --annotate

cloud-init devel schema --config-file ./cloud.txt --annotate

Daha hızlı çalıştırmak için fetch ve curl halinde VM oluşturma isteğini bir kenara aldım ki ileride tekrar WEB GUI’nin tıklamalarıyla zaman kaybetmeyeyim:

```
fetch("http://192.168.11.102/horizon/api/nova/servers/", {
  "headers": {
    "accept": "application/json, text/plain, */*",
    "accept-language": "tr-TR,tr;q=0.9,en-US;q=0.8,en;q=0.7",
    "cache-control": "no-cache",
    "content-type": "application/json;charset=UTF-8",
    "pragma": "no-cache",
    "x-csrftoken": "EbtKIYnqiEeecPbRdnLCdf1QAuSJZwtQ",
    "x-requested-with": "XMLHttpRequest"
  },
  "referrer": "http://192.168.11.102/horizon/project/instances/",
  "referrerPolicy": "strict-origin-when-cross-origin",
  "body": "{\"availability_zone\":\"\",\"config_drive\":false,\"user_data\":\"#cloud-config\\nchpasswd:\\n    expire: false\\nhostname: \\\"EPC-1\\\"\\nmanage_etc_hosts: true\\npassword: \\\"123\\\"\\nssh_pwauth: true\",\"disk_config\":\"AUTO\",\"instance_count\":1,\"name\":\"EPC-1\",\"profile\":{},\"scheduler_hints\":{},\"security_groups\":[\"e3b6e34a-6dc5-4c1f-af15-89e0e8d8c711\"],\"create_volume_default\":true,\"source_id\":\"98affb1d-a1be-47c8-a441-17d13facc317\",\"flavor_id\":\"d2e84f33-61eb-4849-bc38-178b9a1c2160\",\"nics\":[{\"net-id\":\"42486caa-22bf-4b34-9eac-09556ebc9054\",\"v4-fixed-ip\":\"\"}],\"key_name\":\"cem-ubuntu-20-ssh\"}",
  "method": "POST",
  "mode": "cors",
  "credentials": "include"
});
```


```
curl 'http://192.168.11.102/horizon/api/nova/servers/' \
  -H 'Accept: application/json, text/plain, */*' \
  -H 'Accept-Language: tr-TR,tr;q=0.9,en-US;q=0.8,en;q=0.7' \
  -H 'Cache-Control: no-cache' \
  -H 'Connection: keep-alive' \
  -H 'Content-Type: application/json;charset=UTF-8' \
  -H 'Cookie: login_region="http://controller:5000/v3"; login_domain=default; sessionid=u3xcge6fryfb9ridt1ml1tszifzj61z6; csrftoken=EbtKIYnqiEeecPbRdnLCdf1QAuSJZwtQ; recent_project=caf3d7f388d14f44962071af1e3c8703' \
  -H 'Origin: http://192.168.11.102' \
  -H 'Pragma: no-cache' \
  -H 'Referer: http://192.168.11.102/horizon/project/instances/' \
  -H 'User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/127.0.0.0 Safari/537.36' \
  -H 'X-CSRFToken: EbtKIYnqiEeecPbRdnLCdf1QAuSJZwtQ' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '{"availability_zone":"","config_drive":false,"user_data":"#cloud-config\nchpasswd:\n    expire: false\nhostname: \"EPC-1\"\nmanage_etc_hosts: true\npassword: \"123\"\nssh_pwauth: true","disk_config":"AUTO","instance_count":1,"name":"EPC-1","profile":{},"scheduler_hints":{},"security_groups":["e3b6e34a-6dc5-4c1f-af15-89e0e8d8c711"],"create_volume_default":true,"source_id":"98affb1d-a1be-47c8-a441-17d13facc317","flavor_id":"d2e84f33-61eb-4849-bc38-178b9a1c2160","nics":[{"net-id":"42486caa-22bf-4b34-9eac-09556ebc9054","v4-fixed-ip":""}],"key_name":"cem-ubuntu-20-ssh"}' \
  --insecure
```


```
#cloud-config
# Add groups to the system
# The following example adds the 'admingroup' group with members 'root' and 'sys'
# and the empty group cloud-users.
groups:
  - admingroup: [root,sys]
  - cloud-users
# Add users to the system. Users are added after groups are added.
# Note: Most of these configuration options will not be honored if the user
#       already exists. Following options are the exceptions and they are
#       applicable on already-existing users:
#       - 'plain_text_passwd', 'hashed_passwd', 'lock_passwd', 'sudo',
#         'ssh_authorized_keys', 'ssh_redirect_user'.
users:
  - default
  - name: foobar
    gecos: Foo B. Bar
    primary_group: foobar
    groups: users
    selinux_user: staff_u
    expiredate: '2032-09-01'
    ssh_import_id:
      - lp:falcojr
      - gh:TheRealFalcon
    lock_passwd: false
    passwd: $6$j212wezy$7H/1LT4f9/N3wpgNunhsIqtMj62OKiS3nyNwuizouQc3u7MbYCarYeAHWYPYb2FT.lbioDm2RrkJPb9BZMN1O/
  - name: barfoo
    gecos: Bar B. Foo
    sudo: ALL=(ALL) NOPASSWD:ALL
    groups: users, admin
    ssh_import_id:
      - lp:falcojr
      - gh:TheRealFalcon
    lock_passwd: true
    ssh_authorized_keys:
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDSL7uWGj8cgWyIOaspgKdVy0cKJ+UTjfv7jBOjG2H/GN8bJVXy72XAvnhM0dUM+CCs8FOf0YlPX+Frvz2hKInrmRhZVwRSL129PasD12MlI3l44u6IwS1o/W86Q+tkQYEljtqDOo0a+cOsaZkvUNzUyEXUwz/lmYa6G4hMKZH4NBj7nbAAF96wsMCoyNwbWryBnDYUr6wMbjRR1J9Pw7Xh7WRC73wy4Va2YuOgbD3V/5ZrFPLbWZW/7TFXVrql04QVbyei4aiFR5n//GvoqwQDNe58LmbzX/xvxyKJYdny2zXmdAhMxbrpFQsfpkJ9E/H5w0yOdSvnWbUoG5xNGoOB csmith@fringe
  - name: testuser
    gecos: Mr. Test
    homedir: /local/testdir
    sudo: ["ALL=(ALL) NOPASSWD:ALL"]
  - name: cloudy
    gecos: Magic Cloud App Daemon User
    inactive: '5'
    system: true
  - name: fizzbuzz
    sudo: false
    shell: /bin/bash
    ssh_authorized_keys:
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDSL7uWGj8cgWyIOaspgKdVy0cKJ+UTjfv7jBOjG2H/GN8bJVXy72XAvnhM0dUM+CCs8FOf0YlPX+Frvz2hKInrmRhZVwRSL129PasD12MlI3l44u6IwS1o/W86Q+tkQYEljtqDOo0a+cOsaZkvUNzUyEXUwz/lmYa6G4hMKZH4NBj7nbAAF96wsMCoyNwbWryBnDYUr6wMbjRR1J9Pw7Xh7WRC73wy4Va2YuOgbD3V/5ZrFPLbWZW/7TFXVrql04QVbyei4aiFR5n//GvoqwQDNe58LmbzX/xvxyKJYdny2zXmdAhMxbrpFQsfpkJ9E/H5w0yOdSvnWbUoG5xNGoOB csmith@fringe
  - snapuser: joe@joeuser.io
  - name: nosshlogins
    ssh_redirect_user: true
# Valid Values:
#   name: The user's login name
#   expiredate: Date on which the user's account will be disabled.
#   gecos: The user name's real name, i.e. "Bob B. Smith"
#   homedir: Optional. Set to the local path you want to use. Defaults to
#           /home/<username>
#   primary_group: define the primary group. Defaults to a new group created
#           named after the user.
#   groups:  Optional. Additional groups to add the user to. Defaults to none
#   selinux_user:  Optional. The SELinux user for the user's login, such as
#           "staff_u". When this is omitted the system will select the default
#           SELinux user.
#   lock_passwd: Defaults to true. Lock the password to disable password login
#   inactive: Number of days after password expires until account is disabled
#   passwd: The hash -- not the password itself -- of the password you want
#           to use for this user. You can generate a hash via:
#               mkpasswd --method=SHA-512 --rounds=4096
#           (the above command would create from stdin an SHA-512 password hash
#           with 4096 salt rounds)
#
#           Please note: while the use of a hashed password is better than
#               plain text, the use of this feature is not ideal. Also,
#               using a high number of salting rounds will help, but it should
#               not be relied upon.
#
#               To highlight this risk, running John the Ripper against the
#               example hash above, with a readily available wordlist, revealed
#               the true password in 12 seconds on a i7-2620QM.
#
#               In other words, this feature is a potential security risk and is
#               provided for your convenience only. If you do not fully trust the
#               medium over which your cloud-config will be transmitted, then you
#               should not use this feature.
#
#   no_create_home: When set to true, do not create home directory.
#   no_user_group: When set to true, do not create a group named after the user.
#   no_log_init: When set to true, do not initialize lastlog and faillog database.
#   ssh_import_id: Optional. Import SSH ids
#   ssh_authorized_keys: Optional. [list] Add keys to user's authorized keys file
#                        An error will be raised if no_create_home or system is
#                        also set.
#   ssh_redirect_user: Optional. [bool] Set true to block ssh logins for cloud
#       ssh public keys and emit a message redirecting logins to
#       use <default_username> instead. This option only disables cloud
#       provided public-keys. An error will be raised if ssh_authorized_keys
#       or ssh_import_id is provided for the same user.
#
#   sudo: Defaults to none. Accepts a sudo rule string, a list of sudo rule
#         strings or False to explicitly deny sudo usage. Examples:
#
#         Allow a user unrestricted sudo access.
#             sudo:  ALL=(ALL) NOPASSWD:ALL
#                       or
#             sudo: ["ALL=(ALL) NOPASSWD:ALL"]
#
#         Adding multiple sudo rule strings.
#             sudo:
#               - ALL=(ALL) NOPASSWD:/bin/mysql
#               - ALL=(ALL) ALL
#
#         Prevent sudo access for a user.
#             sudo: False
#
#         Note: Please double check your syntax and make sure it is valid.
#               cloud-init does not parse/check the syntax of the sudo
#               directive.
#   system: Create the user as a system user. This means no home directory.
#   snapuser: Create a Snappy (Ubuntu-Core) user via the snap create-user
#             command available on Ubuntu systems.  If the user has an account
#             on the Ubuntu SSO, specifying the email will allow snap to
#             request a username and any public ssh keys and will import
#             these into the system with username specified by SSO account.
#             If 'username' is not set in SSO, then username will be the
#             shortname before the email domain.
#
# Default user creation:
#
# Unless you define users, you will get a 'ubuntu' user on Ubuntu systems with the
# legacy permission (no password sudo, locked user, etc). If however, you want
# to have the 'ubuntu' user in addition to other users, you need to instruct
# cloud-init that you also want the default user. To do this use the following
# syntax:
#   users:
#     - default
#     - bob
#     - ....
#  foobar: ...
#
# users[0] (the first user in users) overrides the user directive.
#
# The 'default' user above references the distro's config set in
# /etc/cloud/cloud.cfg.
```


passwd ile şifrenin hash halini girerek

mkpasswd --method=SHA-512 --rounds=4096

```
fetch("http://192.168.11.102/horizon/api/nova/servers/", {
  "headers": {
    "accept": "application/json, text/plain, */*",
    "accept-language": "tr-TR,tr;q=0.9,en-US;q=0.8,en;q=0.7",
    "cache-control": "no-cache",
    "content-type": "application/json;charset=UTF-8",
    "pragma": "no-cache",
    "x-csrftoken": "m0RP3L43xUkxvWvE3GtBtmYUQARcKaZR",
    "x-requested-with": "XMLHttpRequest"
  },
  "referrer": "http://192.168.11.102/horizon/project/instances/",
  "referrerPolicy": "strict-origin-when-cross-origin",
  "body": "{\"availability_zone\":\"\",\"config_drive\":false,\"user_data\":\"#cloud-config\\nchpasswd:\\n  expire: false\\nhostname: EPC-1\\nmanage_etc_hosts: true\\nssh_pwauth: true\\nusers:\\n  - name: tester\\n    gecos: Mr. Test\\n    passwd: testpassword123\\n    sudo: ['ALL=(ALL) NOPASSWD:ALL']\\n    shell: /bin/bash\",\"disk_config\":\"AUTO\",\"instance_count\":1,\"name\":\"EPC-1\",\"profile\":{},\"scheduler_hints\":{},\"security_groups\":[\"e3b6e34a-6dc5-4c1f-af15-89e0e8d8c711\"],\"create_volume_default\":true,\"source_id\":\"98affb1d-a1be-47c8-a441-17d13facc317\",\"flavor_id\":\"d2e84f33-61eb-4849-bc38-178b9a1c2160\",\"nics\":[{\"net-id\":\"42486caa-22bf-4b34-9eac-09556ebc9054\",\"v4-fixed-ip\":\"\"}],\"key_name\":\"cem-ubuntu-20-ssh\"}",
  "method": "POST",
  "mode": "cors",
  "credentials": "include"
});
```


Ayrıca tüm ayar dosyasının etkilerini görmek için WSL içinde, [QEMU](https://cloudinit.readthedocs.io/en/latest/howto/run_cloud_init_locally.html#run-with-qemu) ile yeni bir sanal makinede, [Multipass](https://cloudinit.readthedocs.io/en/latest/howto/run_cloud_init_locally.html#multipass) ile bir sanal makinede yahut daha hızlı bir yöntem olan [LXD](https://cloudinit.readthedocs.io/en/latest/howto/run_cloud_init_locally.html#run-with-lxd)’yi kullanabilirisiniz.

```
#!/bin/bash
# LXD yükleyicisini kur
sudo apt install lxd-installer
# LXD'yi minimal yapılandırma ile başlat
lxd init --minimal
# Cloud-init için kullanıcı verisi (user-data) dosyası oluştur
cat << EOF > /tmp/my-user-data
#cloud-config
runcmd:
  - echo 'Hello, World!' > /var/tmp/hello-world.txt
EOF
# Ubuntu 20.04 (Focal Fossa) tabanlı bir LXD konteyner başlat
# Oluşturduğumuz user-data dosyasını konteyner yapılandırmasına ekle
lxc launch ubuntu:focal my-test --config=user.user-data="$(cat /tmp/my-user-data)"
# Konteynere bağlan
echo "Konteynere bağlanılıyor. Cloud-init'in tamamlanması bekleniyor..."
lxc shell my-test
# Cloud-init'in tamamlanmasını bekle (çalışması devam ediyorsa diye). 
# Ekranda "done" yazınca tamamlanmış demektir.
echo "Cloud-init durumu kontrol ediliyor..."
cloud-init status --wait
# Durum çıktısını daha uzun görebiliriz: cloud-init status --long
# Ayrıca günlüklerini de okuyabilirsiniz: /var/log/cloud-init.log
# Cloud-init tamamlandığında, bu mesaj görünecek
echo "Cloud-init tamamlandı. User-data içeriğini kontrol edelim:"
# Konteyner içinde uygulanan user-data içeriğini göster
cloud-init query userdata
# Cloud-init ayrıca, cloud-init'in kullanıcı yapılandırmasında açıklanan görevleri 
# tamamlayamadığı durumlarda da raporlama yeteneğine sahiptir. 
# Cloud-init çalışırken sorunlarla karşılaşırsa genişletilmiş durum bilgisinde, 
# "bozulmuş (degraded)" kelimesini içerecektir.
cloud-init status --format json
# Bildirilen tüm olası durumların listesi (https://cloudinit.readthedocs.io/en/latest/howto/status.html):
# "not started"
# "running"
# "done"
# "error - done"
# "error - running"
# "degraded done"
# "degraded running"
# "disabled"
# User-data içindeki komutun sonucunu kontrol et
echo "User-data'da tanımlanan dosyanın içeriği:"
cat /var/tmp/hello-world.txt
# cloud-init Günlüklerini açalım ve sonrasında analiz ettirelim
# Konteynerden çık
echo "Konteynerden çıkılıyor... Ctrl+D ile de çıkabilirsiniz..."
exit
# Konteyneri sil
echo "Konteyner siliniyor..."
lxc delete -f my-test
echo "İşlem tamamlandı."
```


cloud-init status --long

cloud-init status --format json

Kaynaklar:

*   [https://cloudinit.readthedocs.io/en/latest/howto/debug\_user\_data.html](https://cloudinit.readthedocs.io/en/latest/howto/debug_user_data.html)
*   [https://cloudinit.readthedocs.io/en/latest/reference/examples.html](https://cloudinit.readthedocs.io/en/latest/reference/examples.html)
*   [https://cloudinit.readthedocs.io/en/latest/howto/debugging.html](https://cloudinit.readthedocs.io/en/latest/howto/debugging.html)