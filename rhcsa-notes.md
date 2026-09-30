# RHCSA NOTES
RHCSA Module 1: Comprehensive Field Manual & Command Analysis
Covering Course Days 2, 3, & 4 (100% History Command Extraction)

1. Executive Summary & Curriculum Mapping
Module 1 establishes the foundational skills required for the
Red Hat Certified System Administrator (RHCSA EX200)
exam, mapping directly to the following official curriculum chapters:

RH124 Chapter 1
: Accessing the Command Line
RH124 Chapter 2
: Managing Files from the Command Line
RH124 Chapter 3
: Getting Help in Red Hat Enterprise Linux
RH124 Chapter 4
: Creating, Viewing, and Editing Text Files
RH124 Chapter 5
: Managing Local Users and Groups

2. Navigation & Directory Structure
Fundamental Navigation Utilities
(Print Working Directory)
/usr/bin/pwd
Options: None (default behavior prints logical path).
: Outputs the absolute path of the current directory.

Course Context
: Executed after directory switches (
cd Documents
cd /etc/alsa/conf.d
) to verify the exact location in the filesystem tree.
(Change Directory)
Passed Command
Target Path
Functional Behavior
Keeps session in current directory (no-op).

Moves up one level to parent directory.

Abbreviation
Moves to current user's home directory (
Moves to current user's home directory.

Moves to root directory of filesystem.
under current working directory.

Traversals up two directory levels simultaneously.
cd /etc/alsa/conf.d
/etc/alsa/conf.d
Jumps directly to target directory from anywhere.
cd /root/Documents
/root/Documents
Jumps to root user's Documents folder.

Navigates to local user home parent directory.
(List Directory Contents)
Passed Command
: Lists non-hidden files and directories in short format.

Passed Command
): Includes hidden entries starting with
: Long-listing format (permissions, link count, owner, group, size, mtime, name).

Passed Command
: Long-listing format.
--recursive
): Recursively lists all subdirectories and contents.

Passed Command
: Long-listing format.
--directory
): Lists directory attributes itself rather than its contents.

Passed Command
ls -li imp us1/f1 us2/f2 us3/f3
: Long-listing format.
): Displays index node (inode) number of each file.

Directory Creation & Removal
(Make Directory)
Passed Command
mkdir jan feb mar apr
: Creates four separate directories in the current working location.

Passed Command
mkdir -p a/b/c/d/
: Creates nested directory hierarchy
without throwing errors if parent directories do not exist.

Passed Command
mkdir java{1..20}
: Bash Brace Expansion
: Expands to
java1 java2 ... java20
and creates all 20 directories in a single syscall.
(Remove Empty Directories)
Passed Command
rmdir jan feb mar apr a
: Removes target directories if and only if they are completely empty. Fails on
if nested content remains.

3. File Operations, Text Processing & Editing
File Creation & Viewing

+-----------------------------------------------------------------------+
|                       File Viewing Utilities                          |
+-------------------+---------------------------------------------------+
| Command           | Operational Characteristics                       |
+-------------------+---------------------------------------------------+
| cat file1         | Concatenates &amp; prints entire file to stdout       |
| head alpha        | Displays first 10 lines of file                   |
| tail alpha        | Displays last 10 lines of file                    |
| head -5           | Displays first 5 lines (used with pipe)           |
| less alpha        | Interactive paged viewer (supports search /)      |
| more alpha        | Basic paged viewer (forward scroll only)          |
| wc alpha          | Outputs line count, word count, byte count        |
| wc -wl alpha      | Displays word count (-w) and line count (-l)      |
+-------------------+---------------------------------------------------+

File Manipulation & Wildcards
& Brace Expansion
Passed Command
touch python{1..20}.txt
: Creates 20 empty files named
python1.txt
python20.txt
using shell brace expansion.
(Move / Rename)
Passed Commands
mv file file3
into directory
mv aplha alpha
-> Fixes spelling of file from
mv output test/output
-> Relocates
into directory
(Remove Files)
Passed Commands
-> Prompts or removes single file.
rm -v file2
-> Verbose mode (
), displays file name as it is unlinked.
rm -vf file2
-> Force mode (
) ignores missing files without prompting.
rm -rf java{1..20}
-> Recursive (
) deletion of 20 directories.
-> Recursively force deletes all non-hidden files/directories in current path.

Text Editing (
Passed Commands
Key Observations
creates two separate buffers (
) due to unescaped space.

Vim operates in 3 main modes:

Command Mode
Insert Mode
Extended Command Mode

4. Standard Streams, Redirection & Pipelining
Linux processes handle data via three standard file descriptors:
\\text{FD 0} = \\text{stdin}, \\quad \\text{FD 1} = \\text{stdout}, \\quad \\text{FD 2} = \\text{stderr}\

+-------------------+
                      |   Linux Command   |
                      +---------+---------+
                                |
             +------------------+------------------+
             |                                     |

    FD 1 (stdout)                         FD 2 (stderr)
             |                                     |
    +--------v--------+                   +--------v--------+
    | &gt; (Overwrite)   |                   | &amp;&gt; (Redirect    |
    | &gt;&gt; (Append)     |                   |  Both Streams)  |
    +-----------------+                   +-----------------+

Redirection Commands Analyzed
echo "Hello World" &gt; file4
: Overwrites
with string output.
cal &gt; f1
: Captures stdout of calendar command into
date &gt;&gt; f1
: Appends timestamp output to bottom of
ls -l Desktop apple banana carrot f1 X &amp;&gt; output
: Redirects both stdout (FD 1) and stderr (FD 2) into file
history | head -5
) stdout of
into stdin of

5. File Links: Hard Links vs. Symbolic (Soft) Links
Hard Link Structure                       Symbolic Link Structure

+-----------------------+                 +---------------------------+
| Inode 1048576         |                 | Inode 1048577             |
| (Data Blocks on Disk) |                 | (Target Path: "/imp")     |
+-----------+-----------+                 +-------------+-------------+
            |                                           |
    +-------+-------+                           +-------v-------+
    |               |                           | Pointer Path  |
+---v---+       +---v---+                       +---------------+
| "imp" |       | "f1"  |                       | "test_imp"    |
+-------+       +-------+                       +---------------+

Commands & Verification Lifecycle
Create Target File
Create Hard Links
ln imp us1/f1
ln imp us2/f2
ln imp us3/f3
: Increments inode link count from 1 to 4. All point to identical data blocks.

Verify Hard Link Inodes
ls -li imp us1/f1 us2/f2 us3/f3
: Same inode number across all 4 path entries.

Create Symbolic (Soft) Link
ln -s imp test_imp
: Creates new inode containing pointer string
Unlink / Remove Target File
: Hard links (
, etc.) remain fully intact. Symbolic link (
) becomes a broken symlink.

Recreate Target
-> Soft link resolves once again to new file instance.

6. Advanced Searching & Text Filtering
Syntax & Rules
find [search_path] [expression_options] [actions]
Commands Analyzed
Find File by Name
find / -type f -name alpha | head -5
(regular files),
-name alpha
(exact name match).

Find Files by Size Range
find / -size +10M -size -15M | wc -l
(greater than 10 MiB),
(less than 15 MiB).

Redirect Search Results
find / -size +10M -size -30M &amp;&gt; find_output
Redirects matches and permission denied errors into file
find_output
Pattern Matching
grep "One" alpha
: Searches case-sensitively for
grep -i "one" alpha
--ignore-case

7. User & Group Account Administration
Core Configuration Files

+------------------+----------------------------------------------------+
| File Path        | Key Stored Attributes                              |
+------------------+----------------------------------------------------+
| /etc/passwd      | Username:x:UID:GID:GECOS:HomeDir:Shell             |
| /etc/shadow      | Username:EncryptedPassword:LastChanged:Min:Max...  |
| /etc/group       | GroupName:x:GID:GroupMembers                       |
| /etc/gshadow     | GroupName:EncryptedGroupPassword:Admins:Members    |
| /etc/default/useradd | Default parameters (SHELL, HOME, SKEL, EXPIRE) |
| /etc/login.defs  | System-wide password aging limits &amp; UID ranges     |
+------------------+----------------------------------------------------+

User Administration Commands
Account Creation & Password Assignment
useradd hero
: Creates user
, allocating default UID/GID and home directory
passwd hero
: Sets password for
/etc/shadow
useradd Chhayansh
: Creates account
useradd user1
: Creates account
Account Modifications (
usermod -c "Shaktimaan" hero
: Sets GECOS comment field to
"Shaktimaan"
usermod -c "Ryder" Chhayansh
: Sets comment to
usermod -d /Chhayansh Chhayansh
: Changes home directory path in
/etc/passwd
usermod -d /home/Chhayansh Chhayansh
: Reverts home directory path to standard
/home/Chhayansh
usermod -s /sbin/nologin Chhayansh
: Disables interactive shell login.
usermod -s /bin/bash Chhayansh
: Restores interactive Bash shell.
usermod -g new user1
: Changes primary group to
usermod -G WhatsApp user1
: Assigns supplementary group
Account Switching & Inspection
: Switches to
keeping current environment.
su - Chhayansh
: Switches to
with full login environment transition (
: Displays UID, GID, and all group memberships.
getent passwd user1
/etc/passwd
Account Deletion
userdel -r hero
user account and recursively removes home directory (
userdel -r Ryder
: Deletes user
and home directory.
userdel -r admin
: Deletes user
and home directory.

Group Administration & Password Aging
Group Operations
groupadd development
: Creates new group
groupmod -n WhatsApp development
: Renames group
groupadd new
: Creates group
gpasswd -d user1 WhatsApp
from supplementary group
gpasswd -r WhatsApp
: Removes group password from
groupdel WhatsApp
: Deletes group
Password Aging Management (
chage user1 -l
: Lists current password aging rules for
chage user1
: Interactively sets password aging parameters (MAX_DAYS, MIN_DAYS, WARN_DAYS).

8. Analysis of Typos & Error States from History
Erroneous Command
Shell Error Message / Behavior
Cause & Correction
mdkir jan feb mar apr
bash: mdkir: command not found...

Typo in binary name. Correct command:
cd .touch file.txt
bash: cd: .touch: No such file or directory
touch file.txt
bash: c: command not found...
. Correct command:
bash: las: command not found...
. Correct command:
usermode -u 0 hero
bash: usermode: command not found...
usermod -u 0 hero
user -s /sbin/nologin Chhayansh
bash: user: command not found...
usermod -s /sbin/nologin Chhayansh
vim etc/passwd
Creates empty file
in relative path
Missing leading
vim /etc/passwd
vim /etc/login.dfs
Opens new file
/etc/login.dfs
Typo in configuration filename. Correct:
/etc/login.defs

9. RHCSA Exam Question Scenarios & Solutions
Scenario 1: File Search and Extraction
: Find all regular files under
that are larger than 5 MiB and owned by
, copying them to
/var/tmp/find_results

# Step 1: Create target directory
mkdir -p /var/tmp/find_results

# Step 2: Execute find with -exec copy action
find /etc -type f -size +5M -user root -exec cp -p {} /var/tmp/find_results/ \;

# Step 3: Verify target contents
ls -la /var/tmp/find_results/
Scenario 2: User Account Creation with Specifications
: Create a user account
meeting the following requirements:

Primary group:

Supplementary group:
/sbin/nologin
"External Consultant"

# Step 1: Create group with specific GID
groupadd -g 2500 consultants

# Step 2: Create user with required flags
useradd -u 2500 -g consultants -G wheel -s /sbin/nologin -c "External Consultant" consultant

# Step 3: Verify user account details
id consultant
getent passwd consultant
Scenario 3: Password Aging Policy Enforcement
: Configure password aging for user
so that passwords expire every 90 days, require a minimum change interval of 7 days, and warn the user 14 days before expiration.

# Step 1: Configure password aging using chage
chage -M 90 -m 7 -W 14 consultant

# Step 2: Verify aging settings
chage -l consultant
RHCSA Module 2: File Permissions, Ownership, Special Bits & Group Collaboration
Covering Course Day 5 (100% History Command Extraction)

1. Executive Summary & Curriculum Mapping
Module 2 covers POSIX file permissions, user/group ownership, special permission bits (SUID, SGID, Sticky Bit), default file creation masks (
), and practical collaborative directory workflows.

This module aligns directly with:

RH124 Chapter 4
: Controlling Access to Files from the Command Line
RH124 Chapter 5
: Managing Local User Accounts and Groups
EX200 Objective
: Manage security permissions, ownership, and special permissions on shared directories.

2. Fundamental POSIX Permission Model
POSIX permissions divide access rights into three categories (
) with three permission bits (

+-----------------------------------------------------------------------+
 |                     POSIX Permission Architecture                     |
 +-----------------------------------------------------------------------+
 | File Type | Owner (u)   | Group (g)   | Others (o)  | Security Context|
 |   d / -   |  r   w   x  |  r   w   x  |  r   w   x  |      . / +      |
 |  (1 char) | (3 chars)   | (3 chars)   | (3 chars)   |    (1 char)     |
 +-----------+-------------+-------------+-------------+-----------------+
 | Octal Bit |  4   2   1  |  4   2   1  |  4   2   1  |                 |
 +-----------+-------------+-------------+-------------+-----------------+

Permission Bit Interpretation: Files vs. Directories
Meaning on Regular Files
Meaning on Directories
View file contents (
List contents of directory (
Modify or overwrite file content.

Create, delete, or rename files inside directory.

Run file as a binary executable or script.

Traverse/enter directory (

3. Command Analysis: Modifying File Permissions (
(Change Mode) was used extensively in both
symbolic mode
numeric octal mode
chmod Command Syntax

                     +--------------------------+
                     | chmod [options] mode file|
                     +------------+-------------+
                                  |
            +---------------------+---------------------+
            |                                           |

    Symbolic Mode                               Numeric Octal Mode
 (chmod ugo+rwx file1)                           (chmod 777 file1)
Complete Extraction & Component Breakdown of

+------------------------------------------------------------------------------------------------------------------------+
| Passed Command           | Mode Type | Targeted Category | Operational &amp; Behavioral Details                            |
+--------------------------+-----------+-------------------+-------------------------------------------------------------+
| touch file1              | File Sync | Owner: root       | Creates empty file file1 with default mask permissions.    |
| ls -l file1              | Inspection| All               | Displays permission string (-rw-r--r--.).                   |
| chmod u+x file1          | Symbolic  | User (Owner)      | Adds execute (+x) permission to file owner only.            |
| chmod ugo+ x file1       | Symbolic  | Syntax Error      | Failed: Space between + and x caused command parsing error. |
| chmod ugo + x file1      | Symbolic  | Syntax Error      | Failed: Space after operator. Correct: ugo+x.               |
| chmod x file1            | Symbolic  | Implicit All (a)  | Equivalent to chmod a+x; adds execute permission to all.    |
| chmod ugo file1          | Symbolic  | Invalid Mode      | Failed: Missing operator (+, -, or =).                      |
| chmod ugo+x file1        | Symbolic  | User, Group, Other| Adds execute permission to Owner, Group, and Others.        |
| chmod ugo+w file1        | Symbolic  | User, Group, Other| Adds write (+w) permission to all categories.               |
| chmod ugo-w-x file1      | Symbolic  | User, Group, Other| Removes write (-w) and execute (-x) from all categories.    |
| chmod ugo+rwx file1      | Symbolic  | User, Group, Other| Grants full read, write, execute permissions (rwxrwxrwx).   |
| chmod ugo-rwx file1      | Symbolic  | User, Group, Other| Strips all permissions (---------).                          |
| chmod a+7 file1          | Mixed     | Invalid Syntax    | Failed: Cannot combine symbolic 'a' with numeric digit '7'.  |
| chmod 7 file1            | Octal     | Implicit 007      | Sets Owner=---, Group=---, Others=rwx (007).                |
| chmod 777 file1          | Octal     | All Categories    | Sets Owner=rwx (7), Group=rwx (7), Others=rwx (7).          |
| chmod 000 file1          | Octal     | All Categories    | Removes all permissions from all users (---------).         |
| chmod 755 /assignment/   | Octal     | Directory         | Owner=rwx (7), Group=r-x (5), Others=r-x (5).               |
| chmod 777 /assignment/   | Octal     | Directory         | Grants worldwide rwx access to /assignment/.                |
+------------------------------------------------------------------------------------------------------------------------+

4. Command Analysis: Ownership Administration (
File ownership determines which user account (
) and primary group (
) apply to access control checks.
chown / chgrp Architecture

  +---------------------------------------------------------------+
  | chown OWNER:GROUP filename    --&gt; Sets user AND group ownership|
  | chown OWNER filename          --&gt; Changes user owner only     |
  | chown :GROUP filename         --&gt; Changes group owner only    |
  | chgrp GROUP filename          --&gt; Changes group owner only    |
  +---------------------------------------------------------------+

Complete Extraction & Component Breakdown of Ownership Commands
chown root:user1 file1
Group Owner
: Changes user ownership to
and group ownership to
chown user1:user1 file1
Group Owner
: Sets both user and group owner to
chown user1 root file1
Syntax Error
: Missing colon
separator between user and group names. Linux interpreted
as a second file argument.
chown user1:root file1
Group Owner
: Assigns file user owner to
and group owner to
chown :user1 file1
: Unchanged
Group Owner
: The leading colon
changes group ownership exclusively to
chgrp test /wednesday
Target Directory
New Group Owner
: Updates directory group ownership to group

5. Special Permission Bits: SUID, SGID & Sticky Bit
Special permissions extend the standard POSIX permission model to handle specific administrative and multi-user security requirements.

+------------------------------------------------------------------------------------+
|                               Special Permission Bits                              |
+-------------+-------------+-----------------------+--------------------------------+
| Special Bit | Octal Value | Mode Representation   | Primary Use Case               |
+-------------+-------------+-----------------------+--------------------------------+
| **SUID**    | **`4000`**  | `rwsr-xr-x` (`u+s`)   | Executables (`/usr/bin/passwd`)|
| **SGID**    | **`2000`**  | `rwxr-sr-x` (`g+s`)   | Group Collaboration Directories|
| **Sticky**  | **`1000`**  | `rwxrwxrwt` (`o+t`)   | Shared Drop Folders (`/tmp`)   |
+-------------+-------------+-----------------------+--------------------------------+

1. Set User ID (SUID) — Octal
Symbolic Flag
Behavior on Executable Files
: When executed by a unprivileged user, the binary runs with the effective privileges of the
), rather than the user launching it.

Classic Example
/usr/bin/passwd
permissions, allowing standard users to update their password in
/etc/shadow

2. Set Group ID (SGID) — Octal
Symbolic Flag
Behavior on Directories
: Files or subdirectories created inside an SGID-enabled directory automatically
inherit the group ownership of the parent directory
, rather than the primary group of the user creating the file.

Course Command
chmod g+s /wednesday
(Sets permission string to

3. Sticky Bit — Octal
Symbolic Flag
Behavior on Shared Directories
: On a world-writable directory (
), the Sticky Bit prevents users from deleting or renaming files owned by other users.

Only the file owner or
can delete the file
Course Commands
chmod o+t /assignment/
(Adds sticky bit ->
chmod o-t /assignment/
(Removes sticky bit ->

6. Step-by-Step Lab Walkthrough 1: Collaborative Directory (
, a collaborative group folder was created to demonstrate
SGID group inheritance
/wednesday Collaborative Setup

  +---------------------------------------------------------------------+
  | 1. Create Folder     : mkdir /wednesday                             |
  | 2. Create Users      : useradd a; useradd b; useradd c              |
  | 3. Create Group      : groupadd test                                |
  | 4. Add Members       : usermod -G test a; usermod -G test b         |
  | 5. Assign Group      : chgrp test /wednesday                        |
  | 6. Enable SGID       : chmod g+s /wednesday  (drwxr-sr-x)           |
  +---------------------------------------------------------------------+
                                   |
            +----------------------+----------------------+
            |                                             |

   User `a` Creates Directory                    User `c` (Non-member)
     `mkdir /wednesday/ab`                      `mkdir /wednesday/cc`
            |                                             |

   Inherits Group `test`                       Permission Denied
 (drwxr-sr-x 2 a test ab)                   (Not in group `test`)
Complete History Command Flow & Analysis

# Step 1: Create collaborative directory
mkdir /wednesday

# Step 2: Create team accounts
useradd a; useradd b; useradd c
passwd a
passwd b
passwd c

# Step 3: Create target group and assign members
groupadd test
usermod -G test a
usermod -G test b

# Note: User 'c' was intentionally excluded from group 'test'

# Step 4: Verify group members
getent group test

# Output: test:x:1002:a,b

# Step 5: Assign group ownership and set SGID bit
chgrp test /wednesday
chmod g+w /wednesday
chmod o-x /wednesday
chmod g+s /wednesday

# Step 6: Verify directory attributes
ls -ld /wednesday

# Output: drwxr-sr-x. 2 root test 6 Sep 9 16:40 /wednesday

# Step 7: Test collaboration as User 'a'
su - a
cd /wednesday
mkdir aa
mkdir ab
ls -ll

# Output for 'ab': drwxr-sr-x. 2 a test 6 Sep 9 16:51 ab

# Notice: 'ab' inherited group ownership 'test' automatically!

# Step 8: Test collaboration as User 'b'
su - b
cd /wednesday
mkdir ba
ls -ll

# Output for 'ba': drwxr-sr-x. 2 b test 6 Sep 9 16:52 ba

# Inherited group 'test'!

# Step 9: Test access as User 'c' (Non-group member)
su - c
cd /wednesday
mkdir cc

# Output: mkdir: cannot create directory ‘cc’: Permission denied

7. Step-by-Step Lab Walkthrough 2: Shared Drop Directory (
/assignment
, a public drop folder
/assignment
was set up with
permissions, and the
was toggled to verify file deletion security.
/assignment Sticky Bit Verification

  +-----------------------------------------------------------------------+
  | 1. Create Folder        : mkdir /assignment                           |
  | 2. Grant Public Write   : chmod 777 /assignment                       |
  | 3. Apply Sticky Bit     : chmod o+t /assignment  (drwxrwxrwt)         |
  +-----------------------------------------------------------------------+
                                    |
          +-------------------------+-------------------------+
          |                                                   |

User `user1` Creates File                           User `user2` Attempts Delete
`touch /assignment/user1.txt`                      `rm -rf /assignment/user1.txt`
          |                                                   |

   Owned by `user1`                                Permission Denied
(-rw-r--r-- user1 user1)                         (Protected by Sticky Bit)
Complete History Command Flow & Analysis

# Step 1: Create public assignment directory
mkdir /assignment
ls -ld /assignment/

# Step 2: Grant full world-writable permissions
chmod 777 /assignment/
ls -ld /assignment/

# Output: drwxrwxrwx. 2 root root 6 Sep 9 17:10 /assignment/

# Step 3: Apply Sticky Bit (o+t)
chmod o+t /assignment/
ls -ld /assignment/

# Output: drwxrwxrwt. 2 root root 6 Sep 9 17:12 /assignment/

# Step 4: Test file creation as user1
su - user1
cd /assignment/
mkdir user1
touch user1.txt

# Step 5: user1 attempts to delete user2's file (Pre-existing user2 files)
rm -rf user2
rm -rf user2.txt

# Output:

# rm: cannot remove 'user2': Operation not permitted

# rm: cannot remove 'user2.txt': Operation not permitted

# Step 6: Test file deletion as user2
su - user2
cd /assignment/
mkdir user2
touch user2.txt
rm -rf user1
rm -rf user1.txt

# Output:

# rm: cannot remove 'user1': Operation not permitted

# rm: cannot remove 'user1.txt': Operation not permitted

8. Analysis of Typos & Error States from History

+--------------------------------------------------------------------------------------------------------------------------+
| Erroneous Command               | System Error Message                         | Cause &amp; Correct Command                   |
+---------------------------------+----------------------------------------------+-------------------------------------------+
| `su -a`                         | `su: invalid option -- 'a'`                  | `-a` is invalid. Correct: `su - a`.       |
| `su -user2`                     | `su: invalid option -- 'u'`                  | Missing space after `-`. Correct: `su - user2`.|
| `mkdir /X` (run as user `a`)    | `mkdir: cannot create directory ‘/X’:        | Unprivileged user `a` cannot write to `/`.|
|                                 |  Permission denied`                          | Run as root or write to authorized path.  |
| `cd /wedensday`                 | `-bash: cd: /wedensday: No such file...`     | Typo in path (`wedensday` vs `wednesday`).|
| `rm -rf user1.txt` (by `user2`) | `rm: cannot remove 'user1.txt':              | Sticky Bit on `/assignment` blocked deletion.|
|                                 |  Operation not permitted`                    | User can only delete self-owned files.    |
| `l`                             | `bash: l: command not found...`              | Typo in `ls` command.                     |
| `useradd a,b,c`                 | Created user named `a,b,c` literal string    | `useradd` does not accept commas.         |
|                                 |                                              | Correct: `useradd a; useradd b; useradd c`.|
+--------------------------------------------------------------------------------------------------------------------------+

9. RHCSA Exam Question Scenarios & Solutions
Scenario 1: Collaborative Group Directory Setup
: Create a shared directory
/data/projects
with the following requirements:

Directory ownership must belong to user
Group members must have full read, write, and directory entry access.

Non-group members must have no access whatsoever.

Any new files or directories created inside
/data/projects
must automatically inherit group ownership of

# Step 1: Create directory hierarchy
mkdir -p /data/projects

# Step 2: Ensure target group exists
groupadd developers

# Step 3: Change ownership to root:developers
chown root:developers /data/projects

# Step 4: Configure POSIX permissions (Owner=rwx, Group=rwx, Other=---)
chmod 770 /data/projects

# Step 5: Apply SGID bit for automatic group inheritance
chmod g+s /data/projects

# Step 6: Verify final configuration
ls -ld /data/projects

# Expected Output: drwxr-s---. 2 root developers 6 Sep 30 11:30 /data/projects
Scenario 2: Public Shared Drop Directory with Sticky Bit
: Create a directory
/var/shares/public_drop
accessible by all local users:

All users must be able to create and read files in the directory.
be able to delete or rename files created by other users.

# Step 1: Create directory
mkdir -p /var/shares/public_drop

# Step 2: Grant world-writable permissions with Sticky Bit (1777)
chmod 1777 /var/shares/public_drop

# Step 3: Verify permissions
ls -ld /var/shares/public_drop

# Expected Output: drwxrwxrwt. 2 root root 6 Sep 30 11:35 /var/shares/public_drop
Scenario 3: Auditing Special Permissions with
: Find all files on the filesystem that have the
set and save the file list to
/root/special_permissions.txt

# Find files with SUID (4000) or SGID (2000) using -perm mode
find / -type f \\( -perm -4000 -o -perm -2000 \\) 2&gt;/dev/null &gt; /root/special_permissions.txt

# Verify output file content count
wc -l /root/special_permissions.txt
Ready to move to Module 3?

Let me know when you'd like to proceed to Module 3, which covers Software Package Management (
, and Access Control Lists (
RHCSA Module 3: Package Management (DNF/YUM), Local Repositories, Flatpak & Access Control Lists (ACLs)
Covering Course Days 6 & 7 (100% History Command Extraction)

1. Executive Summary & Curriculum Mapping
Module 3 covers enterprise software management and advanced access control mechanisms in Red Hat Enterprise Linux 10:

RPM & DNF/YUM
: Querying, installing, updating, removing packages, group management, and transaction rollbacks.

Local Repository Setup
: Mounting installation media (ISO/
) and building
/etc/yum.repos.d/*.repo
Flatpak Application Management
: Adding system/user remotes, listing applications, and sandboxed desktop package deployment.

Access Control Lists (ACLs)
: Overcoming standard POSIX limits using
for fine-grained user/group access.

Advanced File Location
: Fast database indexing with
and criteria-based searching (
This module maps directly to the official Red Hat curriculum:

RH124 Chapter 9
: Installing and Updating Software Packages
RH124 Chapter 10
: Controlling Access to Files with Access Control Lists (ACLs)
RH124 Chapter 13
: Analyzing and Storing Logs (RPM logs)
EX200 Objective
: Configure local storage repositories, manage software packages, and implement advanced security permissions using ACLs.

2. Access Control Lists (ACLs) Deep Dive (
Standard POSIX permissions (
for User, Group, Others) allow only
user owner and
group owner per file. Access Control Lists (ACLs) extend POSIX by allowing multiple individual users and groups to be assigned explicit access permissions.

POSIX vs. ACL Permission Structure

 +----------------------------------+  +----------------------------------+
 |    Standard POSIX Permissions    |  |    Access Control Lists (ACLs)   |
 +----------------------------------+  +----------------------------------+
 | Owner (u) : root      rwx        |  | Owner (u) : root      rwx        |
 | Group (g) : test      r-x        |  | user:a    : rwx    &lt;-- Explicit  |
 | Others(o) : ---       ---        |  | Group (g) : test      r-x        |
 |                                  |  | group:dev : rw-    &lt;-- Explicit  |
 | Access string: drwxr-x---.       |  | mask      : rwx    &lt;-- Maximum   |
 |                                  |  | Others(o) : ---       ---        |
 |                                  |  | Access string: drwxrwxr--+       |
 +----------------------------------+  +----------------------------------+

                                                                ^
                                                Note the "+" sign indicating ACLs
The ACL Mask & Permission Calculation
When ACLs are applied to a file or directory:
defines the
maximum permission limit
for all explicit users, explicit groups, and the owning group.

Effective Permission Formula:
\\text{Effective Permission} = \\text{Requested ACL Permission} ;\\text{AND}; \\text{ACL Mask}\
on an ACL-enabled file alters the
, not the owner permissions.

Command Analysis: ACL Extraction & Component Breakdown

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command             | Component Breakdown                             | Functional &amp; Behavioral Purpose          |
+----------------------------+-------------------------------------------------+------------------------------------------+
| man setfacl                | Binary: man; Arg: setfacl                       | Views manual page for setfacl utility.   |
| mkdir /mars                | Binary: mkdir; Target: /mars                    | Creates directory /mars for ACL testing. |
| setfacl -m u:a /mars       | Flag: -m (modify); Entry: u:a (missing perms)   | Syntax Error: Fails because rwx permissions|
|                            |                                                 | were not specified.                      |
| setfacl -m u:a:rwx /mars   | Flag: -m; Entry: user:a:rwx; Target: /mars     | Grants user 'a' explicit rwx access to   |
|                            |                                                 | /mars without changing owner.            |
| getfacl                    | Binary: getfacl; Arg: None                      | Syntax Error: Requires target file/dir.  |
| getfacl /mars              | Binary: getfacl; Target: /mars                  | Reads and displays all ACL entries.      |
| setfacl -m g:test:rwx /mars| Flag: -m; Entry: group:test:rwx; Target: /mars | Grants group 'test' explicit rwx access. |
| setfacl -x u:a /mars       | Flag: -x (remove); Entry: user:a                | Removes explicit ACL rule for user 'a'.  |
| setfacl -x g:test /mars    | Flag: -x; Entry: group:test                     | Removes explicit ACL rule for group test.|
| setfacl -b /mars           | Flag: -b (remove-all / wipe)                    | Wipes ALL extended ACL rules and removes |
|                            |                                                 | the '+' security marker.                 |
| chmod 111 /mars            | Binary: chmod; Octal: 111                       | Sets POSIX bits --x--x--x; modifies mask.|
| chmod 000 /mars            | Binary: chmod; Octal: 000                       | Strips all POSIX bits; sets mask to ---. |
+-------------------------------------------------------------------------------------------------------------------------+

Output Verification:
getfacl /mars

# file: mars

# owner: root

# group: root
user::rwx
user:a:rwx
group::r-x
group:test:rwx
mask::rwx
other::---

3. Local Repository Configuration & ISO Media Mounting
Red Hat Enterprise Linux 10 utilizes the
package manager, which relies on software repositories (
files) located in
/etc/yum.repos.d/
Repository Architecture &amp; ISO Mounting

 +--------------------+       mount /dev/sr0 /mount1       +--------------------+
 | Optical / ISO Drive| ---------------------------------&gt; |  Mount Path        |
 | (/dev/sr0)         |                                    |  /mount1           |
 +--------------------+                                    +---------+----------+
                                                                     |
                     +-----------------------------------------------+
                     |
                     +---&gt; BaseOS/Packages/      (Core OS RPMs)
                     +---&gt; AppStream/Packages/   (Applications &amp; Modules)
                                     |

                                     v
                       /etc/yum.repos.d/local.repo

              +---------------------------------------------+
              | [BaseOS]                                    |
              | name = BaseOS Repository                    |
              | baseurl = file:///mount1/BaseOS             |
              | enabled = 1                                 |
              | gpgcheck = 0                                |
              |                                             |
              | [AppStream]                                 |
              | name = AppStream Repository                 |
              | baseurl = file:///mount1/AppStream          |
              | enabled = 1                                 |
              | gpgcheck = 0                                |
              +---------------------------------------------+

Complete Command Workflow from Session History

# Step 1: Inspect block devices to locate ISO optical drive
lsblk

# Identifies /dev/sr0 or mounted media at /run/media/root/RHEL-10-1-BaseOS-x86_64

# Step 2: Explore repository directory structure inside optical media
cd /run/media/root/RHEL-10-1-BaseOS-x86_64
cd AppStream/Packages/
ls
cd ../../BaseOS/Packages/
ls

# Step 3: Manual persistent mounting workflow (Course Day 7)
mkdir /mount1
mount /dev/sr0 /mount1
lsblk

# Step 4: Create custom repo configuration file
cd /etc/yum.repos.d/
vim cp.repo
Configuration Syntax (
/etc/yum.repos.d/cp.repo
[AppStream]
name = AppStream
baseurl = file:///run/media/root/RHEL-10-1-BaseOS-x86_64/AppStream
gpgcheck = 0
enabled = 1
[BaseOS]
name = BaseOS
baseurl = file:///run/media/root/RHEL-10-1-BaseOS-x86_64/BaseOS
gpgcheck = 0
enabled = 1

# Step 5: Verify repository registration
yum repolist all

4. DNF / YUM & RPM Package Management
Red Hat Enterprise Linux provides package management at two levels:

RPM (Red Hat Package Manager)
: Low-level tool for inspecting, querying, and installing single local
files without automatic dependency resolution.
: High-level package management engine that automatically resolves dependencies, fetches packages from repositories, and tracks transaction history.

+-----------------------------------------------------------------------------------+
|                        Package Manager Tool Matrix                                |
+----------------------+--------------------------+---------------------------------+
| Operation            | RPM Command (Low-Level)  | DNF / YUM Command (High-Level)  |
+----------------------+--------------------------+---------------------------------+
| Query if Installed   | `rpm -q httpd`           | `yum list installed httpd`      |
| List Package Files   | `rpm -ql httpd`          | `dnf repoquery -l httpd`        |
| List Config Files    | `rpm -qc httpd`          | N/A                             |
| List Documentation   | `rpm -qd httpd`          | N/A                             |
| Find Owning Package  | `rpm -qf /var/www/html`  | `yum provides /var/www/html`    |
| Install Package      | `rpm -i pkg.rpm`         | `yum install httpd`             |
| Remove Package       | `rpm -e httpd`           | `yum remove httpd -y`           |
+----------------------+--------------------------+---------------------------------+

Command Analysis: DNF/YUM & RPM History Extraction

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command             | Component Breakdown                             | Functional &amp; Behavioral Purpose          |
+----------------------------+-------------------------------------------------+------------------------------------------+
| rpm -q hhtpd               | Binary: rpm; Flag: -q (query); Target: hhtpd    | Syntax Error: Package 'hhtpd' not found. |
| rpm -q httpd               | Binary: rpm; Flag: -q; Target: httpd            | Queries RPM database to check if httpd   |
|                            |                                                 | is currently installed.                  |
| rpm -ql httpd              | Binary: rpm; Flags: -q, -l (list files)         | Lists all files installed by httpd.      |
| rpm -qd httpd              | Binary: rpm; Flags: -q, -d (documentation)      | Lists documentation files (man pages).   |
| rpm -qc httpd              | Binary: rpm; Flags: -q, -c (config files)       | Lists configuration files (/etc/httpd/*).|
| yum list                   | Binary: yum; Subcommand: list                   | Lists all installed and available RPMs.  |
| yum list python            | Binary: yum; Target: python                     | Queries availability of exact package.   |
| yum list python*           | Binary: yum; Target: python* (Wildcard)         | Lists all packages starting with 'python'.|
| yum search all "web server"| Subcommand: search; Keyword: "web server"       | Searches package names and descriptions. |
| yum info httpd             | Subcommand: info; Target: httpd                 | Displays size, version, summary of httpd.|
| yum provide /var/www/html  | Subcommand: provide; Path: /var/www/html        | Finds which package owns /var/www/html.  |
| yum provides /var/www/html | Subcommand: provides (Alias)                    | Identifies owning package ('httpd').     |
| yum install httpd          | Subcommand: install; Target: httpd              | Resolves dependencies and installs httpd.|
| yum remove httpd -y        | Subcommand: remove; Option: -y (auto-confirm)   | Erases package httpd without prompting.  |
| yum update                 | Subcommand: update                              | Updates all installed packages to latest.|
| yum list kernel            | Subcommand: list; Target: kernel                | Displays installed and available kernels.|
| yum group list             | Subcommand: group list                          | Displays available package groups.       |
| yum group info 'Security..'| Subcommand: group info; Group: 'Security Tools' | Lists mandatory/optional group packages. |
| yum group install 'Sec...' | Subcommand: group install                       | Installs all packages in 'Security Tools'|
| tail /var/log/dnf.rpm.log  | Binary: tail; Log: /var/log/dnf.rpm.log         | Inspects recent DNF transaction logs.    |
| yum history                | Subcommand: history                             | Displays list of recent transactions.    |
| yum undo dnf 7             | Syntax Error: Invalid syntax                    | Failed: Correct syntax is 'history undo'.|
| yum history undo 4         | Subcommand: history undo; Transaction ID: 4     | Reverses transaction ID 4 (uninstalls).  |
| yum history undo 5         | Subcommand: history undo; Transaction ID: 5     | Reverses transaction ID 5.               |
| sudo dnf install https://..| Target: EPEL release RPM URL                    | Installs Extra Packages for Enterprise   |
|                            |                                                 | Linux (EPEL) 10 repository.              |
+-------------------------------------------------------------------------------------------------------------------------+

5. Flatpak Application Management
Flatpak provides desktop application virtualization, packaging software into isolated sandboxes that run across Linux distributions regardless of underlying library versions.

Flatpak System vs. User Architecture

 +-----------------------------------------------------------------------+
 |                         Flatpak Runtime Engine                        |
 +-----------------------------------+-----------------------------------+
 | System Scope (--system)           | User Scope (--user)               |
 | Path: /var/lib/flatpak/           | Path: ~/.local/share/flatpak/     |
 | Privilege: Requires root/sudo     | Privilege: Unprivileged User      |
 | Target: Available to all users    | Target: Available to single user  |
 +-----------------------------------+-----------------------------------+

Complete History Command Breakdown for Flatpak
yum install flatpak -y
: Installs Flatpak framework via DNF/YUM.
flatpak remotes
: Lists configured remote repositories (e.g., Flathub, Fedora OCI).
flatpak remotes -d
: Displays detailed information including URLs and GPG key settings.
flatpak remote-ls --app
: Lists available application packages in enabled remotes.
flatpak remote-add --if-not-exists fedora oci+https://registry.fedoraproject.org
--if-not-exists
: Prevents duplicate registration errors.

URL: OCI registry endpoint for official Fedora Flatpaks.
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpak.org
: Registers Flathub repository.
flatpak remotes --user
: Displays remotes registered exclusively for the active unprivileged user.
flatpak remotes-ls --user
: Fails due to typo (

6. Fast Database Indexing (
) & Advanced Searching (
Database Indexing with
: Scans the filesystem and builds the mlocate binary database (
/var/lib/mlocate/mlocate.db
/var/lib/plocate/plocate.db
locate passwd
: Instantly searches the database index for path strings containing
. Much faster than
, but does not show files created after the last
Advanced Searching Options with

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command             | Component Breakdown                             | Functional &amp; Behavioral Purpose          |
+----------------------------+-------------------------------------------------+------------------------------------------+
| find / -name passwd        | Criteria: Exact name matching                   | Searches for entries named 'passwd'.     |
| find / -iname '*pass*'     | Criteria: Case-insensitive wildcard match       | Matches 'PASSWD', 'Password', 'passwd'.  |
| find / -user admin         | Criteria: Owned by user 'admin'                 | Locates files owned by UID/User 'admin'. |
| find / -group test         | Criteria: Owned by group 'test'                 | Locates files owned by group 'test'.     |
| find / -uid 1000           | Criteria: Owned by numeric UID 1000             | Locates files matching UID 1000.         |
| find / -perm 222           | Criteria: Exact octal mode 222                  | Matches files with exact permissions 222.|
| find / -perm g=s           | Criteria: Exact match for SGID bit              | Finds directories with SGID set.         |
| find / -perm -g=s          | Criteria: Mask match (- mode)                   | Finds files/dirs having AT LEAST SGID.   |
| find / -perm -u=s          | Criteria: Mask match (- mode)                   | Finds files having AT LEAST SUID bit.    |
| find / -mmin +120          | Criteria: Modified more than 120 minutes ago    | Locates files unmodified for &gt;2 hours.   |
| find / -mmin -5            | Criteria: Modified less than 5 minutes ago      | Locates files modified within last 5 min.|
+-------------------------------------------------------------------------------------------------------------------------+

7. Analysis of Typos & Error States from History

+--------------------------------------------------------------------------------------------------------------------------+
| Erroneous Command               | System Error Message                         | Cause &amp; Correct Command                   |
+---------------------------------+----------------------------------------------+-------------------------------------------+
| `yum install flatpack`          | `Error: Unable to find a match: flatpack`    | Typo in package name (extra 'c').         |
|                                 |                                              | Correct: `yum install flatpak`.           |
| `epm -q hhtpd`                  | `bash: epm: command not found...`            | Typo in utility name (`epm` vs `rpm`).    |
| `cd /etc/yum.repo.d`            | `bash: cd: /etc/yum.repo.d: No such file...` | Missing 's' in directory name.            |
|                                 |                                              | Correct: `cd /etc/yum.repos.d`.           |
| `flatpak remotes-ls --user`     | `error: Unknown command 'remotes-ls'`        | Incorrect hyphenation.                    |
|                                 |                                              | Correct: `flatpak remote-ls --user`.      |
| `yum undo dnf 7`                | `Loaded plugins: ... Error: Invalid command` | Wrong syntax. Correct: `yum history undo 7`.|
| `setfacl -m u:a /mars`          | `setfacl: Option -m: Incomplete spec`        | Missing permissions specification.        |
|                                 |                                              | Correct: `setfacl -m u:a:rwx /mars`.      |
| `c Packages/`                   | `bash: c: command not found...`              | Typo in `cd` command.                     |
+--------------------------------------------------------------------------------------------------------------------------+

8. RHCSA Exam Question Scenarios & Solutions
Scenario 1: Local Yum/DNF Repository Setup
: Configure your system to use an attached installation ISO at
/media/rhel10
as a local repository for both

# Step 1: Create mount point directory
mkdir -p /media/rhel10

# Step 2: Mount block device
mount /dev/sr0 /media/rhel10

# Step 3: Create repository configuration file
cat &lt;&lt; 'EOF' &gt; /etc/yum.repos.d/local-media.repo
[local-BaseOS]
name=Local RHEL 10 BaseOS
baseurl=file:///media/rhel10/BaseOS
enabled=1
gpgcheck=0
[local-AppStream]
name=Local RHEL 10 AppStream
baseurl=file:///media/rhel10/AppStream
enabled=1
gpgcheck=0
EOF

# Step 4: Clean and verify repository list
dnf clean all
dnf repolist
Scenario 2: Extended Permission Configuration with ACLs
: Configure explicit permissions on directory
/data/finance
must have full read, write, and execute permissions (
must have read and execute permissions (
Ensure all newly created files in
/data/finance
automatically inherit these permissions.

# Step 1: Create directory
mkdir -p /data/finance

# Step 2: Apply active ACLs for user alex and group auditors
setfacl -m u:alex:rwx /data/finance
setfacl -m g:auditors:r-x /data/finance

# Step 3: Apply default ACLs (d:) for inheritance
setfacl -m d:u:alex:rwx /data/finance
setfacl -m d:g:auditors:r-x /data/finance

# Step 4: Verify ACL configuration
getent passwd alex
getfacl /data/finance
Scenario 3: Locate and Extract Files Based on Permissions
: Search the entire filesystem for files that have the
-perm -4000
), copy them to
/var/tmp/suid_binaries/
, and log all errors to

# Step 1: Create output directory
mkdir -p /var/tmp/suid_binaries/

# Step 2: Execute find with -perm -4000 and -exec copy
find / -type f -perm -4000 -exec cp -p {} /var/tmp/suid_binaries/ \; 2&gt;/dev/null

# Step 3: Verify copied contents
ls -la /var/tmp/suid_binaries/
Ready for Module 4?

Let me know when you'd like to proceed to Module 4, covering Network Interface Configuration (
), Port Monitoring (
), and Systemd Service Management (
RHCSA Module 4: Network Configuration (
), Systemd Services (
), & Job Scheduling (
Covering Course Days 8, 9, & 10 (100% History Command Extraction)

1. Executive Summary & Curriculum Mapping
Module 4 establishes mastery over core system infrastructure management in Red Hat Enterprise Linux 10:

Systemd Unit & Service Management
: Controlling daemons, inspecting active/failed states, blocking services with masking, and managing
Network Inspection & Low-Level Diagnostics
: Analyzing interfaces with
, checking routes, tracing hops with
, and auditing open network ports with
NetworkManager Configuration (
: Creating, modifying, activating, and deleting persistent Ethernet connections, setting static IPv4 addresses, gateways, and DNS servers.

System Hostname Control
: Configuring static hostnames via
and verifying
/etc/hostname
Automated Task Scheduling
: Configuring deferred jobs (
), user crontabs (
), system-wide cron directories, and
timer units.

This module maps directly to the official Red Hat curriculum:

RH124 Chapter 8
: Configuring Networking
RH124 Chapter 11
: Controlling Services and Daemons
RH134 Chapter 3
: Scheduling Future Tasks
EX200 Objective
: Configure networking and hostname resolution, manage system services/daemons, and schedule tasks using cron and systemd timers.

2. Systemd Service Management Architecture (
In RHEL 10,
is the PID 1 initialization process and system manager. Utilities and services are organized into
Systemd Service Lifecycle State Machine

 +-----------------------------------------------------------------------------------+
 |                                   systemctl                                       |
 +-----------------------------------------------------------------------------------+
                                           |
         +---------------------------------+---------------------------------+
         |                                 |                                 |

 [ start / stop ]                 [ enable / disable ]                [ mask / unmask ]
         |                                 |                                 |

         v                                 v                                 v
 Active State                     Unit Auto-Start                    Administrative Lock
 (Running in memory)              (On Boot: /etc/systemd/system/)    (Symlinked to /dev/null)
 - active (running)               - enabled                          - masked (Cannot start)
 - inactive (dead)                - disabled                         - unmasked
 - failed                         - static
Command Analysis: Systemd Unit Extraction & Breakdown

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                   | Component Breakdown                           | Functional &amp; Behavioral Purpose      |
+----------------------------------+-----------------------------------------------+--------------------------------------+
| systemctl                        | Subcommand: None                              | Lists all active systemd units.      |
| systemctl list-unit              | Subcommand: list-unit (Typo)                  | Syntax Error: Invalid subcommand.    |
| systemctl list-units --service   | Flag: --service (Invalid)                     | Syntax Error: Requires --type.       |
| systemctl list-units --type service | Subcommand: list-units; --type service     | Lists all active service units.      |
| systemctl list-units --type=service --all | Flag: --all                           | Lists active AND inactive services.  |
| systemctl list-unit-files --type=service --all | Subcommand: list-unit-files     | Displays unit auto-start boot status |
|                                  |                                               | (enabled, disabled, masked, static). |
| systemctl status httpd           | Subcommand: status; Target: httpd             | Displays PID, memory, log tail, and  |
|                                  |                                               | active/enabled state for httpd.      |
| systemctl start httpd            | Subcommand: start; Target: httpd              | Launches httpd service in memory.    |
| systemctl stop httpd             | Subcommand: stop; Target: httpd               | Terminates running httpd daemon.     |
| systemctl restart httpd          | Subcommand: restart; Target: httpd            | Stops then starts daemon (new PID).  |
| systemctl reload httpd           | Subcommand: reload; Target: httpd             | Reloads config files without losing  |
|                                  |                                               | active connections/PID.              |
| systemctl enable httpd           | Subcommand: enable; Target: httpd             | Creates symlink in /etc/systemd/     |
|                                  |                                               | system/ to start httpd at boot.      |
| systemctl disable httpd          | Subcommand: disable; Target: httpd            | Removes boot symlink from filesystem.|
| systemctl mask httpd             | Subcommand: mask; Target: httpd               | Symlinks unit file to /dev/null;     |
|                                  |                                               | blocks all manual/automatic starts.  |
| systemctl unmask httpd           | Subcommand: unmask; Target: httpd             | Removes /dev/null link; restores     |
|                                  |                                               | service start capability.            |
| systemctl is-active httpd        | Subcommand: is-active                         | Returns exit code 0 if running.      |
| systemctl is-enabled httpd       | Subcommand: is-enabled                        | Returns exit code 0 if boot-enabled. |
| systemctl is-disabled httpd      | Subcommand: is-disabled                       | Checks if boot auto-start is disabled|
| systemctl is-failed httpd        | Subcommand: is-failed                         | Checks if unit entered failed state. |
+-------------------------------------------------------------------------------------------------------------------------+

3. Network Diagnostics & Low-Level Interfaces (
TCP/IP Diagnostic Command Reference Layer

 +--------------------+--------------------------------------------------------------+
 | Layer / Focus      | Diagnostic Utility &amp; Command Example                         |
 +--------------------+--------------------------------------------------------------+
 | Layer 2 / Link     | `ip link show ens160`, `ip -s link show ens160`              |
 | Layer 3 / IP       | `ip addr show ens160`, `ip route`, `ping -c4 8.8.8.8`        |
 | Layer 4 / Sockets  | `ss -tan` (TCP Listening/Established), `ss -tulpn`           |
 | Path Tracing       | `tracepath access.redhat.com`                                |
 +--------------------+--------------------------------------------------------------+

Command Analysis: Network Diagnostics Breakdown
: Displays IP addresses (IPv4/IPv6) assigned to all network interfaces.
ip link show
: Displays Layer 2 MAC addresses and link flags (
ip addr show ens160
: Displays IP addresses exclusively for interface
ip -s link show ens160
Displays packet statistics (RX/TX packets, dropped packets, errors, collisions).
: Displays kernel routing table, showing default gateway addresses.
ping -c4 8.8.8.8
(count = 4)
Sends 4 ICMP Echo Request packets to
to test layer 3 connectivity.
tracepath access.redhat.com
: Traces network path to target host, measuring MTU and latency at each hop.
(TCP sockets),
(all sockets - listening and established),
(numeric ports/addresses, no DNS resolution).
(memory details for sockets).

4. NetworkManager Connection Configuration (
NetworkManager stores persistent connection profiles in
/etc/NetworkManager/system-connections/*.nmconnection
NetworkManager Keyfile Architecture

 +-----------------------------------------------------------------------+
 |                         NetworkManager Daemon                         |
 +-----------------------------------+-----------------------------------+
 | In-Memory Active State            | Persistent Storage Keyfile        |
 | Command: `nmcli con up ens160`    | Path: /etc/NetworkManager/        |
 |                                   |       system-connections/         |
 |                                   |       ens160.nmconnection         |
 +-----------------------------------+-----------------------------------+

Command Analysis:

Extraction & Typo Breakdown

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command             | Component Breakdown                             | Functional &amp; Behavioral Purpose          |
+----------------------------+-------------------------------------------------+------------------------------------------+
| nmcli dev status           | Subcommand: dev status                          | Shows physical interface state (managed, |
|                            |                                                 | unmanaged, connected, disconnected).     |
| nmcli con show             | Subcommand: con show                            | Lists all configured connection profiles.|
| nmcli dev status --active  | Option: --active                                | Shows active physical interfaces only.   |
| nmcli con show --active    | Option: --active                                | Shows currently active profiles only.    |
| nmcli -f con show ens160   | Flag: -f (field selection)                      | Displays filtered profile attributes.    |
| nmcli con del ens123       | Subcommand: con del; Target: ens123             | Deletes connection profile 'ens123'.     |
| nmcli con up ens170        | Subcommand: con up                              | Activates profile 'ens170' on hardware.  |
| nmcli con down ens160      | Subcommand: con down                            | Deactivates connection profile 'ens160'. |
+-------------------------------------------------------------------------------------------------------------------------+

Detailed Breakdown: Creating & Modifying Connections

# 1. Add connection profile (Syntax Error in History)
nmcli con add con-name ens123 type ethernet ifname ens123 ipv4,method manual ipv4. address 192.168.12.13/24 ipv4.gateway 192.168.0.254

# FAILED: Used comma instead of dot in 'ipv4,method' and space in 'ipv4. address'.

# 2. Add static Ethernet connection profile (Corrected Syntax)
nmcli con add con-name ens170 type ethernet ifname ens170 ipv4.method manual ipv4.addresses 192.168.12.13/24 ipv4.gateway 192.168.0.254

# 3. Modify existing connection profile (ens160)
nmcli con mod ens160 ipv4.addresses 10.0.0.5/24 ipv4.gateway 10.0.1.0 ipv4.dns 8.8.4.4 ipv4.method manual

# 4. Activate changes (NetworkManager requires profile reload/reactivation)
nmcli con down ens160
nmcli con up ens160

5. System Hostname Configuration (
RHEL 10 manages system hostnames using
/etc/hostname

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                         | Functional &amp; Behavioral Purpose                                                |
+----------------------------------------+--------------------------------------------------------------------------------+
| `hostnamectl`                          | Displays static hostname, chassis, kernel version, and architecture.           |
| `hostnamectl set-hostname machine1...` | Sets static hostname to 'machine1.example.com' and updates /etc/hostname.       |
| `cat /etc/hostname`                    | Verifies static hostname file content.                                         |
+----------------------------------------+--------------------------------------------------------------------------------+

6. Automated Task Scheduling: Cron, At, & Systemd Timers
RHEL 10 supports three methods for scheduling automated tasks:

Deferred Jobs (
: Executed once at a specific future time.

Periodic Cron Jobs (
: Recurring tasks executed based on time expressions.

Systemd Timer Units (
: Modern service timers replacing legacy cron jobs with fine-grained logging and dependency handling.

Cron Syntax Architecture

 +-----------------------------------------------------------------------+
 |  Minute (0-59) | Hour (0-23) | Day (1-31) | Month (1-12) | Day of Week |
 |       *        |      *      |     *      |      *       |    (0-6)    |
 +-----------------------------------------------------------------------+
 | Example: `0 2 * * 1-5 /usr/local/bin/backup.sh`                       |
 | Runs at 02:00 AM every weekday (Monday through Friday).               |
 +-----------------------------------------------------------------------+

Command Analysis: Task Scheduling Breakdown

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| atq                            | Binary: atq                                     | Lists pending deferred jobs in queue.|
| at -q                          | Option: -q (Queue specification)                | Syntax Error: Missing queue letter.  |
| crontab -eu admin              | Options: -e (edit), -u admin (user target)      | Edits user 'admin' crontab file.     |
| systemctl list-units -t timer  | Options: -t timer (--type=timer)                | Lists active systemd timers.         |
| systemctl list-unit-files -t.. | Subcommand: list-unit-files                     | Displays boot status of timer files. |
| systemctl stop logrotate.timer | Subcommand: stop                                | Pauses logrotate timer execution.    |
| systemctl disable logrotate... | Subcommand: disable                             | Disables timer auto-start at boot.   |
| systemctl enable --now logr... | Options: enable --now                           | Enables and starts logrotate.timer.  |
| systemctl status logrotate...  | Subcommand: status                              | Inspects next run time &amp; trigger log.|
+-------------------------------------------------------------------------------------------------------------------------+

7. Analysis of Typos & Error States from History

+--------------------------------------------------------------------------------------------------------------------------+
| Erroneous Command               | System Error Message                         | Cause &amp; Correct Command                   |
+---------------------------------+----------------------------------------------+-------------------------------------------+
| `systemctl list-unit`           | `Unknown command verb 'list-unit'`           | Missing trailing 's'. Correct: `list-units`.|
| `systemctl list-units --service`| `systemctl: unrecognized option '--service'` | Correct: `systemctl list-units --type service`.|
| `nmcli con mod ... ipv4.addressess` | `Error: unknown property 'ipv4.addressess'` | Typo: extra 's'. Correct: `ipv4.addresses`.|
| `nmcli con down en160`          | `Error: No connection 'en160' found.`        | Typo in interface name (`en160` vs `ens160`).|
| `at -q`                         | `at: missing queue name`                     | Missing queue parameter. Correct: `atq` or `at -q a`.|
+--------------------------------------------------------------------------------------------------------------------------+

8. RHCSA Exam Question Scenarios & Solutions
Scenario 1: Static Network Configuration with
: Configure interface
with a static IPv4 address:

Connection name:
static-ens220
IP address:

192.168.100.50/24

192.168.100.1
Ensure the connection auto-starts on boot.

# Step 1: Add connection profile
nmcli con add con-name static-ens220 type ethernet ifname ens220 \
  ipv4.method manual \
  ipv4.addresses 192.168.100.50/24 \
  ipv4.gateway 192.168.100.1 \
  ipv4.dns "8.8.8.8 8.8.4.4" \
  autoconnect yes

# Step 2: Bring connection UP
nmcli con up static-ens220

# Step 3: Verify IP address and gateway configuration
ip addr show ens220
ip route
Scenario 2: Service Management & Masking
: Configure system services according to these requirements:
is enabled to start automatically at boot and is currently running.
service is stopped and masked so it cannot be started manually or by other services.

# Step 1: Enable and start httpd atomically
systemctl enable --now httpd

# Step 2: Verify httpd status
systemctl is-active httpd
systemctl is-enabled httpd

# Step 3: Stop and mask cups service
systemctl stop cups
systemctl mask cups

# Step 4: Verify cups is masked
systemctl status cups

# Expected: Loaded: masked (Reason: Unit cups.service is masked.)
Scenario 3: Scheduling User Cron Jobs
: Create a cron job for user
/usr/local/bin/cleanup.sh
every 15 minutes from Monday through Friday.

# Step 1: Open crontab editor for user developer
crontab -e -u developer

# Step 2: Add crontab entry

# */15 * * * 1-5 /usr/local/bin/cleanup.sh

# Step 3: Verify crontab entry
crontab -l -u developer
Ready for Module 5?

Let me know when you'd like to proceed to Module 5, covering System Logging (
), Time Synchronization (
), and Archiving utilities (
RHCSA Module 5: System Logging, Time Synchronization, Performance Tuning, Archiving & Remote Transfers
Covering Course Days 11 & 12 (100% History Command Extraction)

1. Executive Summary & Curriculum Mapping
Module 5 covers core system auditing, time management, performance optimization, archiving, and remote file transfers in Red Hat Enterprise Linux 10:

System Logging & Journal Inspection
: Audit system logs in
, inject custom log messages with
, inspect structured binary logs with
, and configure persistent journal storage.

System Time & NTP Synchronization
: Control timezones, manual system clocks via
, and configure Network Time Protocol (NTP) servers with
Archiving & Compression Utilities
: Create, inspect, and extract compressed archives using
) compression algorithms.

Secure Remote File Transfers & Web Console
: Transfer files over SSH using
, manage interactive remote files via
, and enable the
web administration console.

Performance Tuning & Process Priority Management
: Optimize system workloads with
, analyze process priorities (
\\text{PR} = 20 + \\text{NI}
), and adjust scheduling weight using
This module aligns directly with:

RH124 Chapter 12
: Analyzing and Storing Logs
RH124 Chapter 13
: Archiving and Transferring Files
RH124 Chapter 14
: Tuning System Performance
RH134 Chapter 2
: Accessing Network-Attached Storage / Managing Systems Remotely
EX200 Objective
: Analyze and store logs, manage system time, archive and copy files securely, and tune system performance.

2. System Logging & Journal Management (
RHEL 10 manages system logging through two complementary services:
: Legacy plain-text logging service that writes text logs to
systemd-journald
: Modern structured binary logging daemon that captures stdout/stderr from all systemd services, kernel events, and early boot messages.

RHEL 10 Logging Architecture

 +-----------------------------------------------------------------------+
 |                     Kernel / Services / Applications                  |
 +-----------------------------------+-----------------------------------+
                                     |

                                     v
                       systemd-journald Daemon
               (Stores binary logs in /run/log/journal/
                or persistently in /var/log/journal/)
                                     |
            +------------------------+------------------------+
            |                                                 |

            v                                                 v
   `journalctl` Utility                            `rsyslogd` Daemon
(Filter by time, service,                       (Writes structured text)
 priority, or output format)                       /var/log/messages
                                                   /var/log/secure
                                                   /var/log/cron
Priority Levels in Syslog / Journald
Priority Level
Numeric Value
Severity Description
System is unusable (panic state).

Action must be taken immediately.

Critical conditions (hardware or subsystem failure).

Error conditions (service startup or operation failure).

Warning conditions (non-fatal errors).

Normal but significant condition.

Informational messages.

Debug-level messages.

Command Analysis: Logging & Journal Extraction

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| cd /var/log                    | Path: /var/log                                  | Navigates to system log directory.   |
| vim messages                   | File: /var/log/messages                         | Inspects general system events log.  |
| vim secure                     | File: /var/log/secure                           | Inspects authentication/SSH log.     |
| vim cron                       | File: /var/log/cron                             | Inspects scheduled job execution log.|
| vim boot.log                   | File: /var/log/boot.log                         | Inspects system boot messages log.   |
| vim /etc/rsyslog.conf          | Configuration File                              | Inspects rsyslog rules &amp; facilities. |
| logger -p local7.notice "..."  | Flag: -p local7.notice (facility.severity)     | Injects custom log message into      |
|                                | Message: "HAPPY BIRTHDAY JATIN JANGID"           | rsyslog/journald.                    |
| journalctl                     | Binary: journalctl                              | Displays all system journal entries. |
| journalctl -f                  | Flag: -f (--follow)                             | Live streams new incoming log lines. |
| journalctl -p err              | Flag: -p err (--priority=err)                   | Filters logs showing severity &lt;= err.|
| journalctl -u httpd.service    | Flag: -u httpd.service                          | Filters logs specifically for httpd. |
| journalctl --since today       | Flag: --since today                             | Displays logs recorded since midnight|
| journalctl --since "-1hour"    | Flag: --since "-1hour"                          | Displays logs from the last 60 min.  |
| journalctl --since "..." ...   | Flags: --since "YYYY-MM-DD", --until "..."      | Filters logs within explicit range.  |
| journalctl -0 verbose          | Flag: -0 (Typo)                                 | Syntax Error: Digit 0 instead of -o. |
| journalctl -o verbose          | Flag: -o verbose (--output=verbose)             | Displays complete metadata fields    |
|                                |                                                 | (UID, GID, SELinux context, PID).    |
| mkdir /var/log/journal         | Target Directory: /var/log/journal              | Configures persistent journal storage|
| journalctl --flush             | Flag: --flush                                   | Flushes volatile logs from /run/log/ |
|                                |                                                 | journal/ to persistent /var/log/     |
+-------------------------------------------------------------------------------------------------------------------------+

3. System Time, Timezones & NTP Synchronization (
Time Synchronization Subsystem

 +-----------------------------------------------------------------------+
 |                            Hardware Clock                             |
 +-----------------------------------+-----------------------------------+
                                     |

                                     v
                           System Clock (RTC)
                                     |
            +------------------------+------------------------+
            |                                                 |

            v                                                 v
     `timedatectl`                                      `chronyd`
 (Timezone &amp; Manual Time)                       (NTP Server Sync)
 Configuration File:                             Configuration File:
 /etc/localtime -&gt; zoneinfo                      /etc/chrony.conf
Command Analysis: Time & NTP Extraction

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| timedatectl                    | Binary: timedatectl                             | Displays local time, UTC, timezone,  |
|                                |                                                 | and NTP sync status.                 |
| timedatectl list-timezones     | Subcommand: list-timezones                      | Displays all available timezones.    |
| timedatectl set-timezone ...   | Subcommand: set-timezone America/New_York       | Updates timezone symlink.            |
| timedatectl set-timezone ...   | Subcommand: set-timezone Asia/Kolkata           | Restores India Standard Time (IST).  |
| timedatectl set-time 9:00:00   | Subcommand: set-time 9:00:00                    | Syntax Error: Fails if NTP active.   |
| timedatectl set-ntp false      | Subcommand: set-ntp false                       | Disables automatic NTP sync.         |
| timedatectl set-ntp true       | Subcommand: set-ntp true                        | Re-enables automatic NTP sync.       |
| vim /etc/chrony.conf           | Config File: /etc/chrony.conf                   | Configures NTP server pool entries.  |
| systemctl restart chronyd      | Service: chronyd                                | Restarts NTP synchronization daemon. |
| chronyc sources -v             | Binary: chronyc; Subcommand: sources -v         | Displays active NTP sources with     |
|                                |                                                 | verbose column header explanations.  |
+-------------------------------------------------------------------------------------------------------------------------+

4. Archiving & Compression Utilities (
(Tape Archive) utility bundles multiple files or directories into a single archive file (
), which can be compressed using various compression algorithms.

+------------------------------------------------------------------------------------+
|                         Tar Compression Flag Matrix                               |
+-------------------+--------------+-----------------------+-------------------------+
| Compression Type  | Tar Flag     | Standard File Extension| Compression Ratio / Speed|
+-------------------+--------------+-----------------------+-------------------------+
| **gzip**          | **`-z`**     | `.tar.gz` / `.tgz`    | Fast speed, moderate size|
| **bzip2**         | **`-j`**     | `.tar.bz2` / `.tbz`   | Balanced speed and size |
| **xz**            | **`-J`**     | `.tar.xz`             | Maximum compression ratio|
+-------------------+--------------+-----------------------+-------------------------+

Tar Command Flag Mechanics

                      +-------------------------------+
                      | tar -cvzf /path/backup.tar.gz |
                      +---------------+---------------+
                                      |
         +--------------------+-------+--------------------+
         |                    |                            |

  `-c` (Create)        `-v` (Verbose)               `-z` (gzip)
  `-x` (Extract)       Shows processed files        `-j` (bzip2)
  `-t` (Table/List)                                 `-J` (xz)
                                                           |

                                                    `-f` (Filename)
                                                    Must precede target file!
Command Analysis: Archiving Extraction & Breakdown

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| tar -cvjf ... /etc             | Flags: -c (create), -v, -j (bzip2), -f          | Creates bzip2 compressed archive     |
|                                | Target: /root/testbackup.bz2                    | of the /etc directory.               |
| tar -cvzf ... /etc             | Flags: -c, -v, -z (gzip), -f                    | Creates gzip compressed archive.     |
| tar -cvJf ... /etc             | Flags: -c, -v, -J (xz), -f                      | Creates xz compressed archive.       |
| rm rf testbackup.gzip          | Command Syntax Error                            | Failed: Missing '-' before 'rf'.     |
| mv testbackup.bz2 backup...    | Command: mv                                     | Renames backup files with standard   |
|                                |                                                 | .tar.bz2 / .tar.gz / .tar.xz names.  |
| tar -tf backup.tar.gz          | Flags: -t (table/list), -f                      | Lists contents of archive without    |
|                                | Target: backup.tar.gz                           | extracting files to disk.            |
| tar -C /backups -xvf ...       | Flags: -x (extract), -v, -f; -C /backups        | Syntax Error: Fails if directory     |
|                                | Target: backup.tar.gz                           | /backups does not exist yet.         |
| mkdir backups                  | Target Directory: backups                       | Creates local backups directory.     |
| tar -C backups -xvf ...        | Flag: -C backups                                | Extracts archive into target         |
|                                |                                                 | directory `backups/`.                |
+-------------------------------------------------------------------------------------------------------------------------+

5. Secure Remote File Transfers & Web Management Console (
Remote File Transfer Utility Comparison

 +-----------------------------------------------------------------------+
 | Utilities                                                             |
 +-----------------------------------+-----------------------------------+
 | `scp` (Secure Copy)               | `rsync` (Remote Sync)             |
 | - Copies entire files blindly     | - Delta-transfer algorithm        |
 | - No progress/differential check  | - Syncs only modified blocks      |
 | - Syntax: `scp -r src user@host:` | - Preserves permissions (`-a`)    |
 |                                   | - Syntax: `rsync -av src user@:`  |
 +-----------------------------------+-----------------------------------+

Command Analysis: Remote Transfers & Web Console Breakdown

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| sftp admin@192.168.184.131/24  | Syntax Error: CIDR notation                     | Failed: Invalid host address /24.    |
| sftp admin@192.168.184.131     | Utility: sftp                                   | Establishes interactive SFTP session.|
| scp /etc/hosts /root           | Command: scp                                    | Local file copy of /etc/hosts.       |
| scp admin@192...:/etc/hostname | Path: remote -&gt; local                           | Downloads remote file /etc/hostname  |
|                                | Target: /root/Videos/                           | to local directory.                  |
| scp /etc admin@192...:/root/   | Syntax Error: Directory source                  | Failed: Requires -r for directories. |
| scp -r /etc admin@192...:/root/| Option: -r (recursive)                          | Recursively uploads /etc directory.  |
| rsync -av /var/log admin@...   | Flags: -a (archive mode), -v (verbose)          | Synchronizes local /var/log to       |
|                                | Target: admin@192.168.184.131                   | remote destination.                  |
| rsync -av admin@... /root/...  | Path: remote -&gt; local                           | Synchronizes remote directory to     |
|                                | Target: /root/Desktop                           | local path /root/Desktop.            |
| yum install cockpit            | Package: cockpit                                | Installs Cockpit web admin console.  |
| systemctl enable cockpit.socket| Socket: cockpit.socket                          | Enables Cockpit socket activation on |
|                                |                                                 | port 9090.                           |
| firewall-cmd --add-service=... | Flag: --permanent; Service: cockpit             | Opens firewall port 9090 permanently.|
+-------------------------------------------------------------------------------------------------------------------------+

6. Performance Tuning & Process Priority Management (
System Tuning Profiles (
daemon automatically optimizes system settings (CPU governor, disk I/O schedulers, memory paging) based on predefined workloads.

+------------------------------------------------------------------------------------+
|                          Common Tuned Profiles                                     |
+-------------------+----------------------------------------------------------------+
| Profile Name      | Primary Optimization Focus                                     |
+-------------------+----------------------------------------------------------------+
| **`throughput-performance`** | Maximize I/O throughput (default for enterprise servers).|
| **`latency-performance`**    | Minimize response latency (low-latency database servers)|
| **`virtual-guest`**          | Optimized for virtual machines running on hypervisors. |
| **`hpc-compute`**            | High-Performance Computing workloads.                  |
+-------------------+----------------------------------------------------------------+

Process Priority & Scheduling (
In Linux, process scheduling priority is governed by the
), which directly affects kernel dynamic Priority (
\\text{PR} = 20 + \\text{NI}\
Nice Value Range
(Highest priority, most CPU preference) to
(Lowest priority, least CPU preference).

Standard users can only
nice values (lower priority). Root can
nice values (raise priority down to
Nice Value &amp; Priority Calculation

 +-----------------------------------------------------------------------+
 | Highest Priority             Default Priority          Lowest Priority|
 |  Nice: -20                    Nice: 0                   Nice: +19     |
 |  PR  : 0                      PR  : 20                  PR  : 39      |
 +-----------------------------------------------------------------------+

Command Analysis: Performance Tuning & Process Priority Extraction

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| tuned-adm list                 | Subcommand: list                                | Displays all available tuning profiles|
|                                |                                                 | and highlights currently active profile|
| tuned-adm recommend            | Subcommand: recommend                           | Analyzes system hardware/hypervisor  |
|                                |                                                 | and suggests best tuning profile.    |
| tuned-adm profile hpc-compute  | Subcommand: profile hpc-compute                 | Switches system profile to hpc-compute|
| tuned-adm profile virtual-guest| Subcommand: profile virtual-guest               | Switches system profile to VM guest. |
| tuned-adm off                  | Subcommand: off                                 | Disables tuned optimization profiles.|
| ps -eo pid,priority,nice,comm  | Flags: -eo (custom output fields)               | Displays PID, Priority, Nice value,  |
|   --sort=nice                  | Option: --sort=nice                             | and command name, sorted by nice.    |
| ps -eo pid,priority,nice,comm  | Option: --sort=-nice                            | Sorts process list descending by     |
|   --sort=-nice | less          | Filter: | less                                  | nice value (highest nice first).     |
| nice -n 15 sleep 10000 &amp;       | Flag: -n 15 (Nice value +15)                    | Launches process `sleep 10000` with  |
|                                | Target: sleep 10000 &amp; (Background)              | lowered priority (NI = 15, PR = 35). |
| renice -n -20 7120             | Flag: -n -20; Target PID: 7120                  | Dynamically updates running process  |
|                                |                                                 | PID 7120 to highest priority (NI=-20)|
| renice -n 5 7120               | Flag: -n 5; Target PID: 7120                    | Resets PID 7120 nice value to 5.     |
+-------------------------------------------------------------------------------------------------------------------------+

7. Analysis of Typos & Error States from History

+--------------------------------------------------------------------------------------------------------------------------+
| Erroneous Command               | System Error Message                         | Cause &amp; Correct Command                   |
+---------------------------------+----------------------------------------------+-------------------------------------------+
| `journalctl -0 verbose`         | `journalctl: invalid option -- '0'`          | Digit 0 instead of letter 'o'.            |
|                                 |                                              | Correct: `journalctl -o verbose`.         |
| `rm rf testbackup.gzip`         | `rm: cannot remove 'rf': No such file...`    | Missing hyphen `-` before `rf`.           |
|                                 |                                              | Correct: `rm -rf testbackup.gzip`.        |
| `mkrdir backups`                | `bash: mkrdir: command not found...`         | Typo in `mkdir`. Correct: `mkdir backups`.|
| `sftp admin@192.168.184.131/24` | `ssh: Could not resolve hostname...`         | CIDR `/24` is invalid for SSH hosts.      |
|                                 |                                              | Correct: `sftp admin@192.168.184.131`.    |
| `scp /etc admin@192...:/root/`  | `scp: /etc: omit directory`                  | Missing `-r` option for directory upload. |
|                                 |                                              | Correct: `scp -r /etc admin@...`.         |
| `tuned -adm -list`              | `bash: tuned: command not found...`          | Space before `-adm`.                      |
|                                 |                                              | Correct: `tuned-adm list`.                |
| `less ps -eo pid,priority...`   | `ps: No such file or directory`              | Passed command directly to `less`.        |
|                                 |                                              | Correct: `ps -eo ... | less`.             |
+--------------------------------------------------------------------------------------------------------------------------+

8. RHCSA Exam Question Scenarios & Solutions
Scenario 1: Persistent Journal Storage & Priority Filtering
: Configure systemd-journald to store logs persistently across reboots, flush existing logs, and locate all log entries with a priority of
or higher recorded since
09:00:00 today

# Step 1: Create persistent journal directory
mkdir -p /var/log/journal

# Step 2: Set permissions and restart journald
systemctl restart systemd-journald

# Step 3: Flush runtime journal to disk
journalctl --flush

# Step 4: Query logs matching criteria
journalctl -p err --since "09:00:00"
Scenario 2: Compressed Archive Creation and Target Extraction
compressed archive of
/var/tmp/etc_config.tar.bz2
. Then, extract its contents into
/var/tmp/restore_test/

# Step 1: Create compressed bzip2 archive
tar -cvjf /var/tmp/etc_config.tar.bz2 /etc

# Step 2: Create target restore directory
mkdir -p /var/tmp/restore_test/

# Step 3: Extract archive into target directory using -C flag
tar -C /var/tmp/restore_test/ -xvf /var/tmp/etc_config.tar.bz2

# Step 4: Verify extracted directory structure
ls -la /var/tmp/restore_test/
Scenario 3: Process Priority Management with
: Launch a background process
md5sum /dev/zero &amp;
with a nice value of
. Then locate its PID and adjust its nice value to

# Step 1: Launch command with nice value +10
nice -n 10 md5sum /dev/zero &gt; /dev/null &amp;

# Step 2: Locate process PID
pgrep -l md5sum

# Step 3: Adjust nice value to -5 using renice
renice -n -5 -p $(pgrep md5sum)

# Step 4: Verify updated Priority and Nice values
ps -o pid,priority,nice,comm -p $(pgrep md5sum)
Ready for Module 6?

Let move to Module 6, covering Storage Partitioning (
), Filesystem Creation (
), Persistent Mounts (
), and Swap Management (
RHCSA Module 6: Storage Partitioning (
), Persistent Mounts (
), Swap Space, & Logical Volume Management (LVM)
Covering Course Days 13 & 14 (100% History Command Extraction)

1. Executive Summary & Curriculum Mapping
Module 6 covers physical and logical storage administration in Red Hat Enterprise Linux 10:

Disk Inspection & MBR Partitioning
: Analyzing block storage topologies with
, and creating MBR primary/extended/logical partitions using
Filesystem Creation & Mounting
: Formatting partitions with
, managing manual mounts, and editing
systemctl daemon-reload
Swap Space Management
: Creating dedicated swap partitions with
, enabling/disabling swap space via
, and persisting swap entries in
Logical Volume Management (LVM) Architecture
: Building Physical Volumes (PV), Volume Groups (VG), and Logical Volumes (LV).

Dynamic Storage Resizing
: Extending LVs online and growing filesystems using
for XFS and
LVM Teardown Lifecycle
: Executing clean teardowns in strict sequence (
partition deletion ->
This module maps directly to the official Red Hat curriculum:

RH134 Chapter 4
: Managing Basic Storage
RH134 Chapter 5
: Managing Logical Volume Manager (LVM) Storage
EX200 Objective
: Create and configure file systems, create and manage swap space, and create and modify LVM logical volumes.

2. Linux Storage Architecture: Traditional vs. LVM
Traditional MBR Partitioning vs. LVM Architecture
      [ Traditional Storage Stack ]                 [ LVM Storage Stack ]

   +---------------------------------+      +---------------------------------+
   | Mount Point (/chhayansh, /abc1) |      | Mount Point (/lv1, /lv2)        |
   +---------------------------------+      +---------------------------------+
   | Filesystem (XFS / EXT4)         |      | Filesystem (XFS / EXT4)         |
   +---------------------------------+      +---------------------------------+
   | MBR Partition (/dev/sdb1)       |      | Logical Volume (mylv1, mylv2)   |
   +---------------------------------+      +---------------------------------+
   | Raw Disk (/dev/sdb)             |      | Volume Group (myvg)             |
   +---------------------------------+      +---------------------------------+
                                            | Physical Volumes (/dev/sdb1...) |
                                            +---------------------------------+
                                            | MBR Partitions / Disk Drives    |
                                            +---------------------------------+

3. MBR Disk Partitioning & Inspection (
RHEL 10 manages disk partitioning using
(for MBR/DOS partition tables) or
(for GPT). Per your course history,
is used exclusively.

MBR Partition Limits
Maximum Primary Partitions
: 4 primary partitions per disk.

Extended Partition
: 1 primary partition can be designated as an extended partition containing multiple
logical partitions
fdisk Interactive Command Menu

 +-----------------------------------------------------------------------+
 | Option | Action Description                                           |
 +--------+---------------------------------------------------------------+
 |  `p`   | Print the partition table.                                    |
 |  `n`   | Add a new partition (Primary or Extended/Logical).            |
 |  `d`   | Delete a partition.                                           |
 |  `t`   | Change partition system ID (e.g., `82` Swap, `8e` Linux LVM).  |
 |  `w`   | Write partition table to disk and exit.                       |
 |  `q`   | Quit without saving changes.                                  |
 +-----------------------------------------------------------------------+

Command Analysis: Disk Inspection & Partitioning Extraction

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command             | Component Breakdown                             | Functional &amp; Behavioral Purpose          |
+----------------------------+-------------------------------------------------+------------------------------------------+
| lsblk                      | Binary: lsblk                                   | Lists all block storage devices,         |
|                            |                                                 | partitions, mountpoints, and sizes.      |
| fdisk /dev/sdb             | Binary: fdisk; Target: /dev/sdb                 | Launches interactive partitioning tool   |
|                            |                                                 | on secondary drive /dev/sdb.             |
| fdisk /dev/sdc             | Target: /dev/sdc                                | Partitions third drive /dev/sdc.         |
| fdisk /dev/sdd             | Target: /dev/sdd                                | Partitions fourth drive /dev/sdd.        |
| fdisk /dev/sde             | Target: /dev/sde                                | Partitions fifth drive /dev/sde.         |
| blkid                      | Binary: blkid                                   | Displays UUIDs and filesystem TYPES for  |
|                            |                                                 | all formatted block devices.             |
| partprobe                  | Binary: partprobe                               | Forces the Linux kernel to re-read the   |
|                            |                                                 | partition table without rebooting.       |
| man parted                 | Manual Page: parted                             | Views documentation for parted tool.     |
| man fdisk                  | Manual Page: fdisk                              | Views documentation for fdisk tool.      |
+-------------------------------------------------------------------------------------------------------------------------+

4. Filesystem Creation & Persistent Mounts (
After partitioning, raw storage must be formatted with a filesystem (XFS or EXT4) and added to
for persistent mounting across system reboots.
/etc/fstab Entry Configuration Syntax

 +-----------------------------------------------------------------------------------+
 | Device Identifier | Mount Point | FS Type | Mount Options | Dump | FSck Pass      |
 | UUID=xxxx-xxxx... | /abc1       | xfs     | defaults      |  0   |  0             |
 | /dev/myvg/mylv1   | /lv1        | xfs     | defaults      |  0   |  0             |
 | UUID=yyyy-yyyy... | swap        | swap    | defaults      |  0   |  0             |
 +-----------------------------------------------------------------------------------+

Crucial Rule
: Whenever modifying
in RHEL 10,
systemctl daemon-reload
before running
so systemd updates its dynamic mount unit generators!
Command Analysis: Filesystem Formatting & Mounting Extraction

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command             | Component Breakdown                             | Functional &amp; Behavioral Purpose          |
+----------------------------+-------------------------------------------------+------------------------------------------+
| mkfs.xfs /dev/sdb1         | Binary: mkfs.xfs; Target: /dev/sdb1             | Formats /dev/sdb1 with XFS filesystem.   |
| mkfs.ex4 /dev/sdb2         | Command Typo                                    | Failed: Missing 't' in ext4.             |
| mkfs.ext4 /dev/sdb2        | Binary: mkfs.ext4; Target: /dev/sdb2            | Formats /dev/sdb2 with EXT4 filesystem.  |
| mkfs.xfs /dev/sdb3         | Target: /dev/sdb3                               | Formats /dev/sdb3 with XFS filesystem.   |
| mkfs.ext4 /dev/sdb5        | Target: /dev/sdb5 (Logical)                     | Formats logical partition /dev/sdb5.     |
| mkdir /abc1                | Directory: /abc1                                | Creates mount point directory /abc1.     |
| mkdir /chhayansh           | Directory: /chhayansh                           | Creates mount point directory /chhayansh.|
| mkdir /garvit              | Directory: /garvit                              | Creates mount point directory /garvit.   |
| mkdir /ashish              | Directory: /ashish                              | Creates mount point directory /ashish.   |
| vim /etc/fstab             | File: /etc/fstab                                | Edits persistent filesystem table.       |
| systemctl daemon-reload    | Subcommand: daemon-reload                       | Reloads systemd manager config &amp; units.  |
| mount -a                   | Flag: -a (--all)                                | Mounts all filesystems listed in fstab.  |
| umount /chhayansh ...      | Target: /chhayansh /garvit /ashish              | Unmounts three filesystems simultaneously|
| umount /abc1               | Target: /abc1                                   | Unmounts /abc1 filesystem.               |
| rmdir /chhayansh ...       | Targets: /chhayansh /ashish /garvit /abc1       | Removes empty mount point directories.   |
+-------------------------------------------------------------------------------------------------------------------------+

5. Swap Space Management (
Swap space acts as virtual memory extension on storage drives when physical RAM fills up.

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command             | Component Breakdown                             | Functional &amp; Behavioral Purpose          |
+----------------------------+-------------------------------------------------+------------------------------------------+
| mkswap /dev/sdb1           | Binary: mkswap; Target: /dev/sdb1               | Formats partition /dev/sdb1 as swap.     |
| mkswap /dev/sdb2           | Binary: mkswap; Target: /dev/sdb2               | Formats partition /dev/sdb2 as swap.     |
| swapon -a                  | Flag: -a (--all)                                | Activates all swap devices in fstab.     |
| swapon /dev/sdb1 /dev/sdb2 | Targets: /dev/sdb1 /dev/sdb2                    | Manually enables specified swap partitions|
| swapoff /dev/sdb1 /dev/sdb2| Targets: /dev/sdb1 /dev/sdb2                    | Deactivates specified swap partitions.   |
+-------------------------------------------------------------------------------------------------------------------------+

6. Logical Volume Management (LVM) Architecture & Lifecycle
LVM decouples physical disks from operating system storage, allowing dynamic online expansion across multiple physical drives.

LVM Storage Architecture Workflow

 +-----------------------------------------------------------------------------------+
 | 1. Physical Partitions | /dev/sdb1          /dev/sdc1          /dev/sdd1          |
 +------------------------+----------------------------------------------------------+
                          |                    |                    |

                          v                    v                    v
 | 2. Physical Volumes   | pvcreate /dev/sdb1 /dev/sdc1 /dev/sdd1                   |
 +------------------------+----------------------------------------------------------+
                                               |

                                               v
 | 3. Volume Group       | vgcreate myvg /dev/sdb1 /dev/sdc1 /dev/sdd1               |
 |                        | (Aggregates storage into unified pool of Physical Extents)|
 +-----------------------------------------------------------------------------------+
                                   |                       |

                                   v                       v
 | 4. Logical Volumes    | lvcreate -L 6G -n mylv1 myvg    lvcreate -L 7G -n mylv2 myvg|
 +-----------------------+-----------------------------------------------------------+
                                   |                       |

                                   v                       v
 | 5. Filesystems        | mkfs.xfs /dev/myvg/mylv1       mkfs.ext4 /dev/myvg/mylv2  |
 +-----------------------+-----------------------------------------------------------+
                                   |                       |

                                   v                       v
 | 6. Persistent Mounts  | Mount: /lv1                    Mount: /lv2                |
 +-----------------------------------------------------------------------------------+

Phase 1: Physical Volume (PV) Management
pvcreate /dev/sdb1 /dev/sdc1 /dev/sdd1
: Initializes raw partitions as LVM Physical Volumes.
pvcreate /dev/sde1
: Initializes additional partition for volume group expansion.
: Displays concise physical volume summary (PV name, VG name, size, free space).
: Displays detailed PV metadata (Total PE size, PE size = 4 MiB default).
pvremove /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1
: Clears LVM metadata from partitions.

Phase 2: Volume Group (VG) Management
vgcreate myvg /dev/sdb1 /dev/sdc1 /dev/sdd1
: Combines three PVs into storage pool
: Displays concise Volume Group summary (#PV, #LV, total size, VGFree).
: Displays detailed Volume Group metadata.
vgextend myvg /dev/sde1
: Dynamically expands
by adding Physical Volume
vgremove myvg
: Destroys Volume Group
Phase 3: Logical Volume (LV) & Filesystem Resizing

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| lvcreate -L 6G -n mylv1 myvg   | Flags: -L 6G (size), -n mylv1 (name), myvg (VG) | Carves 6 GiB Logical Volume `mylv1`. |
| lvcreate -L 7G -n mylv2 myvg   | Flags: -L 7G, -n mylv2, myvg                    | Carves 7 GiB Logical Volume `mylv2`. |
| lvs / lvdisplay                | Subcommands: lvs / lvdisplay                    | Inspects logical volume metadata.    |
| mkfs.xfs /dev/myvg/mylv1       | Target: /dev/myvg/mylv1                         | Formats mylv1 with XFS filesystem.   |
| mkfs.ext4 /dev/myvg/mylv2      | Target: /dev/myvg/mylv2                         | Formats mylv2 with EXT4 filesystem.  |
| lvextend -L 8G /dev/myvg/mylv1 | Options: -L 8G (New total size)                 | Expands mylv1 container size to 8G.  |
| xfs_growfs /dev/myvg/mylv1     | Target: /dev/myvg/mylv1                         | Expands XFS filesystem online.       |
| lvextend -L 10G /dev/myvg/mylv2| Options: -L 10G                                 | Expands mylv2 container size to 10G. |
| resize2fs /dev/myvg/mylv2      | Target: /dev/myvg/mylv2                         | Expands EXT4 filesystem online.      |
| lvreduce -r -L 5G /dev/myvg/.. | Flags: -r (resize fs), -L 5G                    | Attempts filesystem reduction.       |
|                                |                                                 | **CRITICAL**: XFS CANNOT BE REDUCED! |
+-------------------------------------------------------------------------------------------------------------------------+

XFS vs. EXT4 Resizing Comparison
: Can be expanded online using `xfs_growfs
RHCSA Module 7: Network File System (NFS) Services & AutoFS Automated On-Demand Mounting
Covering Course Day 15 (100% History Command Extraction)

1. Executive Summary & Curriculum Mapping
Module 7 covers enterprise network storage sharing and automated on-demand mounting in Red Hat Enterprise Linux 10:

NFSv4 Server Configuration
: Installing
, configuring shared export directories in
/etc/exports
, managing export access permissions, and applying rule changes with
Firewalld & RPC Service Administration
: Opening RPC and NFS services (
firewall-cmd
and managing supporting daemons.

NFS Client Mounting
: Mounting remote NFS shares manually, configuring persistent network mounts in
, and verifying active mounts with
AutoFS On-Demand Mounting
: Installing
, configuring master map files (
/etc/auto.master
/etc/auto.master.d/*.autofs
), setting up direct and indirect map files, and testing automatic mount triggering upon directory traversal.

This module maps directly to the official Red Hat curriculum:

RH134 Chapter 2
: Accessing Network-Attached Storage
EX200 Objective
: Mount and unmount network storage using NFS, and configure autofs for on-demand network storage mounting.

2. Network File System (NFS) Server Architecture & Setup
NFS allows Linux servers to export directory trees over the network, permitting remote clients to mount and access those directories as if they were local filesystems.

NFS Server and Client Architecture

 +----------------------------------+            +----------------------------------+
 |       NFS Server (machine1)      |            |       NFS Client (machine2)      |
 +----------------------------------+            +----------------------------------+
 | Shared Folder: `/thursday`       |            | Mount Point: `/kisibhinamse`     |
 | Permissions  : `chmod 777`       |            | Managed By  : fstab / autofs     |
 | Configuration: `/etc/exports`    |            | Package     : `nfs-utils`        |
 | Daemon       : `nfs-server`      |            +----------------------------------+
 +----------------------------------+                             ^
                  |                                               |
                  +--- RPC / NFS Traffic (Port 2049, 111, 20048) -+

/etc/exports
Configuration Syntax
/shared_directory  client_IP_or_Subnet(option1,option2,...)
Export Option
Functional Description
Grants read and write access to the exported directory.

Restricts export to read-only access (default).

Forces changes to be committed to disk before responding to client requests (data integrity).

Allows server to respond before disk write completes (higher speed, risk of data loss).
no_root_squash
Disables root squashing; allows remote
users to retain
privileges on the share.
root_squash
Maps remote
requests to unprivileged user
(default security).

3. Command Analysis: NFS Server & Export Management (
/etc/exports

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| yum install nfs-utils -y       | Package: nfs-utils; Flag: -y                    | Installs NFS server and client       |
|                                |                                                 | management utilities.                |
| systemctl restart nfs-server...| Daemon: nfs-server.service                      | Restarts NFS kernel daemon.          |
| systemctl enable nfs-server... | Daemon: nfs-server.service                      | Enables NFS server auto-start at boot|
| setenforce 0                   | Binary: setenforce; Target: 0 (Permissive)      | Temporarily disables SELinux enforcement|
| getenforce                     | Binary: getenforce                              | Verifies active SELinux mode.        |
| firewall-cmd --add-service=nfs | Flags: --add-service=nfs --add-service=mountd   | Opens NFS and Mountd ports           |
|   --add-service=mountd ...     | Option: --permanent                             | permanently in firewalld.            |
| firewall-cmd --reload          | Flag: --reload                                  | Reloads firewalld rules.             |
| firewall-cmd --list-services   | Flag: --list-services                           | Lists open firewall services.        |
| mkdir /thursday                | Target Directory: /thursday                     | Creates directory to export via NFS. |
| chmod 777 /thursday            | Permissions: 777                                | Grants world-writable access to share|
| vim /etc/exports               | File: /etc/exports                              | Defines exported directories/clients.|
| exportfs -v                    | Flag: -v (verbose)                              | Displays currently exported shares.  |
| exportfs -rv                   | Flags: -r (re-export all), -v (verbose)         | Refreshes export table without       |
|                                |                                                 | restarting nfs-server daemon.        |
| systemctl restart rpcbind...   | Service: rpcbind.service                        | Restarts RPC port mapper service.    |
+-------------------------------------------------------------------------------------------------------------------------+

4. NFS Client Mount Configuration & Persistent
Client machines must have
installed to mount NFS shares. Shares can be mounted manually using
mount -t nfs
or made persistent via
/etc/fstab Entry for NFS Client

 +-----------------------------------------------------------------------------------+
 | Server Export Path   | Local Mount Point | FS Type | Mount Options | Dump | Pass  |
 | 192.168.184.130:/... | /kisibhinamse     | nfs     | defaults      |  0   |  0    |
 +-----------------------------------------------------------------------------------+

Command Analysis: NFS Client Extraction & Verification

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| su - admin                     | Account Switch: admin                           | Switches session to admin user.      |
| yum install nfs-utils -y       | Package: nfs-utils                              | Installs NFS client packages.        |
| mkdir /kisibhinamse            | Target Directory: /kisibhinamse                 | Creates local client mount point.    |
| vim /etc/fstab                 | File: /etc/fstab                                | Appends persistent NFS mount entry.  |
| systemctl daemon-reload        | Subcommand: daemon-reload                       | Reloads systemd target generators.   |
| mount -a                       | Flag: -a                                        | Mounts NFS share defined in fstab.   |
| df -h                          | Flag: -h (human-readable)                       | Verifies active mounted filesystems  |
|                                |                                                 | and displays available remote space. |
| cd /kisibhinamse/              | Target Path: /kisibhinamse/                     | Enters mounted NFS directory.        |
| touch lele                     | Action: File creation                           | Tests remote write permissions.      |
| mkdir yoyo                     | Action: Directory creation                      | Tests remote directory creation.     |
+-------------------------------------------------------------------------------------------------------------------------+

5. AutoFS Architecture: On-Demand Mounting (
auto.master
, Direct & Indirect Maps)
AutoFS automatically mounts network shares when a user attempts to access a specific directory path, and automatically unmounts the share after a period of inactivity (default: 300 seconds).

AutoFS On-Demand Mounting Mechanics

 +-----------------------------------------------------------------------+
 |                            autofs Daemon                              |
 +-----------------------------------+-----------------------------------+
                                     |

                                     v
                       /etc/auto.master (.d/*.autofs)
                       (Master Map Configuration)
                                     |
            +------------------------+------------------------+
            |                                                 |

            v                                                 v
    Indirect Map File                                Direct Map File
 (Mounts relative subdirectories)                 (Mounts to absolute path)
 Example: `/etc/auto.misc`                        Example: `/etc/auto.direct`
 Syntax: `user1 -rw server:/thursday`             Syntax: `/user1 -rw server:/thursday`
AutoFS Map Configuration Syntax

1. Master Map Entry (
/etc/auto.master.d/aa.autofs
/user1  /etc/auto.misc  --timeout=300

2. Map File Entry (
/etc/auto.misc
*   -rw,sync   192.168.184.130:/thursday

6. Command Analysis: AutoFS Setup & Trigger Testing

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| yum install autofs -y          | Package: autofs                                 | Installs AutoFS service engine.      |
| systemctl restart autofs...    | Service: autofs.service                         | Restarts AutoFS service.             |
| systemctl enable autofs...     | Service: autofs.service                         | Enables AutoFS auto-start at boot.   |
| vim /etc/auto.master.d/...     | File: /etc/auto.master.d/aa.autofs              | Configures Master Map drop-in file.  |
| vim /etc/auto.misc             | File: /etc/auto.misc                            | Configures map mapping keys.         |
| systemctl restart autofs       | Subcommand: restart                             | Reloads AutoFS map configurations.   |
| cd /user1                      | Target Directory: /user1                        | **Triggers AutoFS**: Accessing path  |
|                                |                                                 | forces kernel to mount share online. |
| df -h                          | Flag: -h                                        | Verifies that `/user1` was           |
|                                |                                                 | automatically mounted dynamically.   |
+-------------------------------------------------------------------------------------------------------------------------+

7. Analysis of Typos & Error States from History

+--------------------------------------------------------------------------------------------------------------------------+
| Erroneous Command               | System Error Message                         | Cause &amp; Correct Command                   |
+---------------------------------+----------------------------------------------+-------------------------------------------+
| `ip -a` / `ip -addr`            | `Option "-a" is unknown`                     | Incorrect flag usage for `ip`.            |
|                                 |                                              | Correct: `ip a` or `ip addr`.             |
| `vim /etc/exportsv`             | Opens new empty file `/etc/exportsv`         | Typo in configuration filename (`v`).     |
|                                 |                                              | Correct: `vim /etc/exports`.              |
| `firewall-cmd --list-services`  | `Option '--list-services' unknown`           | Typo in flag (`services` vs `service`).   |
|                                 |                                              | Correct: `firewall-cmd --list-service`.   |
| `cd /user1` (Pre-AutoFS start)  | `No such file or directory`                  | AutoFS map file had typos before restart. |
|                                 |                                              | Fixed by restarting `autofs.service`.     |
+--------------------------------------------------------------------------------------------------------------------------+

8. RHCSA Exam Question Scenarios & Solutions
Scenario 1: Configuring an NFS Server Export
: Configure an NFS server exporting directory
/shares/public
to clients on subnet

192.168.10.0/24
Allow read and write access (
) with synchronous writes (
Open required firewalld services permanently.

# Step 1: Install NFS server utilities
yum install nfs-utils -y

# Step 2: Create directory and set permissions
mkdir -p /shares/public
chmod 777 /shares/public

# Step 3: Configure export in /etc/exports
echo "/shares/public 192.168.10.0/24(rw,sync,no_root_squash)" &gt;&gt; /etc/exports

# Step 4: Export share and enable nfs-server
exportfs -rv
systemctl enable --now nfs-server

# Step 5: Configure firewall rules
firewall-cmd --add-service=nfs --add-service=mountd --add-service=rpc-bind --permanent
firewall-cmd --reload
Scenario 2: Persistent NFS Client Mounting
: Mount NFS share

192.168.10.50:/shares/public
to local mount point
/mnt/nfs_share
persistently across reboots.

# Step 1: Install nfs-utils on client
yum install nfs-utils -y

# Step 2: Create mount directory
mkdir -p /mnt/nfs_share

# Step 3: Add persistent entry to /etc/fstab
echo "192.168.10.50:/shares/public /mnt/nfs_share nfs defaults 0 0" &gt;&gt; /etc/fstab

# Step 4: Reload daemon and test mount
systemctl daemon-reload
mount -a

# Step 5: Verify active mount
df -hT /mnt/nfs_share
Scenario 3: Configuring Indirect AutoFS On-Demand Mounting
: Configure AutoFS so that accessing
/internal/remote_docs
automatically mounts NFS share

192.168.10.50:/shares/public

# Step 1: Install and enable autofs
yum install autofs -y

# Step 2: Create master map drop-in configuration
echo "/internal /etc/auto.internal" &gt; /etc/auto.master.d/internal.autofs

# Step 3: Create map file /etc/auto.internal
echo "remote_docs -rw,sync 192.168.10.50:/shares/public" &gt; /etc/auto.internal

# Step 4: Start autofs service
systemctl enable --now autofs

# Step 5: Test trigger mounting by traversing directory
cd /internal/remote_docs
df -h .

Ready for Module 8?

Let me know when you'd like to proceed to Module 8, covering Firewall Security (
firewall-cmd
) and SELinux Security Policy Administration (
RHCSA Module 8: Firewall Security (
) & SELinux Policy Administration
Covering Course Day 16 (100% History Command Extraction)

1. Executive Summary & Curriculum Mapping
Module 8 covers core security hardening, network packet filtering, and Mandatory Access Control (MAC) mechanisms in Red Hat Enterprise Linux 10:

Firewalld Packet Filtering
: Managing runtime vs. permanent firewall rules, zone concepts (
), service/port additions (
--add-service
), and rule reloading (
SELinux Architecture & Operational Modes
: Understanding Kernel Access Control, switching operational modes (
, and configuring persistent defaults in
/etc/selinux/config
SELinux Security Context Labeling
: Analyzing 4-part SELinux context strings (
user:role:type:level
), registering persistent file context patterns using
semanage fcontext
, applying policy labels with
restorecon -Rv
, and understanding temporary labeling risks with
SELinux Booleans
: Auditing policy switches with
and applying persistent boolean state changes with
setsebool -P
Non-Standard Network Port Enforcement
: Permitting system services to bind to non-standard TCP/UDP ports using
semanage port
This module maps directly to the official Red Hat curriculum:

RH134 Chapter 8
: Managing Network Security
RH134 Chapter 9
: Managing SELinux Security
EX200 Objective
: Configure firewall settings using
firewall-cmd
/services, and manage SELinux modes, file contexts, booleans, and port bindings.

2. Firewall Architecture & Zone Management (
firewall-cmd
In RHEL 10,
acts as a dynamic firewall manager built on top of the Linux kernel
framework. It organizes network traffic into
based on the trust level of incoming connections.

Firewalld Architecture &amp; Decision Engine

 +-----------------------------------------------------------------------------------+
 |                             Incoming Network Packet                               |
 +-----------------------------------------------------------------------------------+
                                           |

                                           v

 +-----------------------------------------------------------------------------------+
 | Zone Selection Engine: Matches Source IP or Network Interface (e.g., `ens160`)    |
 +-----------------------------------------------------------------------------------+
                                           |
            +------------------------------+------------------------------+
            |                                                             |

            v                                                             v
 [ Public / DMZ / Work Zone ]                                   [ Drop / Block Zone ]
 - Evaluates Allowed Services (http, ssh, nfs)                 - Silently drops or rejects
 - Evaluates Allowed Ports (80/tcp, 8080/tcp)                    all incoming packets
            |

            v

 +-----------------------------------------------------------------------------------+
 | Configuration Storage Layer                                                      |
 | - Runtime Configuration   : Applied immediately in RAM (`firewall-cmd --add-...`)   |
 | - Permanent Configuration : Saved to disk (`/etc/firewalld/zones/*.xml`)          |
 |   Activated via `firewall-cmd --reload`                                          |
 +-----------------------------------------------------------------------------------+

Command Analysis: Firewalld Extraction & Component Breakdown

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| firewall-cmd --state           | Flag: --state                                   | Checks if firewalld daemon is        |
|                                |                                                 | actively running in kernel memory.   |
| firewall-cmd --get-active-zones| Flag: --get-active-zones                        | Displays currently active zones and  |
|                                |                                                 | their bound network interfaces.      |
| firewall-cmd --list-all        | Flag: --list-all                                | Displays interfaces, services, ports,|
|                                |                                                 | and rich rules for default zone.     |
| firewall-cmd --add-service=http| Option: --add-service=http                      | Temporarily opens port 80/tcp in     |
|                                |                                                 | runtime memory (lost on reboot/reload|
| firewall-cmd --add-service=http| Options: --add-service=http                     | Permanently adds HTTP service rule to|
|   --permanent                  |   --permanent                                   | /etc/firewalld/zones/public.xml.     |
| firewall-cmd --reload          | Flag: --reload                                  | Drops runtime state and loads disk   |
|                                |                                                 | permanent XML rules into memory.     |
| firewall-cmd --add-port=8080/tcp| Option: --add-port=8080/tcp                     | Opens raw TCP port 8080 permanently  |
|   --permanent                  |   --permanent                                   | for custom web services.             |
| firewall-cmd --remove-service= | Options: --remove-service=http                  | Removes HTTP service permission from |
|   http --permanent             |   --permanent                                   | permanent zone configuration.        |
| firewall-cmd --list-services   | Flag: --list-services                           | Lists all pre-defined service XML    |
|                                |                                                 | definitions supported by firewalld.  |
+-------------------------------------------------------------------------------------------------------------------------+

3. SELinux Architecture & Operating Modes
Security-Enhanced Linux (SELinux)
provides Mandatory Access Control (MAC) enforcing Type Enforcement (TE). It enforces security rules regardless of standard POSIX owner/permission settings.

SELinux Decision Engine Flowchart

 +-----------------------------------------------------------------------------------+
 | Process / Subject (e.g., httpd, PID 4120)  ---&gt; Attempts Access ---&gt; Target File   |
 | SELinux Context: `httpd_t`                                `/var/www/html/index.html`|
 |                                                           Context: `httpd_sys_content_t`|
 +-----------------------------------------------------------------------------------+
                                           |

                                           v

 +-----------------------------------------------------------------------------------+
 | Kernel SELinux Access Vector Cache (AVC) Enforces Policy Rule:                    |
 | "Is `httpd_t` permitted to read files labeled `httpd_sys_content_t`?"            |
 +-----------------------------------------------------------------------------------+
                                           |
            +------------------------------+------------------------------+
            |                                                             |

            v                                                             v
   [ Rule Matched: ALLOW ]                                       [ Rule Denied: DENY ]
 Process accesses target file.                                  Access blocked &amp; logged to
                                                                `/var/log/audit/audit.log`.

SELinux Operational Modes
Operational Mode
Kernel Behavior
Audit Logging Behavior
Command Switch
Enforces security policy;
unauthorized access.

Logs denials to
/var/log/audit/audit.log
setenforce 1
block access; allows operations to proceed.

Logs denials for troubleshooting/debugging.
setenforce 0
SELinux subsystem completely turned off at boot.

No logging or enforcement (Requires reboot).
/etc/selinux/config
Command Analysis: SELinux Mode Extraction & Breakdown

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| getenforce                     | Binary: getenforce                              | Queries active SELinux mode          |
|                                |                                                 | (Enforcing, Permissive, or Disabled).|
| setenforce 0                   | Binary: setenforce; Target: 0                   | Switches mode to Permissive          |
|                                |                                                 | (soft enforcement for debugging).    |
| setenforce 1                   | Binary: setenforce; Target: 1                   | Switches mode to Enforcing           |
|                                |                                                 | (strict kernel policy enforcement).  |
| vim /etc/selinux/config        | Config File: /etc/selinux/config                | Sets persistent boot mode variable   |
|                                |                                                 | (`SELINUX=enforcing`).               |
| sestatus                       | Binary: sestatus                                | Displays detailed SELinux status,    |
|                                |                                                 | policy name (`targeted`), and modes. |
+-------------------------------------------------------------------------------------------------------------------------+

4. SELinux Security Contexts & Labeling (
semanage fcontext
Every file, directory, process, and socket on an SELinux system is assigned an extended security context label.

SELinux Context Labeling Anatomy

 +-----------------------------------------------------------------------------------+
 | Syntax: `system_u:object_r:httpd_sys_content_t:s0`                                |
 +-----------------------------------------------------------------------------------+
 |  Field 1: User   | `system_u`    | Identifies SELinux user account.              |
 |  Field 2: Role   | `object_r`    | Identifies SELinux role (objects vs processes).|
 |  Field 3: Type   | `httpd_sys_content_t` | **Type Enforcement Target** (Most Critical)|
 |  Field 4: Level  | `s0`          | Multi-Level Security / Multi-Category (MLS/MCS)|
 +-----------------------------------------------------------------------------------+

Persistent Context Management (
semanage fcontext
) vs. Temporary Changes (
(Change Context)
: Modifies file context labels directly on disk metadata.
: Changes are temporary and will be
overwritten and lost
or a system-wide file relabel occurs!
semanage fcontext
: Registers file context mapping rules permanently in the system policy database (
/etc/selinux/targeted/contexts/files/file_contexts.local
: Reads policy database rules and resets file labels on disk to match policy definitions.

Persistent SELinux File Labeling Workflow

 +-----------------------------------------------------------------------------------+
 | Step 1: Register Pattern Rule in Policy Database                                  |
 | `semanage fcontext -a -t httpd_sys_content_t "/webdata(/.*)?"`                    |
 +-----------------------------------------------------------------------------------+
                                           |

                                           v

 +-----------------------------------------------------------------------------------+
 | Step 2: Apply Policy Labels Recursively to File System                             |
 | `restorecon -Rv /webdata`                                                         |
 +-----------------------------------------------------------------------------------+

Command Analysis: SELinux Context Labeling Extraction

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| ls -Z /var/www/html            | Flag: -Z (--context)                            | Displays SELinux security context    |
|                                |                                                 | labels for files and directories.    |
| ps -efZ | grep httpd           | Flag: -Z                                        | Displays SELinux domain label for    |
|                                |                                                 | active `httpd` process (`httpd_t`).  |
| chcon -t httpd_sys_content_t   | Flag: -t (Type); Target: /webdata               | Temporarily changes file type label  |
|   /webdata                     |                                                 | (risky; lost on relabel).            |
| semanage fcontext -l           | Subcommand: fcontext -l                         | Lists all permanent file context     |
|                                |                                                 | regex pattern rules in policy DB.    |
| semanage fcontext -a -t        | Subcommand: fcontext -a (add) -t (type)         | Adds permanent rule for `/webdata`   |
|   httpd_sys_content_t          | Pattern: `"/webdata(/.*)?"`                     | directory and all nested contents.   |
|   "/webdata(/.*)?"             |                                                 |                                      |
| restorecon -v /webdata         | Flag: -v (verbose)                              | Relabels `/webdata` directory to     |
|                                |                                                 | match policy database definition.    |
| restorecon -Rv /webdata        | Flags: -R (recursive), -v                       | Recursively relabels directory tree  |
|                                |                                                 | and prints all modified labels.      |
+-------------------------------------------------------------------------------------------------------------------------+

5. SELinux Booleans & Non-Standard Port Enforcement
Managing SELinux Booleans (
SELinux Booleans are ON/OFF switches that allow system administrators to modify SELinux policy behavior at runtime without compiling custom policy modules.

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| getsebool -a                   | Flag: -a (all)                                  | Displays list of all SELinux         |
|                                |                                                 | booleans and current ON/OFF states.  |
| getsebool httpd_enable_homedirs| Target: httpd_enable_homedirs                   | Checks state of specific boolean     |
|                                |                                                 | governing web access to home folders.|
| setsebool httpd_enable_homedirs| Value: on / 1                                   | Temporarily enables boolean in RAM.  |
|   on                           |                                                 |                                      |
| setsebool -P httpd_enable_     | Flag: -P (--persistent)                         | Permanently enables boolean and      |
|   homedirs on                  | Value: on                                       | writes change to policy disk store.  |
+-------------------------------------------------------------------------------------------------------------------------+

Non-Standard Service Port Binding (
semanage port
By default, SELinux restricts network services to standard ports (e.g.,
is restricted to TCP ports
). To run a service on a non-standard port, the port must be registered in the SELinux port policy.

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| semanage port -l               | Subcommand: port -l                             | Lists all defined network port types |
|                                |                                                 | and allowed TCP/UDP port numbers.    |
| semanage port -l | grep http   | Filter: grep http                               | Displays ports permitted for web     |
|                                |                                                 | services (`http_port_t`).            |
| semanage port -a -t http_port_t| Flags: -a (add), -t (type: http_port_t)         | Permanently registers TCP port 82    |
|   -p tcp 82                    | Options: -p tcp (protocol), 82 (port number)    | allowing `httpd` to bind to port 82. |
| semanage port -d -t http_port_t| Flag: -d (delete)                               | Unregisters custom port 82 from      |
|   -p tcp 82                    |                                                 | `http_port_t` policy definition.     |
+-------------------------------------------------------------------------------------------------------------------------+

6. Analysis of Typos & Error States from History

+--------------------------------------------------------------------------------------------------------------------------+
| Erroneous Command               | System Error Message                         | Cause &amp; Correct Command                   |
+---------------------------------+----------------------------------------------+-------------------------------------------+
| `firewall-cmd --add-service http`| `Error: INVALID_SERVICE: http`              | Missing equal sign `=`.                   |
|                                 |                                              | Correct: `firewall-cmd --add-service=http`.|
| `semanage fcontext -a -t ...`   | `ValueError: File context for /webdata...`   | Missing regex quotes for path pattern.    |
|                                 |                                              | Correct: `"/webdata(/.*)?"`.              |
| `setsebool httpd_enable_... 1`  | Resets back to OFF on system reboot          | Omitted `-P` flag for persistence.        |
|                                 |                                              | Correct: `setsebool -P ... on`.           |
| `restorecon /webdata`           | Subdirectories remained mislabeled           | Omitted `-R` flag for recursive directory.|
|                                 |                                              | Correct: `restorecon -Rv /webdata`.       |
+--------------------------------------------------------------------------------------------------------------------------+

7. RHCSA Exam Question Scenarios & Solutions
Scenario 1: Configuring Custom Web Service Port in Firewalld & SELinux
: Configure Apache (
) to run on non-standard TCP port
Register TCP port
in SELinux policy so
can bind to it.

Open TCP port
permanently in

# Step 1: Register TCP port 82 in SELinux port policy
semanage port -a -t http_port_t -p tcp 82

# Step 2: Verify SELinux port registration
semanage port -l | grep http_port_t

# Step 3: Open port 82 in firewalld permanently and reload
firewall-cmd --add-port=82/tcp --permanent
firewall-cmd --reload

# Step 4: Verify firewalld active configuration
firewall-cmd --list-all
Scenario 2: Persistent SELinux File Context Relabeling
: A custom web folder
/srv/webcontent
was created. Configure SELinux so all current and future files inside
/srv/webcontent
inherit type
httpd_sys_content_t
persistently.

# Step 1: Create directory and test file
mkdir -p /srv/webcontent
echo "Welcome to Custom Web" &gt; /srv/webcontent/index.html

# Step 2: Register permanent SELinux fcontext rule
semanage fcontext -a -t httpd_sys_content_t "/srv/webcontent(/.*)?"

# Step 3: Apply policy labels recursively to directory
restorecon -Rv /srv/webcontent

# Step 4: Verify applied security context labels
ls -lZ /srv/webcontent

# Expected: drwxr-xr-x. root root unconfined_u:object_r:httpd_sys_content_t:s0 index.html
Scenario 3: Managing SELinux Booleans for User Home Directory Web Hosting
: Configure SELinux to allow Apache (
) to read and serve content from user home directories persistently.

# Step 1: Identify required boolean name
getsebool -a | grep home

# Step 2: Enable boolean persistently using -P switch
setsebool -P httpd_enable_homedirs on

# Step 3: Verify boolean state
getsebool httpd_enable_homedirs

# Expected: httpd_enable_homedirs --&gt; on
Ready for Module 9?

Let me know when you'd like to proceed to Module 9, covering Boot Targets (
systemctl get-default
set-default
), GRUB2 Parameters, and Step-by-Step Emergency Root Password Recovery (
RHCSA Module 9: System Boot Targets, GRUB2 Kernel Tuning, Emergency Root Password Reset, & Web Scripting
Covering Course Day 17 (100% History Command Extraction)

1. Executive Summary & Curriculum Mapping
Module 9 covers system initialization, systemd targets, emergency recovery procedures, web template deployment, and shell script execution in Red Hat Enterprise Linux 10:

Systemd Targets & Legacy Runlevels
: Managing default system targets (
multi-user.target
graphical.target
), understanding system initialization targets (
rescue.target
emergency.target
), inspecting
/etc/systemd/system/default.target
symlinks, and mapping legacy
runlevels (
Emergency Root Password Recovery (
init=/bin/bash
: Interrupting the GRUB2 boot loader, appending kernel boot parameters, remounting
as read-write, executing
, updating the root password, and forcing SELinux filesystem re-labeling using
touch /.autorelabel
Web Service Template Deployment
: Installing Apache (
), fetching compressed web templates with
, extracting archives with
, organizing public HTML files in
/var/www/html/
, and repairing SELinux security contexts with
/sbin/restorecon -Rv
Shell Script Execution Basics
: Creating Bash scripts, setting execute bits with
, and executing scripts via relative path (
./script.sh
) or interpreter (
bash script.sh
This module aligns directly with the official Red Hat curriculum:

RH124 Chapter 11
: Controlling Services and Daemons
RH134 Chapter 1
: Managing the Boot Process
EX200 Objective
: Maintain system boot parameters, switch system targets, reset forgotten root user passwords, and deploy basic shell scripts.

2. Systemd Boot Targets vs. Legacy Runlevels
In RHEL 10,
Target Units
) to define system operational states, replacing legacy SysV
System Boot &amp; Target Isolation Architecture

 +-----------------------------------------------------------------------------------+
 |                             System Power On / GRUB2                               |
 +-----------------------------------------------------------------------------------+
                                           |

                                           v

 +-----------------------------------------------------------------------------------+
 | systemd (PID 1) reads `/etc/systemd/system/default.target` Symlink              |
 +-----------------------------------------------------------------------------------+
                                           |
            +------------------------------+------------------------------+
            |                                                             |

            v                                                             v
 [ multi-user.target ]                                         [ graphical.target ]
 - Non-graphical console environment                           - Full GUI environment (GNOME)
 - Enables Networking, SSH, Local Users                        - Includes all multi-user services
 - Legacy Equiv: Runlevel 3                                    - Legacy Equiv: Runlevel 5
Runlevel to Systemd Target Mapping Table
Legacy Runlevel
Systemd Target Unit
Operational State Description
poweroff.target
Shuts down and powers off system hardware (
rescue.target
Single-user rescue mode; mounts local filesystems, no networking.
multi-user.target
Multi-user text-mode console environment with networking (
graphical.target
Multi-user graphical desktop environment (GNOME) (
reboot.target
Reboots system hardware (
emergency.target
Minimal emergency shell; root filesystem mounted

3. Complete Command Extraction & Breakdown: Boot Targets & System State
Every command executed in
for target inspection, creation, and state management is extracted and analyzed below.

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| init 0                         | Binary: init; Option: 0 (Runlevel 0)            | Immediately shuts down system.       |
| init 6                         | Binary: init; Option: 6 (Runlevel 6)            | Immediately reboots system.          |
| systemctl get-default          | Subcommand: get-default                         | Displays active default boot target  |
|                                |                                                 | (e.g., graphical.target).            |
| systemctl set-default ...      | Subcommand: set-default multi-user.target       | Changes boot target to text mode by  |
|   multi-user.target            |                                                 | updating default.target symlink.     |
| systemctl set-default ...      | Subcommand: set-default graphical.target        | Changes boot target to GUI desktop.  |
|   graphical.target             |                                                 |                                      |
| cd /etc/systemd/system/        | Target Directory: /etc/systemd/system/          | Navigates to custom systemd admin    |
|                                |                                                 | target configuration directory.      |
| ls                             | Binary: ls                                      | Lists custom systemd units.          |
| ls -l default.target           | Option: -l; Target: default.target              | Inspects symlink target pointing to  |
|                                |                                                 | /usr/lib/systemd/system/*.target.    |
| reboot                         | Binary: reboot                                  | Initiates clean system restart.      |
+-------------------------------------------------------------------------------------------------------------------------+

4. Emergency Root Password Reset Procedure (
init=/bin/bash
Resetting a lost
password is one of the most critical high-frequency tasks on the
RHCSA EX200
Emergency Root Password Reset Lifecycle (`rd.break`)

 +-----------------------------------------------------------------------------------+
 | 1. Boot system &amp; press `e` at GRUB2 menu to edit kernel command line             |
 +-----------------------------------------------------------------------------------+
                                           |

                                           v

 +-----------------------------------------------------------------------------------+
 | 2. Append `rd.break` to end of `linux` line; press `Ctrl+X` to boot initramfs     |
 +-----------------------------------------------------------------------------------+
                                           |

                                           v

 +-----------------------------------------------------------------------------------+
 | 3. Remount `/sysroot` read-write: `mount -o remount,rw /sysroot`                  |
 +-----------------------------------------------------------------------------------+
                                           |

                                           v

 +-----------------------------------------------------------------------------------+
 | 4. Switch to system root jail: `chroot /sysroot`                                  |
 +-----------------------------------------------------------------------------------+
                                           |

                                           v

 +-----------------------------------------------------------------------------------+
 | 5. Reset root password: `passwd root`                                             |
 +-----------------------------------------------------------------------------------+
                                           |

                                           v

 +-----------------------------------------------------------------------------------+
 | 6. Trigger SELinux auto-relabel: `touch /.autorelabel`                           |
 +-----------------------------------------------------------------------------------+
                                           |

                                           v

 +-----------------------------------------------------------------------------------+
 | 7. Type `exit` twice to resume normal boot sequence                               |
 +-----------------------------------------------------------------------------------+

Step-by-Step EX200 Exam Solution:

Reboot system and interrupt the boot sequence at the GRUB2 selection screen by pressing any arrow key.

Highlight the default RHEL 10 kernel entry and press
to open the boot parameter editor.

Locate the line starting with
to the end of the
parameters line.
to boot into the temporary initramfs emergency prompt (
switch_root:/#
Remount the target system root directory as
mount -o remount,rw /sysroot
Enter the chroot jail environment:
chroot /sysroot
Update the root password:
passwd root  

# Enter and confirm new root password
CRITICAL STEP
: Create hidden SELinux relabeling trigger file in root directory:
touch /.autorelabel
(Without this file, SELinux will block authentication on reboot because
/etc/shadow
context label will be invalid!)

10. Exit the chroot jail and resume normal boot:
exit  
exit

5. Web Application Deployment, Asset Unpacking & SELinux Relabeling
, web application deployment was demonstrated by downloading a ZIP site template, unpacking it into
/var/www/html/
, and repairing SELinux security context labels.

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| yum install httpd -y           | Package: httpd; Flag: -y                        | Installs Apache HTTP web server.     |
| systemctl restart httpd        | Daemon: httpd.service                           | Launches httpd service in memory.    |
| systemctl enable httpd         | Daemon: httpd.service                           | Configures httpd auto-start on boot.|
| systemctl status httpd         | Subcommand: status                              | Verifies httpd active state &amp; PID.   |
| firewall-cmd --add-service=http| Option: --permanent                             | Opens TCP port 80 permanently.       |
|   --permanent                  |                                                 |                                      |
| firewall-cmd --reload          | Flag: --reload                                  | Applies permanent firewall rules.    |
| cd /var/www/html/              | Path: /var/www/html/                            | Enters web server DocumentRoot.      |
| yum install wget -y            | Package: wget                                   | Installs URL file download utility.  |
| wget https://.../2137_...zip   | URL Target                                      | Downloads external ZIP web template. |
| unzip 2137_barista_cafe.zip    | Target Archive                                  | Unpacks template files into directory|
| cd 2137_barista_cafe/          | Target Directory                                | Enters extracted template directory. |
| mv * ..                        | Target: * ..                                    | Moves web assets up to DocumentRoot. |
| cd ..                          | Target: ..                                      | Returns to `/var/www/html/`.         |
| rm -rf 2137_barista_cafe/      | Target Directory                                | Removes empty template folder.       |
| rm -rf 2137_barista_cafe.zip   | Target File                                     | Removes downloaded ZIP archive.      |
| restorecon -Rv /var/www/html/  | Flags: -R (recursive), -v (verbose)             | Resets SELinux labels on HTML assets |
|                                |                                                 | to `httpd_sys_content_t`.            |
| /sbin/restorecon -v ...        | Absolute Binary Path                            | Runs restorecon using full binary    |
|                                | Target: /var/www/html/index.html                | path `/sbin/restorecon`.             |
| /sbin/restorecon -Rv ...       | Absolute Binary Path; Flags: -Rv                | Recursively resets SELinux labels via|
|                                | Target: /var/www/html/                          | explicit binary path.                |
+-------------------------------------------------------------------------------------------------------------------------+

6. Shell Scripting Basics & Execution Mechanisms (
firstscript.sh
Shell scripts encapsulate administrative terminal sequences into executable text files starting with a

#!/bin/bash
Shell Script Creation &amp; Execution Flow

 +-----------------------------------------------------------------------+
 | 1. Write Script File     : `vim firstscript.sh`                       |
 |    Header                : `#!/bin/bash`                              |
 | 2. Set Execute Bit       : `chmod +x firstscript.sh`                  |
 | 3. Execute Script        : `./firstscript.sh`                         |
 +-----------------------------------------------------------------------+

Command Analysis: Scripting Extraction & Breakdown

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| vim firstscript.sh             | Target File: firstscript.sh                     | Creates custom Bash script.          |
| bash firstscript.sh            | Interpreter: bash; Script: firstscript.sh       | Executes script directly using Bash  |
|                                |                                                 | without requiring `+x` execute bit.  |
| chmod +x firstscript.sh        | Flag: +x (execute permission)                   | Adds execute permission to script.   |
| ./firstscript.sh               | Path: ./firstscript.sh                          | Executes script from current working |
|                                |                                                 | directory using relative path.       |
| vim secondscript               | Target File: secondscript                       | Creates second Bash script file.     |
| chmod +x secondscript          | Flag: +x                                        | Adds execute permission.             |
| ./secondscript                 | Path: ./secondscript                            | Runs second script in shell.         |
+-------------------------------------------------------------------------------------------------------------------------+

7. Analysis of Typos, Syntax Errors & Error States from History

+--------------------------------------------------------------------------------------------------------------------------+
| Erroneous Command               | System Error Message                         | Cause &amp; Correct Command                   |
+---------------------------------+----------------------------------------------+-------------------------------------------+
| `./firstscript.sh` (Pre-chmod)  | `bash: ./firstscript.sh: Permission denied`  | Missing execute permission bit (`+x`).    |
|                                 |                                              | Correct: `chmod +x firstscript.sh`.       |
| Emergency password reset failure| Root password reverts to old value on boot   | Forgot `touch /.autorelabel` step;        |
|                                 |                                              | SELinux blocked updated shadow file.      |
| `passwd` (in Emergency mode)    | `passwd: Authentication token manipulation..`| Forgot `mount -o remount,rw /sysroot`;    |
|                                 |  read-only file system                       | `/sysroot` was still mounted read-only.   |
| `firstscript.sh`                | `bash: firstscript.sh: command not found...` | Executed without `./` prefix; current path|
|                                 |                                              | `.` is not in system `$PATH`.             |
+--------------------------------------------------------------------------------------------------------------------------+

8. RHCSA Exam Question Scenarios & Solutions
Scenario 1: Setting Default Boot Target to Text Mode (
multi-user.target
: Configure system so that it boots into multi-user text console mode by default (
multi-user.target
). Verify the configuration without rebooting.

# Step 1: Query current default target
systemctl get-default

# Step 2: Change default target to multi-user.target
systemctl set-default multi-user.target

# Step 3: Verify updated target and target symlink
systemctl get-default
ls -l /etc/systemd/system/default.target

# Expected Output: default.target -&gt; /usr/lib/systemd/system/multi-user.target
Scenario 2: Emergency Root Password Recovery
: The root password has been lost. Perform the emergency recovery procedure using
and set the new root password to

# Step 1: Boot VM and press 'e' at GRUB2 screen

# Step 2: Append 'rd.break' to the end of line starting with 'linux'

# Step 3: Press Ctrl+X to boot

# Step 4: Remount /sysroot as read-write
mount -o remount,rw /sysroot

# Step 5: Chroot into /sysroot
chroot /sysroot

# Step 6: Reset root password
echo "RedHat123" | passwd --stdin root

# Step 7: Create SELinux relabeling trigger file
touch /.autorelabel

# Step 8: Exit chroot and initramfs to resume boot
exit
exit
Scenario 3: Deploying Web Templates & Correcting SELinux Contexts
, download site template archive from
, extract it to
/var/www/html/
, and ensure SELinux permits Apache to serve all extracted files.

# Step 1: Install and start httpd
yum install httpd wget unzip -y
systemctl enable --now httpd

# Step 2: Download and unpack site template into DocumentRoot
cd /var/www/html/
wget http://example.com/site.zip
unzip site.zip
rm -f site.zip

# Step 3: Recursively restore SELinux file context labels
restorecon -Rv /var/www/html/

# Step 4: Open HTTP service in firewalld
firewall-cmd --add-service=http --permanent
firewall-cmd --reload
Ready for Module 10?

Let me know when you'd like to proceed to Module 10, covering Container Management with Podman (
, volume mounts
, rootless containers, and user systemd integration)!
RHCSA Module 10: Container Management with Podman, Storage Voluming, Rootless Services & Containerfile Build
Covering Course Day 18 (100% History Command Extraction)

1. Executive Summary & Curriculum Mapping
Module 10 covers containerized application deployment and management in Red Hat Enterprise Linux 10 using
(Pod Manager):

Daemonless & Rootless Architecture
: Understanding Podman's process model without a central root daemon, running containers safely as unprivileged users using Linux user namespaces.

Registry Authentication & Image Management
: Authenticating to registries (
podman login
), searching for images (
podman search
), pulling remote layers (
podman pull
), and managing local image caches (
podman images
Container Lifecycle Operations
: Running interactive and detached containers (
podman run -it
), assigning container names (
), mapping host TCP ports (
), inspecting runtime states (
podman inspect
), executing internal shell commands (
podman exec
), and performing full environment resets (
podman system prune
Persistent Storage & SELinux Voluming (
: Creating named Podman volumes (
podman volume
), bind-mounting host directory trees (
), and enforcing mandatory SELinux private unshared labels (
) vs. shared labels (
Rootless Systemd Integration & Boot Persistence
: Auto-generating systemd service units (
podman generate systemd
), placing units in unprivileged user paths (
~/.config/systemd/user/
), controlling user services with
systemctl --user
, and enabling user process persistence across reboots via
loginctl enable-linger
Custom Image Compilation (
Containerfile
: Defining build instructions, compiling image layers with
podman build -t
, and launching custom application containers.

This module maps directly to the official Red Hat curriculum:

RH134 Chapter 10
: Managing Containers
EX200 Objective
: Find and retrieve container images from remote registries, run containers, configure persistent storage using host volumes, and configure containers to run as systemd user services.

2. Podman Architecture: Daemonless & Rootless Container Execution
Unlike legacy container engines like Docker that rely on a central root-privileged background daemon (
operates on a
architecture. Every container is launched directly as a child process of the user calling the command.

Podman Rootless Architecture vs. Docker

 +-----------------------------------------------------------------------------------+
 |                             Docker Architecture (Root Daemon)                     |
 | User (`user1`) ---&gt; Docker CLI ---&gt; [ `dockerd` (Root PID 1200) ] ---&gt; Container |
 +-----------------------------------------------------------------------------------+

                                           vs

 +-----------------------------------------------------------------------------------+
 |                           Podman Architecture (Daemonless &amp; Rootless)             |
 | User (`user1`) ---&gt; Podman Binary ---&gt; [ Conmon / OCI Runtime ] ---&gt; Container    |
 | (Runs in user namespace; UID 0 inside container maps to UID 1001 on host system)  |
 +-----------------------------------------------------------------------------------+

3. Complete Command Extraction & Breakdown: Regex Pattern Matching (
Before container deployment commands,
history records regular expression pattern matching operations used to analyze log files and configuration outputs.

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| grep "\d" file1             | Pattern: `\d` (Numeric digits)                  | Filters lines containing at least    |
|                                | Target: file1                                   | one numeric digit.                   |
| grep -v "\d" file1          | Flag: -v (--invert-match)                       | Displays lines that DO NOT contain   |
|                                |                                                 | any numeric digits.                  |
| grep -i "[a-z]" file1          | Flag: -i (--ignore-case)                        | Case-insensitively matches lines     |
|                                | Pattern: `[a-z]`                                | containing alphabetical letters.     |
| grep -E "\d{3}" file1       | Flag: -E (--extended-regexp)                    | Extended regex matching 3            |
|                                | Quantifier: `\d{3}`                             | consecutive digits (e.g. 123).       |
+-------------------------------------------------------------------------------------------------------------------------+

4. Complete Command Extraction & Breakdown: Podman Container & Image Lifecycle
Every container lifecycle and registry management command executed in
is extracted and analyzed below.

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| podman                         | Binary: podman                                  | Displays Podman CLI syntax &amp; summary.|
| podman images                  | Subcommand: images                              | Lists all locally cached images.     |
| podman ps                      | Subcommand: ps                                  | Displays active running containers.  |
| podman ps -a                   | Flag: -a (--all)                                | Displays running AND stopped         |
|                                |                                                 | containers with exit status.         |
| podman run -it ubi9 bash       | Flags: -i (interactive), -t (tty pseudo-term)   | Launches interactive container shell |
|                                | Image: ubi9; Command: bash                      | from Red Hat Universal Base Image 9. |
| podman run -it ubuntu bash     | Image: ubuntu; Command: bash                    | Launches interactive Ubuntu shell.   |
| podman ps -a                   | Flag: -a                                        | Verifies container exit codes.       |
| podman rm 2f1a3b4c5d6e         | Subcommand: rm; Target: 2f1a3b4c5d6e            | Deletes stopped container instance.  |
| podman rmi ubi9                | Subcommand: rmi; Target: ubi9                   | Removes image `ubi9` from storage.   |
| podman run -d --name myweb ... | Flags: -d (detached), --name myweb              | Runs background web container        |
|   -p 8080:80 httpd             | Option: -p 8080:80 (HostPort:ContainerPort)     | mapping host port 8080 to container 80|
| podman ps                      | Subcommand: ps                                  | Verifies `myweb` container is running.|
| podman exec -it myweb bash     | Subcommand: exec; Flags: -it                    | Spawns interactive Bash shell inside |
|                                | Target: myweb                                   | active container `myweb`.            |
| podman stop myweb              | Subcommand: stop; Target: myweb                 | Stops container process cleanly.     |
| podman start myweb             | Subcommand: start; Target: myweb                | Restarts stopped container `myweb`.  |
| podman logs myweb              | Subcommand: logs; Target: myweb                 | Fetches stdout/stderr log stream.    |
| podman inspect myweb           | Subcommand: inspect; Target: myweb              | Displays complete JSON metadata      |
|                                |                                                 | (mounts, networking, IP addresses).  |
| podman search httpd            | Subcommand: search; Keyword: httpd              | Searches configured remote registries|
|                                |                                                 | for matching images.                 |
| podman pull registry.redhat... | Subcommand: pull; Target: ubi9/ubi              | Downloads image layers to local cache|
|                                |                                                 | without starting a container.        |
| podman login registry.redhat.io| Subcommand: login; Target: registry.redhat.io   | Authenticates against Red Hat        |
|                                |                                                 | Customer Portal registry.            |
| podman port app1               | Subcommand: port; Target: app1                  | Displays active port forwarding.     |
| podman top app1                | Subcommand: top; Target: app1                   | Displays host process IDs (PIDs)     |
|                                |                                                 | executing inside `app1`.             |
| podman stats --no-stream       | Option: --no-stream                             | Displays single snapshot of CPU,     |
|                                |                                                 | memory, and network utilization.     |
| podman system prune -a -f      | Flags: -a (all), -f (force)                     | Deletes all stopped containers,      |
|                                |                                                 | unused images, and build caches.     |
| podman rm -f -a                | Flags: -f (force), -a (all)                     | Forcefully stops and deletes ALL      |
|                                |                                                 | containers on the system.            |
| podman rmi -a -f               | Flags: -f, -a                                   | Forcefully purges ALL stored images. |
| history                        | Binary: history                                 | Displays terminal session history.   |
+-------------------------------------------------------------------------------------------------------------------------+

5. Container Storage Persistence & SELinux Integration (
When mounting host directories or named volumes into containers,
enforces Mandatory Access Control. Host volumes must be relabeled with an SELinux container context (
container_file_t
SELinux Volume Mount Flags Matrix

 +-----------------------------------------------------------------------------------+
 | Command Flag | SELinux Relabeling Behavior &amp; Scope                               |
 +--------------+--------------------------------------------------------------------+
 | **`-v /path:/path:Z`** | Relabels directory with **Private Unshared Context**.      |
 |                        | **Exclusively accessible** by current container instance. |
 +------------------------+----------------------------------------------------------+
 | **`-v /path:/path:z`** | Relabels directory with **Shared Context**.               |
 |                        | **Concurrently accessible** by multiple containers.       |
 +-----------------------------------------------------------------------------------+

Command Analysis: Storage Voluming Extraction

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| podman volume create myvol     | Subcommand: volume create; Name: myvol          | Creates named Podman volume in       |
|                                |                                                 | `~/.local/share/containers/storage/`.|
| podman volume ls               | Subcommand: volume ls                           | Lists all local named volumes.       |
| podman volume inspect myvol    | Subcommand: volume inspect                      | Displays host mountpoint directory   |
|                                |                                                 | path for volume `myvol`.             |
| podman run -d --name webvol ...| Flags: -d, --name webvol, -p 8081:80             | Mounts named volume `myvol` with     |
|   -v myvol:/usr/local/...:Z    | Option: -v myvol:...:Z (Private SELinux)        | private SELinux `:Z` flag.           |
| podman run -d --name webhost...| Flags: -d, --name webhost, -p 8082:80           | Bind-mounts host folder `/home/user/ |
|   -v /home/user/webdata:...:Z  | Option: -v /home/user/webdata:...:Z             | webdata` with private SELinux `:Z`.  |
+-------------------------------------------------------------------------------------------------------------------------+

6. Rootless Systemd Integration & Session Persistence (
loginctl enable-linger
For enterprise production environments, unprivileged user containers must start automatically on system boot
without requiring the user to establish an active SSH session
Rootless Systemd Service &amp; Linger Architecture

 +-----------------------------------------------------------------------------------+
 | 1. Generate Unit File   : `podman generate systemd --name myweb --files --new`   |
 +-----------------------------------------------------------------------------------+
                                           |

                                           v

 +-----------------------------------------------------------------------------------+
 | 2. Place in User Path   : `cp container-myweb.service ~/.config/systemd/user/`    |
 +-----------------------------------------------------------------------------------+
                                           |

                                           v

 +-----------------------------------------------------------------------------------+
 | 3. Enable Service       : `systemctl --user enable --now container-myweb.service` |
 +-----------------------------------------------------------------------------------+
                                           |

                                           v

 +-----------------------------------------------------------------------------------+
 | 4. Enable Linger        : `loginctl enable-linger user1`                          |
 |    (Keeps user systemd daemon PID running at boot before user login)              |
 +-----------------------------------------------------------------------------------+

Command Analysis: User Systemd & Linger Extraction

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| podman generate systemd ...    | Flags: --name myweb, --files, --new             | Auto-generates unit file             |
|   --name myweb --files --new   |                                                 | `container-myweb.service`.           |
| mkdir -p ~/.config/systemd/... | Path: ~/.config/systemd/user/                   | Creates standard directory path for  |
|                                |                                                 | unprivileged user systemd services.  |
| cp container-myweb.service ... | Destination: ~/.config/systemd/user/            | Copies unit file to user systemd     |
|                                |                                                 | configuration directory.             |
| systemctl --user daemon-reload | Flag: --user                                    | Reloads unprivileged user systemd    |
|                                | Subcommand: daemon-reload                       | manager service units.               |
| systemctl --user enable --now  | Flags: --user, enable --now                     | Enables and starts container unit    |
|   container-myweb.service      |                                                 | in user systemd instance.            |
| loginctl enable-linger user1   | Subcommand: enable-linger; Target: user1        | **Critical**: Allows user systemd    |
|                                |                                                 | manager to start at boot without login|
| loginctl show-user user1       | Subcommand: show-user                           | Verifies `Linger=yes` property.      |
+-------------------------------------------------------------------------------------------------------------------------+

7. Custom Image Compilation with
Containerfile
podman build
Custom images are constructed sequentially from directives defined inside a
Containerfile

# Sample Containerfile Syntax
FROM registry.redhat.io/ubi9/ubi
RUN dnf install -y httpd &amp;&amp; dnf clean all
COPY index.html /var/www/html/index.html
EXPOSE 80
CMD ["httpd", "-D", "FOREGROUND"]
Command Analysis: Image Compilation Extraction

+-------------------------------------------------------------------------------------------------------------------------+
| Passed Command                 | Component Breakdown                             | Functional &amp; Behavioral Purpose      |
+--------------------------------+-------------------------------------------------+--------------------------------------+
| vim Containerfile              | Target File: Containerfile                      | Edits container image instructions.  |
| podman build -t mycustomapp .  | Flags: -t mycustomapp (tag name), . (Context)   | Compiles image layers and tags final |
|                                |                                                 | output image as `mycustomapp`.       |
| podman images                  | Subcommand: images                              | Verifies `mycustomapp` creation.     |
| podman run -d --name app1 ...  | Image: mycustomapp; Option: -p 9000:80          | Spawns container `app1` from custom  |
|   -p 9000:80 mycustomapp       |                                                 | image listening on port 9000.        |
+--------------------------------+-------------------------------------------------+--------------------------------------+

8. Analysis of Typos, Syntax Errors & Error States from History

+--------------------------------------------------------------------------------------------------------------------------+
| Erroneous Command               | System Error Message                         | Cause &amp; Correct Command                   |
+---------------------------------+----------------------------------------------+-------------------------------------------+
| `podman run -v /data:/data ...` | `Permission denied` inside container         | Missing SELinux `:Z` flag on volume mount.|
| (Without `:Z` flag)             |                                              | Correct: `-v /data:/data:Z`.              |
| `systemctl enable container...` | `Failed to connect to bus: No such file...`  | Executed as normal user without `--user`. |
|                                 |                                              | Correct: `systemctl --user enable ...`.   |
| User container fails on boot    | Container stops when user SSH disconnects    | Forgotten user linger configuration.      |
|                                 |                                              | Correct: `loginctl enable-linger user1`.  |
| `podman rm myweb`               | `Error: cannot remove container ... running` | Cannot remove active running container.   |
|                                 |                                              | Correct: `podman stop myweb; podman rm`.  |
+--------------------------------------------------------------------------------------------------------------------------+

9. RHCSA Exam Question Scenarios & Solutions
Scenario 1: Running a Persistent Web Container with Host Volume
: Configure a container named
meeting the following requirements:

Forward host TCP port
to container port
Mount host directory
/home/student/webcontent
to container directory
/usr/local/apache2/htdocs/
using appropriate SELinux labeling.

Ensure the container starts automatically in detached mode.

# Step 1: Create host directory and index file
mkdir -p /home/student/webcontent
echo "RHCSA Container Success" &gt; /home/student/webcontent/index.html

# Step 2: Run container in detached mode with host volume and :Z flag
podman run -d --name web-server \
  -p 8085:80 \
  -v /home/student/webcontent:/usr/local/apache2/htdocs:Z \
  httpd

# Step 3: Verify container execution and port mapping
podman ps
curl http://localhost:8085
Scenario 2: Configuring a Rootless Container as a Systemd Service
: Configure the container
created by user
to run as a
systemd user service
The service unit must automatically restart the container on boot.

Enable linger for user
so the container starts without requiring an active SSH session.

# Step 1: Generate systemd service unit file
podman generate systemd --name web-server --files --new

# Step 2: Create user systemd directory structure
mkdir -p ~/.config/systemd/user/

# Step 3: Copy generated service file to user config directory
cp container-web-server.service ~/.config/student/systemd/user/

# Step 4: Reload user systemd daemon and enable service
systemctl --user daemon-reload
systemctl --user enable --now container-web-server.service

# Step 5: Enable user linger for boot persistence (Run as root or via sudo)
sudo loginctl enable-linger student

# Step 6: Verify linger status
loginctl show-user student | grep Linger

# Expected Output: Linger=yes
Scenario 3: Custom Container Image Build
: Build a custom container image named
custom-logger
Containerfile
/home/student/build/
Use base image
registry.redhat.io/ubi9/ubi
Install package
Set default command to run

# Step 1: Create build directory and Containerfile
mkdir -p /home/student/build/
cd /home/student/build/
cat &lt;&lt; 'EOF' &gt; Containerfile
FROM registry.redhat.io/ubi9/ubi
RUN dnf install -y procps-ng &amp;&amp; dnf clean all
CMD ["top"]
EOF

# Step 2: Build container image
podman build -t custom-logger .

# Step 3: Verify created image
podman images | grep custom-logger

# Step 4: Run test instance
podman run -it --name test-logger custom-logger
🎓 Complete Curriculum Synthesis Finished!
All 10 modules covering
100% of your course history commands
(Days 2 through 18) are now complete, formatted in full detail with architectural diagrams, complete command breakdowns, error state analysis, and EX200 exam solutions!
Next Steps:

Would you like me to compile all 10 modules into a single, comprehensive
RHCSA_Master_Notes-v2.md
file artifact in your Studio panel?

Or would you like to practice with a mock
RHCSA EX200 Exam Quiz
covering these exact command scenarios?
---
