# Turn any command into a System V service

Instead of scheduling commands in cron or starting them in scripts just add this to the /etc/init.d folder and start from the terminal.

## Requirements

Linux and System V

    To check if your distribution uses System V, open a terminal and type:

    $ service --status-all

## Setup

Copy the script to /etc/init.d/.

    Make sure it's executable:

    $ sudo chmod +x /etc/init.d/custom_service

    Start the service:

    $ sudo service custom-service start

    Stop

    $ sudo service custom-service stop

    The service and script can be completely deleted

    $ sudo service custom-service uninstall

## Details

The script contains three example commands.

They are contained in an infinite while loop.

All output is sent to the $LOGFILE (/var/log/custom-service.log)

COMMAND='while true; do [[ $(date +"%H") > 20 ]] && echo $(uptime) $(free -m | grep cache:); [[ $(date +"%H.%M") < 01.29 ]] && echo $(uptime) $(free -m | grep cache:); [[ $(date +"%H.%M") = 01.26 && -f '$LOGFILE' ]] && rm -f '$LOGFILE'; sleep 10; done'

If the hour is greater than 20; echo cpu and memory information.
If the hour.minute is less than 1.29; echo cpu and memory.
If the hour.minute is equal to 1.26 AND the log file exists; remove the log file.
The while loop sleeps for 10 seconds before evaluating again.

COMMAND='while true; do [[ $(date +"%H") > 20 || $(date +"%H") < 08 ]] && echo $(uptime) $(free -m | grep cache:); [[ $(date +"%H") = 08 && -s '$LOGFILE' ]] && cp '$LOGFILE' /tmp/cpu-mem.csv && cat /dev/null > '$LOGFILE'; [[ $(date +"%H") < 20 || $(date +"%H") > 08 ]] && sleep 3000; sleep 60; done'

If the hour is greater than 20 OR less than 8; echo cpu and memory.
If the hour is equal to 8 AND the log file has a size greater than 0; copy it to /tmp/ and delete the contents of the file.
If the hour is less than 20 OR greater than 8; sleep for 3000 seconds.

COMMAND='while true; do echo $(uptime) $(free -m | grep cache:); [[ $(date +"%H") = 08 && -s '$LOGFILE' ]] && cp '$LOGFILE' /tmp/cpu-mem.csv && cat /dev/null > '$LOGFILE'; sleep 150; done'

echo cpu and memory every 150 seconds.
If the hour is 8 AND the log file contains anything; copy and empty it.
