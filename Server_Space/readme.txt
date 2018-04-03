The aim of the server space file is to check the free space on each of the baltic server, and spend this to individual files log files then to send a weekly summary of available space to the it team.

the script several useful bits of code

mkdir -p

-this makes the directories if they do not exist

if ! mount -t smbfs -o read-only -o username=administrator,password=f10urm111 //baltic1/C$ /server_mounts/baltic1-drive-c
then
        exit 2
fi

- this uses if statement mounts an smb file share for the server defining a name for the share, if the mount fails the script exits if not it continues

space=$(df -h | grep disk| awk '{print $4}')
echo "$space free on Baltic1 C drive" >>$FREESPACE.$date
echo "$space free on $date" >>$LOGFILE.Baltic1-Cdrive.txt

- this creates a variable space, it then reads the disk usage for the share into that variable and reads any line with the word disk in it from the disk usage, from that line awk reads the 4th value after the word disk in this case the free space on the disk in human readable format. Finally we echo the variable for disk space with some text and spend this info to the log file for the server, we also spend that data along with the current date to the individual log file for the server. 

in order to get to this version i tried

df -h | grep disk >log.txt

- which looked for any line with the word disk in it and wrote it to the log file

echo "disk1" df -h | grep disk >log2.txt

- which wrote some text and along with the resulting disk space to the file

df -h | grep disk| awk '{print $4}'>>log.tmp


- which just wrote the resulting free disk space to the file without the other disk info we weren't worried about

I settled on writing the output to a string and that including that string in the other text i echoed to the file

space=$(df -h | grep disk| awk '{print $4}')
echo "$space free space on disk" >>log2.txt

- this allowed the text and result to go into 1 line without having to output it to a temp file and read it back.

if ! umount /server_mounts/baltic1-drive-c
then
        exit 2
fi

- this unmounted the disk within an if statement that quit if it was unsuccessful.

echo "Individual log files can be found at $LOGFILES" >>$FREESPACE.$date

- this wrote a few final details to the end of the log file prior to emailing it.

mail -s "Free Space on servers" ict@balticmill <$FREESPACE.$date

- this wrote the content of the finished file to email and sent it to the it team.

additional development of the script might include adding more servers, reading back the previous disk space from the log file , checking recent disk usage and emailing warning when the disk becomes full or the there has been abnormal disk usage.


 


