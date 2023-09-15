# Push an existing git repo to a new/different repo

Follow below steps to push/copy an existing git repo to a new/different repo

1. Create a Git Repo 
2. `cd` to the existing git repo folder 
3. Execute below commands 
    ```
        git remote rename origin upstream
        git remote add origin <new-git-repo>
        git push origin main
    ```


