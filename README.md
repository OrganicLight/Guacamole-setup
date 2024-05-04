# Guacamole-setup
Setting up guacamole server with cloudflare and docker
Setting up Guacamole with Docker and Cloudflare.
Use your ubuntu machine from anyware via the browser.

First. Check out this repo :
https://github.com/boschkundendienst/guacamole-docker-compose/tree/master

Following this repo I had a few issues, so we made a few adaptations.

1. Download the prepare.sh and generate the (/initdb.sql) or clone it from this rep.
2. Download the docker-compose.yml file, wich is slightli modifide version of the repo.
3. You need docker and docker compose so make use u wave it installed.
4. Compose the yml file.

        -- docker-compose up -d 
        
5. Set up the database.

        -- sudo docker exec -it postgres_guacamole_compose psql postgresql://guacamole_user:ChooseYourOwnPasswordHere1234@localhost/guacamole_db
        # dont worry about the password and the account, we dont need these.
        # now that you started the shell just copy the content of the db_file to the shell.
        
6. Set up your domain with cloudflare tunnels using localhost as urls.
7. Download and Install the Cloudflare service wih yout API key.

        -- curl -L --output cloudflared.deb https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb && /
        sudo dpkg -i cloudflared.deb && sudo cloudflared service install 
        
8. Allow port for guacamole.

        -- ufw allow 8080
        
9. Check out if your server is running at localhost:8080/guacamole or domain/guacamole and use the original acc and pssw "guacadmin".
10. Install x11vnc on your machine.

                    sudo apt-get install x11vnc 

                    x11vnc -storepasswd 

                    sudo nano /etc/systemd/system/x11vnc.service 

                    [Unit]
                    Description=Start x11vnc at startup.
                    After=multi-user.target

                    [Service]
                    Type=simple
                    Environment="DISPLAY=:0"
                    #Environment="XAUTHORITY=/var/run/user/121/gdm/Xauthority"
                    ExecStart=/usr/bin/x11vnc -auth /run/lightdm/root/:0 -forever -loop -noxdamage -repeat -ncache_cr  -rfbauth /etc/x11vnc.passwd -rfbport 5900 -shared
                    Restart=always

                    [Install]
                    WantedBy=multi-user.target
                    
                    sudo systemctl daemon-reload 
                    sudo systemctl enable x11vnc.service 
                    sudo systemctl start x11vnc.service 

12. Allow port for VNC

                    ufw allow 5900    

13. Settup a connection in the guacamole panel:

          setting -> connection -> new
          protocol -> VNC
          network
            hostname -> host.docker.internal 
            port -> 5900
          auth
            user -> your user
            pssw -> your vnc password
    save and your are ready to go.
    # make sure you use the x11 graphic contructor. Maibe logout and log is with x11.     
