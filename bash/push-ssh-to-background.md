# Push a ssh connection to background 

Ever wonder if you can push the ssh connection to background, just as you would do for any bash job? I recently
discovered that there is a way to do the exact thing! 

Once you have ssh'd into any server, you can put the connection in background by just typing `~<ctrl+z>'. Yes thats it! 
It will not be typed to the screen, but when you hit that keystroke, you will be returned session you ssh'd from.  

If you want to go back to the connect, just type `fg` and you will be back into the server you ssh'd to. 

This is a time saver. I just to log out of the server and log back in everytime! 

Hope this helps! 

