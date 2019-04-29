# Sensu-installtaion-Ubuntu-
Sensu-iinstallations 


ubuntu V.16

Install the GPG public key

1).... wget -q http://sensu.global.ssl.fastly.net/apt/pubkey.gpg -O- | sudo apt-key add -

2)...  echo "deb http://sensu.global.ssl.fastly.net/apt sensu main" | sudo tee /etc/apt/sources.list.d/sensu.list

3)...  sudo apt-get update

Install sensu

4)... sudo apt-get install sensu

5)... (configuration files) /etc/sensu/conf.d/client.json
        
            {
                  "client": {
                    "name": "sensu-server",
                    "address": "127.0.0.1",
                    "environment": "sensu",
                    "subscriptions": [ "linux"],
                    "keepalive":
                    {
                   "handler": "mailer",
                    "thresholds": {
                    "warning": 250,
                    "critical": 300
                  }
                    },
            "socket": {
                  "bind": "127.0.0.1",
                  "port": 3030
                    }
                  }
            }


6)...  (configuration files) /etc/sensu/conf.d/transport.json

                {
                "transport": 
                {
                "name": "rabbitmq",
                "reconnect_on_error": true
                }
                }     

7)...    (configuration files)  /etc/sensu/conf.d/api.json

                {
                     "api": 
                {
                       "host": "localhost",
                "bind": "0.0.0.0",
                     "port": 4567
                     }
                }

Install Redis

8)...    sudo apt-get -y install redis-server


9)...    (configuration files) /etc/sensu/conf.d/redis.json

                {
                "redis": 
                {
                       "host": "127.0.0.1",
                       "port": 6379
                    }
                       }

Install Erlang

10)...    (configuration files)    
                
            sudo wget http://packages.erlang-solutions.com/erlang-solutions_1.0_all.deb
            wget   http://packages.erlang-solutions.com/site/esl/esl-erlang/FLAVOUR_1_general/esl-erlang_20.3.6-1~ubuntu~trusty_amd64.deb   
            apt-get update
            sudo apt-get -y install erlang-nox

Install RabbitMQ

11)...    http://www.rabbitmq.com/releases/rabbitmq-server/v3.6.0/rabbitmq-server_3.6.0-1_all.deb

12)...    sudo dpkg -i rabbitmq-server_3.6.0-1_all.deb

13)...    (configuration files)     /etc/sensu/conf.d/rabbitmq.json

            
                {
                      "rabbitmq": 
                {
                        "host": "127.0.0.1",
                        "port": 5672,
                        "vhost": "/sensu",
                        "user": "sensu",
                        "password": "secret"
                        }
                }

14)...    Start the services

            service sensu-server start 
            service sensu-client start
            service rabbitmq-server start
            service redis-server start    
            service sensu-api start

15)...    sudo rabbitmqctl add_vhost /sensu


16)...    sudo rabbitmqctl add_user sensu secret sudo rabbitmqctl set_permissions -p /sensu sensu ".*" ".*" ".*"


17)...    Install Uchiwa

            sudo apt-get install uchiwa
            
18)...    (configuration files)  Replace the existing uchiwa.json config file as  (/etc/sensu/uchiwa.json)

            {
                  "sensu": [
                    {
              "name": "sensu",
              "host": "localhost",
              "port": 4567,
              "timeout": 10
                }
              ],
              "uchiwa": {
                  "host": "0.0.0.0",
              "port": 8080,
              "refresh": 10
              }
          }

19)...    Start the service
            sudo service uchiwa start

20)...    Install Sensu Plugins In the directory mentioned below

        Directory : cd /opt/sensu/embedded/bin/
            sensu-install -p cpu-checks  
            sensu-install -p disk-checks  
            sensu-install -p memory-checks  
            sensu-install -p nginx  
            sensu-install -p process-checks  
            sensu-install -p load-checks  
            sensu-install -p vmstats  
            sensu-install -p mailer

21)...    (configuration files)  
                /etc/sensu/conf.d/check_cpu_linux.json

                {
                      "checks": {
                     "check-cpu-linux": {
                   "handlers": ["mailer"],
                   "command": "/opt/sensu/embedded/bin/check-cpu.rb -w 80 -c 90 ",
                   "interval": 60,
                   "occurrences": 5,
                  "subscribers": [ "linux" ]
                     }
                     }
              }

22)...    (configuration files)
                /etc/sensu/conf.d/check_memory_linux.json

                {
                      "checks": {
                          "check_memory_linux": {
                     "handlers": ["mailer"],
                        "command": "/opt/sensu/embedded/bin/check-memory-percent.rb -w 90 -c 95",
                    "interval": 60,
                    "occurrences": 5,
                     "subscribers": [ "linux" ]
                    }
                    }
                }

23)...    (configuration files)
                /etc/sensu/conf.d/check_disk_usage_linux.json

                    {
                         "checks": {
                        "check-disk-usage-linux": {
                    "handlers": ["mailer"],
                        "command": "/opt/sensu/embedded/bin/check-disk-usage.rb -w 80 -c 90",
                        "interval": 60,
                        "occurrences": 5,
                            "subscribers": [ "linux" ]
                        }
                        }
                        }

24)...    Enable alerts

            apt-get install postfix

    (configuration files)

                /etc/sensu/conf.d/ handler_mail.json

                {
                         "handlers": {
                                 "mailer": {
                       "type": "pipe",
                      "command": "/opt/sensu/embedded/bin/handler-mailer.rb"
                        }
                         }
                   }






Sensu Installtaion on ubuntu 

Sensu Master Installation

Install the GPG public key

wget -q http://sensu.global.ssl.fastly.net/apt/pubkey.gpg -O- | sudo apt-key add -

*Create an APT configuration file at /etc/apt/sources.list.d/sensu.list

echo "deb http://sensu.global.ssl.fastly.net/apt sensu main" | sudo tee
/etc/apt/sources.list.d/sensu.list

Update APT and Install Sensu

sudo apt-get update && sudo apt-get install sensu

*Client Configuration Copy the following contents to a configuration file located at
/etc/sensu/conf.d/client.json

{
"client": {
"name": "sensu-server",
"address": "127.0.0.1",
"environment": "sensu",
"subscriptions": [ "linux"],
"keepalive":
{
"handler": "mailer",
"thresholds": {
"warning": 250,
"critical": 300

}
},
"socket": {
"bind": "127.0.0.1",
"port": 3030
}
}
}

*Copy the following contents to a configuration file located at
/etc/sensu/conf.d/transport.json

{
"transport":
{
"name": "rabbitmq",
"reconnect_on_error": true
}
}

*Copy the following contents to an api configuration file located at
/etc/sensu/conf.d/api.json

{
"api":
{
"host": "localhost",
"bind": "0.0.0.0",
"port": 4567
}
}

Install Redis

Install Redis using APT

sudo apt-get -y install redis-server

*Copy the following contents to a configuration file located at /etc/sensu/conf.d/redis.json

{
"redis":
{
"host": "127.0.0.1",
"port": 6379
}
}
sudo apt-get update
sudo apt-get upgrade

Install Erlang
wget https://packages.erlang-solutions.com/erlang-solutions_1.0_all.deb
sudo dpkg -i erlang-solutions_1.0_all.deb
sudo apt-get update
sudo apt-get install esl-erlang
Install RabbitMQ
echo "deb https://dl.bintray.com/rabbitmq/debian xenial main" | sudo tee
/etc/apt/sources.list.d/bintray.rabbitmq.list
wget -O- https://www.rabbitmq.com/rabbitmq-release-signing-key.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get install rabbitmq-server

sudo systemctl start rabbitmq-server.service
sudo systemctl enable rabbitmq-server.service
*Copy the following contents to a configuration file located at
/etc/sensu/conf.d/rabbitmq.json

{
"rabbitmq":
{
"host": "127.0.0.1",
"port": 5672,
"vhost": "/sensu",
"user": "sensu",
"password": "secret"
}
}

*Start the services

service sensu-server start && service sensu-client start && service rabbitmq-server start
&& service redis-server start && service sensu-api start

*Create a dedicated RabbitMQ vhost for Sensu

sudo rabbitmqctl add_vhost /sensu

Create a RabbitMQ user for Sensu

sudo rabbitmqctl add_user sensu secret
sudo rabbitmqctl set_permissions -p /sensu sensu ".*" ".*" ".*"

Install Uchiwa

Install Uchiwa dashboard

sudo apt-get install uchiwa

*Replace the configuration in
/etc/sensu/uchiwa.json

{
"sensu": [
{
"name": "sensu",
"host": "localhost",
"port": 4567,
"timeout": 10
}
],
"uchiwa": {
"host": "0.0.0.0",
"port": 8080,
"refresh": 10
}
}

*Start the service

sudo service uchiwa start

Install Sensu Plugins

*Go to the following location and run the commands to install sensu plugins.
cd /opt/sensu/embedded/bin/
sensu-install -p cpu-checks
sensu-install -p disk-checks
sensu-install -p memory-checks
sensu-install -p nginx
sensu-install -p process-checks
sensu-install -p load-checks
sensu-install -p vmstats
sensu-install -p mailer

*Copy the following contents to a CPU check configuration file located at
/etc/sensu/conf.d/check_cpu_linux.json

{
"checks": {
"check-cpu-linux": {
"handlers": ["mailer"],
"command": "/opt/sensu/embedded/bin/check-cpu.rb -w 80 -c 90 ",
"interval": 60,
"occurrences": 5,
"subscribers": [ "linux" ]
}
}
}

*Copy the following contents to a memory check configuration file located at
/etc/sensu/conf.d/check_memory_linux.json

{
"checks": {

"check_memory_linux": {
"handlers": ["mailer"],
"command": "/opt/sensu/embedded/bin/check-memory-percent.rb -w 90 -c 95",
"interval": 60,
"occurrences": 5,
"subscribers": [ "linux" ]
}
}
}

*Copy the following contents to a disk check configuration file located at
/etc/sensu/conf.d/check_disk_usage_linux.json

{
"checks": {
"check-disk-usage-linux": {
"handlers": ["mailer"],
"command": "/opt/sensu/embedded/bin/check-disk-usage.rb -w 80 -c 90",
"interval": 60,
"occurrences": 5,
"subscribers": [ "linux" ]
}
}
}

*Enable alerts

*Install postfix (MTA) to send alert mails

apt-get install postfix

*Copy the following contents to a pipe handler configuration file located at
/etc/sensu/conf.d/ handler_mail.json

{
"handlers": {
"mailer": {
"type": "pipe",
"command": "/opt/sensu/embedded/bin/handler-mailer.rb"
}
}
}

*Copy the following contents to a mailer configuration file located at
/etc/sensu/conf.d/mailer.json

{
"mailer": {
"admin_gui": "http://yoursensuserverip:8080/",
"mail_from": "alerts@powerupcloud.com",
"mail_to": "alerts@powerupcloud.com",
"smtp_address": "localhost",
"smtp_port": "25",
"smtp_domain": "localhost"
}
}

*Restart the sensu server to update the configurations

service sensu-server restart

*Enable sensu services to start automatically

sudo update-rc.d sensu-server defaults
sudo update-rc.d sensu-client defaults
sudo update-rc.d sensu-api defaults
sudo update-rc.d uchiwa defaults

*At this point, you can access Sensu dashboard at http://sensuserver-ip-address:8080

