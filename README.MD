Java大作业：开发自己的文本编辑器—Editor
----
# 1 需求分析
## 1.1 基本要求
设计类似于Windows Notepad（记事本）文本编辑器的程序，可以创建，编辑和保存文本格式的文件。

## 1.2 提高要求
- 在文本编辑时实现**复制/粘贴**功能 （这个功能是系统提供的，无需自己实现）
- 在文本中**搜索/替换**字符（字符串）；
- 编辑Java或C/C++ 源程序时**语法**关键字**加亮**；
- 可打开并对**二进制格式的文件**进行编辑和保存；
- 在文本中以字符串形式**插入当前的日期/时间**。

## 1.3 老师没要求，但是文本编辑器需要的其他功能
- 设置字体
- 在状态栏上显示字数、行号列号、编码等
- 识别文件编码
- 做一个右键菜单（复制粘贴、搜索、全选等）

# 2 开发准备
## 2.1开发环境
- NetBeans 8.2
- JDK 1.8
- Github 协作

## 2.2开发参考
[写自己的文本编辑器(一): 高亮关键字](http://www.cppblog.com/biao/archive/2010/02/05/107292.html)

# 3 基本功能的开发
## 3.1 基本界面
基本界面从上到下依次设计为：

*为了方便起见，今后我们使用“ 控件简单描述（变量名称）”的形式来定义一个控件。*

- 菜单栏（menuBar）
- 工具栏 （toolBar）
- 文本区域（textArea）
- 状态栏（statusBar）

为了实现代码高亮的效果，我们应该使用textPane控件

>Java中提供的多行文本编辑器有: JTextComponent, JTextArea, JTextPane, JEditorPane等, 都可以使用. 但是因为语法着色中文本要使用多种风格的样式, 所以这些文本编辑器的document要使用StyledDocument.  
JTextArea使用的是PlainDocument, 此document不能进行多种格式的着色.  
JTextPane, JEditorPane使用的是StyledDocument, 默认就可以使用.  
为了实现语法着色, 可以继承自DefaultStyledDocument, 设置其为这些文本编辑器的documet, 或者也可以直接使用JTextPane, JEditorPane来做. 为了方便, 这里就直接使用JTextPane了.  

在此处我们先把程序框架搭好，功能以后再实现。

## 3.2 保存和读取功能
### 3.2.1 打开文件（Open）
我们在菜单中，添加一个目录：File（menuFile），目录下添加一个菜单项：打开（menuFileOpen），设置快捷键为Ctrl+O

双击该菜单项，添加以下代码

```java
private void menuFileOpenActionPerformed(java.awt.event.ActionEvent evt)                                             
    {                                                 
        JFileChooser fileChooser=new JFileChooser();
        int i=fileChooser.showOpenDialog(null);
        if(i!= JFileChooser.APPROVE_OPTION) return;  //如果用户没有选择文件
        currentFile=fileChooser.getSelectedFile();
        try (BufferedReader reader=Files.newBufferedReader(currentFile.toPath()))
         {
                    int ch;
                    StringBuffer str=new StringBuffer();
                    while ((ch=reader.read())!=-1)
                     {
                         str.append((char)ch);
                     }
                    textArea.setText(str.toString());
                    reader.close();
         }
         catch (IOException x)
         {
                    JOptionPane.showMessageDialog(this, x.toString(),"打开失败", JOptionPane.ERROR_MESSAGE);
          }
    }           
```

在这里，我们使用BufferedReader来高效地读取文件，它具有缓冲功能。

但是，读取的文件不能太大（几M以上），因为这是Java。

[关于Java中对话框的使用](http://blog.csdn.net/qq_34944851/article/details/51613810)

`JOptionPane.showMessageDialog(this, x.toString(),"打开失败", JOptionPane.ERROR_MESSAGE);`

### 3.2.2 保存文件（Save）
在菜单File目录下添加一个菜单项：保存（menuFileSave），以实现基本保存功能，设置快捷键为Ctrl+S

保存文件需要判断两种情况
> 用户打开的某一个文件  
用户要新建一个文件

```java
    private void menuFileSaveActionPerformed(java.awt.event.ActionEvent evt)                                             
    {                                                 
        if (currentFile==null)  //如果是新建的文件
        {
            JFileChooser fileChooser=new JFileChooser();
            int i=fileChooser.showSaveDialog(null);
            if(i!= JFileChooser.APPROVE_OPTION) return;  //如果用户没有选择文件
            currentFile=fileChooser.getSelectedFile();
        }
        try (BufferedWriter writer=Files.newBufferedWriter(currentFile.toPath()))
        {
            writer.write(textArea.getText());
            writer.close();
        }
        catch (IOException x)
        {
            JOptionPane.showMessageDialog(this, x.toString(),"保存失败", JOptionPane.ERROR_MESSAGE);
        }
    }                                         
```

### 3.2.3 另存为（SaveAs）
在菜单File目录下添加一个菜单项：另存为（menuFileSaveas）

代码：

```java
private void menuFileSaveasActionPerformed(java.awt.event.ActionEvent evt)                                               
    {                                                   
        JFileChooser fileChooser=new JFileChooser();
        int i=fileChooser.showSaveDialog(null);
        if(i!= JFileChooser.APPROVE_OPTION) return;  //如果用户没有选择文件
        File newFile=fileChooser.getSelectedFile();
        try (BufferedWriter writer=Files.newBufferedWriter(newFile.toPath()))
        {
            writer.write(textArea.getText());
            writer.close();
        }
        catch (IOException x)
        {
            JOptionPane.showMessageDialog(this, x.toString(),"保存失败", JOptionPane.ERROR_MESSAGE);
        }
    }
```

2017年6月6日 2017-06-06 23:05:43
