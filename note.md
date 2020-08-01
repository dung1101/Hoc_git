[https://git-scm.com/doc](https://git-scm.com/doc)
# 1 Git config
3 level:
- all system: `/etc/gitconfig`
- user: `~/.gitconfig` or `~/.config/git/cònig`
- repo: `.git/config`

# Identity
```
git config --global user.username "dung1101"
git config --global user.email "dung@gmail.com"
```

# Editor
```
git config --global core.editor "'C:/Program Files/Notepad++/notepad++.exe' -multilnst -nosession"
```

# List
```
git config --list
git config user.username
```

# 2 Repository
```
# Khởi tạo
git init

# clone
git clone https://github.com/dung1101/Hoc_git.git
# or
git clone git@github.com:dung1101/Hoc_git.git
```

# 3 Quản lý repo
## Lifecycle
![lifecycle.png]()

## Check status
```
git status
```

## Ignore file
`.gitignore`: git sẽ bỏ qua tất cả các file được liệt kê trong file này.

Các file gitirgnore github xây dựng sẵn
[https://www.github.com/github/gitignore](https://www.github.com/github/gitignore)

Hoặc trang tạo file theo từ khóa
[https://www.gitignore.io/](https://www.gitignore.io/)

## Git add
Chuyển các file sang trạng thái staged
```
# Tất cả repo
git add *

# Thư mục hiện tại
git add .

# file abc
git add abc.xyz
```

Hủy bỏ add
```
git rm -rf --cached [file/thư mục]
```

## View satged, unstaged changes
```
git diff --staged
```

## Git Commit
```
git commit -m "message"
```

## log
```
git log
```

## Skipping the staging area
```
git commit -a -m "commit all"
```

## Moving file under track
```
git mv file1 file2
```

# View commit history
```
git log

-p: present diferent between commits
-p -2: diff between 2 commit

--stat:

--pretty: change format 

--since : filter log
--after: filter log

...
```

# 8 Undo thing
## 8.1 undoing things
commit lại: commit nhưng thiếu cần chỉnh sửa và commit lại, thì lần commit lại sẽ ghi thêm vào và chỉ tính là một commit thôi. Ví dụ:
```
git commit -m "new feature"
git add forgotten_file
git commit --amend 
```
như vậy lần commit thứ 2 sẽ add thêm file forgotten vào và sẽ chỉ tính là 1 commit "new feature"

## 8.2 unstaging a staged file
trong trường hợp muốn tách rõ commit này thêm tính năng gì thay đổi file nào nhưng lỡ add tất cả các file vào .
```
git reset HEAD <file>
```

## 8.3 Unmodifying a Modifiled file
reset lại lại file đã chỉnh sửa
```
git checkout -- <file>
```

# 9 Working with remote repository
## 9.1 
```
git clone <link>: download repo
git remote : return short name  (origin)
git remote -v: url của remote repo
```

```
git remote add <short name> <url>
```

## 9.2 fetching and pulling
```
git fetch: get info change from remote
git pull <short name> <branch>: get change and merge to local 
```

## 9.3 rename and remove
```
git remote rename <old name> <new name>
git remote rm <short name>
```

# 10 Tagging
đánh dấu 1 điểm quan trọng trong lịch sử(release, version)
## 10.1 listing tags
```
git tag
git tag -l "v1.8.5" 

git show <tag name>
```

## 10.2 creating tag
2 loại:
- annotated
```
git tag -a v1.0 -m "first release version "
```
- lightweight
```
git tag v1.1
```

## 10.3 tagging later
```
git tag -a v1.0 -m "init" <commit id>
```

## sharing tags
mặc định sẽ ở local
```
git push origin <tag>: từng tag
git push origin --tags: all tag
```

## checking out tags
create new branch from a tag
```
git checkout -b <branch> <tag>
```

## 10.6 delete tag
```
git tag -d <tag>: del in local
git push origin --delete <tag>: del in remote
```

## Move all uncommitted changes to another branch
```
git stash push
git checkout <another_branch>
git stash apply
```


