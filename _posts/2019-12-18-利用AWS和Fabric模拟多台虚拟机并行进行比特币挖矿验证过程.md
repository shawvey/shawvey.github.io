---
img: 24.jpg
layout: post
title:  "利用AWS和Fabric模拟多台虚拟机并行进行比特币挖矿验证过程"
date:   2019-12-18 19:37:47 +0700
categories: posts
tags: [云计算,python]
comments: True
author: shawvey

---

> 该项目用到了AWS的EC2、SQS、S3服务，并利用Fabric库让虚拟机执行shell语句。项目代码：[Cloud Nonce Discovery](https://github.com/shawvey/Cloud-Nonce-Discovery)

### Part1 介绍

本项目的目的是创建Cloud Nonce Disvoery（CND）系统。CND系统允许用户指定参数大小，例如虚拟机（VM）N，最大时间T和难度系数D。当用户运行CND脚本时，CND系统将启动N个虚拟机，并且在最大时间T秒内，使用蛮力搜索找到一个金色的随机数。当系统查找到某个随机数双重哈希运算后的值具有D个前导零，此随机数将被视为一个金色的随机数。此时，CND系统首先将打印金色随机数和其哈希值，然后下载日志文件传到S3存储桶，最后关闭所有虚拟机。

本报告将首先介绍区块链的验证工作（PoW）阶段，然后介绍python连接AWS的接口——Boto3框架。根据下图，CND系统使用了一些AWS免费套餐服务，例如Amazon弹性计算云（EC2），亚马逊简单队列服务（SQS）和亚马逊简单存储服务（S3）。这些AWS服务将在下文中具体介绍。

![](https://i.loli.net/2019/12/19/z6dr2UN17EAhS3p.png)

### Part2 验证工作

CND系统通过运行PoW脚本来检查随机数是否为黄金随机数。 在PoW脚本中，一个名为*goldennonce（）*的Python函数从CND脚本接收两个参数，分别是难度级别D和一个十进制随机数。 此函数将首先创建一个具有连续D个零的字符串，以D值等于5为例，则生成的字符串为“ 00000”。 输入的随机数将附加到其数据定义为字符串“ *COMSM0010cloud*”的块上，并且使用Python hashlib模块对新字符串进行双SHA256哈希算法处理。 如果生成的哈希值具有D或大于D的前导零，则此随机数被称为黄金随机数，并且该黄金随机数的详细信息将被清晰地打印在屏幕上。



### Part3 Boto3 框架

Boto3是用于Python编程语言连接Amazon Web Services（AWS）的SDK。 Boto3提供了一个易于使用的API，该API允许用户启动，停止和管理所有AWS服务，例如SQS和EC2。开发人员必须在使用Boto3之前配置身份验证文件*credentials*。 *credentials*配置文件应存储在系统的*〜/ .aws*文件夹中。credentials文件里的内容可以在AWS网站的账户详细信息界面中找到，会话超时到期后AWS将对其内容进行更新，该凭证文件使Boto3可以连接特定的AWS账户。



### Part4 亚马逊 EC2服务

亚马逊弹性计算云（Amazon EC2）是一项重要服务，它使开发人员能够创建实例并在远程亚马逊数据中心上运行。 用户可以根据自身需求从具有不同性能的各种机器配置中选择合适的实例配置。 如下表所示，当不同类型的EC2实例全部启动5个实例并找到具有两个前导零的相同黄金随机数时，它们具有不同的运行时间。

| 实例类型  |  运行时间   |
| :-------: | :---------: |
| T2.micro  | 13.73945 秒 |
| C4.xlarge | 13.80175 秒 |
| C5.large  | 13.53883 秒 |

用户还可以选择要启动的实例数目。 根据下表所示，如果用户启动了N个C4.xlarge实例，发现黄金随机数的平均速度要比使用单个C4.xlarge实例快N倍。

| 实例数量 |  运行时间   |
| :------: | :---------: |
|    1     | 66.72627 秒 |
|    2     | 34.27059 秒 |
|    3     | 21.93457 秒 |
|    4     | 17.11261 秒 |
|    5     | 13.80175 秒 |
|    6     | 10.89037 秒 |
|    7     | 9.97410 秒  |
|    8     | 8.81076 秒  |

CND系统调用*boto3.client（）*函数来连接EC2，然后系统将通过调用以下代码的*run_instances（）*函数来设置各种配置，例如亚马逊系统映像（AMI），实例类型，安全组等。

```python
ec2.run_instances(ImageId=ubuntu_id,I nstanceType=type,KeyName=key_pair,Sec urityGroups=['SecurityGroupName'],Min Count=int(N),MaxCount=int(N))
```

系统需要等到N个实例变成正在运行状态再进行其他操作。 为了确保所有实例都在运行，系统等待时间设置为100秒。 根据以下代码所示，当所有实例准备就绪时，系统将从所有VM获得公共IP地址，并将其放入MyHosts阵列中。

```python
MyHosts = []
for r in Reservations['Reservations']:
for instance in r['Instances']: MyHosts.append(instance['PublicIpAd
dress'])
```

以下代码是CND系统中的*Scram（）*函数。当系统运行直到达到最大时间或发现到一个金色随机数时，系统将调用*Scram（）*函数关闭所有正在运行的虚拟机。 *Scram（）*函数会先寻找那些正在运行的实例，然后调用*Terminate_instances（）*函数将其关闭。

```python
def Scram():
ec2=boto3.client('ec2',region_name=' us-east-1')
filters = [{'Name':'instance-state- name','Values': ['running']}]
Reservations=ec2.describe_instances( Filters=filters)
for r in Reservations['Reservations']:
for instance in r['Instances']: n=instance['InstanceId'] ec2.terminate_instances(Instanc
eIds=[n])
```



### Part5 亚马逊SQS服务

亚马逊简单队列服务（Amazon SQS）是一种分布式消息队列服务，它能使虚拟机之间能够进行通信。 Boto3调用*boto3.client（）*函数以在boto3与Amazon SQS服务之间建立连接。 之后，系统调用*create_queue（）*函数创建一个名为*CloudComputing*的队列[详见以下代码]。

```python
Sqs = boto3.client('sqs') Response = sqs.create_queue(QueueName='CloudComputing',)
```

如果一台虚拟机找到了黄金随机数，则该虚拟机会将黄金随机数的详细信息返回给系统，下文的Fabric部分将对此加以强调。 以下代码演示了CND系统从一个实例接收到消息后会将消息发送到特定的*CloudComputing*队列。

```python
Response=sqs.get_queue_url(QueueName ='CloudComputing')
sqs.send_message(QueueUrl=response[' QueueUrl',MessageBody=’Finished!’)
```

每个实例计算一个随机数时都将检查*CloudComputing*队列中是否有消息。 如果队列中有消息，则表明另一个实例已经找到了金色随机数。 其他正在运行的实例将退出验证工作，然后继续进行下一步[请参见以下代码]。

```python
messages = sqs.receive_message(QueueUrl=response['QueueUrl'] )
if 'Messages' in messages: 
  break
```

为了节省资源，在所有实例停止查找黄金随机数之后，CND系统将删除*CloudComputing*队列。 

```python
 sqs.delete_queue(QueueUrl=response[' QueueUrl'])
```



### Part6 亚马逊S3服务

亚马逊简单存储系统（Amazon S3）是用于存储无限个对象的服务。 客户端可以使用Boto3 SDK远程读取和更新S3对象。

S3 Web服务中已经事先生成了一个名为*cloudcomputinglogs*的现有S3存储桶。 当所有实例停止寻找黄金随机数时，本地计算机将立即从每个实例下载日志文件，并将日志文件存储在名为logs的本地文件夹中。从实例下载所有日志文件后，本地计算机将日志文件夹上传到S3服务的*cloudcomputinglogs*存储桶。

以下的*upload_logs（）*函数显示了Boto3连接到S3，并将整个日志文件夹上传到命名为*cloudcomputinglogs*的现有S3存储桶中。

```python
def upload_logs():
	s3_resource=boto3.resource("s3", region_name="us-east-1")
	try:
		bucket_name="cloudcomputinglogs" root_path = './logs' my_bucket=s3_resource.Bucket(bucke
		t_name)
	for path,subdirs,files in os.walk(root_path):
		directory_name=path.replace(root_pa th,"logs")
	for file in files: my_bucket.upload_file(os.path.joi n(path,file),directory_name+'/'+f ile)
	except Exception as err:
    print(err)
```



### Part7 Fabric

Fabric库用于通过SSH与虚拟机进行交互，该工具还可以在远程虚拟机上执行大量Shell命令。CND系统必须并行使用N个虚拟机来搜索黄金随机数，而Fabric的**@parallel**装饰器可让N个VM同时运行任务。在CND系统中，不同的实例需要测试不同的随机数，这需要系统对实例进行排序。

如下代码所示，ihosts数组等于上述Amazon EC2部分中提到的MyHosts数组。 Env.host表示当前主机的公共IP地址。 每个实例的序号可以通过调用循环获知，并且参数*LowRange*被赋值为当前实例的序号。

```python
for num in range(int(N)):
	if env.host == ihosts[num]:
          LowRange = num
```

接下来，Fabric将PoW脚本上传到每个实例中。

```python
put('PoW.py', 'PoW.py', use_sudo=True)
```

然后，CND系统尝试通过蛮力找到黄金随机数，并且每个实例将分配不同的值进行测试。 范围步长设置为N（当前实例的总数量），以防止对同一值进行两次或两次以上评估。然后，Fabric调用*run（）*函数以使VM能够执行工作量证明任务。

```python
for CurrentNum in range(int(LowRange),2**32,int(N)):
	Getvalue= run("python3 -c 'import PoW;PoW.goldennonce(%d,%s)'"%(int(D),str(C urrentNum)))
```

最后，当一个实例找到了一个黄金随机数并通过SQS向其他实例发送消息。 此时，Fabric将调用*get（）*函数，从所有实例下载日志文件并将日志文件存储在本地文件夹中。

```python
get('/var/log/syslog','logs/Instance%d.log '%int(LowRange), use_sudo=True)
```



### Part8 项目运行过程

在本节中，我们以T = 20，D = 2，N = 8为例，演示CND系统的运行过程。当用户输入fab命令：`fab -f CND.py start：T = 200，D = 2，N = 8`，CND系统将开始执行代码。 

下图说明CND系统已经启动了8个实例：

![picture1.png](https://i.loli.net/2019/12/19/71BSmnPVI2NKHov.png)

在AWS EC2服务界面的右侧显示了不同实例的公共IP地址：

![2.png](https://i.loli.net/2019/12/19/UsTyr7voFWzghqj.png)

创建实例之后，系统将创建一个名为*CloudComputing*的SQS队列。

![3.png](https://i.loli.net/2019/12/19/tTSJrysZGVFIKUq.png)

CND系统将输出实例正在执行的大量命令。 在下图中，所有实例开始并行执行任务，CND系统通过Fabric将PoW脚本上传到每个实例。

![4.png](https://i.loli.net/2019/12/19/CchXqtuLywYm4ln.png)

然后，虚拟机们开始执行PoW任务。从下图可以看出，每个实例一次测试一个随机数，在还未评估完当前随机数之前，当前虚拟机无法测试下一个随机数。

![5.png](https://i.loli.net/2019/12/19/DK2J5ITHmc8hFEL.png)

当一台虚拟机找到黄金随机数时，它将打印黄金随机数、其哈希值和总运行时间。

![6.png](https://i.loli.net/2019/12/19/AVgO25Ncso38bQe.png)

接着，CND系统将从所有实例下载日志文件，并将这些文件上传到S3存储桶。

![7.png](https://i.loli.net/2019/12/19/ISJ2VNpeck3R5Bz.png)

![8.png](https://i.loli.net/2019/12/19/9sTfHBDEliFJRqM.png)



最后，CND系统将关闭所有创建了的EC2实例和SQS队列，这意味着我们的CND系统已成功执行。

### Part9 结论

当前的CND系统可以成功实现大量有用的功能，例如控制实例、并行执行远程命令、从实例获取日志文件等等。
但是，该系统仍然存在一些缺点。

首先，AWS仅允许用户使用Boto3创建八个或更少的实例。当尝试创建8个以上实例时，AWS将关闭某些实例。其次，搜索黄金随机数的速度很慢，因为CND系统需要检查SQS队列是否接收到消息以及一个实例接收随机数时是否存在超时。第三，CND系统花费时间将消息发送到SQS队列，而这时其他实例仍在搜索黄金随机数，这导致其他实例无法在一个实例找到黄金随机数时立即停止搜索。

笔者对当前CND系统有两个未来的研究方向设想。一方面，作者期望创建一个识别所有虚拟机状态的函数，此功能将使得当前CND系统不再需要等待一段较长的固定时间。另一方面，CND系统现在无法自动为用户选择合适的实例数，但自动伸缩组服务应该可以用于实现此功能。

### 引用

[1]Anon., (2019). Boto 3 Documentation [online]. Anon.[Viewed 20 November 2019]. Available from:https://boto3.amazonaws.com/v1/documentation/api/latest/index.html

[2] Diginmotion., (2010). Running a website on Amazon EC2 [online]. Diginmotion. [Viewed 1 December 2019]. Available from:https://s3.amazonaws.com/academia.edu.documents/27741916/running-a-website-on-amazon-ec2.pdf

[3]Matthias Brantner, Daniela Florescu, David Graf,Donald Kossmann, Tim Kraska, Building a database on S3, Proceedings of the 2008 ACM SIGMOD international conference on Management of data, June 09-12, 2008,Vancouver, Canada [doi>10.1145/1376616.1376645]
