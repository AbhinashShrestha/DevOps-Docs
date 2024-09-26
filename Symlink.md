### Creating a Symbolic Link

You create a symbolic link using the `ln -s` command:

```bash
ln -s /home/anon/sym_study/real/sachi maboroshi
```

- `/home/anon/sym_study/real/sachi` is the target file or directory.
- `maboroshi` is the name of the symbolic link created in the current directory.

### Understanding Symbolic Links

A symbolic link (`maboroshi`) acts as a pointer or reference to the target file or directory (`/home/anon/sym_study/real/sachi`). Any operations performed on the symbolic link are actually applied to the target file or directory itself.

### Example Operations

1. **Navigate to the Symbolic Link Directory:**

   ```bash
   cd maboroshi/
   ```

2. **Create a File (`a.txt`) Inside the Symbolic Link Directory:**

   ```bash
   touch a.txt
   ```

   This command creates `a.txt` inside `/home/anon/sym_study/real/sachi`, not within the `maboroshi` directory itself.

3. **Navigate Back and Check the Target Directory:**

   ```bash
   cd ..
   ls real/sachi/
   ```

   Here, `ls real/sachi/` lists the contents of the target directory (`/home/anon/sym_study/real/sachi`). You will see `a.txt` listed here, confirming that the file creation was applied to the target directory through the symbolic link.

### Key Points

- **Symlink Behavior**: Symlinks are lightweight references to files or directories. Operations on the symlink affect the target.
  
- **Directory Operations**: Operations like file creation, deletion, or modification within the directory represented by the symlink (`maboroshi`) are actually applied to the target directory (`/home/anon/sym_study/real/sachi`).

- **Permissions and Ownership**: Symlinks themselves have their own permissions and ownership, but actions on them affect the permissions and ownership of the target file or directory.

By understanding these principles, you can effectively use symbolic links to reference files or directories without duplicating content, which is useful for managing resources efficiently in Linux systems.