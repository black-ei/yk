​	

# **mall项目:**

## **了解电商:**

### **常见电商模式 :**

​	**电商行业的一些常见模式:**

 	

- **B2C：商家对个人，如：小米官网、苹果官网等**
-  **C2C平台：个人对个人，如：闲鱼、ebay** 
- **B2B平台：商家对商家，如：阿里巴巴、阿里云等** 
- **O2O：线上和线下结合，如：美团，滴滴等** 
- **P2P：在线金融，贷款，如：拍拍贷、蚂蚁金融等。** 
- **B2C平台：天猫、京东、一号店等**

### **一些专业术语:**

​		**SaaS：软件即服务 --> 云计算的一块** 

​		**SOA：面向服务 --> web service --> httpclient** 

​		**RPC：远程过程调用** 

​		**RMI：远程方法调用** 

​		**PV：(page view)，即页面浏览量； 用户每1次对网站中的每个网页访问均被记录1次。用户对同一页面的多次访问，访问量累计** 

​		**UV：(unique visitor)，独立访客 指访问某个站点或点击某条新闻的不同IP地址的人数。在同一天内，**

​		**uv只记录第一次进入网站的具 有独立IP的访问者，在同一天内再次访问该网站则不计数**。

​		**PV与带宽：** 

​				**计算带宽大小需要关注两个指标：峰值流量和页面的平均大小。**

​				 **计算公式是：网站带宽= ( PV * 平均页面大小（单位MB）* 8 )/统计时间（换算到秒）**

​				 **为什么要乘以8？**

​						 **网站大小为单位是字节(Byte)，而计算带宽的单位是bit，1Byte=8bit这个计算的是平均带宽，高峰期还需要						扩大一定倍数**

​		 		****

​		**PV、QPS、并发**

​			 **QPS：每秒处理的请求数量。8000/s**

​					**比如你的程序处理一个请求平均需要0.1S，那么1秒就可以处理10个请求。**

​					**QPS自然就 是10，多线程情况下，这个数字可能就会有所增加。**

​	 		**由PV和QPS如何需要部署的服务器数量？**

​					 **根据二八原则，80%的请求集中在20%的时间来计算峰值压力：（每日PV * 80%） / （3600s * 24 * 20%） * 每个页面的请求数 = 每个页面每秒的请 求数量 然后除以服务器的QPS值，即可计算得出需要部署的服务器数量**

****



## shop      :

### 		域名处理:

​			**我们在开发的过程中，为了保证以后的生产、测试环境统一。尽量都采用域名来访问项目。**

​			**主域名是：www.mrshop.com，** 

​			**管理系统域名：manage.mrshop.com** 

​			**网关域名：api.mrshop.com**

​			 **...**

​			**那么，当我们在浏览器输入一个域名时，浏览器是如何找到对应服务的ip和端口的呢？**

​				一个域名一定会被解析为一个或多个ip。这一般会包含两步： 本地域名解析 浏览器会首先在本机的hosts文件中查找域名映射的		P地址，如果查找到就返回IP ，没找到则进行 域名服务器解析，一般本地解析都会失败，因为默认这个文件是空的Windows下的		hosts文件地址：C:/Windows/System32/drivers/etc/hosts Linux下的hosts文件所在路径： /etc/hosts

​		样式：

```c
# My hosts
127.0.0.1 localhost
0.0.0.0 account.jetbrains.com
127.0.0.1 www.xmind.net
```

**只需要把打包方式设置为pom即可,暂时先不要引入其他依赖**

```xml
<!--父级项目不需要打包所有packging的类型为pom-->
<packaging>pom</packaging>

```



### mingrui-shop-basics

(**项目依赖服务**)

#### 					**eureka-server**:

(注册中心-server)

#### 					**zull-server  **

(网关-server)

(需要配置跨域)

域名服务器解析 本地解析失败，才会进行域名服务器解析，域名服务器就是网络中的一台计算机，里面记录了所有 注册备案的域名和ip映射关系，一般只要域名是正确的，并且备案通过，一定能找到。



vue项目使用域名会出现ERROR:"Invalid Host header"

解决

```
//打开build文件夹下webpack.dev.conf.js文件在devServer下加入
disableHostCheck: true,
```

##### 使用nginx做端口的反向代理

什么是反向代理？

​			代理：通过客户机的配置，实现让一台服务器代理客户机，客户的所有请求都交给代理服务器处理。

​			反向代理：用一台服务器，代理真实服务器，用户访问时，不再是访问真实服务器，而是代理服务器

​		**nginx可以当做反向代理服务器来使用：**

​			我们需要提前在nginx中配置好反向代理的规则，不同的请求，交给不同的真实服务器处理

​			当请求到达nginx，nginx会根据已经定义的规则进行请求的转发，从而实现路由功能



​		**我们希望的是直接域名访问： http://manage.mrshop.com 。这种情况下端口默认是80，如何才能把 请求转移到9001端口呢？ 这里	就要用到反向代理工具：Nginx**

**Nginx是一个高性能的Web和反向代理服务器，它有许多非常优越的特性:作为一个Web服务器:与Apache相比，Nginx使用更少的资源，支持更多的并发连接，体现出更高的效率，这使得Nginx特别受主机提供商的欢迎。能够支持多达50000个并发连接数的响应，感谢Nginx为我们选择了epoll和kqueue作为开发模式。作为负载均衡服务器:Nginx既可以直接在内部支持Rails和PHP，也可以支持作为HTTP代理服务器在国外服务。Nginx用C编写，无论是系统资源开销还是CPU利用效率都比Perlbal好得多。作为邮件代理服务器:Nginx也是一个非常好的邮件代理服务器(最早开发之一，该产品的目的也是作为邮件代理服务器)，最后，FM描述了一次成功而精彩的体验。Nginx安装非常简单，配置文件简单(也支持per语法)，bug很少:Nginx启动特别容易，几乎7 * 24不间断的操作都可以完成，甚至几个月都不需要重启操作。您也可以在不间断服务的情况下进行软件版本升级。**

NIO：not-blocking-io 非阻塞IO

BIO：blocking-IO 阻塞IO

nginx可以作为web服务器，但更多的时候，我们把它作为网关，因为它具备网关必备的功能：

​		**1.反向代理**

​		**2.负载均衡**

​		**3.动态路由**

​		**4.请求过滤**

 **nginx作为web服务器***

​	web应用服务器，如：

​		tomcat

​		resin

​		jetty

​	web服务器，如：

​		Apache 服务器

​		Nginx

​		IIS

​	**区分：web服务器不能解析jsp等页面，只能处理js、css、html等静态资源。**

​	**并发：web服务器的并发能力远高于web应用服务器。**

Nginx + tomcat !!!! 负载均衡

nginx直接解压使用

nginx可以通过命令行来启动，操作命令：

* **启动： start nginx.exe**

* 停止： nginx.exe -s stop

* 重新加载： nginx.exe -s reload

  **配置nginx:需要修改nginx/conf/nginx.conf文件**

  



#### **upload-server:**

(image上传server)

##### nginx解决url转发

**(可能多个模块使用到,所以单个提取出做一个服务) (需要配置跨域)**



​		如果将图片放在项目目录的话,前台对项目的访问量就会非常的大,造成了没有必要的服务器压力,所以我们将它放到本地目录将图片放入本地目录浏览器将无法使用http请求访问,使用nginx代理本地目录

​	 **hosts****文件中新增** 

```
127.0.0.1 image.mrshop.com

server {
	listen 80; 
	server_name image.mrshop.com; 
	
	proxy_set_header X-Forwarded-Host $host; 
	proxy_set_header X-Forwarded-Server $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; 
    
    	location ~ .*\.(gif|jpg|pdf|jpeg|p	ng)$ {
        #root D:/nginx-1.15.5/temp/images/;#指定图片存放路径(可以放在nginx文件夹路 径里也可以放其他p盘)
        root E:\images;
        }
        location / { 
        root html;
        index index.html index.htm;
        }
}
```

使用nginx来做upload的转发

```
# 上传路径的映射
# 只要包含/api-xxx/upload 都会把请求映射到8200服务 
# rewrite "^/api-xxx/(.*)$" /$1 break; 
# 将/api-xxx 替换成/ 
location /api-xxx/upload {
	proxy_pass http://127.0.0.1:8200;#upload服务地址
    proxy_connect_timeout 600;
    proxy_read_timeout 600;
    rewrite "^/api-xxx/(.*)$" /$1 break; 
 }
```

nginx已经做了url的重写 zull配置使/upload/** 不被路由, 忽略上传服务的默认配置

```
# 包含此路径的不进行路由 
ignored-patterns: /upload/**
# 忽略上传服务 
ignored-services: -upload-server
```

##### fastDFS:

**什么是分布式文件系统**:

先思考一下，之前上传的功能，有没有什么问题？

上传本身没有任何问题，问题出在保存文件的方式，我们是保存在服务器机器，就会有下面的问题：

单机器存储，存储能力有限

无法进行水平扩展，因为多台机器的文件无法共享,会出现访问不到的情况

数据没有备份，有单点故障风险

并发能力差

这个时候，最好使用分布式文件存储来代替本地文件存储。

分布式文件系统（Distributed File System）是指文件系统管理的物理存储资源不一定直接连接在本地

节点上，而是通过计算机网络与节点相连。

通俗来讲：

传统文件系统管理的文件就存储在本机。

分布式文件系统管理的文件存储在很多机器，这些机器通过网络连接，要被统一管理。无论是上传

或者访问文件，都需要通过管理中心来访问

 **什么是FastDFS**:

FastDFS是由淘宝的余庆先生所开发的一个轻量级、高性能的开源分布式文件系统。用纯C语言开发，功能丰富：

- 文件存储
- 文件同步
- 文件访问（上传、下载）
- 存取负载均衡
- 在线扩容

适合有大容量存储需求的应用或系统。同类的分布式文件系统有谷歌的GFS、HDFS（Hadoop）、TFS（淘宝）等。



**FastDFS两个主要的角色：Tracker Server 和 Storage Server 。**

- Tracker Server：跟踪服务器，主要负责调度storage节点与client通信，在访问上起负载均衡的作用，和记录storage节点的运行状态，是连接client和storage节点的枢纽。
- Storage Server：存储服务器，保存文件和文件的meta data（元数据），每个storage server会启动一个单独的线程主动向Tracker cluster中每个tracker server报告其状态信息，包括磁盘使用情况，文件同步情况及文件上传下载次数统计等信息
- Group：文件组，多台Storage Server的集群。上传一个文件到同组内的一台机器上后，FastDFS会将该文件即时同步到同组内的其它所有机器上，起到备份的作用。不同组的服务器，保存的数据
- 不同，而且相互独立，不进行通信。Tracker Cluster：跟踪服务器的集群，有一组Tracker Server（跟踪服务器）组成。
- Storage Cluster ：存储集群，有多个Group组成。

**java**上传文件到fastDFS服务

```
pom:

<dependency>
	<groupId>com.github.tobato</groupId>
	<artifactId>fastdfs-client</artifactId>
	<version>1.26.1-RELEASE</version>
</dependency>

appliaction:
fdfs:
	so-timeout: 1501
	connect-timeout: 601
	thumb-image: # 缩略图
		width: 60
		height: 60
	tracker-list: # tracker地址
	- 119.45.191.248:22122
mingrui:
	upload:
	path:
		windows: E:\\upload
		linux: /shenyaqi/upload
	img:
	#图片服务器主机
	host: http://119.45.191.248/


```

fastDFS配置类:

```
@Configuration
@Import(FdfsClientConfig.class)
// 解决jmx重复注册bean的问题
@EnableMBeanExport(registration = RegistrationPolicy.IGNORE_EXISTING)
public class FastClientImporter {
}

```

controller:

```java
package com.baidu.shop.upload.controller;

import com.baidu.shop.base.Result;
import com.baidu.shop.status.HTTPStatus;
import com.github.tobato.fastdfs.domain.StorePath;
import com.github.tobato.fastdfs.domain.ThumbImageConfig;
import com.github.tobato.fastdfs.service.FastFileStorageClient;
import lombok.extern.slf4j.Slf4j;
import org.junit.runners.Parameterized;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.multipart.MultipartFile;

import java.io.IOException;
import java.io.InputStream;

@RestController
@RequestMapping("upload")
@Slf4j
public class FastDFSUploadController {

    //图片服务器的地址
    @Value(value = "${mingrui.upload.img.host}")
    private String imgHost;
    //上传
    @Autowired
    private FastFileStorageClient storageClient;
    //缩略图配置
    @Autowired
    private ThumbImageConfig thumbImageConfig;

    @PostMapping
    public Result<String> uploadImg(@RequestParam MultipartFile  file) throws IOException {

        InputStream inputStream = file.getInputStream();//获取文件输入流
        String filename = file.getOriginalFilename();//文件名

        String ex = filename.substring(filename.indexOf(".") + 1);//文件后缀名
        //上传并生成缩略图
        StorePath storePath = this.storageClient.uploadImageAndCrtThumbImage(inputStream,
        file.getSize(),ex, null);
        //带分组的路径
        log.info("上传图片全路径:{}",storePath.getFullPath());
        //不带分组路径
        log.info("上传图片路径:{}", storePath.getPath());
        //获取缩略图
        String thumbImagePath = thumbImageConfig.getThumbImagePath(storePath.getFullPath());
        log.info("缩略图路径:{}", thumbImagePath);

        return new Result<String>(HTTPStatus.OK,"上传成功",imgHost+thumbImagePath);
    }

}

```



### mingrui-shop-commoms:

(依赖工具)

#### 					**common-core**:

(依赖utils)

​	***需要使用到util的子项目需要引入该项目依赖****

```java
Result<T> 全局统一返回格式 
BeanUtils.copyProperties(object,t);//bean的copy

```

​				**泛型***https://note.youdao.com/ynoteshare1/index.html?id=a83103814321b63e12f932c5552c197d&type=note

​					**springmvc全局异常处理: https://blog.csdn.net/justry_deng/article/details/86571671**

```java
如果不采用BindingResult来容纳异常信息时，那么异常会被向外抛出。注解校验不通过时，可能抛出的异常有BindException异常、ValidationException异常(或其子类异常)、MethodArgumentNotValidException异常

@RestControllerAdvice//controller增强
@ExceptionHandler(value="")//value值为要处理的异常(要与方法参数一致)


	List<String> list = new ArrayList<>();

        e.getBindingResult().getFieldErrors().stream().forEach(fieldError -> {
            list.add("Field -->"+fieldError.getField()+" : 								"+fieldError.getDefaultMessage());
            log.error("Field -->"+fieldError.getField()+" : "+fieldError.getDefaultMessage());
        });
      String msg = list.parallelStream().collect(Collectors.joining(","));
```

### 		**mingrui-shop-service-api**:

(服务接口)

**(项目接口实现分离,利于开发 )**

​					**使用swagger2(swagger2辅助对接)**

```java
	@ApiModel(value="") //value值:实体类描述
	@ApiModelProperty(value = "实体类属性(字段)描述",example = "举例说明")
	@Api(tags = "接口描述")
	@ApiOperation(value = "接口方法描述")
```

#### 				**校验参数(Validation)**

```java
@NotNull(message="") //不能为null，可以是空
@NotEmpty()//不能为null，集合、数组、map等size()不能为0；字符串trim()后可以等于“”
@Validated()
当Controller层中参数是一个对象模型时，只有将@Validated直接放在该模型前，该模型内部的字段才会被校验(如果有对该模型的字段进行约束的话)。
当一些约束是直接出现在Controller层中的参数前时，只有将@Validated放在类上时，参数前的约束才会生效。
@Valid//多级嵌套模型的校验,给复杂对象字段加上@Valid注解
 @NotNull(message = "是否为父级节点不能为NULL",groups = {MingruiOperation.add.class})//按不同分组进行校验
```

### mingrui-shop-service:

(业务实现)

#### 	**ctegoryServiceImpl:**

(分类的实现)

​	vue:删除完成后给:key重新设置一个值可实现组件刷新

​			 **删除分类**

​					**1.判断id是否合法,不合法return Error("id不合法")**

​					**2.根据id查询当前节点,判断是否有数据(安全),判断isParent是否为父级节点 为父节点return  Error("为父节点不能被删						除!")**

​					3.根据分类id查询分类与品牌的中间表,如果有数据,return  Error("当前分类有品牌绑定不能被删除,请先删除品牌")

​					4.判断当前节点间的父级id 是否为0 如果为0当前节点就是根节点 return  Error("当前节点为根节点不能被删除")**

​					5.根据当前节点的parentId查询父级节点下的子节点,判断父级节点下是否只有当前节点一个节点**

​					6.只有当前节点就将父节点isParent改为0(不是父节点),如果父节点下的子节点大于1个不做任何操作**

​					7.删除当前节点 return succees("操作成功")**

#### **BrandServiceImpl**:

(品牌的实现)

​		queryPageInfo:

```java
<!--分页工具-->
<dependency>
	<groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.2.10</version>
</dependency>

//分页插件
PageHelper.startPage(brandDTO.getPage(),brandDTO.getRows());
//查询
List<BrandEntity> list = brandMapper.selectByExample(example);
//获取数据的total(条数)
PageInfo<BrandEntity> pageInfo = new PageInfo<>(list);

```



insert:

```java
新增返回ID ktMapper做法:
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Integer id;
 

CategoryBrandMapper多继承一个接口InsertListMapper<CategoryBrand>用来做中间表批量新增
    
	List<CategoryBrandEntity> list = new ArrayList<>();
	String[] categoryArr = brandDTO.getCategories().split(",");
	
Arrays.asList(categoryArr).stream().forEach(str -> {
		CategoryBrandEntity categoryBrandEntity = new 																CategoryBrandEntity();categoryBrandEntity.setBrandId(brandEntity.getId()); 									categoryBrandEntity.setCategoryId(Integer.parseInt(str));
		list.add(categoryBrandEntity); 放入list集合
}); 
	categoryBrandMapper.insertList(list);//批量新增
//处理品牌首字母新增
<dependency>
	<groupId>com.belerweb</groupId>
    <artifactId>pinyin4j</artifactId>
    <version>2.5.1</version>
</dependency>
    
```

update:

```
//执行修改操作
brandMapper.updateByPrimaryKeySelective(brandEntity);
//通过brandID删除中间表的数据
this.deleteCategoryAndBrand(brandEntity.getId());
//新增新的数据
this.insertCategoryAndBrand(brandDTO,brandEntity)
```

delete:

```
//删除品牌
brandMapper.deleteByPrimaryKey(id)
//同时删除关系标的数据
this.deleteCategoryAndBrand(id);
private void deleteCategoryAndBrand(Integer id){
	Example example = new Example(CategoryBrandEntity.class);
	example.createCriteria().andEqualTo("brandId",id);
	categoryBrandMapper.deleteByExample(example);
}
```

#### **SpecificationServiceImpl**:

(规格和规格参数的实现)

规格:CURD(删除判断该规格下是否有绑定参数)

规格参数:CURD

接口:通过分类id查询品牌信息

```
@ApiOperation(value="通过分类id获取品牌")
@GetMapping(value = "brand/getBrandByCategory")
public Result<List<BrandEntity>> getBrandByCategory(Integer cid);

public interface BrandMapper extends Mapper<BrandEntity> {
	@Select(value = "select b.* from tb_brand b,tb_category_brand cb where b.id = cb.brand_id and cb.category_id=#{cid}")
    List<BrandEntity> getBrandByCategory(Integer cid);
}
```

接口:通过分类id查询规格参数:

#### GoodsServiceImpl:

```
//因为在数据库表中加入了分类id字段,所以不需要通过连表规格分组表,再连参数表查询,直接通过cid查询参数表

if(ObjectUtil.isNotNull(specParamDTO.getCid())){ criteria.andEqualTo("cid",specParamDTO.getCid()); }
```

queryPage

```java
 @Override
    public Result<List<SpuDTO>> getSpuInfo(SpuDTO spuDTO) {
        //分页插件
        if(spuDTO.getPage()!=null && spuDTO.getRows()!=null && spuDTO.getRows()>0) 					PageHelper.startPage(spuDTO.getPage(),spuDTO.getRows());

        Example example = new Example(SpuEntity.class);
        Example.Criteria criteria = example.createCriteria();
      if(null!=spuDTO){
          //判断 1上架 0下架
          if (spuDTO.getSaleable()!=null && spuDTO.getSaleable()<2 && spuDTO.getSaleable()>=0) criteria.andEqualTo("saleable",spuDTO.getSaleable());

          //模糊匹配
          if(spuDTO.getTitle()!=null && !"".equals(spuDTO.getTitle()))
              criteria.andLike("title","%"+spuDTO.getTitle()+"%");
          //排序
          if(!StringUtils.isEmpty(spuDTO.getSort())) example.setOrderByClause(spuDTO.getOrderByClause());
      }

        List<SpuEntity> spuEntities = spuMapper.selectByExample(example);

        //查询分类名称 和 品牌名称封装到supDTO返回集合
        List<SpuDTO> spuDTOList = spuEntities.stream().map(spuEntity -> {
            //克隆bean 将SpuEntity克隆到SpuDTO
            SpuDTO dto = BaseBean.copyProperties(spuEntity, SpuDTO.class);
            //通过cid...查询品牌分类名称
            dto.setCategoryName(categoryMapper.selectByIdList(Arrays.asList(dto.getCid1(), dto.getCid2(), dto.getCid3())).stream().map(category -> category.getName()).collect(Collectors.joining("/")));

            //根据spuBrandId查询brandName
            dto.setBrandName(brandMapper.selectByPrimaryKey(dto.getBrandId()).getName());
            return dto;
        }).collect(Collectors.toList());

		//因为分页缺少total 所以必须填写spuEntities才能完成分页
        PageInfo<SpuEntity> pageInfo = new PageInfo<>(spuEntities);
        //但我们想要的结果集是带categoryName和BrandName的 List<SpuDTO>,所以不能返回PageInfo<SpuEntity>,
        //pageInfo.getTotal()获取total,再次封装返回
        return this.setResult(HTTPStatus.OK,pageInfo.getTotal()+"",spuDTOList);
    }
```

save:

![](D:\Learningdata\6monthFack\image\20210107151450.png)

```java
//四张表的数据组成的一个json字符串,我们使用spuDTO来接收
/*
*private SpuDetailDTO spuDetail spuDetail 接收信息
*private List<SkuDTO> skus;笛卡尔积后有多个不同版本的商品,所以是集合
*/

package com.baidu.shop.dto;

import com.baidu.shop.base.BaseDTO;
import com.baidu.shop.validate.group.MingruiOperation;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;

import javax.persistence.Id;
import javax.validation.Valid;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotEmpty;
import javax.validation.constraints.NotNull;
import java.util.Date;
import java.util.List;

@Data
@ApiModel(value = "spu数据传输DTO")
public class SpuDTO extends BaseDTO{

    @ApiModelProperty(value = "主键",example = "1")
    @NotNull(message = "主键不能为空",groups = {MingruiOperation.update.class})
    private Integer id;
    @ApiModelProperty(value = "标题")
    @NotEmpty(message = "标题不能为空",groups = {MingruiOperation.update.class,MingruiOperation.add.class})
    private String title;
    @ApiModelProperty(value = "子标题")
    private String subTitle;
    @ApiModelProperty(value = "1级类目id", example = "1")
    @NotNull(message = "1级类目id不能为空",groups ={MingruiOperation.add.class,MingruiOperation.update.class})
    private Integer cid1;
    @ApiModelProperty(value = "1级类目id", example = "1")
    @NotNull(message = "2级类目id不能为空",groups ={MingruiOperation.add.class,MingruiOperation.update.class})
    private Integer cid2;
    @ApiModelProperty(value = "1级类目id", example = "1")
    @NotNull(message = "3级类目id不能为空",groups ={MingruiOperation.add.class,MingruiOperation.update.class})
    private Integer cid3;
    @ApiModelProperty(value = "商品所属品牌id",example = "1")
    @NotNull(message = "所属品牌id不能为空",groups ={MingruiOperation.add.class,MingruiOperation.update.class})
    private Integer brandId;
    @ApiModelProperty(value = "是否上架,0下架,1上架",example = "1")//不需要验证,新增时直接设置默认值
    private Integer saleable;
    //不需要验证,新增时直接设置默认值
    @ApiModelProperty(value = "是否有效，0失效，1有效", example = "1")
    private Integer valid;
    //不需要验证,新增时直接设置默认值
    @ApiModelProperty(value = "添加时间")
    private Date createTime;
    //不需要验证,新增时直接设置默认值,修改时使用java代码赋值
    @ApiModelProperty(value = "最后修改时间")
    private Date lastUpdateTime;
    private String categoryName;
    private String brandName;
    @ApiModelProperty(value = "大字段数据")
    @Valid//复杂数据类型的验证需要加valid
    private SpuDetailDTO spuDetail;
    @ApiModelProperty(value = "sku属性数据集合")
    @Valid
    private List<SkuDTO> skus;

}

```

```
//save
 @Override
    @Transactional//操作多个事务需要开启事务
    public Result<JSONObject> saveSpuInfo(SpuDTO spuDTO) {

        final Date date = new Date();
        //新增spu
        //spuDetailEntity加@GeneratedValue(strategy = GenerationType.IDENTITY)返回新增ID
        SpuEntity spuEntity = BaseBean.copyProperties(spuDTO, SpuEntity.class);
        spuEntity.setSaleable(1);//设置默认值
        spuEntity.setValid(1);
        spuEntity.setCreateTime(date);设置新增时间
        spuEntity.setLastUpdateTime(date);
        spuMapper.insertSelective(spuEntity);
        //通过返回的spuid新增spudetail
        SpuDetailEntity spuDetailEntity = BaseBean.copyProperties(spuDTO.getSpuDetail(), SpuDetailEntity.class);
        spuDetailEntity.setSpuId(spuEntity.getId());//spuDetail需要spu返回的主键作为spuId
        spuDetailMapper.insertSelective(spuDetailEntity);
        //新增sku,因为是一对多可能会有多个不同商品,所以是list
        spuDTO.getSkus().stream().forEach(skuDTO -> {
            SkuEntity skuEntity = BaseBean.copyProperties(skuDTO, SkuEntity.class);
            skuEntity.setSpuId(spuEntity.getId());
            skuEntity.setCreateTime(date);
            skuEntity.setLastUpdateTime(date);
            skuMapper.insertSelective(skuEntity);//与stock是一对一,所以需要返回主键
            //通过返回的skuID新增Stock
            StockEntity stockEntity = new StockEntity();
            stockEntity.setSkuId(skuEntity.getId());
            stockEntity.setStock(skuDTO.getStock());
            stockMapper.insertSelective(stockEntity);
        });

        return this.setResultSuccess();
    }

```

update(前端回显):

```vue
(vue:Goods)
 editItem(item) {
 	//定义商品数据的集合
      const obj = item;
      this.isEdit = true;//watch监听selectedGoods,根据isEdit判断修改或新增,虽然请求是异步的,但防止数据加载太快在this.isEdit = true;之后,所以先=true
      // 查询商品详情
      this.$http
        .get("goods/getSpuDetail", {
          params: {
            spuId: item.id,
          },
        })
        .then((resp) => {
          obj.categories = [];//解决undefined不被监控的问题
          //商品详情
          obj.spuDetail = resp.data.data;
          //特殊规格
          obj.spuDetail.specTemplate = JSON.parse(resp.data.data.genericSpec);
          //通用规格
          obj.spuDetail.specifications = JSON.parse(resp.data.data.specialSpec);
            //查询sku
            this.$http.get('goods/getSkus', {
              method:'get',
              params: {
                spuId: item.id,
              }}).then((req) => {
                obj.skus = req.data.data;
                //将商品集合给selectedGoods,selectedGoods传入子组件goodsForm进行回显
                this.selectedGoods = obj;
            })
            
            .catch((error) => console.log(error));
        });
      //this.isEdit = true;//还没有执行this.isEdit = true;而判断为新增清除了数据
      this.show = true;
    },
    (vue:goodsForm)
    this.goods = Object.deepCopy(val);
          // 先得到分类名称
          const names = val.categoryName.split("/");
      	//延迟加载,解决异步加载时还未获得数据就执行
          setTimeout(()=>{
              // 组织商品分类数据
            this.goods.categories = [
              { id: val.cid1, name: names[0] },
              { id: val.cid2, name: names[1] },
              { id: val.cid3, name: names[2] }
            ];
          },40);

          // 将skus处理成map
          const skuMap = new Map();
          this.goods.skus.forEach(s => {
            skuMap.set(s.indexes, s);
          });
          this.goods.skus = skuMap;
        }
      }    

```

(update后台):

```java
    
    @Override
    @Transactional
    public Result<JSONObject> editSpuInfo(SpuDTO spuDTO) {
        SpuEntity spuEntity = BaseBean.copyProperties(spuDTO, SpuEntity.class);
        //保持新增时间的一致
        final Date date = new Date();
        //修改spu
        spuMapper.updateByPrimaryKeySelective(spuEntity);
        //修改spuDetail
  spuDetailMapper.updateByPrimaryKeySelective(BaseBean.copyProperties(spuDTO.getSpuDetail(),SpuDetailEntity.class));
        //如果新增特有规格前台(笛卡尔积)会生成很多不同的版本所以(没有skuId)先删除原有的sku去,再将现在的sku新增
        //删除sku和stock
        delSkuAndStock(spuEntity.getId());
        //进行新增sku和stock
        saveSkuAndStock(spuDTO,spuEntity.getId(),date);

        return this.setResultSuccess();
    } 
```

```java
//封装的删除sku和stock,新增sku和stock方法
    /*
     * 进行新增sku和stock*/
    private void saveSkuAndStock(SpuDTO spuDTO,Integer spuId,Date date){
        //新增sku,因为是一对多可能会有多个不同商品,所以是list
        spuDTO.getSkus().stream().forEach(skuDTO -> {
            SkuEntity skuEntity = BaseBean.copyProperties(skuDTO, SkuEntity.class);
            skuEntity.setSpuId(spuId);
            skuEntity.setCreateTime(date);
            skuEntity.setLastUpdateTime(date);
            skuMapper.insertSelective(skuEntity);
            //通过返回的skuID新增Stock
            StockEntity stockEntity = new StockEntity();
            stockEntity.setSkuId(skuEntity.getId());
            stockEntity.setStock(skuDTO.getStock());
            stockMapper.insertSelective(stockEntity);
        });
    }
/*
    通过spuId删除sku和stock
    * */
    private void delSkuAndStock(Integer spuId){
        Example example = new Example(SkuEntity.class);
        example.createCriteria().andEqualTo("spuId",spuId);
        //通过spuId查询skuId
        List<Long> collect = skuMapper.selectByExample(example).stream().map(sku -> sku.getId()).collect(Collectors.toList());
        //删除sku
        skuMapper.deleteByIdList(collect);
        //删除stock,(sku主键也是stock的主键)
        stockMapper.deleteByIdList(collect);
    }
```

delete:

```java
   //删除商品
    @Override
    @Transactional
    public Result<JSONObject> delSpuInfo(@NotNull(message = "id不能为空") Integer spuId) {
        spuMapper.deleteByPrimaryKey(spuId);//删除spu
        spuDetailMapper.deleteByPrimaryKey(spuId);//删除puDetail
        //直接删除就无法删除stock,先查询查询sku,获得skuID
        this.delSkuAndStock(spuId);
        return this.setResultSuccess();
    }
```

updwon(商品上下架):



```

    @Override
    public Result<List<SkuDTO>> downGood(@NotNull Integer spuId) {
        //saleable
        SpuEntity spuEntity = spuMapper.selectByPrimaryKey(spuId);
        //每次修改不同的状态
        spuEntity.setSaleable(spuEntity.getSaleable()==1?0:1);
        spuMapper.updateByPrimaryKeySelective(spuEntity);
        return this.setResultSuccess();
    }
```

