BACKUP DATABASE command
Last Updated: 2024-01-15
The BACKUP DATABASE command creates a backup copy of a database or a table space. A backup of your database and related stored data must be created to prevent data loss if a database service outage occurs.

For information about database backup operations between different operating systems and hardware platforms, see “Backup and restore operations between different operating systems and hardware platforms”.

Scope
In a partitioned database environment, if no database partitions are specified, this command affects only the database partition on which it is run.

If the option to perform a partitioned backup is specified, the command can be called only on the catalog database partition. If the option specifies that all database partition servers are to be backed up, it affects all database partition servers that are listed in the db2nodes.cfg file. Otherwise, it affects the database partition servers that are specified on the command.

Authorization
One of the following authorities:
SYSADM
SYSCTRL
SYSMAINT
Required connection
Database. This command automatically establishes an exclusive connection to the specified database.
Note: If a connection to the specified database exists, that connection is terminated and a new connection established specifically for the backup operation. The connection is stopped at the completion of the backup operation.
Command syntax
Read syntax diagramSkip visual syntax diagram
BACKUP
DATABASE
DB
database-alias
USER
username
USING
password
ON
DBPARTITIONNUM
DBPARTITIONNUMS
Partition number(s)
ALL DBPARTITIONNUMS
EXCEPT
DBPARTITIONNUM
DBPARTITIONNUMS
Partition number(s)
TABLESPACE
(
,
tablespace-name
)
NO TABLESPACE
ONLINE
INCREMENTAL
DELTA
USE
TSM
XBSA
Open sessions
SNAPSHOT
LIBRARY
library-name
SCRIPT
script-name
Options
LOAD
library-name
Open sessions
Options
TO
,
dir
pipename
dev
remote-storage
DEDUP_DEVICE
WITH
num-buffers
BUFFERS
BUFFER
buffer-size
PARALLELISM
n
COMPRESS
COMPRLIB
name
EXCLUDE
COMPROPTS
string
ENCRYPT
ENCRLIB
name
EXCLUDE
ENCROPTS
string
UTIL_IMPACT_PRIORITY
priority
EXCLUDE LOGS
INCLUDE LOGS
WITHOUT PROMPTING
Partition number(s)
Read syntax diagramSkip visual syntax diagram
(
,
db-partition-number1
TO
db-partition-number2
)
Open sessions
Read syntax diagramSkip visual syntax diagram
OPEN
num-sessions
SESSIONS
Options
Read syntax diagramSkip visual syntax diagram
OPTIONS
"options-string"
@
file-name
Command parameters
DATABASE | DB database-alias
Specifies the alias of the database to back up.
USER username
Identifies the user name under which to back up the database.
USING password
The password that is used to authenticate the user name. If the password is omitted, the user is prompted to enter it.
ON
Back up the database on a set of database partitions.
DBPARTITIONNUM db-partition-number1
Specifies a database partition number in the database partition list.
DBPARTITIONNUMS db-partition-number1 TO db-partition-number2
Specifies a range of database partition numbers so that all partitions from db-partition-number1 up to and including db-partition-number2 are included in the database partition list.
ALL DBPARTITIONNUMS
Specifies that the database is to be backed up on all partitions that are specified in the db2nodes.cfg file.
EXCEPT
Specifies that the database is to be backed up on all partitions that are specified in the db2nodes.cfg file, except those specified in the database partition list.
DBPARTITIONNUM db-partition-number1
Specifies a database partition number in the database partition list.
DBPARTITIONNUMS db-partition-number1 TO db-partition-number2
Specifies a range of database partition numbers so that all partitions from db-partition-number1 up to and including db-partition-number2 are included in the database partition list.
TABLESPACE tablespace-name
A list of names that are used to specify the table spaces to be backed up.
NO TABLESPACE
Specifies a metadata-only backup. A metadata-only backup does not contain any tablespace data. Among the metadata that it does contain is the database's recovery history file. This file can be restored by using the HISTORY FILE option of the RESTORE DATABASE command.

ONLINE
Specifies online backup. The default is offline backup. Online backups are only available for databases that are configured with logarchmeth1 enabled. During an online backup, Db2® obtains IN (Intent None) locks on all tables that exist in SMS table spaces as they are processed. S (share locks) is no longer held on LOB data in SMS table spaces during online backup.

INCREMENTAL
Specifies a cumulative (incremental) backup image. An incremental backup image is a copy of all database data that changed since the most recent successful, full backup operation.
DELTA
Specifies a noncumulative (delta) backup image. A delta backup image is a copy of all database data that changed since the most recent successful backup operation of any type.
USE
TSM
Specifies that the backup is to use Tivoli® Storage Manager (TSM) as the target device.
XBSA
Specifies that the XBSA interface is to be used. Backup Services APIs (XBSA) are an open application programming interface for applications or facilities that need data storage management for backup or archiving purposes.
SNAPSHOT
Specifies that a snapshot backup is to be taken.
You cannot use the SNAPSHOT parameter with any of the following parameters:
TABLESPACE
INCREMENTAL
WITH num-buffers BUFFERS
BUFFER
PARALLELISM
COMPRESS
UTIL_IMPACT_PRIORITY
SESSIONS
The default behavior for a snapshot backup is a full database offline backup of all paths that make up the database. This backup includes all containers, local volume directory, database path (DBPATH), and primary log and mirror log paths (INCLUDE LOGS is the default for all snapshot backups unless EXCLUDE LOGS is explicitly stated).

Snapshot backup is supported when ENCRLIB is set in the following conditions: the database is encrypted. This ensures that the snapshot backup is itself encrypted. The configured encryption library is one of the native Db2 encryption libraries. This can be the encryption library or one of the combined encryption/compression libraries. If you have configured a non-IBM encryption library for Backup, we consider this to imply that you want this setting honored for all backups. Creating a snapshot backup using native Db2 encryption would violate this preference.

LIBRARY library-name
Integrated into IBM® Db2 Server is a Db2 ACS API driver for the following storage hardware:
IBM TotalStorage™ SAN Volume Controller
IBM Enterprise Storage Server® Model 800
IBM Storwize® V7000
IBM System Storage® DS6000™
IBM System Storage DS8000®
IBM System Storage N Series
IBM XIV®
If you have other storage hardware, and a Db2 ACS API driver for that storage hardware, you can use the LIBRARY parameter to specify the Db2 ACS API driver.

The value of the LIBRARY parameter is a fully qualified library file name.

SCRIPT script-name
The name of the executable script capable of performing a snapshot backup operation. The script name must be a fully qualified file name.
OPTIONS
"options-string"
Specifies options to be used for the backup operation. The string is passed exactly as it was entered, without the double quotation marks.
@ file-name
Specifies that the options to be used for the backup operation are contained in a file on the Db2 server. The string is passed to the vendor support library. The file must be a fully qualified file name.
You cannot use the vendoropt database configuration parameter to specify vendor-specific options for snapshot backup operations. You must use the OPTIONS parameter of the backup utilities instead.

OPEN num-sessions SESSIONS
The number of I/O sessions to create between the Db2 product and the TSM product or another backup vendor product. This parameter has no effect when you back up to tape, disk, or other local device. If you specify the INCLUDE LOGS parameter for an online backup, an extra session is created for the OPEN num-sessions SESSIONS parameter after the initial sessions are closed. If you are creating a Single System View (SSV) online backup, for each node that is backed up, an extra session is created for the OPEN num-sessions SESSIONS parameter after the initial sessions are closed. If you use this parameter with the TSM option, the number of entries that are created in the history file is equal to the number of sessions created.
TO dir | pipename | dev | remote-storage
A list of paths, named pipes, devices, or remote storage locations to which the backup image is to be stored. Backing up to a named pipe is supported on only Unix and Linux platforms.

The full path to each target directory must be specified. If USE TSM, TO, and LOAD are omitted, the default target directory is the current working directory of the client computer. The target directories or devices must be locally addressable on the database server. If the target directory is on Network File System (NFS), the NFS needs to be configured with the nolock option (llock on AIX). Otherwise, the backup may hang.

To back up to remote storage such as to IBM Cloud Object Storage or Amazon Simple Storage Service (S3), specify a remote storage location by using a storage access alias. Local staging space is needed to temporarily store the backup image that is to be transferred to the remote storage server. Each backup session for the remote storage has a maximum size of 5 GB, which can produce a total database backup image size that is slightly smaller than 5 TB. Refer to Remote storage requirements. The syntax for specifying a remote storage location is:
DB2REMOTE://<alias>//<storage-path>

In a partitioned database, the target directory or device must exist on all database partitions, and can optionally be a shared path. The directory or device name can be specified by using a database partition expression. For more information about database partition expressions, see Using database partition expressions.

This parameter can be repeated to specify the target directories and devices that the backup image spans. If more than one target is specified (target1, target2, and target3, for example), target1 is opened first. The media header and special files (including the configuration file, table space table, and history file) are placed in target1. All remaining targets are opened, and are then used in parallel during the backup operation. Because there is no general tape support on Windows operating systems, each type of tape device requires a unique device driver.

Use of tape devices or floppy disks might generate messages and prompts for user input. Valid response options are:
c
Continue. Continue using the device that generated the warning message (for example, when a new tape has been mounted)
d
Device terminate. Stop using only the device that generated the warning message (for example, when there are no more tapes)
t
Terminate. Abort the backup operation.
If the tape system does not support the ability to uniquely reference a backup image, multiple backup copies of the same database must not be kept on the same tape.

LOAD library-name
The name of the shared library (DLL on Windows operating systems) containing the vendor backup and restore I/O functions to be used. It can contain the full path. If the full path is not given, it defaults to the path on which the user exit program resides.
DEDUP_DEVICE
Optimizes the format of the backup images for target storage devices that support data deduplication.
WITH num-buffers BUFFERS
The number of buffers to be used. If the number of buffers that you specify is not enough to create a successful backup, then the minimum value necessary to complete the backup is automatically chosen for this parameter. If you are backing up to multiple locations, you can specify a larger number of buffers to improve performance. If you specify the COMPRESS parameter, to help improve performance, you can add an extra buffer for each table space that you specify for the PARALLELISM parameter.
BUFFER buffer-size
The size, in 4 KB pages, of the buffer used when building the backup image. Db2 automatically chooses an optimal value for this parameter unless you explicitly enter a value. The minimum value for this parameter is eight pages.
If using tape with variable block size, reduce the buffer size to within the range that the tape device supports. Otherwise, the backup operation might succeed, but the resulting image might not be recoverable.

With most versions of Linux®, using the default buffer size included with Db2 for backup operations to a SCSI tape device results in error SQL2025N, reason code 75. To prevent the overflow of Linux internal SCSI buffers, use this formula:
bufferpages <= ST_MAX_BUFFERS * ST_BUFFER_BLOCKS / 4

where bufferpages is the value you want to use with the BUFFER parameter, and ST_MAX_BUFFERS and ST_BUFFER_BLOCKS are defined in the Linux kernel under the drivers/scsi directory.
PARALLELISM n
Determines the number of table spaces that can be read in parallel by the backup utility. Db2 automatically chooses an optimal value for this parameter unless you explicitly enter a value.
UTIL_IMPACT_PRIORITY priority
Specifies that the backup runs in throttled mode, with the priority specified. Throttling allows you to regulate the performance impact of the backup operation. Priority can be any number between 1 and 100, with 1 representing the lowest priority, and 100 representing the highest priority. If the UTIL_IMPACT_PRIORITY keyword is specified with no priority, the backup runs with the default priority of 50. If UTIL_IMPACT_PRIORITY is not specified, the backup runs in unthrottled mode. An impact policy must be defined by setting the util_impact_lim configuration parameter for a backup to run in throttled mode.
COMPRESS|ENCRYPT
Indicates that the backup is to be compressed or encrypted. You cannot specify both parameters simultaneously. The COMPRESS and ENCRYPT parameters are synonyms and can be used interchangeably only when either COMPRLIB or ENCRLIB is specified too. If you specify COMPRESS without COMPRLIB, the default compression library libdb2compr.so is used for compression. If you specify ENCRYPT without ENCRLIB, the default encryption library libdb2encr.so is used for encryption. If you want to specify another library, you can use COMPRESS and ENCRYPT interchangeably. You can use either COMPRLIB or ENCRLIB to specify the libdb2compr_encr.so library. If the encrlib database configuration parameter is set to a non-NULL value, neither COMPRLIB nor ENCRLIB can be specified. If encrlib database configuration parameter is not set, a specified ENCROPTS command option will be used rather than encropts database configuration parameter. If the encrlib and encropts database configuration parameters are set to a non-NULL value, neither COMPROPTS nor ENCROPTS can be specified on the command.
Note: For databases which are natively encrypted, data is decrypted prior to backup. Encryption of the backup can be achieved by using this ENCRYPT parameter of the BACKUP DATABASE command. Encryption of the backup can also be achieved through the encrlib database configuration parameter. The backup will be encrypted using the algorithm employed by the specified encryption library, independent of any database native encryption configured cipher or algorithm.
COMPRLIB|ENCRLIB name
Indicates the name of the library that is used during the compression or encryption process. For example, db2compr.dll for Windows; libdb2compr.so for Linux and UNIX operating systems. The name must be a fully qualified path that refers to a file on the server. If this parameter is not specified, the default Db2 compression library is used. If the specified library cannot be loaded, the backup operation fails.
EXCLUDE
Indicates that the library is not stored in the backup image.
COMPROPTS|ENCROPTS string
Describes a block of binary data that is passed to the initialization routine in the library. The database manager passes this string directly from the client to the server. Any issues of byte reversal or code page conversion are handled by the compression library. If the first character of the data block is '@', the rest of the data is interpreted as the name of a file on the server. The database manager then replaces the contents of the string with the contents of this file and passes this new value to the initialization routine. The maximum length for string is 1024 bytes.
For the default Db2 libraries libdb2compr_encr.so (compression and encryption) or libdb2encr.so (encryption only), the format of the ENCROPTS string is as follows:
Cipher=cipher-name:Mode=mode-name:Key Length=key-length:
  Master Key Label=label-name-1...:Master Key Label=label-name-n

Cipher is optional. Valid values are AES and 3DES (the default is AES).
Mode is optional. The default is CBC.
Key length is optional. Valid values for AES are 128, 192, and 256 (the default is 256), and the only valid value for 3DES is 168.
Master key label is optional. The default is the database master key label.
If you are using other libraries, the format of the ENCROPTS string depends on those libraries.
EXCLUDE LOGS
Specifies that the backup image must not include any log files. When you performe an offline backup operation, logs are excluded whether this option is specified, except for snapshot backups.Logs are excluded by default in the following backup scenarios:
Offline backup of a single-partitioned database.
Online or offline backup of a multi-partitioned database, when not using a single system view backup.
If you specify the EXCLUDE LOGS with a snapshot backup, writes to log files are allowed during the backup. These log files are included by default in the snapshot backup, but are not usable for recovery. If this backup is restored, the log files must not be extracted from the backup. If the log path was set to the default when the backup was taken, then it is not possible to exclude the log files from being restored and they must be deleted manually after the backup is restored. If the log path was not the default, then the log files can be excluded at restore time by using the LOGTARGET EXCLUDE options with the RESTORE DATABASE command.

INCLUDE LOGS
Specifies that the backup image must include the range of log files that are required to restore and roll forward this image to some consistent point in time. This option is not valid for an offline backup, except for snapshot backups. INCLUDE LOGS always the default option for any online backup operation, except a multi-partitioned online backup where each database partition is backed up independently (that is, a non-single system view backup).
If any of the log files that are required for the backup have previously been backed up and are no longer in the log path, then the Db2 database manager retrieves them for backup from the overflow log path, if the path has been set. Otherwise, the database manager retrieves them for backup from the current log path or mirror log path. These log files are removed from the log path after the backup is complete.

WITHOUT PROMPTING
Specifies that the backup runs unattended, and that any actions that normally require user intervention returns an error message.

Note:
If the backup command indicates which partitions in a partitioned database are to be backed up, the backup operation is implicitly performed WITHOUT PROMPTING.
Options that are specified on the BACKUP DATABASE command in a partitioned database environment are applied on each partition individually. For example, if a backup operation is specified to USE TSM OPEN 3 SESSIONS, Db2 opens three TSM sessions on each partition.
Examples
In the following example, the database WSDB is defined on all four database partitions, numbered 0 - 3. The path /dev3/backup is accessible from all database partitions. Database partition 0 is the catalog partition. To perform an offline backup of all the WSDB database partitions to /dev3/backup, issue the following command from database partition 0:
db2 BACKUP DATABASE wsdb ON ALL DBPARTITIONNUMS TO /dev3/backup

The backup is performed simultaneously on all partitions. All four database partition backup images are stored in the /dev3/backup directory. This directory can be a shared directory that is accessible from more than one partition, or a locally mounted directory accessible from each partition individually, or a combination of both.

You can use a named pipe to back up one database directly into another without saving the intermediate backup image.
The following example copies a source database (srcdb) into the target database (tgtdb). You can enter the BACKUP and RESTORE commands in either order.
(in one session)
$ db2 backup db srcdb to /<pipename>

(in another session)
$ db2 restore db srcdb from /<pipename> into tgtdb

In the following example database SAMPLE is backed up to a TSM server that uses two concurrent TSM client sessions. Db2 calculates the optimal buffer size for this environment.
db2 backup database sample use tsm open 2 sessions with 4 buffers

In the following example, a table space-level backup of table spaces (syscatspace, userspace1) of database payroll is done to tapes.
db2 backup database payroll tablespace (syscatspace, userspace1) to
   /dev/rmt0, /dev/rmt1 with 8 buffers without prompting

The USE TSM OPTIONS keywords can be used to specify the TSM information to use for the backup operation. The following example shows how to use the USE TSM OPTIONS keywords to specify a fully qualified file name:
db2 backup db sample use TSM options @/u/dmcinnis/myoptions.txt

The file myoptions.txt contains the following information: -fromnode=bar -fromowner=dmcinnis
Following is a sample weekly incremental backup strategy for a recoverable database. It includes a weekly full database backup operation, a daily noncumulative (delta) backup operation, and a midweek cumulative (incremental) backup operation:
   (Sun) db2 backup db sample use tsm
   (Mon) db2 backup db sample online incremental delta use tsm
   (Tue) db2 backup db sample online incremental delta use tsm
   (Wed) db2 backup db sample online incremental use tsm
   (Thu) db2 backup db sample online incremental delta use tsm
   (Fri) db2 backup db sample online incremental delta use tsm
   (Sat) db2 backup db sample online incremental use tsm

In the following example, three identical target directories are specified for a backup operation on database SAMPLE. This is suitable if the target file system is made up of multiple physical disks.
db2 backup database sample to /dev3/backup, /dev3/backup, /dev3/backup

The data is concurrently backed up to the three target directories, and three backup images are generated with extensions .001, .002, and .003.
In the following example, the database WSDB is defined on all four database partitions, numbered 0 - 3. Database partition 0 is the catalog partition. To perform an online backup of table space USERSPACE1 on database partitions 1 and 2, with the backup image to be stored on a TSM server, issue the following command from partition 0:
db2 BACKUP DATABASE wsdb ON DBPARTITIONNUMS (1, 2) TABLESPACE (USERSPACE1)
 ONLINE USE TSM

Sample output that is generated to indicate the sqlcode returned by each partition.
Example 1
All partitions are successful (sqlcode >= 0)
$ db2 backup db foo on all dbpartitionnums tablespace(T1)
Part Result
---- ------
0000 DB20000I The BACKUP DATABASE command completed successfully.
0001 SQL2430W The database backup succeeded, but the following
          table spaces do not exist on this database partition: "T1".

Backup successful. The timestamp for this backup image is :
                                                   20040908010203

Example 2
One or more partitions fail (sqlcode < 0)
$ db2 backup db foo on all dbpartitionnums to /backups
Part Result
---- ------
0000 SQL2001N  The utility was interrupted. The output data may be incomplete.
0001 SQL2419N The target disk "/backups" has become full.

SQL2429N The database backup failed. The following database partitions returned errors: "1".

The following backups include the log directories in the image created:
db2 backup db sample use snapshot

db2 backup db sample online use snapshot

db2 backup db sample use snapshot INCLUDE LOGS

db2 backup db sample online use snapshot INCLUDE LOGS

The following backups will NOT include the log directories in the image created:
db2 backup db sample use snapshot EXCLUDE LOGS

db2 backup db sample online use snapshot EXCLUDE LOGS

The following command encrypts a backup image by using the default encryption options:
BACKUP DATABASE ccards ENCRYPT;

The following commands encrypt a backup image by using the default encryption library and explicitly provided encryption options:
BACKUP DATABASE ccards
  ENCRYPT ENCRLIB 'libdb2encr.so'
  ENCROPTS 'Cipher=3DES';

BACKUP DATABASE ccards
  ENCRYPT ENCRLIB 'libdb2encr.so'
  ENCROPTS 'Master Key Label=mylabel.mydb.myinstance.myserver';

The following command creates a backup image that is both compressed and encrypted:
BACKUP DATABASE ccards
  ENCRYPT ENCRLIB libdb2compr_encr.so;

Usage notes
The data in a backup cannot be protected by the database server. Make sure that backups are properly safeguarded, particularly if the backup contains LBAC-protected data.
When backing up to tape, use of a variable block size is not supported. If you must use this option, ensure that you have tested procedures in place that enable you to recover successfully, using backup images that were created with a variable block size.
The backup utility cannot be used with a Type 2 connection.
When using a variable block size, you must specify a backup buffer size that is less than or equal to the maximum limit for the tape devices that you are using. For optimal performance, the buffer size must be equal to the maximum block size limit of the device being used.
Snapshot backups should be complemented with regular disk backups in case of failure in the filer/storage system.
As you regularly backup your database, you might accumulate large database backup images, many database logs, and load copy images, all of which might be taking up a large amount of disk space. Refer to “Managing recovery objects” for information about how to manage these recovery objects.
You can use the OPTIONS parameter to enable backup operations in TSM environments that support proxy nodes. For more information, see the “Configuring a Tivoli Storage Manager client” topic.
You can use the DB2_BCKP_PAGE_VERIFICATION registry variable to enable DMS and AS page validation during the backup. The validation is capable of identifying many types of structural integrity problems, including data, index, and XML object page checksum validation failures, as well as anomalies in meta information of these pages. However, it is not possible to identify all imaginable integrity problems. Some limitations include, but are not limited to:
Whether the version of a data page reflects what Db2 last wrote to the table space container file on disk (that is, a lost I/O write) is not identified.
Any logical discontinuity between data on a page and the objects correlated to this data, such as indexes, constraints, or MQTs, is not identified.
Whether the version of a data page resides in the location within the table space container file on disk where Db2 wrote it (that is, a misplaced I/O write), will not be detected if the misplaced data page resides in a different table space in a location where a page of the same objectID is expected.
The integrity of LOB or Long Field data pages are not validated.
The integrity of empty (zeroed out) pages are not verified and are not reported as errors even when they are expected to contain data.
For SMS table spaces, integrity validation is limited to assuring page counts are correct per object, no further validation is performed.
You can use the DB2_BCKP_INCLUDE_LOGS_WARNING registry variable to specify that some online backups can now succeed even if they have not successfully included all of the required logs.
After you issue a BACKUP DATABASE command with the ONLINE option and the INCLUDE LOGS option, the resulting backed-up database image includes all the log files necessary to roll forward to the end of backup.
