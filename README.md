# 🕹️ OverTheWire — Bandit Level 2 → Level 3

In this short lab I completed **Bandit Level 2 → Level 3**. The goal was to **find the password for Level 3** stored in a file whose name begins with dashes and includes spaces (`--spaces in this filename--`) in the home directory, then use that password to proceed to the next level.

---

📋 **Lab Overview**

**Goal**

* Locate the password for the next level inside the file named `--spaces in this filename--` in the home directory.
* Demonstrate safe techniques for reading files whose names look like command-line options.

**Why this matters**

* Teaches how Unix commands treat filenames that start with `-` (they can be parsed as options).
* Shows two safe ways to reference and read files that look like options:

  * use `./filename` (explicit path)
  * use `--` to signal end-of-options for many GNU utilities

**Learning Outcomes**

* Recognise tricky filenames that start with `-` or contain spaces.
* Use `--` to stop option parsing.
* Use quoting carefully and understand when quoting alone will not stop option parsing.
* Read files safely without accidentally passing them as options to commands.

---

🛠 **Step-by-Step Journey**

### Step 1 — Connect to the level

Task: SSH into `bandit2` on port `2220` using credentials from the previous level.

```bash
ssh -p 2220 bandit2@bandit.labs.overthewire.org
# (enter the password you obtained from level 1)
```

### Step 2 — Confirm location & list files

Task: See where you are and list files.

```bash
bandit2@bandit:~$ pwd
/home/bandit2

bandit2@bandit:~$ ls
--spaces in this filename--
```

✅ The file `--spaces in this filename--` is present and its name begins with dashes and contains spaces.

### Step 3 — Try naive approaches (and observe failures)

Task: Attempt common ways to read the file and note why they fail.

```bash
bandit2@bandit:~$ cat ./--spaces in this filename--
cat: ./--spaces: No such file or directory
cat: in: No such file or directory
cat: this: No such file or directory
cat: filename--: No such file or directory
```

The shell split the argument at spaces — not what we want.

```bash
bandit2@bandit:~$ cat "--spaces in this filename--"
cat: unrecognized option '--spaces in this filename--'
Try 'cat --help' for more information.
```

Quoting the name did not help here because `cat` still parsed the argument as an option (it begins with `--`).

### Step 4 — Correct method: use `--` to stop option parsing

Task: Tell `cat` that what follows are operands (filenames), not options.

```bash
bandit2@bandit:~$ cat -- "--spaces in this filename--"
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
```

✅ Success — the password for the next level is revealed.

> Note: The `--` POSIX/GNU convention indicates "end of options" for many CLI programs (including `cat`, `ls`, `rm`, etc.). Anything after `--` is treated as a positional argument (e.g., a filename), even if it starts with `-`.

### Step 5 — Exit

```bash
bandit2@bandit:~$ exit
logout
Connection to bandit.labs.overthewire.org closed.
```

---

🏁 **End of Lab**

**Found password for Level 3:** `MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx`
(Use this string to log into the next bandit level.)

---

✅ **Key Commands Summary**

| Task                                     | Command                                           |
| ---------------------------------------- | ------------------------------------------------- |
| SSH into bandit2 on port 2220            | `ssh -p 2220 bandit2@bandit.labs.overthewire.org` |
| Show working directory                   | `pwd`                                             |
| List files                               | `ls`                                              |
| Try to read file (bad — spaces split)    | `cat ./--spaces in this filename--`               |
| Try to read file (bad — option parsed)   | `cat "--spaces in this filename--"`               |
| Read file correctly using end-of-options | `cat -- "--spaces in this filename--"`            |

---

💡 **Notes / Tips**

* `--` is your friend when dealing with filenames that look like options. Use it with commands that accept it (most GNU coreutils do).
* If a filename contains spaces, quoting alone may not be enough if the name also starts with `-` (because the program can still treat the argument as an option). Combining `--` with quoting fixes both issues.
* Alternative: reference the file via an explicit path that does not start with `-`, e.g. `cat ./-filename` works when filenames start with a single `-` (but in this case the name includes spaces so you'd still need quoting or escaping).
* You can also escape spaces (`--spaces\ in\ this\ filename--`) but escaping doesn't solve the leading-dash interpretation — `--` is still the safest universal approach.
* Practice: try `ls -- --badfile` vs `ls -l -- --badfile` to see how `--` changes parsing.

---

✅ **References**

* OverTheWire — Bandit wargame (exercise practice)
* `cat` / GNU coreutils: use of `--` (end-of-options)
* Shell quoting & escaping basics
