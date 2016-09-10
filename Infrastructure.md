The page contains instructions and tips for infrastructure used in this course, including Instabase, web servers, python, postgreSQL, SQLite, etc.

## Instabase/Jupyter Common Errors and Cheat Sheet 
 
1. Jupyter uses websocket to run your code (the websocket is connected to a python kernel that runs the code). Sometimes when you lose the internet connectivity, the websocket connection might get broken -- please refresh the page -- it will reconnect your notebook to the kernel.
 
1. If you get logged out from Instabase (from events like browser clearing the cookie), you might get 404 (page not found error) -- please refresh the page. If you are logged out, it will take you to the  login page and after the login, everything should work again.
 
1. Occasionally your container might go in bad state (because of abrupt reboot or broken installations). In general, if your container gets into a bad state, please go to this URL -- https://www.instabase.com/user/stop_machine/nb (this stops the current container so that you can lunch a fresh container for you). This will clear all old state and you will start with a fresh state.
 
1. When you load a notebook (open a ipynb or refresh a page) -- please wait for "Kernel Ready" before you can run any code in the notebook. 
 
1. If you run a buggy code that might get into some infinite loop, or infinite recursion or infinite wait on stdin [keyboard input] -- please click on "Kernel -> Interrupt" to get out of the infinite wait.
 
1. Instabase periodically kills containers that has been inactive for more than 4 hours. While it automatically saves all your file changes to Instabase Filesystem every few minutes but to be completely safe, please make sure to click File --> Save before leaving the page.
 
1. When you open a file with Jupyter, it typically takes 5-10 seconds to setup a container for you and launch the notebook. In some cases though, there might not be enough resource capacity (CPU and Memory)  in the cluster -- in that case Instabase automatically expands the cluster capacity. If launching your notebook requires adding new machines to the cluster -- the time to launch your notebook can be up to 1-3 minutes. In no case, it should take more than 3 minutes though.
 
1. Google Chrome is the recommended browser for Instabase.