# 🐧 File Permissions in Linux

## 📋 Project Description

As a security professional on a large organization's research team, I am responsible for making sure users have the appropriate permissions. In this activity, I used Linux commands to examine the existing permissions on files and directories in `/home/researcher2/projects`, check whether they match the authorization that should be given, and modify them to authorize the right users and remove any unauthorized access.

**Skills practiced:** `ls -la`, reading permission strings, `chmod` (symbolic mode), least privilege, access control

---

## 🔍 Check File and Directory Details

I used the `ls -la` command to display detailed information about the contents of the projects directory. The `-l` option shows the long format (permissions, owner, group, size, and modified date), and the `-a` option includes hidden files, such as `.project_x.txt`.

```bash
cd /home/researcher2/projects
ls -la
```

📸 **Screenshot: Current permissions before changes**

<img width="867" height="295" alt="01-ls-la-before" src="https://github.com/user-attachments/assets/78e4ca74-52fd-4f63-a931-bd7986a7c588" />

---

## 🔠 Describe the Permissions String

A 10-character permissions string is split into four parts:

- **1st character:** the file type. `d` means directory, and `-` means a regular file.
- **2nd to 4th characters:** the **user** (owner) permissions.
- **5th to 7th characters:** the **group** permissions.
- **8th to 10th characters:** the **other** permissions (everyone else).

In each set, `r` means read, `w` means write, `x` means execute, and `-` means the permission is not granted.

**Example: `project_k.txt` (`-rw-rw-rw-`)**

- `-` is a regular file, not a directory.
- `rw-` means the user can read and write.
- `rw-` means the group can read and write.
- `rw-` means other can read and write.

This file was open to anyone on the system, including write access, which is not appropriate for the organization.

**Example: `drafts` (`drwx--x---`)**

- `d` is a directory.
- `rwx` means the user can read, write, and execute.
- `--x` means the group can only execute.
- `---` means other has no permissions.

---

## ✏️ Change File Permissions

The organization does not allow **other** to have write access to any files. `project_k.txt` was the one file that broke this rule, so I removed write permission from other:

```bash
chmod o-w project_k.txt
```

I also made `project_m.txt` a restricted file. No one except the user should have access to it, so I removed read permission from the group:

```bash
chmod g-r project_m.txt
```

📸 **Screenshot: chmod on `project_k.txt` and `project_m.txt`**

<img width="827" height="312" alt="02-chmod-project-k_project-m" src="https://github.com/user-attachments/assets/b00894fd-5a77-4174-9fe6-f74023deb1c6" />

I ran `ls -la` after each command to confirm the change. The `o-w` command means: **o**ther, **remove** (`-`), **w**rite. The `g-r` command means: **g**roup, **remove**, **r**ead.

---

## 🙈 Change File Permissions on a Hidden File

`.project_x.txt` is a hidden file (its name starts with a period). It is an archived project file, so it should be read-only for both the user and the group, and other should have no access. It was writable by the group and not even readable by the group, so I fixed it in one command:

```bash
chmod u-w,g-w,g+r .project_x.txt | ls -la
```

This removes write from the user, removes write from the group, and adds read to the group. I used pipe in order to output the changes immediately upon changing permission. I also used `ls -la` because hidden files do not show up with a plain `ls -l`.

📸 **Screenshot: chmod on `.project_x.txt`**

<img width="873" height="230" alt="03-chmod-hidden-file_with-pipe" src="https://github.com/user-attachments/assets/aaaca416-e7b8-4dd0-ba3b-38ba0e0232d8" />

---

## 📁 Change Directory Permissions

The `drafts` directory should only be accessible to the user `researcher2`. The group still had execute permission on it, so I removed it:

```bash
chmod g-x drafts | ls -la
```

📸 **Screenshot: chmod on `drafts` and final permissions**

<img width="802" height="231" alt="04-chmod-drafts_with-pipe" src="https://github.com/user-attachments/assets/074e2002-050f-4bca-8b4c-a1ccbb1b7254" />

---

## ✅ Summary

I used `ls -la` to examine the permissions of every file and directory in `/home/researcher2/projects`, including hidden files, and compared them to the authorization that should be given. I then used `chmod` to correct the mismatches: I removed write access from other on `project_k.txt`, removed group read access from `project_m.txt`, made the hidden `.project_x.txt` read-only for the user and group, and removed group execute access from the `drafts` directory. These changes follow the principle of least privilege, so users only have the access they need, and unauthorized access is removed. This activity helped me understand how Linux permission strings work and how to adjust them safely with `chmod`.
