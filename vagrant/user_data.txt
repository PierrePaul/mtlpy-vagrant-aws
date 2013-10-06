#!/bin/bash
sudo apt-get update
sudo apt-get install nginx -y
sudo rm /etc/nginx/sites-enabled/default
sudo ln -s /vagrant/nginx-default /etc/nginx/sites-enabled/default
sudo service nginx start
