---
layout: post
title: "Youtube Downloader"
featured-img: QtPython
categories: [personal, Python, QT]
published: true
summary: "A python application with PyQt6 and PyTubeFix"
---
# Youtube Downloader

Youtube downloader is a personal project where I wanted to learn Qt and Python, as I had only used python for making 1 page scripts without classes during my work at Cyborn. I wanted a project that challenged me and would have relations to my interests although this tool is in a legal gray area, this is only for personal use. I only download when the products are not officially available for purchase. 


Within this project most of the heavy lifting for parsing and downloading from Youtube is done by a 3rd party library called [pytubefix](https://github.com/JuanBindez/pytubefix){:target="_blank"}. The other software that I used to make this program is [ffmpeg](https://www.ffmpeg.org/){:target="_blank"} as it's the technology that I used to recombine the audio, video and subtitle files into 1 mp4. 

![](../assets/img/posts/Youtube%20Downloader/Youtube%20Downloader.png)


## Features

My youtube downloader application has the following features:

- Global settings where you can default what you want to download (audio only/allowing captions etc)
- It allows downloading multiple languages for audio and the captions
- It has an option to only download the audio and it will output a .wav file instead of an .mp4
- It has a queueing system with delays if you wanted to download multiple video's overnight
- It'll also recognize playlists and automatically add all of them in the video tab 


## What I learned making this project

In this project I learned the following: 
- The basics of Qt with PyQt6
- Qt threadpools with using runnables  
- Making classes in python with using private (self.__somePrivateVariable) and protected variables (self._SomeProtectedVariable) 
- Defining variables with types (which was one of the things I most dislike about non-typed languages)
- Debugging python library code to fix pytubefix and add new features to it in visual studio 
- Making a merge request from a forked repository in github 


## Reflection

This is a bit of a controversial project as this is a bit in a gray area legal wise and I don't recommend you use it (as i've seen people getting banned for abusing pytubefix) but it was very educational to me as I did learn a few more skills (a better understanding of python and using Qt) and added support for regionalized languages on pytubefix as a side quest. 

The hardest part for me in this entire project was how to handle the queue, as I wanted the user to be able to remove items from the queue while it was running. This is because I'm running through a for loop and would have given certain issues if you removed items from the array if they're lower or equal to the current index. That would skip an item from the array or give an array out of bounce issue. I did manage to make sure that the problem would never arise by having callbacks only when we're removing an item where it's index is bigger then the current index.

```python 
    def _RemoveSelectedItem(self): 
        selectedItems: list[QListWidgetItem] = self._QueueList.selectedItems()
        for item in selectedItems: 
            index :int = self._QueueList.row(item)
            
            # when the queue has started we want to check if we need to cancel the current item 
            if self._DownloadRunnable != None and self._DownloadRunnable.IsRunning(): 
                itemStatus = self._Queue[index].Status
                # when the item is waiting to be processed it's safe to remove it from the list,
                # when the item is done, we don't need to care because it's already passed
                if itemStatus == Status.WaitingToBeProcessed: 
                    self._DownloadRunnable.GetDownloadCallbackSignals().OnExternalRemovedToQueue.emit(self._Queue[index].VideoSettings)
                
                # when the item is processing, we should ask the user if they're sure if 
                # they want to cancel the running item
                if itemStatus == Status.Processing:
                    returnValue = self.CreateQuestionDialog("Are you sure?", "Do you want to cancel the item that is currently being processed?")
                    # when the user aborts, we cancel the operation 
                    # if returnValue == QMessageBox.StandardButton.No or returnValue == QMessageBox.StandardButton.Abort: 
                    #     return 
                    self._DownloadRunnable.GetDownloadCallbackSignals().OnCurrentItemRemovedToQueue.emit(self._Queue[index].VideoSettings)
                    
            self._QueueList.takeItem(index)
            self._Queue.pop(index)
```
Something else that I struggled with and didn't find a proper solution for at this moment was canceling the current item from the queue as I didn't find something similar as a cancelation token that I would normally use in C#.  

