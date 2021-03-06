# Work with Multiple Github Accounts
All the steps here are based on Mac OS Catalina.

## 1. Generate an SSH Key for your repo with user1.

>```
>ssh-keygen -t rsa -b 4096 -C "your-email-address"
>```
It is better to provide the email of your github account. 

When prompting the rsa path, choose a new path instead of the default one, eg: `id_rsa_user1`.

Then, a `id_rsa_user1.pub` file is also generated.
```
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/rocky.chen/.ssh/id_rsa): id_rsa_user1
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa_user1.
Your public key has been saved in id_rsa_user1.pub.
```

Copy the output and fill them into your Github profile. Please refer to [this article](https://help.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account) for detail steps.
>```
>cat id_rsa_user1.pub
>```

Add your SSH key to SSH agent:
>```
>ssh-add ~/.ssh/id_rsa_user1
>```

Double check if your SSH key added correctly by the following command.
>```
>ssh-add -l
># Your SSH key should be in the output list.
>```

## 2. Setup SSH Config
SSH config file locates at `~/.ssh/` folder and put following content into it:

>```
># user1 account
>Host github.com-user1
>    HostName github.com
>    IdentityFile ~/.ssh/id_rsa_user1
>    IdentitiesOnly yes
>```

## 3. Git Clone your repo with user1.
Since the host is set in the SSH config file above, `git clone` command is a little different with the default one. From the repo, the SSH url should be like:

>```
>git@github.com:user1/repo_name.git
>```

Here, we need to update it as:

>```
>git@github.com-user1:user1/repo_name.git
>```

So the full command is:

>```
>git clone git@github.com-user1:user1/repo_name.git
>```

## 4. Setup GIT config
### 4.1 Global setting change
First, update `~/.gitconfig` by replace the `[user]` section to the following (comments not included):
>```
>[includeIf "gitdir:~/code/<user1_working_folder>/"] # The folder doesn't need to be user1's local git repo and it can be the parent folder of the repos you want to access with user1.
>	path = .gitconfig-user1
># you might want to add another user config here later.
>```
Second, add `~/.gitconfig-user1` file with following information:
>```
>[user]
>   email = <user1_github_email>
>   name = <user1's name>
>```

### 4.2 Local setting change
After you cloned the repo, enter the repo folder and run:

>```
>git config --local -e
>```

Check the section `[remote "origin"]` and modify the `url` field to:

>```
>url = git@github.com-user1:user1/repo_name.git
>```

### 4.3 Verify your GIT config
Goto a local repo of user1 (it should be under the folder specified in `~/.gitconfig`), run the following commands:
>```
>$ git config --get user.email    	# should return user1's email.
>$ git config --get user.name     	# should return user1's name.
>```

## 5. Add another GIT user account
Repeat the step 1 to 4 to add another GIT user account to your machine. I would rather recommend you to do step 6 to verify your user1's account working well before adding another user.

## 6. Verify your setup
After complete all steps above, try to make a simple PR to verify the user1 was setup correctly.
For example, adding a README markdown file:
```
$ echo "# Readme" >> README.md
$ git init
$ git add README.md
$ git commit -m "Add readme."
$ git remote add origin git@github.com-user1:user1/<repo_name>.git
$ git push -u origin master
```
Goto user1's Github page to double check if a PR could be created based the remote branch and commit was make by user1.

## 7. References
When I tried to make it, I searched online articles to help me. The following articles I found helpful during my setting up processes:

* [Generating a new SSH key and adding it to the ssh-agent](https://help.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

* [Manage multiple GIT accounts on a single machine](https://medium.com/@geeky_sh/manage-multiple-git-accounts-on-a-single-machine-d49d710ec229)

* [Setup multiple git identities & git user informations](https://gist.github.com/bgauduch/06a8c4ec2fec8fef6354afe94358c89e)
