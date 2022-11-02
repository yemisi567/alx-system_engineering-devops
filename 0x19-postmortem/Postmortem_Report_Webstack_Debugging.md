                                         My first postmortem

Summary

Anyone attempting to access a website received a 504 error on September 20, 2020, at midnight i.e 12AM - 1:05 AM due to a server access issue and this was caused by web server didn't receive a timely response from another server upstream when it attempted to load one of the web pages.

TimeLine
. 00:00 WAT Anyone attempting to access the website receives a 500 error
. 00:05 WAT Ensuring that Apache and MySQL are operational.
. 00:12 WAT An investigation into the website's loading issues discovered that the database and server were both functioning properly.
. 00: 20 WAT After short restart to Apache server provided a result of 200 and OK while trying to curl the website.
. 00:30 WAT Checking error logs for potential sources of the error
. 00:40 WAT To see if the Apache server was abruptly shut down, look in /var/log. There was no sign of the PHP error log.
. 00:54 WAT Changing the filename and restarting Apache.
. 01:05 WAT The website is currently loaded properly and the server is operating regularly.

Root cause and Resolution
The problem was caused by the wp-settings.php file referencing the incorrect file name. The server responded with a 500 error code when the user attempted to curl it. Checking the error logs revealed that no error log file was being written for the PHP failures, and reading the normal Apache error log did not reveal much about the server's early shutdown. The engineer decided to investigate the php.ini file's error log option after realizing that the php log errors were not being directed anywhere, and discovered that all error logging was disabled.Once enabled, the Apache server's error logging was restarted to see if any issues were being recorded in the log. The php log confirmed what was already suspected: a file with a.phpp extension was not there in the wp-settings.php file. It is obvious that the site access error was caused by a typographical error. Since the problem was discovered on just one server, it's possible that it also occurred on other servers. The issue might be resolved quickly by using puppet to change the file extension, which would also affect other servers. When the server was restarted, a short deployment of the puppet code correctly loaded all incorrectly typed file extensions.

Corrective and preventative measures
Error logging should be enabled on all servers and websites to make it simple to find faults when something goes wrong.
Before deploying on a multi-server setup, all servers and sites should be tested locally. This will allow issues to be fixed before coming live and reduce the amount of time needed to fix a downed site.

;
