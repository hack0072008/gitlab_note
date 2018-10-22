# rpm指定版本包下载：
    http://mirrors.zju.edu.cn/gitlab-ce/yum/el7/



# rpm版本包下载：
    wget http://mirrors.zju.edu.cn/gitlab-ce/yum/el7/gitlab-ce-8.17.3-ce.0.el7.x86_64.rpm

# rpm包安装：
    rpm -ivh gitlab-ce-8.17.3-ce.0.el7.x86_64.rpm

# rpm包卸载：
    rpm -e gitlab-ce
    or
    yum erase gitlab-ce
    
# gitlab版本升级：
    wget http://mirrors.zju.edu.cn/gitlab-ce/yum/el7/gitlab-ce-11.3.6-ce.0.el7.x86_64.rpm   (latest)
    rpm -Uvh gitlab-ce-10.0.4-ce.0.el7.x86_64.rpm
    注意：
         官方升级策略:
          8.13.4 -> 8.17.7 -> 9.5.10 -> 10.8.7 -> 11.3.4
          https://docs.gitlab.com/ee/policy/maintenance.html#upgrade-recommendations
          
         升级后500错误 和 "no implicit conversion of nil into String":
          https://gitlab.com/gitlab-org/omnibus-gitlab/issues/3189

# gitlab-ctl reconfigure 假死，解决方法：
    ctrl + C
    systemctl restart gitlab-runsvdir
    gitlab-ctl reconfigure
    参考--https://gitlab.com/gitlab-org/omnibus-gitlab/issues/160



# 启动后502错误：
    配置中 nginx 和 unicorn 的端口冲突导致(nginx默认为80，unicorn默认为8080)
    1、权限问题:chmod -R 755 /var/log/gitlab
    2、Gitlab的默认启动端口是80,8080
       external_url 'http://localhost:8888' #指定访问端口，默认是80
       unicorn['listen'] = '127.0.0.1'
       unicorn['port'] = 8001    # 为unicorn worker的工作端口，默认为8080，如果你的8080端口被占用的，这一项需要更改。
    3、内存不足的问题
       安装gitlab的时候，已经说明你的空余内存需要有4G左右的内存，所以在安装gitlab的时候，请给足内存，在安装。
    注：参考 http://www.mamicode.com/info-detail-2317465.html




# 恢复前需：
    gitlab-ctl stop unicorn
    gitlab-ctl stop sidekiq

# 恢复：
    gitlab-rake gitlab:backup:restore BACKUP=1538267907_2018_09_30
    注意：gitlab_rails['backup_path']下有 1538267907_2018_09_30_gitlab_backup.tar 文件。
         恢复后记得删除 git_data_dirs 下的的 repositories.old.xxxx目录，恢复一次就生成一次以前的备份，空间很大


# 无提示恢复(无效果)：
    gitlab-rake gitlab:backup:restore BACKUP=1538267907_2018_09_30 -s


# 恢复备份时的权限错误：
    可以用:gitlab-rake gitlab:check检查
    A.一般是配置gitlab_rails['backup_path'] = "/data1/backups"(/var/opt/gitlab/backups 为默认值)，此处的目录中的第一级目录必须修改权限为git用户(chown -R git /data1/)
    B.仓库的目录也必须是git用户的,查看配置中的git_data_dirs值:
    chown -R git:git /data1/gitlab-data/repositories

# 恢复时备份文件不存在错误：
    备份文件名中除了后半部分 _gitlab_backup.tar 其他的全是输入的时间戳
    -rwx------ 1 git root 9108725760 Oct  8 15:49 1538267907_2018_09_30_gitlab_backup.tar
    [root@ima_sengled source]# gitlab-rake gitlab:backup:restore BACKUP=1538267907_2018_09_30
    ...
    
# 重装清理操作
    gitlab-ctl stop
    yum erase gitlab-ce
    ps -ef|grep gitlab-ctl|awk '{print $2}'|kill -9
    find / -name gitlab    ---->  清除缓存和临时文件
    /etc/gitlab/gitlab.rp 的 git_data_dirs 下清空
    rm -rf /etc/gitlab/gitlab.rb

# 注意：
    恢复后，用户和组全部恢复到备份中的内容。
    恢复后，无法恢复用户的图像信息。


