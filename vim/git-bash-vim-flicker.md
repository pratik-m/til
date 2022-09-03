# Git Bash VIM flicker issue 

I recently started using vim and its fantastic! I've been using it on windows on git-bash and there is a unusual flicker
appears when I hit escape or when there is an error. I though it might be due to the `errorbell` functionality in vim.
Even I set noerrorbell, the flicker still continued to appear. 

Finally, after bit of research, I got a way to fix the issue. Just as errorbell, you have visualbell as well. I just had
to set novisualbell and voila! the flicker went away. 

add the below to your .vimrc file 
```
set noerrorbell
set novisualbell
```
