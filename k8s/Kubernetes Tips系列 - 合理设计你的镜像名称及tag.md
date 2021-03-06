### 前言
容器化给我们带来很多好处，比如镜像交付的不可变性，交付物的标准化，使得CICD的能力能够进一步提升。合理的设计好镜像名称更加能够在管理镜像及出问题的时候事半功倍。

### 一个栗子
我们使用阿里云的容器镜像服务托管镜像，镜像的名字是这样的格式：registry.cn-qingdao.aliyuncs.com/[namespace]/[imageName]:[buildNumber]-[gitCommitHash]  
#### registry.cn-qingdao.aliyuncs.com  
这部分是描述镜像仓库，没什么可说的
#### imageName  
镜像的名字，以我们的经验是{appName}-{category}-{env}，appName跟category还好理解，一个是应用名字，一个是分类，例如是frontend的http服务还是backend的rpc服务，见名知意，那这个env是什么呢？说好的镜像不可变呢，为啥又跟环境扯上了关系。  
这个就要说下我们的开发流程了，按照标准最简单的CICD流程
> 所有的开发人员从master分支checkout独立的featureBranch，在各自的featureBranch上进行开发，
开完毕后merge到master分支进行构建镜像测试，然后预发，然后发布到线上  

但是现实总是残酷的，我们作为一个创业公司，不可避免的要提高开发效率，多个版本并行，跨迭代测试，功能先测后上是常有的事，那么这个如何解决呢？
经过我们的讨论，我们这样设计的，开发人员在各自的featureBranch上进行开发，开发完毕在DEV环境自测，测试完毕后merge到Test分支，测试环境用Test分支进行测试。  
但是发布的时候就有所不同，发布的话是用master分支进行发布需要上线的功能merge到master分支进行发布，所以测试同学测试的分支未必是发布的分支，这样是否会有问题？其实是有问题的，但是在效率面前，我们承担了风险，为了使风险最低，镜像不会直接上线，而是走预发流程在staging进行基本的验证，重要服务可能还会导入小量的流量验证，真正上线的时候，还有金丝雀发布来进一步保证。我们用master构建出的镜像为了与测试分支区分，就标注了prod字样，意思是线上镜像。这样就解释了为什么镜像名字当中含有环境的信息。
#### buildNumber
这个是我们jenkins系统这次构建的构建号，通过构建号能够找到构建日志信息
#### gitCommitHash
这个是git的短hash，在git当中可以通过这个hash值找到提交点的各种详细信息，甚至提交点被合并到了哪些分支等等的信息，我们可以通过提交点来回退版本，更精准。
### 作者其他文章
[https://github.com/zrbcool/blog-public](https://github.com/zrbcool/blog-public)  
### 微信订阅号
![](http://oss.zrbcool.top/Fv816XFbZB2JQazo5LHBoy2_SGVz)