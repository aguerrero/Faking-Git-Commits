# Faking Git Commits

I took [a class](https://github.com/mikeizbicki/ucr-cs100) that used github to track and record grades.
The professor offered extra credit if we could hack his grading system to change grades.
So I figured out a way to fake git commits.

My first idea involved social engineering.  I was going to convince our professor
that he had made a grading mistake, but he paid a lot of
attention to what was going on. I didn't think that would go too well
for me, so I turned to Google and started reading up on git. I
didn't know exactly what I was looking for, but I knew I was on the
right track. In [one of our labs](https://github.com/mikeizbicki/ucr-cs100/assignments/labs/lab1-git/) we covered how to add files to a git
repository and commit the changes; what we didn't cover was how git
decided on who made the commits in the first place. If you look at a
repository's log with `git log`, you'll see the history of all the commits
made up to that point. If you look at the output, you'll see an output
similar to: 
```
$ git log
960b39317524a98752955ec757e1284805c3a6a9
Author: Antoine Guerrero <asguerrero3@gmail.com>
Date:   Tue Sep 2 23:28:11 2014 -0700

    Add README
```
Looking at the example above, we see that the author is identified by a
name followed by an email address. How does git determine which email
address to use? It uses the name and email address in your
`~/.gitconfig` file.  Mine looks like:
```
[user]
    name = Antoine Guerrero
    email = asguerrero3@gmail.com
```
Since the user gets to set the email, and no validation
ever takes place, there is no way of telling if your name and email address
are actually your name and email address. With this in mind, we'll now take
a look at how I used this information to hack our grading system.

## Changing My Grade
After determining that there was no way git could tell if I was who my
configurations said I was, I was left with one last hurdle. When pushing
to a GitHub repository using the https protocol, you need to authenticate
yourself using your login information. I wasn't too sure what would
happen if I pushed a commit using a different email than the one used
for my GitHub credentials, but I told Mike of my idea and with his
permission, I went and tried out my idea. The process of committing with
Mike's credentials was straight forward. First, I modified the grade file
in vim and then added the file with `git add grade`. The commit was made
with `git commit --author="Mike Izbicki <mike@izbicki.me>" -m "Change
grade"`. By using the `--author` flag, I was able to override the author
that git would credit for the commit. Instead of using the information in
my `~/.gitconfig` file, git used the author name and email address passed
along the `--author` flag.

The only thing left to see was what would happen when I tried to push
the new commits with my Github credentials.  I ran `git push origin master`, and everything worked just fine.  
Github now displayed that Mike was the author of my commit. 

You may be wondering why git even allows this
to happen in the first place and the answer is simple. The person pushing
new commits to a repository may not be the person who made the changes.
In a team environment many people may commit code changes, but for whatever
reason one person may be the only one responsible for pushing the code.
So what happens to be a Git feature helped me get extra credit in class.

## Using Git For Evil
In the example above, Mike happened to be a contributor to my repository,
but would the same method work for non contributors? It turns out it
still works. If you look up the git log to this repository, you'll see
that a few GitHub staff members and Linus Torvalds have also contributed
to this repository and made changes to the file `important.txt`.
Check it out:

```
$ git blame important.txt
88e3b9b3 (Linus Torvalds  2014-09-02 23:36:29 -0700 1) Antoine is the best programmer EVER!!!!!!!!!!!!
a4dd8a84 (Chris Wanstrath 2014-09-02 23:37:45 -0700 2) I agree. We should hire him.
684875b2 (PJHyett         2014-09-02 23:33:09 -0700 3) I'm tired of working for GitHub, I QUIT!
```

While this case of committing commit fraud was rather innocent, there are a few
not-so-innocent ways to abuse this feature. Consider the average CS student
that will be searching for jobs after graduation. Such a student may be
building a portfolio to present to possible employers, but may not have
anything on GitHub that catches too much attention. One way to change
that could be to fake some commits from some high profile programmers like
Linus Torvalds. Having some commits from Linus may get your project some
attention and make people assume you are a better programmer than you
really are. After all, if your project was bad, there would be no way
Linus would have looked at it and contributed in the first place.

## How to Avoid Fake Commits
The correct way to avoid these fake commits is by signing the commits with a RSA key.
If someone pushes a commit without the correct key, git will notice. 
If you run the command `git log --show-signature`, then any commit not signed
would immediately be discovered. 
This can be used to fix the grading system used by cs100. 
But in the real world, this would not help
in the case of a person committing to their own repository in an attempt
to bolster their list of contributors since they would receive no
benefit from verifiable commits.

# Additional Information
More information on the use of git can be found be reading the
documentation found at www.git-scm.com/documentation.
