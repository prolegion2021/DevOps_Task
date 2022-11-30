# Linux/Bash
# A. Create a script that uses the following keys:
### 1.  When starting without parameters, it will display a list of possible keys and their description.
![1](1_Linux_Bash/screenshots/1.png)
```Bash
#!/bin/bash
function all {  
                echo Please wait some time
                address=`ip a | grep inet | fgrep -v inet6 | fgrep -v 127.0.0.1 | awk '{print $2}'`
                for i in $address
                do
                        MASK=${i##*/}
                        NETWORK=${i%.*}
                        RESULT=$NETWORK".0/"$MASK
                        nmap -sP -PR $RESULT | awk '{print $5 $6}' | fgrep -v latency | fgrep -v address
                done
}

function target() {
                echo At your machine next ports are opened:
                netstat -lntu | egrep tcp | egrep -v tcp6 | awk '{print $4}' | awk -F ':' '{print $2}'
}

function anything {
                echo You must use options \"--all\" or \"--target\"
}
case $1 in
        --all)  
                all
                ;;
        --target)
                target
                ;;
        *)
                anything
                ;;
esac

```
### 2. The --all key displays the IP addresses and symbolic names of all hosts in the current subnet
![2](1_Linux_Bash/screenshots/2.png)
```Bash
#!/bin/bash
echo From which ip were the most requests?
cat apache_logs.txt | awk '{print $1}' | sort | uniq -c | sort -rnk1 | head -1

echo What is the most requested page?
cat apache_logs.txt | awk '{print $7}' | sort | uniq -c | sort -rnk1 | head -3

echo How many requests were there from each ip?
cat apache_logs.txt | awk '{print $1}' | sort | uniq -c | sort -rnk1

echo What non-existent pages were clients referred to?
egrep 'error404' apache_logs.txt | awk '{print $7}' | sort | uniq -c | sort -rnk1

echo What time did site get the most requests?
cat apache_logs.txt | awk '{print $4}' | awk -F '/' '{print $3}' | awk '{print $1}' |  sort | uniq -cd | sort -rnk1 | head -5

echo What search bots have accessed the site \(UA + IP\)?

echo bingbot
cat apache_logs.txt | egrep bingbot | awk '{print $1}' | sort | uniq -d

```
### 3. The --target key displays a list of open system TCP ports.
![3](1_Linux_Bash/screenshots/3_1.png)
![4](1_Linux_Bash/screenshots/3_2.png)
```Bash
#!/bin/bash

#OPTIONS

# Check if the input folders exists.
checkFolder() {
    if [ -d "$1" ]; then
        echo "$2 directory: $1"
    elif [[ "$2" = "Destination" && ! -d "$1" ]]; then
            mkdir -p "$1"
            echo "Directory '$1' has been created"
    else
        echo "No such dirrectory: '$1'"
        exit
    fi
}

# Backup data and save results and errors to the log file.
backupFunction() {
    if [[ -f "$3" || -f "$4" ]]; then
        touch "$3" "$4"
    fi
    rsync -a --delete --out-format='%t %o %n' $1 $2 1>>"$3" 2>>"$4"
}    

optionHelp() {
cat <<EOF
Usage: backup [SOURCE DIRECTORY] [DESTINATION DIRECTORY]
    SOURCE DIRECTORY - ex.: /home/
        Full path to the directory backup of which will be created.
    DESTINATION DIRECTIORY - ex.: /backup/
        Full path to the directory to which backup will be saved of SOURCE DIRECTORY.
        If DESTINATION DIRECTORY doesn't exist, script will create this onу automatically.
EOF
}

#MAIN

if [[ -z "$1" || -z "$2" ]]; then
    optionHelp
elif [ "$1" = --help ]; then
    optionHelp
else
    SOURCE_DIR="$1"
    checkFolder "$SOURCE_DIR" "Source"

    DEST_DIR="$2"
    checkFolder "$DEST_DIR" "Destination"

    LOG_FILE="backup.log"
    ERR_LOG="error.log"

    backupFunction "$SOURCE_DIR" "$DEST_DIR" "$LOG_FILE" "$ERR_LOG"
fi

```
