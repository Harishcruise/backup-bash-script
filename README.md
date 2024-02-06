# Backup Script

## Overview

This Bash script is designed for performing both complete and incremental backups for a specified user on a Linux system. It utilizes tar to create backup files for `.c` and `.txt` files in the user's home directory.

## Features

- **Complete Backups**: Regular complete backups of specified file types.
- **Incremental Backups**: Periodic incremental backups for files modified in the last 2 minutes.
- **Automatic Backup Handling**: The script automatically manages backup directories and logs.

## Usage

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/Harishcruise/backup-script.git

2. **Navigate to the Project Directory:**

    ```bash
    cd backup-script

3. **Configure the Script:**

    Open the script (backup.sh) and set the USERNAME variable to the desired username.

4. **Run the Script:**

    ```bash
    ./backup.sh

5. **Monitor Backups:**

    Check the backup.log file for detailed information about backups.

6. **Configuration:**

    USERNAME: Set the desired username for whom the backup is being performed.
    Backup Directory Structure:
    cb: Complete backups.
    ib: Incremental backups.
    backup.log: Log file containing backup information.

7. **Customization:**
    Feel free to customize the script based on your specific requirements. You can adjust file types, backup intervals, or any other parameters according to your needs.

8. **Contributing:**
    If you'd like to contribute to this project, please fork the repository and submit a pull request. Contributions, suggestions, and bug reports are welcome!
