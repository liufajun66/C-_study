## 使用WinForm实现文件列表

![image-20240802211000423](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20240802211000423.png)

## 功能一：使用Panel进行控件分区

1. 从工具箱中选出两个Panel，放置Form窗体上。设置Dock属性即可。

## 功能二：实现内边距

把ListView放到Pannel中，要求有内边距

1. 在Winform中拖拽ListView在合适的位置后，使用Anchor属性，设置Left,Top,Right,Bottom。即可固定ListView控件。当窗体
2. 给ListView控件的Dock属性，设置为fill，然后给Panel修改padding，即可实现内边距

## 功能三：实现列表表头

```c#

listView1.Columns.Add("文件名", 200, HorizontalAlignment.Left);
listView1.Columns.Add("修改时间", 200, HorizontalAlignment.Center);
listView1.Columns.Add("类型", 200, HorizontalAlignment.Center);
listView1.Columns.Add("文件大小", 200, HorizontalAlignment.Center);
```

## 功能四：点击按钮，获取指定路径下的文件夹和文件

```c#
 private void btnSelectDirectory_Click(object sender, EventArgs e)
 {
     // 1. 创建一个文件夹窗体对话框
     FolderBrowserDialog dialog = new FolderBrowserDialog();
     // 2. 关闭创建新文件的按钮
     dialog.ShowNewFolderButton = false;
     DialogResult result = dialog.ShowDialog();
     if (result == DialogResult.OK)
     {
         txtUrl.Text = dialog.SelectedPath;  // 把选择的文件夹路径显示
         LoadDirectory();
     }
 }

 private void LoadDirectory()
 {
     // 清空
     listView1.Items.Clear();

     DirectoryInfo directoryInfo = new DirectoryInfo(txtUrl.Text);
     // 第一步先操作文件夹
     DirectoryInfo[] dires =  directoryInfo.GetDirectories();
     for (int i = 0; i < dires.Length; i ++)
     {
         ListViewItem item = new ListViewItem(dires[i].Name);

         // 实现：跳过隐藏文件夹和系统文件夹 
         if ((dires[i].Attributes & FileAttributes.Hidden) > 0) continue;
         if ((dires[i].Attributes & FileAttributes.System) > 0) continue;

         string lastWriteTime = dires[i].LastWriteTime.ToString("yyyy/MM/dd hh:mm");
         string fileType = GetFileType(dires[i].Extension);
         string size = "";
         item.SubItems.Add(lastWriteTime);
         item.SubItems.Add(fileType);
         item.SubItems.Add(size);
         listView1.Items.Add(item);
     }

     // 第二步：添加文件夹中的文件
     FileInfo[] files =  directoryInfo.GetFiles();
     Array.ForEach(files, item =>
     {
         ListViewItem file = new ListViewItem(item.Name);
         
         string lastWriteTime = item.LastWriteTime.ToString("yyyy/MM/dd hh:mm");
         string fileType = GetFileType(item.Extension);
         string size = Math.Ceiling(item.Length / 1024M).ToString("N0") + " KB";
         file.SubItems.Add(lastWriteTime);
         file.SubItems.Add(fileType);
         file.SubItems.Add(size);
         listView1.Items.Add(file);
     });
 }

 private string GetFileType(string extension)
 {
     if (extension == ".jpg")
     {
         return "JPG";
     } 
     if (extension == ".png")
     {
         return "PNG";
     } 
     if (extension == ".txt")
     {
         return "文本文件";
     } 
     if (extension == ".md")
     {
         return "MD文件";
     }
     return "未知文件名";
 }
```



## 功能五：筛选掉所有的隐藏文件夹

```c#	
public bool IsHideDirectory(DirectoryInfo dire) {
    if ((dire.Attributes & FileAttributes.Hide) > 0) {
        return true;
    } else {
        return false;
    }
}
```

## 注意点：

1. 查找父控件：找到当前控件的父控件，点击ESC按键，即可找到父控件。
2. 多个Panel面板设置区域大小时，有可能一个区域大的覆盖一个区域小的。
   + 解决方案1：删除Panel，重新拉去Panel布局
   + 解决方案2：在xxForm
3. 继承关系
   1. 在窗体设置字体后，窗体内的控件都可以继承
   2. 字体、字体颜色、背景都是有继承性的
4. ListView中子项的嵌套关系如下
   + ListView ListView1 = new ListView(); 创建一个面板        ListView1.Items.Add() 添加一项
   + ListViewItem item = new ListViewItem();  创建一个项   
   + ListViewItem.SubItems.Add(); 项的子项





