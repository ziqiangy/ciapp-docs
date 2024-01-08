1. to revert the changes or reset the head
   $ git reset --hard HEAD
2. to delete or get rid of untracked files
   $ git clean -f -d
3. to delete ignored files, plus -x
   $ git clean -fdx



4. undo a git add
   
   git restore --staged <file/path>

5. untrack a file
   
   ```git
   git rm --cached <filename>
   ```
