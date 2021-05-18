# Archiving and Compression Tools

A file archiving tool groups a set of files into a single standalone file that we can backup to several types of media, transfer across a network, or send via email. The most frequently used archiving utility in Linux is **tar**. When an archiving utility is used along with a compression tool, it allows to reduce the disk size that is needed to store the same files and information.

#### The tar utility

**tar** bundles a group of files together into a single archive (commonly called a tar file or tarball). The name originally stood for tape archiver, but we must note that we can use this tool to archive data to any kind of writeable media (not only to tapes). Tar is normally used with a compression tool such as **gzip**, **bzip2**, or **xz** to produce a compressed tarball.

###### Basic syntax:

```shell
tar [options] [pathname ...]
```

Where **…** represents the expression used to specify which files should be acted upon.

##### Most commonly used tar commands

| **Long option**   | **Abbreviation** | **Description**                                  |
| ----------------- | ---------------- | ------------------------------------------------ |
| –create           | c                | Creates a tar archive                            |
| –concatenate      | A                | Appends tar files to an archive                  |
| –append           | r                | Appends files to the end of an archive           |
| –update           | u                | Appends files newer than copy in archive         |
| –diff or –compare | d                | Find differences between archive and file system |
| –file archive     | f                | Use archive file or device ARCHIVE               |
| –list             | t                | Lists the contents of a tarball                  |
| –extract or –get  | x                | Extracts files from an archive                   |

##### Normally used operation modifiers

| **Long option**   | **Abbreviation** | **Description**                                              |
| ----------------- | ---------------- | ------------------------------------------------------------ |
| –directory dir    | C                | Changes to directory dir before performing operations        |
| –same-permissions | p                | Preserves original permissions                               |
| –verbose          | v                | Lists all files read or extracted. When this flag is used along with –list, the file sizes, ownership, and time stamps are displayed. |
| –verify           | W                | Verifies the archive after writing it                        |
| –exclude file     | —                | Excludes file from the archive                               |
| –exclude=pattern  | X                | Exclude files, given as a PATTERN                            |
| –gzip or –gunzip  | z                | Processes an archive through gzip                            |
| –bzip2            | j                | Processes an archive through bzip2                           |
| –xz               | J                | Processes an archive through xz                              |

**Gzip** is the oldest compression tool and provides the least compression, while **bzip2** provides improved compression. In addition, **xz** is the newest but (usually) provides the best compression. This advantages of best compression come at a price: the time it takes to complete the operation, and system resources used during the process.

Normally, **tar** files compressed with these utilities have **.gz**, **.bz2**, or **.xz** extensions, respectively. 

In the following examples we will be using these files: file1, file2, file3, file4, and file5.

###### Grouping and compressing with gzip, bzip2 and xz

Group all the files in the current working directory and compress the resulting bundle with **gzip**, **bzip2**, and **xz** (please note the use of a regular expression to specify which files should be included in the bundle – this is to prevent the archiving tool to group the tarballs created in previous steps).

```
# tar czf myfiles.tar.gz file[0-9]
# tar cjf myfiles.tar.bz2 file[0-9]
# tar cJf myfile.tar.xz file[0-9]
```

###### Listing the contents of a tarball and updating / appending files to the bundle

List the contents of a tarball and display the same information as a long directory listing. Note that **update** or **append** operations cannot be applied to compressed files directly (if you need to update or append a file to a compressed tarball, you need to uncompress the tar file and update / append to it, then compress again).

```
# tar tvf [tarball]
```

Run any of the following commands:

```
# gzip -d myfiles.tar.gz	[#1] 
# bzip2 -d myfiles.tar.bz2	[#2] 
# xz -d myfiles.tar.xz 		[#3] 
```

Then

```
# tar --delete --file myfiles.tar file4 (deletes the file inside the tarball)
# tar --update --file myfiles.tar file4 (adds the updated file)
```

and

```
# gzip myfiles.tar		[ if you choose #1 above ]
# bzip2 myfiles.tar		[ if you choose #2 above ]
# xz myfiles.tar 		[ if you choose #3 above ]
```

Finally,

```
# tar tvf [tarball] #again
```

and compare the modification date and time of **file4** with the same information as shown earlier.

###### Excluding file types

Suppose you want to perform a backup of user’s **home** directories. A good sysadmin practice would be (may also be specified by company policies) to exclude all video and audio files from backups.

Maybe your first approach would be to exclude from the backup all files with an **.mp3** or **.mp4** extension (or other extensions). What if you have a clever user who can change the extension to **.txt** or **.bkp**, your approach won’t do you much good. In order to detect an audio or video file, you need to check its file type with file. The following shell script will do the job.

```
#!/bin/bash
# Pass the directory to backup as first argument.
DIR=$1
# Create the tarball and compress it. Exclude files with the MPEG string in its file type.
# -If the file type contains the string mpeg, $? (the exit status of the most recently executed command) expands to 0, and the filename is redirected to the exclude option. Otherwise, it expands to 1.
# -If $? equals 0, add the file to the list of files to be backed up.
tar X <(for i in $DIR/*; do file $i | grep -i mpeg; if [ $? -eq 0 ]; then echo $i; fi;done) -cjf backupfile.tar.bz2 $DIR/*
```

###### Restoring backups with tar preserving permissions

You can then restore the backup to the original user’s home directory (user_restore in this example), preserving permissions, with the following command.

```
# tar xjf backupfile.tar.bz2 --directory user_restore --same-permissions
```
