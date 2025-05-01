---
layout: post
title: "Youtube Downloader"
featured-img: QtPython
categories: [personal, Python, QT]
published: true
summary: "A python application with PyQt6 and PyTubeFix"
---
# Youtube Downloader

This is a personal project I built to explore and learn Python and Qt beyond simple scripting. While developing this tool, my focus was on deepening my understanding of class-based design, GUI development, and integrating third-party libraries in Python.

> Note: This tool is strictly for **personal use**. I only download content that is not officially available for purchase, and I recognize that this project exists in a legal gray area.

![](../assets/img/posts/Youtube%20Downloader/Youtube%20Downloader.png)

## Key Technologies & Libraries:
Python (with classes and structure)
- Qt for GUI (via PyQt6)
- [pytubefix](https://github.com/JuanBindez/pytubefix){:target="_blank"}: for handling YouTube parsing and downloads
- [ffmpeg](https://www.ffmpeg.org/){:target="_blank"}:  to combine audio, video, and subtitle streams into a single .mp4 file

## Features
The YouTube Downloader application offers several user-friendly features designed to enhance the download experience:

- Global Settings: Customize default download preferences, such as audio-only downloads and enabling captions.
- Multi-language Support: Download audio and captions in multiple languages for a more versatile media experience.
- Audio-Only Mode: Download audio as a .wav file instead of the standard .mp4 format, ideal for music or podcast downloads.
- Queueing System: Add multiple videos to a download queue, with customizable delays, perfect for downloading content overnight without needing to monitor the process.
- Playlist Support: Automatically recognize and add entire playlists to the video tab, simplifying the process for batch downloads.

This tool was designed with ease of use and flexibility in mind, allowing users to tailor their experience to their specific needs.

## What I Learned

This project allowed me to dive into several key aspects of software development, including both technical skills and best practices:

- **Qt with PyQt6**: Gained a basic understanding of Qt and PyQt6 for building GUIs, enhancing my knowledge of cross-platform application development.
- **Qt Thread Pools & Runnables**: Learned how to use thread pools and runnables to perform tasks concurrently in Qt, improving the efficiency of background tasks like downloading and processing videos.
- **Python Classes & Object-Oriented Programming**: Strengthened my skills in object-oriented design by using private and protected variables (e.g., self.__somePrivateVariable, self._SomeProtectedVariable) to encapsulate data and enhance code structure.
- **Type Hinting in Python**: Although I initially disliked dynamic typing, I embraced type hinting (variable: type) to make my code more readable and maintainable, improving overall development workflow.
- **Debugging and Extending Libraries**: Worked on debugging and enhancing the pytubefix library in Visual Studio, learning how to contribute to open-source projects and fix issues in third-party code.
- **GitHub & Merge Requests**: Familiarized myself with GitHub workflows, particularly creating merge requests from a forked repository, collaborating with the open-source community, and integrating updates into my own project.



## Reflection
This project is a bit controversial, as it falls into a legal gray area, and I do not recommend others use it (especially since I've seen individuals getting banned for misusing pytubefix). That said, it was a highly educational experience for me. I gained a deeper understanding of Python and became used to using Qt for building GUI applications. Additionally, I took on the side challenge of adding support for regionalized languages in pytubefix, which expanded the tool’s capabilities.

One of the toughest challenges I faced in this project was implementing the queue system. I wanted users to be able to remove items from the queue while it was still running, which caused a major concern: modifying the queue during iteration. Since I was running through a for loop, directly removing items could cause issues, such as skipping items or triggering an "array out of bounds" error.

To address this, I implemented a solution where callbacks are only triggered when removing an item with an index greater than the current one being processed, ensuring that the integrity of the loop was preserved and preventing any errors from occurring.

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

