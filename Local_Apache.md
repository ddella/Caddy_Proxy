# Setting up a local web server on macOS 13 “Ventura” 
Here is a small guide to getting a local web server running on macOS 13 “Ventura”. This User Tip only contains instructions for configuring the Apache server with **no** scripting languages.

I assume you have:
1. Basic understanding of shell commands, *Terminal.app*, and how to run command-line programs.
2. Basic understanding of web servers.
3. Basic usage of *vi*. You can substitute nano if you want.

If this is not the case **STOP immediately**.

>**Note**: This user tip is specific to **macOS 13 “Ventura”** client NOT server. Pay attention to your OS version.
## Edit the Apache configuration file, as root:
```shell
sudo vi /etc/apache2/httpd.conf
```

## Enable personal websites by uncommenting the following, at line 184:
```shell
LoadModule userdir_module libexec/apache2/mod_userdir.so
```

## and do the same at line 521:
```shell
Include /private/etc/apache2/extra/httpd-userdir.conf
```
## Now save and quit:

## Open the configuration file for personal websites:
```shell
sudo vi /etc/apache2/extra/httpd-userdir.conf
```

## and uncomment the following, at line 16:
```shell
Include /private/etc/apache2/users/*.conf
```

## Save and exit.

## Create personal web sites, enter the following:
```shell
mkdir ~/Sites
echo "<html><body><h1>My personal Apache Web site works</h1></body></html>" > ~/Sites/index.html.en
```

## Open/Create the user config file: It should exist in: "/etc/apache2/users/<your short user name>.conf"
```shell
sudo vi /etc/apache2/users/<your short user name>.conf
```
Enter the following. Choose if you want ALL to have access to user's personal site, see the last lines:

    <Directory "/Users/<your short user name>/Sites/">
      AddLanguage en .en
      AddHandler cgi-script .cgi .pl .php
      Options Indexes MultiViews FollowSymLinks ExecCGI
      AllowOverride None
      # Next line allow access to the local Sites from "localhost" only
      # Require host localhost
      # Next line allow access to the local Sites from ALL
      Require all granted
    </Directory>

## Run the following command to give the Apache web server access to the Sites folder in your home directory.
```shell
chmod +a "_www allow execute" ~/Sites/
```

## Now you are ready to turn on Apache itself. But first, do a sanity check.
```shell
apachectl configtest
```

I got this message. It can be ignored.
    AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using MacBook-Dan.local. Set the 'ServerName' directive globally to suppress this message
    Syntax OK

## Turn on the Apache httpd service by running the following command:
```shell
sudo launchctl load -w /System/Library/LaunchDaemons/org.apache.httpd.plist
```

## Open your favorite browser and type this in the address bar:
```
http://localhost/
```

Output:
>It works!

## Now try your user home directory by entering the following into your browser's address bar:
```shell
http://localhost/~<your short user name>
```

Output:
>My personal Apache Web site works

## Usefull commands:
Restart the service:
```shell
sudo apachectl graceful
```

Stop the service:
```shell
sudo apachectl stop
```

## Credits
https://discussions.apple.com/docs/DOC-250006086
