# Zoo WinDBG调试脚本
    通过当前脚本，可以在不开启WinDBG的情况下，仅通过一系列的python 脚本和WinDBG命令来对WinDBG做批量调试
    用这个库来调试和直接写WinDBG脚本来调试的区别是，如果直接写WinDBG脚本来调试的话，很多判断需要用WinDBG脚本命令来判断
      比如 判断寄存器的值是多少，根据不同的值，取不同的地址来做计算，并准备下一步工作
      如果使用WinDBG脚本，那么就需要这样:
          .if (@eax=5) { .echo XXXXX; ...... } .else { .echo YYYYY; ...... } ; gc
      如果用此框架，那么就容易多了，可以直接用python命令来算
          if GetEax(last) == 5:
              print("XXXXXX")
          else:
              print("YYYYYY")
          # 最后保存内容，继续执行
      WinDBG最大的问题不是需要用命令行来调试，而是WinDBG的命令实在太不亲民
      不过，如果一定要在WinDBG脚本里面来做的话，其实也正常，毕竟通过交互式的Python来做的话，
      到底还是需要提前获取一下文件内容。

### 目前最简单的使用方法是，
##### 1：配置当前目录下的 config.ini 文件，符号路径可以不填，调试器路径一定要填，否则还调个鸡毛啊
##### 2：安装python3
##### 3：安装相关包，缺啥补啥
##### 4：输入【python3 DebugMain.py --cmd -c "!analyze -v" --defshow -d D:/123.dmp】
#####    含义是，启动当前脚本，执行 cmd 命令，功能是输出 D:/123.dmp 的调试信息，然后默认展示，最后弹出个 notepad 出来

# 目前支持：
###### 第一部分，命令参数，必须存在
    # 命令参数，全部互斥，决定执行哪些操作
    parser.add_option("--address", dest="Address", action="store_true", help="address命令内存分析")
    parser.add_option("--heap", dest="Heap", action="store_true", help="heap命令内存分析")
    parser.add_option("--umdh", dest="Umdh", action="store_true", help="Umdh工具返回信息处理")
    parser.add_option("--analyze", dest="Analyze", action="store_true", help="dump文件自动分析")
    parser.add_option("--callstack", dest="Callstack", action="store_true", help="dump文件自动分析")
    parser.add_option("--errline", dest="Errline", action="store_true", help="自动获取导致崩溃的文件以及行号，这个参数使用要注意，因为不是所有dump都能取到错误行号，很可能没有输出")
    parser.add_option("--scriptdebug", dest="ScriptDebug", action="store_true", help="通过脚本来调试一个dump，脚本")
    parser.add_option("--cmdfile", dest="CmdFile", action="store_true", help="命令文件模式，输入一个命令文件，执行此文件内命令脚本")
    parser.add_option("--cmd", dest="Cmd", action="store_true", help="命令模式，输入一行命令，会执行此命令，支持多条")
   
###### 第二部分功能参数，配合命令参数存在
    # 功能参数，可以同时存在
    parser.add_option("--muldump", dest="MulDump", action="store_true", help="判断是否有多个dump要执行，如果是的话，则-d参数可以为一个dump列表文件，内部每一行为一个dump，也可以为一个目录，目录里面存放的.dmp后缀的文件都会被当成dump文件")
    parser.add_option("--defshow", dest="DefaultShow", action="store_true", help="默认展示最终结果，使用默认的编辑器")
    parser.add_option("--answer", dest="Answer", action="store_true", help="只保留最终结果，不保留每一步过程记录")
    parser.add_option("-p", "--path", dest="Path", type="string", help="如果需要指定WinDBG路径")
    parser.add_option("-d", "--dump", dest="Dump", type="string", help="输入需要的dump文件")
    parser.add_option("-s", "--symbol", dest="Symbol", type="string", help="如果需要指定符号路径")
    parser.add_option("-c", "--cmdline", dest="CmdLine", type="string", help="输入的命令")
    parser.add_option("--scriptfile", dest="ScriptFile", type="string", help="输入需要的脚本文件")
    parser.add_option("--infile", dest="InFile", type="string", help="输入需要的普通文件")
    parser.add_option("--indir", dest="InDir", type="string", help="输入需要的目录")
    parser.add_option("--outfile", dest="OutFile", type="string", help="输入需要的普通文件")
    parser.add_option("--outdir", dest="OutDir", type="string", help="输入需要的目录")

###### 结果格式
    1/4                 : D:\dump\2.1\dump1.dmp  --┐
                                                   └-->  C:\Users\CF\AppData\Local\Temp\tmp_wsq8vj2
    2/4                 : D:\dump\2.1\dump2.dmp  --┐
                                                   └-->  C:\Users\CF\AppData\Local\Temp\tmplpdbujgz
    3/4                 : D:\dump\2.1\dump3.dmp  --┐
                                                   └-->  C:\Users\CF\AppData\Local\Temp\tmpdfudkg2c
    4/4                 : D:\dump\2.1\dump4.dmp  --┐
                                                   └-->  C:\Users\CF\AppData\Local\Temp\tmpvkjyt9_3



# 开发者

### NemesisZoo

### QQ：276793422

### 有问题联系我

##### 共享协议，
###### 1：没那么多废话，随便拿，拿走带上我的名字就行，毕竟这玩意我还想再改改，
###### 2：因为不完善，所以出了问题我不负责，其实也没啥太大问题，最多就是调试器跑飞了，病毒执行了，但是我这还没出过这种情况
    

### 帮助
#### 1：调试多个dump，位于“D:\dump\4\dump_1603817706”目录中，只输出检查信息，输出结果放到“D:\dump\4\dump_1603817706\analyze”目录中
####    python3 DebugMain.py --muldump --analyze -d D:\dump\4\dump_1603817706 --outdir D:\dump\4\dump_1603817706\analyze

#### 2：使用脚本调试一个dump，脚本文件是 D:\simple1.py，dump 文件是 D:\dump\3\MEMORY\MEMORY.DMP，调试完成直接显示
####    python DebugMain.py --scriptdebug --defshow -d D:\dump\3\MEMORY\MEMORY.DMP --scriptfile D:\simple1.py



















