# **Segurata - Restricted Terminal**

**Segurata** is a custom terminal that restricts the use of commands to those previously specified. This project is ideal for situations where you want to create a controlled execution environment, limiting the actions of the user to increase security.

---

## **README Content**

- [Requirements](#requirements)
- [Installation and Build](#installation-and-build)
- [Default Terminal Configuration](#default-terminal-configuration)
- [Permission Configuration](#permission-configuration)
- [Additional Recommendations](#additional-recommendations)

---

## **Requirements**

Before you begin, make sure your development environment meets the following requirements:

- **Python 3** or later
- **PyInstaller** (to compile the Python script into an executable binary)
- **Linux** (for terminal configuration and system execution)
- **Administrator privileges** (to modify `/etc/passwd` and configure permissions)

---

## **Installation and Build**

### 1. **Install the Required Dependencies**

First, ensure that Python 3 and `pip` are installed, then install **PyInstaller** to compile the binary.

```bash
sudo apt-get install python3 python3-pip
pip3 install pyinstaller
```

### 2. **Obtain and Build the Segurata Binary**

Create the `segurata.py` script where you define the allowed command behavior:

```python
import sys

allowed_commands = ["chmod 000", "chmod +x"]

def execute_command(command):
    if command in allowed_commands:
        print(f"Executing: {command}")
        # Logic to execute the allowed command
    else:
        print(f"Command '{command}' not allowed.")

if __name__ == "__main__":
    if len(sys.argv) > 1:
        command = sys.argv[1]
        execute_command(command)
    else:
        print("Please enter a valid command.")
```

Once your script is ready, compile it into an executable binary with **PyInstaller**:

```bash
pyinstaller --onefile segurata.py
```

This will generate an executable file named `segurata` in the `dist/` folder.

### 3. **Move the Binary to `/usr/bin`**

Move the binary to a globally accessible location:

```bash
sudo mv dist/segurata /usr/bin/segurata
sudo chmod 111 /usr/bin/segurata
```

---

## **Default Terminal Configuration**

### 1. **Modify `/etc/passwd`**

To change the default terminal of a user to **Segurata**, edit the `/etc/passwd` file, which defines the terminal used by each user when they log in.

Open the file with a text editor:

```bash
sudo nano /etc/passwd
```

Find the line corresponding to the user whose terminal you want to change. For example, for the user `usuario1`, the line might look like this:

```
usuario1:x:1001:1001:Usuario1:/home/usuario1:/bin/bash
```

Change `/bin/bash` to the path of the **Segurata** binary:

```
usuario1:x:1001:1001:Usuario1:/home/usuario1:/usr/bin/segurata
```

### 2. **Save Changes**

Save the file and close the editor (`Ctrl + O`, then `Ctrl + X` if using `nano`).

### 3. **Verify Changes**

Log out and back in to apply the changes. Now, the default terminal should be **Segurata**.

---

## **Permission Configuration**

### 1. **Create a Folder Structure by User**

It is recommended to create a centralized folder for **Segurata**, where you can place a copy of the binary for each user. This way, each user will have their own instance of **Segurata** with controlled permissions.

Create the `segurata` folder and subfolders for each user:

```bash
sudo mkdir /usr/local/segurata
sudo mkdir /usr/local/segurata/user1
sudo mkdir /usr/local/segurata/user2
```

### 2. **Copy the Segurata Binary to Each User's Folder**

Now, copy the **Segurata** binary to each user's folder:

```bash
sudo cp /usr/bin/segurata /usr/local/segurata/user1/
sudo cp /usr/bin/segurata /usr/local/segurata/user2/
```

### 3. **Set Exclusive Permissions for Each User**

Make sure that each user has exclusive permissions to execute their own copy of **Segurata**. Change the ownership and permissions of each file:

```bash
sudo chown user1:user1 /usr/local/segurata/user1/segurata
sudo chmod 111 /usr/local/segurata/user1/segurata

sudo chown user2:user2 /usr/local/segurata/user2/segurata
sudo chmod 111 /usr/local/segurata/user2/segurata
```

### 4. **Configure Allowed Commands in `segurata.py`**

Ensure that **Segurata** only allows the commands you've specified in the `segurata.py` file (e.g., `chmod 000` and `chmod +x`):

```python
allowed_commands = ["ls", "cat","wget","curl","whoami","uname"]
```

---

## **Additional Recommendations**
1. **Security Auditing**: Use tools like `auditd` to track user actions and ensure they don't make unauthorized changes.
2. **Hardening Security**: Consider creating a **chroot** environment or using a **restricted shell** to ensure users cannot escape **Segurata**.
3. **Permission Review**: Conduct periodic audits to ensure that file permissions and folder configurations haven't been altered without authorization.
