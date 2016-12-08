The page contains instructions and tips for infrastructure used in this course, including [Instabase](https://www.instabase.com), web servers, Python, PostgreSQL, SQLite, etc.

## Instabase Common Errors and Cheat Sheet 

1. When you open a notebook file with Jupyter in [Instabase](https://www.instabase.com), it typically takes **5-10 seconds** to setup a container for you and launch the notebook. In some cases though, there might not be enough resource capacity (CPU and Memory)  in the cluster -- in that case [Instabase](https://www.instabase.com) automatically expands the cluster capacity. If launching your notebook requires adding new machines to the cluster -- the time to launch your notebook can be up to **1-3 minutes**. In our tests, we have found that for 99 percentile, the launch time is < 2 minutes.

1. When you load a notebook (open a ipynb or refresh a page) -- please wait for ```Kernel Ready``` before you can run any code in the notebook.
 
1. Jupyter notebooks use websocket to run your code (the websocket is connected to a Python kernel that runs the code). If you are using [Instabase](https://www.instabase.com) for running Jupyter Notebooks, the notebooks connect to a Python kernel running in Instabase datacenters. The websocket connection might break when you lose the internet connectivity intermittently. If you find that your code is not executing, please refresh the page -- it will reconnect your notebook to the kernel.
 
1. Occasionally your container might go in a bad state (because of abrupt reboot or broken installations). In general, if your container gets into a bad state, you can kill your bad container by going to this URL -- [https://www.instabase.com/cmd/stop_machine/nb](https://www.instabase.com/cmd/stop_machine/nb). This stops the current container so that you can launch a fresh container for you). This will clear all old state and you will start with a fresh state.

1. If you get logged out from [Instabase](https://www.instabase.com) (from events like browser clearing the cookie), you might get 404 (page not found error) -- please refresh the page. If you are logged out, it will take you to the  login page and after the login, everything should work again.
 
1. If you run a buggy code that might get into some infinite loop, or infinite recursion or infinite wait on stdin [keyboard input] -- please click on ```Kernel``` > ```Interrupt``` to get out of the infinite wait.
 
1. [Instabase](https://www.instabase.com) periodically kills containers that has been inactive for more than 4 hours. While it automatically saves all your file changes to Instabase Filesystem every few minutes but to be completely safe, please make sure to click ```File``` > ```Save``` before leaving the page.
 
1. Google Chrome is the recommended browser for [Instabase](https://www.instabase.com).

1. When doing query in instabase, use WITH clause instead of using SELECT... INTO ... to create new tables. SELECT... INTO ... would create a real physical table, which means you need to keep changing the name of table until you get your query right.

1. Try to avoid querying like SELECT * FROM... ; if you just want to know how the dataset looks like, since this kind of action would take a long time and could slow down other people's query as well. Use limit 1 if you just want to take a look at the data. Use count(*) if you want to know how large is the dataset. Most of time you do not need to print all the dataset.

1. When instabase is not working as expected, try refreshing and restart the code from the very beginning. 