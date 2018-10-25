

#### import
    import gitlab

#### config param
    url = 'http://xxxxxxx'
    token = 'xxxxxxxxxxxxxx'

#### 登录
    gl = gitlab.Gitlab(url, token)

#### 获取第一页project
    projects = gl.projects.list()

#### 获取所有的project
    projects = gl.projects.list(all=True)


#### 获取所有project的name,id
    for p in gl.projects.list(all=True, as_list=False):
        print(p.name, p.id)


#### 获取第一页project的name,id
    for p in gl.projects.list(page=1):
        print(p.name, p.id)

#### 通过指定id 获取 project 对象
    project = gl.projects.get(501)


#### 查找项目
    projects = gl.projects.list(search='keyword')

#### 创建一个项目
    project = gl.projects.create({'name':'project1'})


#### 获取公开的项目
    projects = gl.projects.list(visibility='public')  # public, internal or private


## 获取 project 对象是以下操作的基础

#### 通过指定project对象获取该项目的所有分支
    branches = project.branches.list()
    print(branches)


#### 获取指定分支的属性
    branch = project.branches.get('master')
    print(branch)

#### 创建分支
    branch = project.branches.create({'branch_name': 'feature1',
                                  'ref': 'master'})


#### 删除分支
    project.branches.delete('feature1')

#### 获取指定项目的所有tags
    tags = project.tags.list()

#### 获取某个指定tag 的信息
    tags = project.tags.list('1.0')

#### 创建一个tag
    tag = project.tags.create({'tag_name':'1.0', 'ref':'master'})

#### 设置tags 说明:
    tag.set_release_description('awesome v1.0 release')

#### 删除tags
    project.tags.delete('1.0')
    or
    tag.delete()


#### 获取所有commit info
    commits = project.commits.list()
    for c in commits:
        print(c.author_name, c.message, c.title)


#### 获取指定commit的info
    commit = project.commits.get('e3d5a71b')

#### 创建一个commit
    data = {
        'branch_name': 'master',  # v3
        'commit_message': 'blah blah blah',
        'actions': [
            {
                'action': 'create',
                'file_path': 'blah',
                'content': 'blah'
            }
        ]
    }
    commit = project.commits.create(data)

#### Compare two branches, tags or commits:
    result = project.repository_compare('develop', 'feature-20180104')
    print(result)

#### get the commits
    for commit in result['commits']:
        print(commit)


#### get the diffs
    for file_diff in result['diffs']:
        print(file_diff)

    
#### get the commits
    for commit in result['commits']:
        print(commit)

#### get the diffs
    for file_diff in result['diffs']:
        print(file_diff)
    
## groups/project members's api
    参考：
        https://docs.gitlab.com/ce/api/members.html
    
    
~~待续~~
