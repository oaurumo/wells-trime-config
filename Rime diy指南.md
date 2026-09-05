Rime diy指南
必须了解一切/必备知识
建议您在定制 Rime 输入法之前，先了解 Rime 输入法的基本概念、Rime 中的数据文件分布及其功能等基础知识。

[[必知必会|RimeWithSchemata]]

重新布署的操作方法
【中州韵】点击状态栏上的 ⟲ (Deploy) 按钮。或者：如果找不到状态栏，可以在终端输入以下命令以触发自动部署：

touch ~/.config/ibus/rime/; ibus restart
【小狼毫】开始菜单→输入法小狼毫→重新安装；当托盘图标被激活时，右键点击“重新安装”

在系统语言文本菜单中选择“重新部署”

对设置的修改在重新部署后才会生效。将新的输入方案转换为输出结果需要一些时间，在此期间如果无法输出中文，请稍等片刻。

如果部署完成后，仍然可以通过按 Ctrl+`来调用方案菜单，但输入方案后仍然无法正常使用，可能是输入的方案未能成功部署。请参照[[查看日志文件|RimeWithSchemata#关于调试]](https)来定位问题所在。

查阅 DIY 食谱集
我们已经将一些关于 Rime 应用程序的常见问题、解决方法以及相关链接整理在下面的[DIY 教程集]中了。

设置项快速查阅手册
《雪齋的文档》全面而详细地解释了各种输入方案以及词典中各个设置项的含义和用法。

定制指南
Rime 的输入方案将 Rime 的输入方式整理成了一种完善且易于分发的格式。不过，并不一定需要创建新的输入方案就能改变 Rime 的行为。

当用户需要对 Rime 中的各种设置进行微调时，最直接但也不是完全正确的做法就是编辑用户资料文件夹中的 .yaml 文件。

这种方法的确存在弊端：

當 Rime 软件进行升级时，相关的配置文件和默认输入方案也会随之更新。用户编辑过的文档内容将被替换成更高版本的格式，因此所做的调整也会随之丢失。
即使在软件升级后手动恢复经过编辑的文件，由于配置文件的其他部分没有更新，这些升级带来的功能和修复措施仍然无法被恢复。
因此，对于随 Rime 一起发布的设置文件以及默认输入方案，建议采用以下定制方法：

创建一个文件名的主部分（在“.”之前），该主部分与要定制的文件相同；其次级扩展名（在“.yaml”之前）则是一个由 .custom 表示的定制文档。

yaml 格式
patch:
  "一級設定項/二級設定項/三級設定項": 新的設定值
  "另一個設定項": 新的設定值
  "再一個設定項": 新的設定值
  "含列表的設定項/@n": 列表第n個元素新的設定值，從0開始計數
  "含列表的設定項/@last": 列表最後一個元素新的設定值
  "含列表的設定項/@before 0": 在列表第一個元素之前插入新的設定值（不建議在補靪中使用）
  "含列表的設定項/@after last": 在列表最後一個元素之後插入新的設定值（不建議在補靪中使用）
  "含列表的設定項/@next": 在列表最後一個元素之後插入新的設定值（不建議在補靪中使用）
  "含列表的設定項/+": 與列表合併的設定值（必須爲列表）
  "含字典的設定項/+": 與字典合併的設定值（必須爲字典，注意YAML字典的無序性）
就是这么回事： patch 定义了一组“补靪”操作，这些操作以源文件中的设置为基础，会添加新的设置项，或者用新的设置值替换现有的设置项的值。

不明白？那就看我演示吧。

第一例：每页的候选数量都是定制的
在 Rime 中，默认情况下，每页最多显示 5 个候选项，而实际可显示的候选项数量为 1 到 9 个（某些特定版本的 Rime 支持显示 10 个候选项）。

将每页显示候选数的默认值设置为 9，在用户目录中创建文件 default.custom.yaml 时请使用此值。

yaml 格式
patch:
  "menu/page_size": 9
重新设置后，更改将生效。


如果只需要将“独孤一个”输入方案的每页候选数量设置为 9，以“朙月拼音”为例，在文件 luna_pinyin.custom.yaml 中写入相同的内容，然后重新部署即可生效。

注：请参考前文“重新布置的操作方法”★

一种定制的点号符号
有些用户习惯使用 / 键来输入标点符号“、”。

仍以【朙月拼音】为例，输入方案中包含以下设置：

yaml 格式
# luna_pinyin.schema.yaml
# ...

punctuator:
  import_preset: default
解释：

punctuator 是 Rime 中负责转换标点符号的组件。该组件会从配置文件中读取标点符号的映射表，从而决定需要进行哪些转换。

punctuator/import_preset 也就是说，该方案需要继承一组预设的符号映射表，并且需要从另一个配置文件 default.yaml 中导入这些信息。

查看 default.yaml ，确认是否存在以下符号：

yaml 格式
punctuator:
  full_shape:
    # ……其他……
    "/" : [ ／, "/", ÷ ]
    # ……其他……
  half_shape:
    # ……其他……
    "/" : [ "/", ／, ÷ ]
    # ……其他……
可以看出，按键 / 已经被指定给 "/", ／, ÷ 等一组符号了。而且，在全角模式和半角模式下，这些符号有不同的定义。

若希望让 / 键直接输出“、”符号，可以这样自定义 luna_pinyin.custom.yaml ：

yaml 格式
patch:
  punctuator/full_shape:
    "/" : "、"
  punctuator/half_shape:
    "/" : "、"
在上述输入方案设置中，我们添加了两组新值。合并后的输入方案如下所示：

yaml 格式
# luna_pinyin.schema.yaml
# ...

punctuator:
  import_preset: default
  full_shape:
    "/" : "、"
  half_shape:
    "/" : "、"
意思是：在由 default 引入的符号表上，覆盖对按键 / 的定义。

通过这种方式，既直接继承了大多数符号的默认定义，又实现了局部的个性化。

第一种方案：定制化的简化字体输出方式
注意：

如果您只需要将 Rime 的输出结果简化成简体字，只需按 Ctrl+`键，然后从菜单中选择“简体字→汉字”即可！
这个例子说明了其中的原理，以及如何通过配置文件来修改默认的字形输出方式。
《Rime》默认的词汇表使用的是传统汉字。这是因为传统汉字比简化字能提供更丰富的信息，进行“繁体→简化”的转换能够确保较高的准确性。

在 Rime 中，过滤器组件 simplifier 负责完成候选词的简繁转换工作。

yaml 格式
# luna_pinyin.schema.yaml
# ...

switches:
  - name: ascii_mode
    reset: 0
    states: [ 中文, 西文 ]
  - name: full_shape
    states: [ 半角, 全角 ]
  - name: simplification    # 轉換開關
    states: [ 漢字, 汉字 ]

engine:
  filters:
    - simplifier  # 必要組件一
    - uniquifier  # 必要組件二
以上是【朙月拼音】中关于繁简转换功能的设置说明。

在 engine/filters 中，除了 simplifier 之外，还使用了 uniquifier 。这是因为有时候，相同的候选项会被合并为同一个简化形式，例如“鐘→钟”和“鍾→钟”。 uniquifier 的作用是在 simplifier 执行转换之后，将文字相同的候选项进行合并。

该输入方案包含三种状态开关：中/西文、全/半角、繁简字。具体如下所示： switches 下的三项。

每个开关可以在两种状态之间切换—— simplifier 。 simplifier 会根据 simplification 这个开关的状态来决定是否进行简化处理。

在初始状态下，输出的是传统汉字；在[方案菜单]中，相关选项显示为“汉字→汉字”。
选择该项后，输出结果为简化汉字形式。在“方案选择”菜单中会显示“汉字→汉字”。
Rime 会记住您的选择，下次打开输入法时，会自动切换到您所选的字形。
也可以忽略上次记住的选择，在方案中重新设置初始值：将 reset 设成 0 或 1，分别选中 states 列表中的两种状态。

yaml 格式
# luna_pinyin.custom.yaml

patch:
  switches:                   # 注意縮進
    - name: ascii_mode
      reset: 0                # reset 0 的作用是當從其他輸入方案切換到本方案時，
      states: [ 中文, 西文 ]  # 重設爲指定的狀態，而不保留在前一個方案中設定的狀態。
    - name: full_shape        # 選擇輸入方案後通常需要立即輸入中文，故重設 ascii_mode = 0；
      states: [ 半角, 全角 ]  # 而全／半角則可沿用之前方案中的用法。
    - name: simplification
      reset: 1                # 增加這一行：默認啓用「繁→簡」轉換。
      states: [ 漢字, 汉字 ]
实际上，默认的输入方案中已经提供了一套“简化字”的简化字版本，名为“简化字”，旨在满足大家填写表格的需求。不过，从他的代码来看，它与前一篇文章中提到的定制版本有所不同：

yaml 格式
# luna_pinyin_simp.schema.yaml
# ...

switches:
  - name: ascii_mode
    reset: 0
    states: [ 中文, 西文 ]
  - name: full_shape
    states: [ 半角, 全角 ]
  - name: zh_simp           # 注意這裏（※1）
    reset: 1
    states: [ 漢字, 汉字 ]

simplifier:
  option_name: zh_simp      # 和這裏（※2）
前文提到， simplifier 这个组件会检查名为 simplification 的开关状态；而这一【简化版】方案则使用了另一个名称的开关 zh_simp ，如※1 处所示。同时，通过在※2 行设置 simplifier/option_name 来指定 simplifier 组件需要关注的开关名称。

何故？

还记得之前关于“全角/半角”切换方式的讨论吗？在切换模式时，如果未明确指定使用 reset 来重置切换开关，那么系统会保持之前设定的状态。

【新月拼音】等大多数方案都没有重新设置 simplification 这个选项——因为用户改变了输入编码的方式，并不意味着需要改变输出的字形。

而“简化字”这一方案则不同，它恰恰满足了改变输出字形需求的要求。当用户从“简化字”模式切换回“标准拼音”模式时，实际上是为了恢复到繁体输出模式。因此，在“简化字”模式中使用了名称独立的开关，而不是与各种输入方案共用的 simplification 开关，这样可以避免影响其他输入方案的繁简转换功能。

一种情况，默认英文输出方式。
一些用户习惯于默认使用英文输出，只有在需要输出中文内容时才进行切换。因此，我们需要在方案中重新设置状态开关的初始值。

还记得吗？我们可以通过在方案中设置 reset 参数，为某些状态开关重新设置初始值：将 reset 设置为 0 或 1，从而分别选中 states 列表中的两种状态。

我们以【朙月拼音】为例：

yaml 格式
# luna_pinyin.custom.yaml

patch:
  "switches/@0/reset": 1  #表示將 switcher 列表中的第一個元素（即 ascii_mode 開關）的初始值重設爲狀態1（即「英文」）。
第一项、定制方案菜单
在【小狼毫】方案選單設定介面上勾勾選選，就可以如此定製輸入方案列表：  

yaml  
# default.custom.yaml

patch:
  schema_list:  # 對於列表類型，現在無有辦法指定如何添加、消除或單一修改某項，於是要在定製檔中將整個列表替換！
    - schema: luna_pinyin
    - schema: cangjie5
    - schema: luna_pinyin_fluency
    - schema: luna_pinyin_simp
    - schema: my_coolest_ever_schema  # 這樣就啓用了未曾有過的高級輸入方案！其實這麼好的方案應該排在最前面哈。
無有設定介面時，又想啓用、禁用某個輸入方案，手寫這樣一份定製檔、重新佈署就好啦。  

一例、定製喚出方案選單的快捷鍵   
喚出方案選單，當然要用鍵盤。默認的快捷鍵爲 Ctrl+` 或 F4。  

不過，有些同學電腦上 Ctrl+` 與其他軟件衝突，F4 甚至本文寫作時在【鼠鬚管】中還不可用。又或者有的玩家切換頻繁，想定義到更好的鍵位。  

那麼……  

yaml  
# default.custom.yaml

patch:
  "switcher/hotkeys":  # 這個列表裏每項定義一個快捷鍵，使哪個都中
    - "Control+s"      # 添加 Ctrl+s
    - "Control+grave"  # 你看寫法並不是 Ctrl+` 而是與 IBus 一致的表示法
    - F4
按鍵定義的格式爲「修飾符甲+修飾符乙+…+按鍵名稱」，加號爲分隔符，要寫出。  

所謂修飾符，就是以下組合鍵的狀態標誌或是按鍵彈起的標誌：  

Release——按鍵被放開，而不是按下  
Shift  
Control  
Alt——Windows上 Alt+字母 會被系統優先識別爲程序菜單項的快捷鍵，當然 Alt+Tab 也不可用  
嗯，Linux 發行版還支持 Super, Meta 等組合鍵，不過最好選每個平臺都能用的啦  
按鍵的名稱，大小寫字母和數字都用他們自己表示，其他的按鍵名稱 參考這裏 这个更直观的文档 的定義，去除代碼前綴 XK_ 即是。  

一例、定製【小狼毫】字體字號   
雖與輸入方案無關，也在此列出以作參考。  

yaml  
# weasel.custom.yaml

patch:
  "style/font_face": "明兰"  # 字體名稱，從記事本等處的系統字體對話框裏能看到
  "style/font_point": 14     # 字號，只認數字的，不認「五號」、「小五」這樣的
一例、定製【小狼毫】配色方案   
註：這款配色已經在新版本的小狼毫裏預設了，做練習時，你可以將文中 starcraft 換成自己命名的標識。  

yaml  
# weasel.custom.yaml

patch:
  "style/color_scheme": starcraft    # 這項用於選中下面定義的新方案
  "preset_color_schemes/starcraft":  # 在配色方案列表裏加入標識爲 starcraft 的新方案
    name: 星際我爭霸／StarCraft
    author: Contralisk <contralisk@gmail.com>, original artwork by Blizzard Entertainment
    text_color: 0xccaa88             # 編碼行文字顏色，24位色值，用十六進制書寫方便些，順序是藍綠紅0xBBGGRR
    candidate_text_color: 0x30bb55   # 候選項文字顏色，當與文字顏色不同時指定
    back_color: 0x000000             # 底色
    border_color: 0x1010a0           # 邊框顏色，與底色相同則爲無邊框的效果
    hilited_text_color: 0xfecb96     # 高亮文字，即與當前高亮候選對應的那部份輸入碼
    hilited_back_color: 0x000000     # 設定高亮文字的底色，可起到凸顯高亮部份的作用
    hilited_candidate_text_color: 0x60ffa8  # 高亮候選項的文字顏色，要醒目！
    hilited_candidate_back_color: 0x000000  # 高亮候選項的底色，若與背景色不同就會顯出光棒
效果自己看！

也可以參照這張比較直觀的圖：  



另，此處有現成的配色方案工具供用家調配：  

http://tieba.baidu.com/p/2491103778

小狼毫：https://bennyyip.github.io/Rime-See-Me/

鼠鬚管：https://gjrobert.github.io/Rime-See-Me-squirrel/  

DIY 處方集   
已將一些定製 Rime 的常見問題、解法及定製檔鏈接收錄於此。  

建議您首先讀完《定製指南》、通曉相關原理，以正確運用這些處方。  

初始設定   
在方案選單中添加五筆、雙拼   
https://gist.github.com/2309739

倣此例，可啓用任一預設或自訂輸入方案，如【粵拼】、【注音】等。（詳解：參見前文「定製方案選單」一節）  

如果下載、自己製作了非預設的輸入方案，將源文件複製到「用戶資料夾」後，也用上面的方法將方案標識加入選單！  

修改於重新佈署後生效。  

【小狼毫】外觀設定   
上文已介紹設定字體字號、製作配色方案的方法。  

使用橫向候選欄、嵌入式編碼行：  

yaml  
# weasel.custom.yaml
patch:
  style/horizontal: true      # 候選橫排
  style/inline_preedit: true  # 內嵌編碼（僅支持TSF）
  style/display_tray_icon: true  # 顯示托盤圖標
【鼠鬚管】外觀與鍵盤設定   
鼠鬚管從 0.9.6 版本開始支持選擇配色方案，用 squirrel.custom.yaml 保存用戶的設定。  

https://gist.github.com/2290714

ibus用户： ibus_rime.custom.yaml 不包含控制配色、字體字號等外觀樣式的設定項。  

在特定程序裏關閉中文輸入   
【鼠鬚管】0.9.9 開始支持這項設定：  

在指定的應用程序中，改變輸入法的初始轉換狀態。如在  

終端 Terminal / iTerm  
代碼編輯器 MacVim  
快速啓動工具 QuickSilver / Alfred 等程序裏很少需要輸入中文，於是鼠鬚管在這些程序裏默認不開啓中文輸入。  
自定義 Mac 應用程序的初始轉換狀態，首先查看應用的 Info.plist 文件得到 該應用的 Bundle Identifier，通常是形如 com.apple.Xcode 的字符串。  

例如，要在 Xcode 裏面默認關閉中文輸入，又要在 Alfred 裏面恢復開啓中文輸入，可如此設定：  

yaml  
# example squirrel.custom.yaml
patch:
  app_options/com.apple.Xcode:
    ascii_mode: true
  app_options/com.alfredapp.Alfred: {}
註：一些版本的 Xcode 標識爲 com.apple.dt.Xcode，請注意查看 Info.plist。  

【小狼毫】0.9.16 亦開始支持這項設定。  

例如，要在 gVim 裏面默認關閉中文輸入，可如此設定：  

yaml  
# example weasel.custom.yaml
patch:
  app_options/gvim.exe:  # 程序名字全用小寫字母
    ascii_mode: true
輸入習慣   
使用Control鍵切換中西文   
https://gist.github.com/2981316

以及修改Caps Lock、左右Shift、左右Control鍵的行爲，提供三種切換方式。 詳見 Gist 代碼註釋。  

方便地輸入含數字的西文用戶名   
通常，輸入以小寫拉丁字母組成的編碼後，數字鍵的作用是選擇相應序號的候選字。  

假設我的郵箱地址是 rime123@company.com，則需要在輸入rime之後上屏或做臨時中西文切換，方可輸入數字部分。  

爲了更方便輸入我的用戶名 rime123，設置一組特例，將 rime 與其後的數字優先識別西文：  

https://gist.github.com/3076166

以方括號鍵換頁   
https://gist.github.com/2316704

添加 Mac 風格的翻頁鍵 [ ] 。這是比較直接的設定方式。下一則示例給出了一種更系統、可重用的設定方式。  

使用西文標點兼以方括號鍵換頁   
https://gist.github.com/2334409

詳見上文「使用全套西文標點」一節。  

以回車鍵清除編碼兼以分號、單引號選字   
https://gist.github.com/2390510

適合一些形碼輸入法（如五筆、鄭碼）的快手。  

關閉逐鍵提示   
table_translator 默認開啓逐鍵提示。若要只出精確匹配輸入碼的候選字，可關閉這一選項。  

以【倉頡五代】爲例：  

yaml  
# cangjie5.custom.yaml
patch:
  translator/enable_completion: false
關閉用戶詞典和字頻調整   
以【五笔86】爲例：

yaml  
# wubi86.custom.yaml
patch:
  translator/enable_user_dict: false
關閉碼表輸入法連打   
註：這個選項僅針對 table_translator，用於屏蔽倉頡、五筆中帶有太極圖章「☯」的連打詞句選項，不可作用於拼音、注音、速成等輸入方案。  

以【倉頡】爲例：  

yaml  
# cangjie5.custom.yaml
patch:
  translator/enable_sentence: false
關閉倉頡與拼音混打   
默認，給出倉頡與拼音候選的混合列表。  

如此設定，直接敲字母只認作倉頡碼，但仍可在敲 ` 之後輸入拼音：  

yaml  
# cangjie5.custom.yaml
patch:
  abc_segmentor/extra_tags: {}
空碼時按空格鍵清空輸入碼   
首先需要關閉碼表輸入法連打（參見上文），這樣才可以在打空時不出候選詞。  

然後設定（以五筆86爲例）：  

yaml  
# wubi86.custom.yaml
patch:
  translator/enable_sentence: false
  key_binder/bindings:
    - {when: has_menu, accept: space, send: space}
    - {when: composing, accept: space, send: Escape}
模糊音
【朙月拼音】模糊音定製模板   
https://gist.github.com/2320943

【明月拼音·简化字／臺灣正體／語句流】也適用， 只須將模板保存到 luna_pinyin_simp.custom.yaml 、 luna_pinyin_tw.custom.yaml 或 luna_pinyin_fluency.custom.yaml 。  

对比模糊音定製模板與【朙月拼音】方案原件， 可見模板的做法是，在 speller/algebra 原有的規則中插入了一些定義模糊音的代碼行。  

類似方案如雙拼、粵拼等可參考模板演示的方法改寫 speller/algebra 。  

【吳語】模糊音定製模板   
https://gist.github.com/2015335

編碼反查   
設定【速成】的反查碼爲粵拼   
https://gist.github.com/2944320

設定【倉頡】的反查碼爲雙拼   
https://gist.github.com/2944319

在Mac系統上輸入emoji表情   
以下配置方法已過時，新的emoji用法見 https://github.com/rime/rime-emoji  

參考 https://gist.github.com/2309739 把 `emoji` 加入輸入方案選單；   
切換到 emoji 輸入方案，即可通過拼音代碼輸入表情符號。查看符號表  

輸入 all 可以列出全部符號，符號後面的括弧裏標記其拼音代碼。  

若要直接在【朙月拼音】裏輸入表情符號，請按此文設定：  

http://gist.github.com/3705586

五筆簡入繁出   
【小狼毫】用家請到[[下載頁|Downloads]]取得「擴展方案集」。  

安裝完成後，執行輸入法設定，添加【五筆·簡入繁出】輸入方案。  

其他版本請參考這篇說明：  

https://gist.github.com/3467172

修正不對稱繁簡字   
繁→簡即時轉換比簡體轉繁體要輕鬆許多，卻也免不了個別的錯誤。  

比如這一例，「乾」字是一繁對多簡的典型。由它組成的常用詞組，opencc 都做了仔細分辨。但是遇到較生僻的詞組、專名，就比較頭疼：  

http://tieba.baidu.com/p/1909252328

活用標點創建自定義詞組   
在【朙月拼音】裏添加一些自定義文字、符號。可以按照上文設定「emoji表情」的方式爲自定義詞組創建一個專門的詞典。  

可是建立詞典稍顯繁瑣，而活用自定義標點，不失爲一個便捷的方法：  

yaml  
# luna_pinyin.custom.yaml
# 如果不需要 ` 鍵的倉頡反查拼音功能，則可利用 ` 鍵輸入自定義詞組
patch:
  recognizer/patterns/reverse_lookup:
  'punctuator/half_shape/`':
    - '佛振 <chen.sst@gmail.com>'
    - 'http://rime.github.io'
    - 上天赋予你高的智商，教你用到有用的地方。
上例 recognizer/patterns/reverse_lookup: 作用是關閉 ` 鍵的反查功能。若選用其他符號則不需要這行。又一例：  

yaml  
patch:
  'punctuator/half_shape/*': '*_*'
'punctuator/half_shape/*' 因爲字符串包含符號，最好用 單引號 括起來；儘量不用雙引號以避免符號的轉義問題。  

然而，重定義「/」「+」「=」這些符號時，因其在節點路徑中有特殊含義，無法用上面演示的路徑連寫方式。 因此對於標點符號，推薦的定製方法爲在輸入方案裏覆蓋定義 half_shape 或 full_shape 節點：  

yaml  
patch:
  punctuator/half_shape:
   '/': [ '/', '/hello', '/bye', '/* TODO */' ]
   '+': '+_+'
   '=': '=_='
