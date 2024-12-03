# Change default shell

To change the default shell, ensure that the shell executable path in present in the file `/etc/shells`.
If missing, append the path to the file.
Run the below command

```bash
chsh -s $(which zsh)
```

The default shell change will only take effect once you've logged out of the system. Restarting the terminal wont help.
