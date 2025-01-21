
## Step 1 –  INSTALL GO LANG. ##

```bash
apt install golang-go -y
```

- Once the Go package is installed, you can verify its version with the following command:

```bash
go version
```

Output:
go version go1.18.1 linux/amd64

## Step 2 – Create a Simple Go Application ##
### In this section, we will create a Go application that will print “Hello Go Application” when accessing the domain. ###

- First, create a project directory for your application.
```bash
mkdir project
```
- Next, navigate to the project directory and initiate the project with the following command:
```bash
cd project
```
```bash
go mod init go.example.com/app
```
- Next, create a Go application file.
```bash
nano main.go
```
- Add the following code:
```
package main

import (
    "fmt"
    "net/http"
)

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello Go Application")
    })

    http.HandleFunc("/greet/", func(w http.ResponseWriter, r *http.Request) {
        name:= r.URL.Path[len("/greet/"):]
            fmt.Fprintf(w, "Hello %s\n", name)
    })

    http.ListenAndServe(":9990", nil)
}
```
- Save and close the file, then compile the file into the executable binary file.
```bash
go build main.go
```
- You can see the executable binary file using the following command:
```bash
ls -l
```
Output:
```
total 6152
-rw-r--r-- 1 root root      35 Oct 15 15:43 go.mod
-rwxr-xr-x 1 root root 6288896 Oct 15 15:44 main
-rw-r--r-- 1 root root     408 Oct 15 15:44 main.go
```
## Step 3 – Create a Systemd Service File for Your Application ##
### Next, you will need to create a systemd file to manage the Go application. ###
```bash
nano /lib/systemd/system/goweb.service
```
- Add the following configurations:

```
[Unit]
Description=GoWeb App

[Service]
Group=lazaqy
Type=simple
Restart=always
RestartSec=5s
WorkingDirectory=/var/www/pajak/
ExecStart=env HTTP_PORT=":5000" /var/www/<project name>/main
PermissionsStartOnly=true
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=sleepservice

[Install]
WantedBy=multi-user.target
```
- Save and close the file, then reload the systemd daemon to apply the changes.
```bash
systemctl daemon-reload
```
- Next, start and enable the Go service using the following command:
```bash
systemctl start goweb
systemctl enable goweb
```
- You can check the status of the Go service with the following command:
```bash
systemctl status goweb
```
Output:
```
● goweb.service - goweb
     Loaded: loaded (/lib/systemd/system/goweb.service; disabled; vendor preset: enabled)
     Active: active (running) since Sun 2023-10-15 15:45:37 IST; 3s ago
   Main PID: 47830 (main)
      Tasks: 6 (limit: 9180)
     Memory: 1.1M
        CPU: 6ms
     CGroup: /system.slice/goweb.service
             └─47830 /root/project/main

Oct 15 15:45:37 ubuntupc systemd[1]: Started goweb.
```

## Step 4 – Configure Nginx as a Reverse Proxy ##
### Next, you will need to install and configure Nginx as a reverse proxy for the Go application. First, install the Nginx package with the following command: ###
```bash
apt install nginx
```
- Next, create an Nginx virtual host configuration file.
```bash
nano /etc/nginx/conf.d/go.conf
```
- Add the following configuration:
```
server {
    server_name go.example.com;
    location / {
        proxy_pass http://localhost:9990;
    }
}
```
- Save and close the file, then verify the Nginx for any syntax errors.
```bash
nginx -t
```
Output:
```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
- Then, restart the Nginx service to reload the changes.
```bash
systemctl restart nginx
```

## Step 5 – Access Go Application ##
### Now, open your web browser and access the Go application using the URL http://go.example.com. You will see the following screen. ###

- Access go web page

- You can also greet with a specific keyword using the URL http://go.example.com:/greet/atlantic. You will see the following screen.
