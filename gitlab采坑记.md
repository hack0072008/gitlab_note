# rpm指定版本包下载：
    http://mirrors.zju.edu.cn/gitlab-ce/yum/el7/



# rpm版本包下载：
    wget http://mirrors.zju.edu.cn/gitlab-ce/yum/el7/gitlab-ce-8.17.3-ce.0.el7.x86_64.rpm

# rpm包安装：
    rpm -ivh gitlab-ce-8.17.3-ce.0.el7.x86_64.rpm

# rpm包卸载：
    rpm -e gitlab-ce

# gitlab-ctl reconfigure 假死，解决方法：
    ctrl + C
    systemctl restart gitlab-runsvdir
    gitlab-ctl reconfigure
    参考--https://gitlab.com/gitlab-org/omnibus-gitlab/issues/160



# 启动后502错误：
    配置中 nginx 和 unicorn 的端口冲突导致(nginx默认为80，unicorn默认为8080)




# 恢复前需：
    gitlab-ctl stop unicorn
    gitlab-ctl stop sidekiq

# 恢复：
    gitlab-rake gitlab:backup:restore BACKUP=1538267907_2018_09_30
    注意：gitlab_rails['backup_path']下有 1538267907_2018_09_30_gitlab_backup.tar 文件。


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
    
# 注意：
    恢复后，用户和组全部恢复到备份中的内容。


