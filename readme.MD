###Which remote URL should I user?
There are several ways to clone repositories available on GitHub.
1. Cloning with HTTPS URLs(recommended)
 需要提供用户名和密码
 可以通过credential helper来保存GitHub的用户名和密码

2. Cloning with SSH URLs
 需要提供SSH key passphrase
 
link:https://help.github.com/articles/which-remote-url-should-i-use/


 ###Generating an SSH key
1. Checked for existing SSH keys
2. Generating an SSH key
3. Adding a new SSH key to your GitHub account
4. Testing your SSH connection

link:https://help.github.com/categories/ssh/


###Caching your GitHub password in Git
$git config --global credential.helper cache

To change the default password cache timeout, enter the following:
$git config --global credential.helper 'cache --timeout=3600'
# Set the cache to timeout after 1 hour (setting is in seconds)

https://help.github.com/articles/caching-your-github-password-in-git/#platform-linux


###使用的命令
git remote -v
git remote set-url git@github.com:orgcheng/android-note.git

Further Rreading: 
Working with Remotes
https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes