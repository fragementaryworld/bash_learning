## <font color=gray>Catalog</font>
[TOC]

---
### 正则表达式RE
#### 基本正则表达式元字符
* **^** : 行首定位符
* **$** : 行尾定位符
* **.** : 匹配单个字符
* **\*** : 匹配前导符0到多次
* **.\*** : 匹配任意多个字符
* **[],[ - ],[ ^ ]** : 匹配指定范围内一个字符
* **\\** 转义字符
* **\\<** : 词首定位符,该位置无字母或数字，只有特殊符号
* **\\>** : 词尾定义符 `grep '\<root\> /etc/passwd'`
* **\\(..\\)** : 匹配稍后使用的字符的标签 `%s/\(172.168.1.\)1/\15/g`,将全文ip为172.168.1.1替换为172.168.1.5
* **x\\{m\\}** : x字符出现m次
* **x\\{m,\\}** : x字符出现m次以上 
* **x\\{m,n\\}** : x字符出现m到n次 
#### 扩展正则表达式元字符
* **+** : 匹配前导符一次或多次
* **?** : 匹配前导符一次
* **|** : 匹配a或b `love|hate`
* **()** : 组字符  `lov(e|h)ate`
* **()\1\2** : 标签匹配字符 `(love)able\1er`
* **x{m}** : x字符出现m次
* **x{m,}** : x字符出现m次以上 
* **x{m,n}** : x字符出现m到n次 

---
### grep family
#### grep
在文件中全局查找指定的正则表达式，并打印所有包含该表达式的行
***grep [option] 'pattern' filename1 filename2***
> 找到: grep返回值为0
> 未找到: grep返回值为1
> 找不到指定文件: grep返回值为2

**grep中另外支持的元字符**
* **\\w** : 所有字母与数字
* **\\W** : 所有字母与数字之外的字符
* **\\b** : 词边界 `\broot\b`

**option**
* -q,--quiet,--silent
* -i,--ignore-case
* -v,--inver-match 反向查找
* -R，-r,--recurve  递归针对目录
* -B,--before-context=NUM 打印匹配内容前NUM行
* -A,--after-context=NUM 打印匹配内容后NUM行
* -C,--context=NUM 打印匹配内容前后NUM行

#### egrep 
扩展的grep,支持扩展的正则表达式元字符，而grep不支持

#### fgrep
固定的grep，按字面解释所有字符,不支持元字符

### sed
sed是一种在线的、非交互的编辑器，每次处理一行。处理时，把当前处理行储存在临时缓冲区中，称为"模式空间"(pattern space)，接着用sed命令处理缓冲区的内容，处理完成后，把缓冲区的内容送往屏幕。接着处理下一行，不断重复，直至文件末尾，文件内容不变
***`sed [options] 'command' file`***
***`sed [options] -f scriptfile file`***
> sed 不管是否找到指定模式，返回值都为0，只有当语法错误时，返回值不为0
> `sed -r` 支持扩展元字符
> `sed -n` silent

**sed基本用法**
```bash
sed -r '' /etc/passwd
sed -r '4,7p' /etc/passwd
sed -rn '4,7p' /etc/passwd
sed -r 's/root/alice/gi' /etc/passwd  (ignore-case)
sed -r 's#root#alice#gi' /etc/passwd
sed -r '/root/d' /etc/passwd
sed -r '\#root#d' /etc/passwd
```