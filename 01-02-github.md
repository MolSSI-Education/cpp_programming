---
title: "Using GitHub"
teaching: 30
exercises: 5
questions:
- "How do I use git and GitHub?"
objectives:
- "Explain reasons to use GitHub."

keypoints:
- "You can use GitHub to store your project online where you or others can access it from a central repository."
- "You can use GitHub to store your projects so you can work on them from multiple computers."
---

## Putting your repository on GitHub.
Now, let's put this project on GitHub so that we can share it with others. In your browser, navigate to `github.com`. Log in to you account if you are not already logged in. On the left side of the page, click the green button that says `New` to create a new repository. Give the repository the name `git-lesson`.

Note for the last question, "Initialize this repository with a README". We will leave this unchecked in our case because we have an existing repository (as described by GitHub, "This will let you immediately clone the repository to your computer. Skip this step if you’re importing an existing repository."). If you were creating the repository on GitHub, you would select this. Sometimes it is more convenient to do this than to initialize the repository locally because of the inclusion of these extra files.

Click `Create repository`.

Now, GitHub very helpfully gives us directions for how to get our code on GitHub.

Before we follow these directions, let's look at a few things in the repository. When you want to be able to put your code online in a repository, you have to tell the `git` software that you have an online repostiry and where it is located. Git calls these `remotes`. Currently, our repository has no remotes. See this by typing

~~~
$ git remote -v
~~~
{: .language-bash}

You should see no output. Now, follow the instructions on GitHub under "...or push an existing repository from the command line"
~~~
$ git remote add origin https://github.com/YOUR_GITHUB_USERNAME/git-lesson.git
$ git push -u origin master
~~~
{: .language-bash}

The first command adds a remote named `origin` and sets the URL to our repository. The word `origin` here is simply a word that is a shortcut for the location of our repository. We could have called it anything (like `banana` or `elephant`, or anything we wanted), but `origin` is used by convention. Now, whenever we say `origin`, git knows that we really mean `https://github.com/YOUR_GITHUB_USERNAME/git-lesson.git`. The second command copies (or "pushes") everything which we have tracked using git to `origin`. The word `master` means we are pushing the `master` branch. 

Now if you refresh the GitHub webpage you should be able to see all of the new files you added to the repository.

## Ignoring Files

Sometimes while you work on a project, you may end up creating some temporary files.
For example, if your text editor is Emacs, you may end up with lots of files called `<filename>~`.
By default, Git tracks all files, including these.
This tends to be annoying, since it means that any time you do "git status", all of these unimportant files show up.

We are now going to find out how to tell Git to ignore these files, so that it doesn't keep telling us about them ever time we do "git status".
Even if you aren't working with Emacs, someone else working on your project might, so let's do the courtesy of telling Git not to track these temporary files.
First, lets ensure that we have a few dummy files. Create empty files in your text editor called `quotes.py~` and `README.md~`.

Now check what Git says about these files:

~~~
$ git status
~~~
{: .bash}

~~~
On branch master
Your branch is up to date with 'origin/master'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	README.md~
	quotes.py~

nothing added to commit but untracked files present (use "git add" to track)
~~~
{: .output}

Now we will make Git stop telling us about these files. We do this with a file called `.gitignore`. A `.gitignore` does what it sounds like - it tells `git` files or directories to ignore. If we can created our repository on GitHub and cloned it to our computer, we could have selected to create the repository with a `.gitignore.` We could have told `GitHub` what language we were planning to use, and it would have given us a starting `.gitignore` with files we would be likely to want to ignore. 

Navigate [here](https://github.com/github/gitignore/blob/master/Python.gitignore) to get a good starting gitignore for python. Copy the contents of this file to a file in your repository called `.gitignore`.

Look at the contents of `.gitignore`

~~~
# Byte-compiled / optimized / DLL files
__pycache__/
*.py[cod]
*$py.class

# C extensions
*.so

# Distribution / packaging
.Python
env/
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg

# PyInstaller
#  Usually these files are written by a python script from a template
#  before PyInstaller builds the exe, so as to inject date/other infos into it.
*.manifest
*.spec

...
~~~

Git looks at `.gitignore` and ignores any files or directories that match one of the lines.

Commit your gitignore to the repository.

~~~
$ git add .gitignore
$ git commit -m "add gitignore to repository
~~~
{: .language-bash}

Next, let's ignore those emacs temporary files we added.

Add the following to the end of `.gitignore`:

~~~
# emacs
*~
~~~

Now do "git status" again. Notice that the files we added are no longer recognized by git.

~~~
$ git status
~~~
{: .bash}

~~~
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   .gitignore

no changes added to commit (use "git add" and/or "git commit -a")
~~~
{: .output}

We want these additions to .gitignore to become a permanent part of the repository, so do

~~~
$ git add .gitignore
$ git commit -m "Ignores Emacs temporary files and data directory"
$ git push
~~~
{: .bash}

One nice feature of .gitignore is that prevents us from accidentally adding a file that shouldn't be part of the repository.
For example:

~~~
$ git add quotes.py~
~~~
{: .bash}

~~~
The following paths are ignored by one of your .gitignore files:
quotes.py~
Use -f if you really want to add them.
~~~
{: .output}

It is possible to override this with the "-f" option for git add.


## More GitHub Features

Navigate to the GitHub page for your project.
Click on "testing.txt".
Here you can see the file and make changes to it.
Click the edit button, which looks like a small pencil near the upper right of the file text box.
Add a line that says "I added this line from the GitHub web interface!", so that the file looks like:

~~~
***************************************
This is the start of testing.txt
***************************************

I added this file from a new clone!
Now I added a new line!
I added this line from the GitHub web interface!

***************************************
This is the end of testing.txt
***************************************

~~~

Scroll to the bottom of the page and write the name "Added a line to testing.txt from the web interface" for this commit.
Then, click the green "Commit changes" button at the bottom left.
You should now see that your change appears in the text box.

Click the "Blame" button to find out who is responsible for each line of code.
Click the "History" button to see a list of all commits that affected this file.
You can click on a commit to see exactly what it did.

Go back to the main project page, and click the "commits" button.
Here you can see a list of all the commits for this project.
Clicking them reveals how they changed the code.

The "Issues" tab lets you create discussions about bugs, performance limitations, feature requests, or ongoing work that are shared with everyone else who is working on the project.
Try filling out a quick issue now.
Then comment and close the issue.

{% include links.md %}
[GitHub]: https://github.com
[GitLab]: https://gitlab.com/
[BitBucket]: https://bitbucket.org/
