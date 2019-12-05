# trial4
trying out alternative rebase scenarios
## Goal
Understanding how rebase works under certain circumstances

1. A local repo is created simply by 'git init'
2. Two local commits are made. 

3. A remot repo is created on GitHub.
4. The remote repo is added to the local one via 'git add remote origin <URL>'
  
State after above operations.

`l:
A<---B<---{master}<--{HEAD}

C<--D<---{origin/master}

r: 
C<--D<---{master}`
            
            
Trying to push the branch pointed by HEAD won't work right away, as remote and local do not share a common history. 
First the local branch should be rebase on top of origin master to obtain following state:

`l:

C<--D<---A<---B<---{master}<--{HEAD}
    ^
    |
{origin/master}`

Digression: 
  * running 'git merge origin/master' won't change anything
  * origin/master by itself cannot be modified, other than by the command 'git fetch'  
  
Now pushing HEAD to origin should work, i.e. running 'git push origin HEAD:master' should result in the following state:


`l:

C<--D<---A<---B<---{master}<--{HEAD}
    ^
    |
{origin/master}

r: 
C<--D<---A<---B<---{master}`

Fetching now will update the origin/master branch by pushing forward to B.

'git fetch' : 

`l:

C<--D<---A<---B<---{master}<--{HEAD}
              ^
              |
        {origin/master}

r: 
C<--D<---A<---B<---{master}`

alternatively, assuem we are again in the previous step :

`l:

C<--D<---A<---B<---{master}<--{HEAD}
    ^
    |
{origin/master}

r: 
C<--D<---A<---B<---{master}`

Assume that developments simultanously take place both on local and remote repos.: 


`C<--D<---A<---B<---E<---F<---{master}<--{HEAD}
    ^
    |
{origin/master}

r: 
C<--D<---A<---B<---G<---H<---{master}`

Pushing HEAD right away will result in the warning that the tracking branch is behind the tracked branch. So first we must run 'git fetch' resulting in:

`l:
C<--D<---A<---B<---E<---F<---{master}<--{HEAD}
               \
                \----G<---H<---{origin/master}


r: 
C<--D<---A<---B<---G<---H<---{master}`


Pushing now won't work again, but for a different reason. The "forking" on the commit B is not a possible final state. Instead we must merge origin/master into master: 'git merge origin/master'

`l:
C<--D<---A<---B<---E<---F<-------/--K<---{master}<--{HEAD}
               \                /
                \----G<---H<---/
                          ^
                          |
                   {origin/master}        
                   
r: 
C<--D<---A<---B<---G<---H<---{master}`


Now we can push to origin : 'git push origin HEAD:master'

`l:
C<--D<---A<---B<---E<---F<-------/--K<---{master}<--{HEAD}
               \                /
                \----G<---H<---/
                          ^
                          |
                   {origin/master}        
                   
r: 
                
              |----E<---F<---\
              v               \ 
C<--D<---A<---B<---G<---H<-----|--K<----{master}`


Finally fetching will bring origin/master on local to point to commit K

Another Commit. 
