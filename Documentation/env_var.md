# Environment Variables

### Linux environment variables act as placeholders for information stored within the system that passes data to programs launched in shells or subshells.

Admins have the ability to modify environment variables to fit personal or larger group needs of users within their environments. As you’ll notice below, admins can alter the hostname, command-line prompt, coloring in shells for text, and various other environment variables to better suit user preference.

#### Commands for Environment Variables:

1. env  – The command lists all of the environment variables in the shell.

2. printenv – The command prints all (if no environment variable is specified) of environment variables and definitions of the current environment.
3. set – The command assigns or defines an environment variable.
4. unset – The command deletes the environment variable. 
5. export – The command exports the value of the newly assigned environment variable.


**An environment variable must be exported to propagate its value to a child process. You can do either of the following:**

```bash
    $ VAR=value ; export VAR
    $ export VAR=value
```