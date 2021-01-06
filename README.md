# Ci-cd-virtualbox-test
test ci/cd in virtual box

# Lab CI/CD

install and test ci/cd in gitlab

## Installation
-Install 4 linux debian server

-Make host onliy virtual network (Vboxnet0)

-for all vm make 2 interface internet/Vboxnet0

-change hostname in 4 vm 
```
hostnamectl set-hostname NAME
```
- Lab1-local-repo 
- Lab1-gitlab
- Lab1-staging
- Lab1-production

install Docker in all vm
```
- download install-docker.sh
- chmod +x install-docker.sh 
- chmod 777 install-docker.sh 
```
install tor&privoxy in all vm
```
apt-get  install tor
 - change /etc/tor/torrc
  -SocksPort 9898
/etc/init.d/tor start 

apt-get  install privoxy

Change /etc/privoxy/config

        listen-address  127.0.0.1:9999

        forward-socks4   /               127.0.0.1:9898 .

        forward-socks5t   /               127.0.0.1:9898 .
```

add .bashrc 

```
alias whereiam='echo $(curl -s http://ip-api.com/json | jq -r ".country,.city")'
alias x="export http_proxy='127.0.0.1:9999'"
alias dx="export http_proxy=''"
```
press x in command line for send traffic to privoxy 

```
send in privoxy
root@local-repo:~# x
root@local-repo:~# whereiam 
Netherlands Amsterdam
```
send in default network
```
root@local-repo:~# dx
root@local-repo:~# whereiam 
Iran Tehran

```
- #docker local repo 
```
docker run -d -p 5000:5000 --restart=always --name registry registry:2
```
- #list images 
```docker images ```
- #docker add tag sample 
```docker tag ubuntu localhost:5000/my-ubuntu```
- #doker restart contaner
```docker container stop registry
docker container start  registry```
- #push change image to local repository
```docker push localhost:5000/my-ubuntu```
- #remove image from repository
```docker image remove localhost:5000/my-ubuntu```
- #test pull 
```docker pull localhost:5000/my-ubuntu ```
- #change damon.json
```root@gitlab:/etc/systemd# cat /etc/docker/damon.json 
{ "insecure-registries":["192.168.56.20:5000"] }```
- #Restart daemon& docker 
```systemctl daemon-reload
systemctl restart docker
```
#git lab server send push request 

root@gitlab:/etc/systemd# cat /etc/docker/damon.json 
{ "insecure-registries":["192.168.56.20:5000"] }

docker tag camunda/camunda-bpm-platform 192.168.56.20:5000/test-camunda
docker push 192.168.56.20:5000/test-camunda:latest

systemctl daemon-reload
systemctl restart docker

```bash
pip install foobar
```
## License
[MIT](https://choosealicense.com/licenses/mit/)
