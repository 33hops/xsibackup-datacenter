Get a 60 GB limit version at https://33hops.com/xsibackup-datacenter-vmware-esxi-backup.html

INSTALLATION

1 - Extract the contents of the .zip package to any persistent path 
in your ESXi server, i.e: /vmfs/volumes/datastore1/xsi-dir

2 - Change to the installation directory and apply permissions to 
the xsibackup file and the bin folder.

	# chmod -R 0700 xsibackup bin

3 - Run xsibackup

	#./xsibackup --help
		

TRIAL VERSION LIMITATIONS

Trial version is limited to 6 hours of use after each host reboot. 
You may reboot your host and use the program for 6 more hours and 
so on.

Trial versions are limited to 20 GB or 40 GB depending on the 
type of trial license. 

All other actions are fully enabled: --check, --restore, --repair, 
--info, --prune, etc...

You may use a trial version as a fully enabled server in any SSH 
enabled host. It's only the client that is limited by the the trial 
version license limits.

ABOUT TRANSFER SPEED.

(c)XSIBackup Datacenter has been tested on commodity hardware and 
on many different chipsets. It is a low level piece of software 
aimed at reaching the hardware theoretical limit, nonetheless it 
can't go beyond that limit.

If you want to achieve close to gigabit LAN speeds (70-80 MB/s), 
in effective data throughput, you need hardware which is capable 
of reaching that speed and sustain it for a long period of time. 
We recommend that you always use Intel NICs. There may be other 
brands that manufacture quality NICs, but Intel NICs are the only 
ones from our experience that always offer maximum performance.

We'll put it the other way around. If you use Realtek NICs and 
TP-Link switches, you can't expect to achieve more than 15-20 MB/s 
of effective throughput, it doesn't have anything to do with ESXi 
being limited in speed or XSIBackup not being fast enough, it's 
your network hardware.

You can buy decent gigabit switches al bargain prices from 
manufacturers like Microtik. If you can afford HP Pro-curve or Cisco 
great, but you don't really need to spend that much.

In regards to the above mentioned manufacturers (Realtek and TP-Link) 
don't get us wrong, they manufacture awesome hardware for its cost. We 
use those brands, but we use them to browse the Internet and read 
e-mails, not to transfer terabyte virtual disks. 

DESCRIPTION:

©XSIBackup-Datacenter is a software solution developed in plain C, having the GLibC library as its main dependency. It follows the same basic design principles of previous XSIBackup releases, namely: an efficient backup and replication tool that can be used directly in any ESXi or Linux host. 
As per the time of writing these words ©XSIBackup-Datacenter can replicate & backup Virtual Machines hosted in ESXi servers just like ©XSIBackup-Free and ©XSIBackup-Pro do and can also backup & replicate file structures in Linux servers both as replicas or as backups to deduplicated repositories.
Both operations may be performed to a locally accessible file system (any accessible disk or share: NFS, iSCSI, SMB…) or over IP. The nomenclature employed for IP transfers is:

user@[FQDN or IP]:Port:/path/in/remote/system

In the future we will extend ©XSIBackup-Datacenter functionality to operate on different virtualization platforms like XEN or KVM.
©XSIBackup-Datacenter is capable of performing two kind of operations on data: 

A/ Replicas.
B/ Deduplicated  backups.

Both of them are performed by using a simple algorithm that compares data present on the host being backed up against the data eventually already present on the remote volume.
©XSIBackup-Datacenter first detects wholes in virtual disks and jumps over them, it can also detect zeroed zones in real data and jump over them as well. Only real data present on disk is actually processed.
The SHA-1 checksum algorithm is used to compare chunks of data and decide whether the block being processed must be sent to the target folder or it is already present on the other side.
When zero awareness is combined with SHA-1 differential algorithm, maximum speed is reached, that is on subsequent data operations to the first run which obviously must process all non zero data.

©XSIBackup-Datacenter downloads data definitions stored on the remote side, so that all comparison operations of the XSIBackup algorithm are performed locally. Thus, all data stored remotely must not be modified in between backup cycles.
In case of backups, which are always performed to a deduplicated repository, you can choose to compress data by employing the acclaimed LZJB algorithm used by Solaris and ZFS. This allows to compress data as well as deduplicate it. The use of data compression is recommended (just add the 
--compression argument to your backup job) it offers some 45% compression ratio. If you are backing up to an already compressed files ystem you may remove the --compression flag and improve effective transfer speed and free your CPU from the compression load.

Over IP Operations (SSH options)

©XSIBackup-Datacenter may operate in client/server mode. When you transfer data over IP, you must invoke the xsibackup binary on the other end. If you ommit the --remote-path argument, the client will look for the binary in the /usr/bin folder of the remote host. You may as well indicate the remote path by explicitly stating the remote installation path, just like you do with Rsync.
--remote-path=/vmfs/volumes/datastore1/xsi-dir/xsibackup

©XSIBackup-Datacenter can tunnel data through SSH to a remote server. The cipher algorithm that we may use for this tunnel can greatly affect transfer speed and CPU usage.
The default set of ciphers in use is: 

aes128-ctr,aes192-ctr,aes256-ctr,aes128-cbc,3des-cbc,blowfish-cbc,cast128-cbc,aes192-cbc,aes256-cbc

The above set should work well even between distant Open SSH versions, i.e.: 5.6 => 7.5 and the other way around. Its downside is that they are not very fast, unless your CPU counts with a special set of instructions to handle this workload.

When ©XSIBackup-Datacenter detects that your local OpenSSH version is 6.5 or above, it will prepend chacha20-poly1305@openssh.com to the list of default algorithms, so that it’s used instead of AES cipher family. This will greatly improve speed due to the nature of this algorithm, created by Prof. Daniel Bernstein when compared to AES if you don’t count with some sort of hardware cryptographic co-processing help.
You can optionally enable the L switch in --options=L (Less Secure Algorithm). It will try to use 

arcfour,blowfish-cbc,aes128-ctr

This last set is comprised by deprecated algorithms, you may want to use them when you don’t need that much security in encryption, like in a controlled LAN, or when you need compatibility with older OpenSSH versions.
On addition to the above, you may pass your own preferred algorithsm to be used to cipher SSH data:

--ssh-ciphers=your-preferredalgorithm1, your-preferredalgorithm2

Folder structure

©XSIBackup-Datacenter consists in a single executable (xsibackup), plus an additional library bin/xsilib, which is only needed in case of being installed to an ESXi host. The first time it is executed ©XSIBackup-Datacenter will create a set of folders to store its logs, jobs and service files. This structure will vary depending on if you install it to an ESXi host or to a Linux server. In case of ESXi hosts the folder structure will be created under the installation directory and will be as follows:

/bin/
/bin/xsilib
/etc/
/etc/jobs
/etc/Jobs/001
/etc/Jobs/002
…
/etc/xsi
/var/
/var/log
/var/log/error.log
/var/log/xsibackup.log
/tmp/xsi

Whereas when installed to a Linux server, the folders used will be those in the Filesystem Hyerarchy Standard (FHS).
©XSIBackup-Datacenter uses temp files to store block maps, variables and different structures while  it is running, thus it depends on a /tmp folder with sufficient space to hold this data. While working with files and VMs in the order of hundreds of gigabytes to one terabyte, this files size will be in the order on some hundreds of KB. Should the files grow beyond that, even ESXi /tmp file system that is 100 MB by default should be able to handle that. In case of Linux FS that may have an arbitrary /tmp partition size, this will never be a problem even on Exabyte VMs.
A folder is created under /tmp for every running job as /tmp/xsi/PID, where PID is the process identification number assigned by the OS. All /tmp/xsi data is deleted both when finishing a process or initiating a new one.
©XSIBackup-Datacenter --replica feature depends on the remote replica set not being touched (modified), should you change a single bit on the remote end, the replica mirror will break and the resulting mirrored set of files may become unusable. This is due to the fact that all the program knows about the remote files is it’s hash map, which is not updated when you modify files by other means than the --replica action.

Scheduling jobs:

Job schedule is performed by means of the crond service and its corresponding crontab. Just place backup jobs in files, assign them execute permissions and add them to the crontab. You can use the argument --save-job=NNN, which will facilitate the creation of backup job files in the etc/jobs folder.
Concurrency:

©XSIBackup-Datacenter spirit it to become a heavy duty backup & replication utility and its basic structural design principles are oriented to that goal. Nevertheless in version 1.0.0.0, locking of the  data/.blocklog file, which is the main block manifest, which is shared by different backup folders and backup processes is provided via .lock files, which is not the most efficient way to manage concurrency. You could in fact hit some circumstance in which some information is written to a file which is supposed to be locked. 
This would be quite rare though, only if you try to write from many different processes to the same repository at the same time you may be able to run over some lock. Even in case this circumstance happened, nothing serious would occur, as duplicating some block info in the manifest file is harmless.
The block manifest file can be rebuilt from the underlying data quite fast by using the --repair action, which would eliminate any duplicates.
The files that allow you to restore some backup are the .map files, stored in the backup folders and the data blocks themselves, which are kept in the /data directory. You could even delete the manifest file (/data/.blocklog) and still be able to rebuild it via the --repair action.
To take on account:
It’s worth to note that each backup jobs maintains a set of temporal files in an exclusive and independent directory and that it backs data up to an exclusive directory on the server repository, which is uniquely identified by a timestamp and an eventual subfolder set by the   subfolder=somesubfolder argument. 
If you don’t differentiate  backups from different servers by using the --subfolder argument, i.e.: 

--subfolder=CURRENT-SERVER

You are taking the small risk that some jobs triggered at the same time are stored to the same time stamped folder . This is unlikely to happen, on top of that the VM being backed up would need to be called the same in both servers for files to mix up. 
So, always use the subfolder option when backing up from different servers. This is a must, not only because of the situation treated above,  but also from a simple organizational point of view.
Take on account that if you trigger multiple simultaneous backups from different servers without having first designed a system to support it, you will most likely clog your network, your disk controller and your server.  As known blocks start to accumulate in the block manifest (/data/.blocklog) the traffic will be reduced to blocks that have changed since last backup cycles and the backups will as a result be performed much faster.
You can think of ©XSIBackup-Datacenter as some “Incredible Hulk” that grows in power as you load it with tons of data. Of course the results you get will be bound by your hardware limits and the limits of our software, but you should easily accumulate many terabytes of real data, which will normally correspond to some exabytes in backups.

Design:

©XSIBackup-Datacenter stores backups to proprietary repositories, nevertheless the structure and format of these repositories has been designed to be “eye friendly” to the system administrator. 

Data chunks are stored in raw format in subfolders of the backup volume file system as well as hash maps corresponding to the files in the backup. Thus you could very well rebuild your files from the data on disk by just adding the data chunks as described in the human friendly .map files, which are nothing but a manifest of the chunks encountered in the file when backed up.

©XSIBackup-Datacenter uses a default block size of 10MB, but it can be as low as 1M. As you may imagine, this could accumulate a big number of chunks in the data folder structure, in the order of millions. 

As you probably already know, the ESXi VMFS file system has around 130.000 possible inodes, thus it is not very convenient to store deduplicated backups, as you will soon run out of inodes. 

Any regular Linux file system will do it, but if you are willing to achieve great results we recommend that you use XFS or ext4, as they will allow to store millions of files and are, at the same time, the fastest file systems. Speed is an important factor when you accumulate a lot of data, as blocks need to be sought in the file system. Using a regular Linux system mounted over NFS3 is the ideal target for your backups. It can also be a specialized device like the popular Synology and QNap NAS boxes.

Data chunks are stored in the data folder inside the repository in a hierarchical subfolder manner. Each subfolder corresponds to an hexadecimal character up to 5 levels under the root of it and blocks are stored in their matching first 5 characters folder.

Assuming the robustness of the SHA-1 hash algorithm, which offers astronomical figure collision free unique IDs and the fact that the .map files are stored in unique folders; the probability to lose data due to some collision or repository corruption is very low. 

Even if you completely delete the .blocklog manifest file, it can always be rebuilt from the constituent .map files and the deduplicated chunks in the data folder by using the --repair argument.

The .blocklog file in the root of the /data folder is a mere way for the client backup processes to know about the preexisting deduplicated chunks. This file is downloaded to the temp client folder previous to every backup cycle, thus the check on the existence of the block is performed locally. This has a small disadvantage, which is not knowing about blocks pertaining to ongoing backup jobs, but offers the huge advantage of performing block lookups locally at local speed.

Once every backup cycle finishes, the newly generated data, that is: data which was not found on the downloaded .blocklog manifest file, is added to the repository shared .blocklog file. This process locks the .blocklog file for the time it takes to complete, generating a /data/.blocklog.lock file, which is removed once the integration of the differential data completes.

The differential data is stored temporarily in the /tmp/xsi/%PID%/.blocklog.diff file of the client while the backup is taking place. The whole temp folder is deleted upon each backup cycle.

©XSIBackup-Datacenter is a low level tool. It’s as secure as dd or rm are in your Linux server, so make sure that you assign it adequate permissions. You may use different remote users than root, that is very convenient, especially when backing up to remote Linux servers, but trying to run it in an ESXi server under a different user than root, will require you to configure permissions accordingly. Also please note that when opening up execute permissions on ©XSIBackup-Datacenter binary to other users than root, you are opening a potential security breach.

IMPORTANT: everything about the .blocklog manifest, the .diff files and the integration of the differential data constitutes a different and isolated subsystem in regards to the backup itself. Loosing differential metadata, registering duplicate block hashes or, as said, deleting the whole .blocklog manifest is unimportant, as it can always be regenerated accurately from the constituent blocks.
Even in the worst of the cases, by receiving a totally corrupt .blocklog file (which of course should never happen) and by messing up all differential data, your files will still be backed up accurately and you will be able to repair your repository afterwords. The worst possible situation in regards to the logic of the deduplication is that some block is reported as inexistent and is copied again. All this assuming that the backup completes and there aren’t any hardware or communication issues.

Designed to be useful:

©XSIBackup-Datacenter has been designed with you in mind. A datacenter system administrator that needs a tool which is easy to use and extremely powerful at the same time.
As you already know (if you read the previous chapters) ©XSIBackup-Datacenter stores deduplicated and eventually compressed chunks of data to the backup volume file system. Map files are stored to folders like the following:

<root of repo>/subfolder/timestamp/VM/vm-disk1-flat.vmdk.map

Whereas blocks are stored in the already explained five level subfolder structure under /data, something like:

<root of repo>/data/a/0/f/3/0/a0f307f7abb76d7…bc3576adef5299a

Just as long as you keep this data intact, you can easily rebuild it by using the --repair command. Then it’s easy to realize that you can merge preexisting repositories into a single one and still keep data intact. This is useful in case you need to consolidate data into a single backup volume.

You can of course duplicate your repositories contents somewhere else. Thanks to the fact that data is split into thousands of deduplicated chunks, you can use Rsync to keep copies of your repositories offsite and use ©XSIBackup-Datacenter to rebuild your VMs or any other data anywhere.


Usage:
Basic usage consists in passing an action first plus one or two paths depending on the type of action being performed, then the rest of arguments.

./xsibackup [action] [source] [target] [options]

Quick examples:

ATTENTION: don’t copy directly from this document into your SSH client. The chances that some character substitution happens is very high.

./xsibackup --add-key 10.10.1.20:22

./xsibackup --backup /home/me/my-data /mnt/NFS/backup –compression
./xsibackup --backup /home/me/my-data /mnt/NFS/backup –compression --rotate=30
./xsibackup --backup “VMs(Win01, Lin03, MyERP)” /vmfs/volumes/backup
./xsibackup --backup “VMs(Win01, Lin03, MyERP)” 10.10.1.20:22:/vmfs/volumes/backup –compression
./xsibackup --replica “VMs(Win01, Lin03, MyERP)” /vmfs/volumes/backup
./xsibackup --replica “VMs(Win01, Lin03, MyERP)” 10.10.1.20:22://vmfs/volumes/backup
./xsibackup --backup “VMs(Win01, Lin03, MyERP)” 192.168.10.91:22:/vmfs/volumes/backup --compression
./xsibackup --replica “VMs(Win01, Lin03, MyERP)” 192.168.10.91:4432:/vmfs/volumes/backup

./xsibackup --repair /vmfs/volumes/backup
./xsibackup --info /vmfs/volumes/backup
./xsibackup --prune /vmfs/volumes/backup/20190603211935

1 - Actions:

Action comes in first place after the call to the binary. It can be one of these:
--backup: this action will perform a deduplicated backup, optionally compressed by the LZJB compression algorithm, to the directory specified in the target argument.

--replica: this action will perform a replication of the data under the source directory to the directory specified in the target argument. It will preserve the folder structure of the source.

--restore: this action will work the same way as --backup but the other way around. You point the source argument to some folder containing a backup, that is, some folder in a repository containing some .map files. The target will be the folder where to restore the contents of the source argument.

--check[=fast|full]: this action will check a whole repository or a folder inside a repository passed as the second argument, it may optionally accept two values: fast or full (default). The fast option will only check the size of the chunks in the repo. The full option will eventually uncompress the data and recalculate its SHA-1 checksum to be compared with the stored value. Please, note that when the repository is compressed, even if you don’t choose the full option, every chunk’s inner header will need to be queried to find the size of the uncompressed data.
--remote-path: this is the path in the remote system that allows XSIBackup to find its counterpart. You can omit it if XSIBackup can be found in the /usr/bin directory.

--prune: this action will delete some folder inside a deduplicated repository and calculate which blocks belong exclusively to that backup, it will then delete those blocks only, liberating the space used by that particular backup in the deduplicated repository.

--info: just pass the root of a repository as a second argument to this action to have a quick view of the most relevant figures: blocks in the repository, size used, size of the files hosted and the achieved compression ratio.
--repair: using this action on a previously existing repository will perform the following: all constituent blocks for all files stored in the repository will be read, sorted and any duplicates will be removed, then each block will be sought in the /data folder to check it exists. This action can repair a repository which block manifest /data/.blocklog has been erased and rebuild it from the individual .map files, but it cannot recover deleted blocks or rebuild missing .map files.
Unless you have lost fundamental data (data blocks or .map files), if a --repair operation is successful, you can consider your repository to be healthy.
--add-key: this action will grab the local RSA public key and place it in a remote server’s authorized_keys file to allow passwordless communications between the local and the remote system. You need to perform this for every system that you want to use over IP, it’s not needed for local data operations.
You do need to use this kind of string as the target: user@IP:Port. The user will be root most of the times, i.e.: 

root@192.168.12.3:22

The key exchange routine will look for authorized_keys files in the default locations for OpenSSH servers, namely: /root/.ssh/ authorized_keys, /home/some-user/.ssh/authorized_keys or /root/.ssh/authorized_keys.

In case of remote ESXi hosts, the default location for this file will be sought, that is: /etc/ssh/keys-root/authorized_keys or /etc/ssh/keys-[some-user]/authorized_keys in case of using some user other than root.
Examples:
./xsibackup --add-key  root@192.168.1.221:22	Add key to host 192.168.1.221 on port 22
./xsibackup --add-key  alice@192.168.1.221:5467	Add key to host 192.168.1.221 on port 5467
./xsibackup --add-key  bob@192.168.1.50:5467 
	Add key to host 192.168.1.50 on port 5467 on user’s bob profile

2 – Source

This is the second argument in case of performing a --backup or --replica action and the only path required when executing a --check, --info or --prune operation.
When performing copy operations (--backup or --replica) this argument must point to an existing directory containing some files. Those files will be backed up or replicated to the target directory.
Virtual Machines:
In case of using XSIBackup-Datacenter in the ESXi Shell, it can detect VMs by name. Just parse a list of virtual machines between parenthesis preceded by the word VMs, like:
“VMs(WIN7#1, LINC#2, ERP01, MAIL-SRV, Other VM)” 

3 –Target

Target is the third argument in the command line. It represents a directory where files will be backed up into an existing deduplicated directory, or replicated to it. If the directory does not exist it will be created and eventually a new repo initialized by XSIBackup-Datacenter.

4 – Options

--block-size[=1M|10M|20M(default)|50M]: (optional) this is the block size that will be used to deduplicate data when using the --backup action. In case of replicas a fixed block size of 1M will be used. You can choose between: 1, 10, 20, or 50 megabyte block sizes when performing a --backup action. 10M is the default --block-size.

Example:
--block-size=1M	Set block size to one megabyte
--block-size=20M	Set block size to twenty megabytes
--block-size=50M	Set block size to fifty megabytes

--quiesce: (optional)  this option has no arguments, use it when backing up VMs to quiesce the guest OS previous to taking the backup snapshot. If you don’t pass this option, no quiescing will take place.

--compression: (optional)  sets whether the backup will compress chunks previous to storing them in the backup repository. It will achieve an additional 50% compression on the data at almost no cost in speed. It’s recommended that you always use it, unless you are storing data to a compressed file system, you may avoid using it in that case.

--subfolder[=yourfolder]: (optional)  this will add a folder with the specified name before the time-stamped folder, so that you can organize your backups in logical containers. It’s useful when storing backups from different clients into a consolidated repository per instance.

--rotate [=ND]: (optional) this option will delete the backup folders older than N days when the number N is followed by a D letter (case insensitive), or will just keep the number of folders stated in the numeric value when you parse just an integer number.

--verbosity: (optional)  accepts values between 0 and 10, more information messages will appear on STDOUT when you raise it.

--save-job[=NNN]: (optional)  will save the current command line job to a job file in etc/jobs/NNN, where NNN is the three digit numeric code assigned to the job. You may then call this file from the host’s crontab to schedule backups or replicas.

--timestamp [=YYYYMMDDhhmmss]: (optional)  allows to set a custom timestamp in case you want to place multiple job outcomes into a single backup folder. If you don’t use it, XSIBackup-Datacenter will create a timestamped folder per backup job.


--ssl-key [=path]: (optional) path to the SSL key pair. If it is not provided by means of this argument it will be sought in the program’s root folder


