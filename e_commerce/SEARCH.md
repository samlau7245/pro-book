
# 查询种类

## 特定维度查询

特定维度仅可查询由系统指定的部分可属性，如品类、品牌、价格、性别、材质等，并且用户无法输入查询值，仅能选择查询值。

由于这些数据在系统内部都维护有基础数据，并且保存在指定的字段中，所以对它们的查询相对比较简单。

用户在选中对应选项时，可以获得对应的唯一ID，之后根据ID查询相应字段获得结果，查询相对比较准确。

## 关键字查询

整个搜索功能最重要的入口，允许用户随意输入要查询的关键字，通常仅有一个查询关键字长度限制。

相对于特定维度查询，关键字查询相对比较复杂，下图是搜索的原理图，下面来一一进行分析。

<img src="/assets/images/e_commerce/37.png"/>

## 逻辑层操作

### 非法词过滤

由于关键字查询功能是对用户开放的，所以用户输入什么内容我们是不可控的。

我们在项目排查时经常会发现一些五花八门的关键字，其中有不少关键字比较敏感，比如涉黄、涉赌等等，这些关键字我们通常都会屏蔽，不进行数据搜索。

要屏蔽对应的关键字，后台就需要维护一套非法词库，当用户输入的关键字在非法词库中就不再做搜索，以减轻服务器压力。

网上一般有现成的词库可以直接导入系统，不满足的后台再进行维护扩充。

<img src="/assets/images/e_commerce/38.png"/>

### 错误词纠正

在输入查询关键字时，用户可能会输入成拼音、或者错别字，如用户本意要输入“阿迪达斯”，实际输入成“阿迪斯”，但是结果依然能返回和“阿迪达斯”匹配的数据。

这是因为逻辑中有一套纠错词处理，当系统对比有错误时，会进行纠正处理。

同样后台也需要维护了一套纠错词库，当用户输入的关键字如果在纠错词库中，系统会自动将错误关键字替换为设置好的关键字；如：阿迪斯->阿迪达斯；阿达斯->阿迪达斯，之后查询实际采用的是转换后的关键字。

<img src="/assets/images/e_commerce/39.png"/>

### 特定跳转

有时我们在电商平台上输入查询关键字，会发现部分关键字结果不会跳转到结果列表页，而是跳转到一个商家店铺主页或者活动页；如输入关键字“阿迪达斯”，可能直接就进入到了阿迪达斯旗舰店页面，也有可能进入阿迪达斯活动专场页面。

要实现这个功能，后台同样需要维护一套跳转规则映射库；当用户的搜索关键字与规则库中的关键字匹配时，则返回规则所指定的跳转路径，前端页面直接跳转过去——通常这个跳转规则是有时间限定的。

<img src="/assets/images/e_commerce/40.png"/>

### 商品搜索

当用户输入的查询关键字通过非法词过滤、纠错词纠正、特定跳转匹配后，依然没有匹配结果，这时系统会将关键字交给商品搜索服务器。

搜索服务器首先会对关键字进行分词处理，然后再根据分词进行商品查询，并根据权重规则获得商品权重值，之后再进行权重值排序，最后返回查询结果。

在商品搜索中有三个非常重要的功能：分词、权重、以及搜索维度。

* `分词`：分词是将一个比较长的关键字拆分成多个合理的比较短的关键字（如:阿迪达斯板鞋->阿迪达斯、板鞋、鞋），之后再去商品服务器中去查询数据并将结果展示出来。
* `权重`：权重是衡量某一指标的重要程度，在电商平台里都是各家的商业机密，网上公开的资料也是少之又少；一个商品的权重高低，直接决定着商品排名情况，当然也就影响着销售额。
* `搜索维度`：也就是用户可以通过哪些属性对商品进行搜索； 其中基础属性中的品牌、品类、价格都会参与搜索，还有特殊属性中后台明确规定参与搜索的属性。

商品搜索服务器会根据需要参与搜索的属性，对查询出的商品信息按各属性进行分组统计，然后由代码逻辑层进行数据整理，再由前端进行展示，最终就形成了搜索列表的样式。

<img src="/assets/images/e_commerce/41.png"/>

### 搜索统计

做为平台重要的数据入口，对用户搜索词的统计功能有多重要就不在多说了。

通过对搜索词数据的统计，可以让运营人员直观的了解到用户对品类、品牌、价格的青睐趋势，为后期的活动运营、市场预测做好数据指导。

常见的统计维度有以下几个：

* 每日、每周、以及每月的搜索访问量统计。
* 搜索关键字的排名统计（组织方式：每日、最近一周、最近一个月、每月）。
* 各品类、各品牌的搜索排名统计（组织方式：每日、最近一周、最近一个月、每月）。
* 各品类、各品牌排名占比（组织方式：每日、最近一周、最近一个月、每月）。
* 各价格区间的的搜索排名统计（组织方式：每日、最近一周、最近一个月、每月）。

### 首页推荐词

在电商首页，平台为了推广活动，会在的搜索框下面显示一些热门搜索词或者推荐搜索词，而这些搜索词通常都会跳转到指定的专题或者活动页，以提升活动曝光率。

在上面讲解的【特定跳转】功能上增加一个首页推荐词字段加以区分就能实现这个功能。

<img src="/assets/images/e_commerce/42.png"/>

### 搜索历史

### 搜索推荐词

当用户选中搜索框并输入查询关键字，下拉列表中会出现相似的一些推荐词，并且推荐词后面有相应的商品数量。

这个功能是通过调用【商品搜索】功能的统计接口，实时获得的数据并显示前几位的数据。

<img src="/assets/images/e_commerce/43.png"/>

### 输入形式

通常查询关键字搜索默认的输入形式是文字形式，现在由于技术的发展，有实力的电商平台也引入了图片输入和语音输入方式。

其实内部逻辑一点都没有变，只是在原始的文字输入之上有加了一层识别组件，通过识别组件先将图片内容或者语音内容转为文字，再由文字进行搜索查询。

<img src="/assets/images/e_commerce/44.png"/>












