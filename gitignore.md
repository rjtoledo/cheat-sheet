# Git Ignore

To create a **`.gitignore`** file that applies to all of your repositories, you can create a global **`.gitignore`** file in your home directory. Any patterns defined in this file will apply to all Git repositories on your system.

Here's how you can create a global **`.gitignore`** file:

1. Open a terminal or command prompt window.
2. Navigate to your home directory by running the command **`cd ~`**.
3. Create a new file called **`.gitignore_global`** by running the command **`touch .gitignore_global`**.
4. Open the file in a text editor of your choice, and add the patterns for files and directories that you want to ignore. For example, to ignore all **`.DS_Store`** files, you can add the following line to the file:

```

.DS_Store
```

1. Save the file and exit the text editor.

Now that you have created a global **`.gitignore`** file, you need to tell Git to use it. Here's how you can do it:

1. Open a terminal or command prompt window.
2. Run the following command to tell Git to use your global **`.gitignore`** file:

```

git config --global core.excludesfile ~/.gitignore_global
```

This command tells Git to use the **`.gitignore_global`** file located in your home directory (**`~`**) as the global ignore file.

Now, any patterns defined in your global **`.gitignore`** file will apply to all Git repositories on your system, including any repositories that you create in the future.
