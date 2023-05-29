From this brilliant blog post... https://blog.josephscott.org/2011/10/14/timing-details-with-curl/

cURL supports formatted output for the details of the request (see the cURL manpage for details, under -w, –write-out <format>). For our purposes we’ll focus just on the timing details that are provided. Times below are in seconds.

Create a new file, curl-format.txt, and paste in:

     time_namelookup:  %{time_namelookup}s\n
        time_connect:  %{time_connect}s\n
     time_appconnect:  %{time_appconnect}s\n
    time_pretransfer:  %{time_pretransfer}s\n
       time_redirect:  %{time_redirect}s\n
  time_starttransfer:  %{time_starttransfer}s\n
                     ----------\n
          time_total:  %{time_total}s\n
Make a request:

 curl -w "@curl-format.txt" -o /dev/null -s "http://wordpress.com/"
Or on Windows, it's...

 curl -w "@curl-format.txt" -o NUL -s "http://wordpress.com/"

What this does:
-w "@curl-format.txt" tells cURL to use our format file
-o /dev/null redirects the output of the request to /dev/null
-s tells cURL not to show a progress meter
"http://wordpress.com/" is the URL we are requesting. Use quotes particularly if your URL has "&" query string parameters


And here is what you get back:
   time_namelookup:  0.001s
      time_connect:  0.037s
   time_appconnect:  0.000s
  time_pretransfer:  0.037s
     time_redirect:  0.000s
time_starttransfer:  0.092s
                   ----------
        time_total:  0.164s
I have not yet seen an option to output the results in microseconds, but if you're aware of one, post in the comments below.


Make a Linux/Mac shortcut (alias)
alias curltime="curl -w \"@$HOME/.curl-format.txt\" -o /dev/null -s "
Then you can simply call...

curltime wordpress.org
Thanks to commenter Pete Doyle!


Make a Linux/Mac stand-alone script
This script does not require a separate .txt file to contain the formatting.

Create a new file, curltime, somewhere in your executable path, and paste in:

#!/bin/bash

curl -w @- -o /dev/null -s "$@" <<'EOF'
    time_namelookup:  %{time_namelookup}\n
       time_connect:  %{time_connect}\n
    time_appconnect:  %{time_appconnect}\n
   time_pretransfer:  %{time_pretransfer}\n
      time_redirect:  %{time_redirect}\n
 time_starttransfer:  %{time_starttransfer}\n
                    ----------\n
         time_total:  %{time_total}\n
EOF
Then call it the same way as the alias:

curltime wordpress.org

Make a Windows shortcut (aka BAT file)
Create a new text file called curltime.bat in the same folder as curl.exe and curl-format.txt, and paste in the following line:

curl -w "@%~dp0curl-format.txt" -o NUL -s %*
Then from the command line you can simply call:

curltime wordpress.org
(Make sure the folder is listed in your Windows PATH variable to be able to use the command from any folder.)
