#!/bin/bash

# Setting username
USERNAME="harish"

# Setting backup directory
BACKUP_DIR="/home/${USERNAME}/backup"

# Removing backup directory and backup logs if present
rm -r "/home/${USERNAME}/backup/cb"

rm -r "/home/${USERNAME}/backup/ib"

rm -r ${BACKUP_DIR}/backup.log

# Creating backup directory and backup logs
mkdir "/home/${USERNAME}/backup/cb"

mkdir "/home/${USERNAME}/backup/ib"


# Create a timestamp for the backup log
TIMESTAMP=$(date +"%a %d %b %Y %I:%M:%S %p")

# Incremental variables
INCR_NUM=10001
CMP_NUM=1

count=1


# Complete Backup Function
create_complete_backup(){
    
    # Create a timestamp for the backup log
    TIMESTAMP=$(date +"%a %d %b %Y %I:%M:%S %p")

    # Complete backup Directory
    COMP_BACKUP_DIR="${BACKUP_DIR}/cb"

    # Creating the tar file name with the specified format
    TAR_FILENAME="cb0000${CMP_NUM}.tar"

    # Log the backup information to backup.log
    echo "${TIMESTAMP} EDT ${TAR_FILENAME}" >> "${BACKUP_DIR}/backup.log"

    #echo "Backup file : find "/home/${USERNAME}" -type f \( -name "*.c" -o -name "*.txt" \)"

    # Find all .C and .txt files in the /home/username directory tree
    find "/home/${USERNAME}" -type f \( -name "*.c" -o -name "*.txt" \) -exec tar -rf "${COMP_BACKUP_DIR}/${TAR_FILENAME}" {} +

    echo "Backup completed successfully!"

    # Incrementing the file Number
    ((CMP_NUM=CMP_NUM+1))
}


# Function to create incremental backup
create_incremental_backup() {

    # Create a timestamp for the backup log
    TIMESTAMP=$(date +"%a %d %b %Y %I:%M:%S %p")

    # Incremental backup Directory
    INCREMENTAL_DIR="${BACKUP_DIR}/ib"

    # Creating the incremental tar file name with the specified format
    INCREMENTAL_FILENAME="ib${INCR_NUM}.tar"

    # Log the backup information to backup.log
    echo "${TIMESTAMP} EDT ${INCREMENTAL_FILENAME}" >> "${BACKUP_DIR}/backup.log"

    #echo "modified or created files : find "/home/${USERNAME}" -type f \( -name "*.c" -o -name "*.txt" \) -mmin -2"

    # Find all .C and .txt files modified in the last 2 minutes
    find "/home/${USERNAME}" -type f \( -name "*.c" -o -name "*.txt" \) -mmin -2 -exec tar -rf "${INCREMENTAL_DIR}/${INCREMENTAL_FILENAME}" {} +
    
    echo "Incremental backup completed successfully!"

    # Incrementing the file Number
    ((INCR_NUM=INCR_NUM+1))
}


# Stores previous Logs
PREVIOUS_LOG=" "

while true
do

LATEST_LOG=$(tail -n 1 "${BACKUP_DIR}/backup.log" 2>/dev/null)
LOG=$(echo ${LATEST_LOG} | cut -d ' ' -f 8)

if [ "$LOG" = "No" ]; then
    LAST_COMPLETE_BACKUP="${PREVIOUS_LOG}"
else
    LAST_COMPLETE_BACKUP="${LOG}"
    PREVIOUS_LOG="${LOG}"
fi

if [ $count -eq 5 ];then
    LAST_COMPLETE_BACKUP=''
    count=1
fi

if [ -n "${LAST_COMPLETE_BACKUP}" ]; then

    # Check if there are any modified files since the last complete backup
    find "/home/${USERNAME}" -type f \( -name "*.c" -o -name "*.txt" \) -newer "${BACKUP_DIR}/${LAST_COMPLETE_BACKUP:0:2}/${LAST_COMPLETE_BACKUP}" -print -quit | grep -q .
    
    if [ $? -eq 0 ]; then
        # Incremental backup needed
        create_incremental_backup
    else
        # No new or modified files
        TIMESTAMP=$(date +"%a %d %b %Y %I:%M:%S %p")
        echo "No changes-Incremental backup was created"
        echo "${TIMESTAMP} EDT No changes-Incremental backup was created" >> "${BACKUP_DIR}/backup.log"
    fi
else
    # Takes complete Backup
    create_complete_backup
fi
#sleeps for 2 minute
sleep 120
((count=count+1))
done