# Weather Cal

这是一个Scriptable的小部件，可让您显示和设置多个格式的元素，包括【日期】【问候语】【日程事件】，【当前天气】【未来天气】【日出/落时间】和【电池电量】。您甚至可以通过编写一个具有单个WidgetStack作为参数的函数（representing a column）来创建自己要显示的元素。

## 如何设置

在开始之前，您可能需要对`weather-cal.js`文件进行一些编辑。您可以在计算机上进行编辑并转移到iOS，也可以仅在Scriptable中进行编辑。

1. 到[free OpenWeather API key](http://openweathermap.org/api)注册一个账号，获得API key，然后粘贴到 `apiKey = ""`的引号内，请注意，这个API可能需要一段时间才生效！
2. 基础设置：编辑`weather-cal.js`的【小部件设置】部分来进行一些基础设置（注意看注释）
3. 运行脚本。它会提示您获取定位信息和选择背景图片（如果启用）。最后，它会在Scriptable中显示预览。
4. 在桌面上添加Scriptable的Widget，长按选择`weather-cal`即可。

如果你需要在这个小部件脚本上使用透明背景，请先使用这个 [Transparent Widgets.js](https://github.com/xkerwin/Weather-Cal/blob/main/Transparent%20Widgets%20Export.js)来制作，在最后，选择导出到相册，然后再次运行`weather-cal`选择刚刚制作好的背景图即可

关于其他调整与设置，参见：[FAQ](https://github.com/xkerwin/Weather-Cal/blob/main/README_cn.md#faq)

## 布局调整

要进行布局，请找到` LAYOUT`部分的标题。您将看到两个带有项目列表的区域：一个用于左列，一个用于右列。您可以在每个列表中添加和删除项目。不要更改此区域中其他部分的代码（列宽除外）。并且，请确保每个项目都以逗号(英文符号)结尾，如下所示：

```
row,
  
  column,
  date,
  battery,
  sunrise,
  space,
  
  column(100),
  current,
  future,
    
row,
  
  column,
  events,
```

### 向小部件添加项目

- __widget item添加__ 每个单词后以逗号结束。您可以在小部件中添加以下项目：_`日期`，`问候`，`事件`，`电池`，`当前`和`未来天气`，`日出（显示日出和日落）`_，以及_`文本文字`_，确保每个项目始终将其放在`column`项目的下面。

- 如果你想更改项目的显示方式，请到` ITEM SETTINGS`部分。大多数项目允许您调整它们的显示方式。

- `row/行`和`column/列`项用于创建小部件的布局结构。在上面的代码中，小部件的一行包含两列，另一行只含一列。您可以添加或删除行和列，您只需知道每一行下面至少要添加一个列。默认情况下，行和列的大小匹配。您还可以使用括号指定其大小，例如：` row（50）`或` column（100）`。

- 默认情况下，小部件中的所有项目均为居左对齐。您可以在列表中的任意位置添加对齐` left`，` right`或` center`），会将对齐方式应用于这个列下的所有内容。

- 添加`space`将添加一个空间，该空间会自动扩展以填充小部件中的可用空间。此空间上方的所有内容都将与顶部对齐，此空间下方的所有内容都将与底部对齐。

## 细节说明

用户可以在`LAYOUT`部分的`items`数组中添加和删除项目，以选择小部件中显示的内容。项目要么是函数对象`events`，要么是函数调用` space（50）`，它们返回一个函数对象。无论哪种方式，小部件构造代码都希望每个项目都是一个带有单个` column`参数的函数。

当脚本运行时，它仅调用`items`数组中的每个函数并传递到当前列。由于大多数窗口小部件项目都需要执行`async`工作，因此脚本在调用函数时会使用`await`表达式。

上面讨论的布局项的特殊之处在于，它们使用并继承了全局的`currentRow`，`currentColumn`和`currentAlignment`变量，以便创建行和列或调整对齐方式。

### 如何创建一个小部件项目

如果要创建自己的窗口小部件项目，请参考以下必需/可选条件：

* __必需条件：__ 具有小部件项目名称的函数，例如：`function date（column）`。函数的名称是用户在`LAYOUT`部分中输入的名称。该函数需要有一个`column`参数，该函数代表将其添加到WidgetStack的元素。对于元素周围的填充/内边距，请使用全局`padding`变量作为`baseline`。
* __遵从条件:__ 如果小部件项目需要显示文本，则应使用`provideText`函数以及`textFormat`对象中的值。如果你愿意，可以使用现有值，也可以增加其他值。
* __遵从条件:__ 如果小部件项目需要显示诸如标签之类的预定义字符串，则必须在`localizedText`对象中定义它们。这可以让用户轻松地将文本翻译成他们喜欢的语言。
* __可选条件:__ 允许用户选择窗口小部件项目的显示方式来设置对象。使用标题注释和解释每个设置或设置组的注释来匹配` ITEM SETTINGS`部分中的现有格式。经过深思熟虑且功能强大的设置当然是最好的。
* __可选条件:__ 用于存储项目所需的结构化数据的变量。名称应为一个单词，后接`Data`。例如：`weatherData`。与其他数据变量（`eventData`，`locationData`等）一起声明。确保没有重复于现有的数据变量包含项所需的信息。
* __遵从条件:__ 如果小部件项目使用数据变量，则需要设置功能。名称应为` setup`，后接一个单词。例如：`setupWeather`。如果您使用的是设置功能，则项目函数应检查数据是否为空，如果是，则运行设置功能。例如：`if (!weatherData) { await setupWeather() }`。如果需要，这是允许其他窗口小部件项目使用提供的数据。例如，`current`和`future`天气项目都使用` weatherData`，因此它们都检查此变量并在需要时运行该设置。

## FAQ

__使用须知:__无论是Github链接还是js文本，都必须导入到app（scriptable）中，Gituhub可以直接找到js文件格式后进行复制粘贴。

* __使用前必要条件:__ 请确认是否给Scriptable获取定位信息的权限，如果之前有禁止的，请在 _系统-设置-隐私-定位服务-Scriptable_ 重新授权。

* __首次运行出错:__ 请检查你的API是否填写，并是否填写格式正确。

* __透明背景:__ 先使用制作“透明背景”的脚本，选择你的屏幕空白页的截图来制作，需要小号就选择小号的，中号就选择中号的，然后选择位置，是你接下来想放置widget的位置，上、中、下，不要搞错了，不然就会出现背景图对不上，不能“透明”的问题，然后脚本运行到最后选择【导出到相册】，然后重新运行需要使用“透明”背景的脚本（script）选择刚刚制作好的“透明”背景图（其他widget脚本方法类似）

* __怎么更换背景图:__ 在`Weather- Cal.js` 文件中，打开编辑它，找到`const forceImageUpdate = false`这一行，更改为`true`，如果不想下次运行重新选择背景图，则再次编辑更改为`false`，代码中有注释的，麻烦仔细看看!

* __刷新频率:__ 觉得有点耗电，是否能修改刷新频次来解决，答案是，__不能__。

* __天气显示不正确:__ 解决方法：[点击这里](https://openweathermap.org/ )注册账户，并且邮箱认证成功后，网站上的API密钥可直接生效使用。找到脚本中的 [API key]( https://home.openweathermap.org/api_keys)   /  [City ID]( https://openweathermap.org/city)

  ```js
  const apiKey = "API KEY 填入引号内！！！！"
  ```

- __怎么添加/删除显示的内容:__

  ```js
  |-----------------------------|
  | greeting     |      100     |
  | date         |      current | 
  | battery      |      sunrise |
  |-----------------------------|
  | events       |       future |
  |-----------------------------|
  
  const items = [
    
    row,//这个是第一行
    
      column,//这个是列，这列归属于第一行
      greeting, //这个是问候语内容
      date,//这个是日期内容
      battery,//这个是电池内容
  
      column(100),//这个是列，这列归属于第一行，（100）代表列宽
      right,//这个代表向右对齐，不设置则默认向左对齐
      current,//这个是当前天气内容
      sunrise,//这个是日出/落内容
      
    row,//这个是第二行
    
      column,//这个是列，归属于第二行
      left,//这个是向左对齐
      events,//这个是日程事件内容
  
      column(100),//这个是列，这列归属于第二行，（100）代表列宽
      right,//这代表向右对齐，不设置则默认向左对齐
      future,//这个是未来天气
    
  ]
  ```

- __间距不正确:__ 找到相对应的`padding`进行修改

  ```js
  |-----------------------------|
  | greeting     |      100     |
  | date         |      current | 
  | battery      |      sunrise |
  |-----------------------------|
  | events       |       future |
  |-----------------------------|
  
  例如：问候语与日期之间的间距太大了，则可以修改"greetingStack"的下内边距
  
  "greetingStack.setPadding(padding, padding, padding, padding)"
  
  一个"padding=5"的间距（默认），四个padding值依次是"上，左，下，右"，要修改"greetingStack"的下内边距则修改第三个padding——"greetingStack.setPadding(padding, padding, padding/2, padding)"，可以直接除以2，或者直接填了一个数值。
  
  同样，如果觉得修改到最小0，还是觉得间距太大了，则可以修改日期的上内边距，同样找到"dateStack"修改第一个padding。
  ```

- __天气小组件延伸脚本-诗词获取:__，***左列增加诗词获取***  可直接下载脚本，重新运行脚本即可。

  链接：https://pan.baidu.com/s/1_zQIUtiRj9yCQSNuVvcNbg 

  提取码：6vyL

  感谢群友@阿哲，提供。
