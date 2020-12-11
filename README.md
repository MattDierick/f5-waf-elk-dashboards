# Kibana dashboards for F5 App Protect WAF
## Screenshots
### Overview Dashboard
![screenshot1](https://user-images.githubusercontent.com/23067500/72393114-c7c25080-36e6-11ea-81c4-655f4c936476.png)
![screenshot2](https://user-images.githubusercontent.com/23067500/72392972-4cf93580-36e6-11ea-8392-1b80d59b8276.png)
![screenshot3](https://user-images.githubusercontent.com/23067500/72392979-4ff42600-36e6-11ea-9cb9-22b8ba737de0.png)
### False Positives Dashboard
![screenshot1](https://user-images.githubusercontent.com/23067500/78085351-8ea58e00-736f-11ea-8cfd-7fed4e29d6dc.png)
![screenshot2](https://user-images.githubusercontent.com/23067500/78085354-92391500-736f-11ea-94ee-c22944ddda33.png)
![screenshot3](https://user-images.githubusercontent.com/23067500/78085359-95cc9c00-736f-11ea-8cab-7dcf0bc52727.png)


Docker

     - Uninstall old versions

           yum remove docker \

                  docker-client \

                  docker-client-latest \

                  docker-common \

                  docker-latest \

                  docker-latest-logrotate \

                  docker-logrotate \

                  docker-engine

    - install docker engine

        yum install -y yum-utils

        yum install docker-ce docker-ce-cli containerd.io

        systemctl start docker

        docker run hello-world

 

Docker compose

           curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

           chmod +x /usr/local/bin/docker-compose

           docker-compose --version

 

Clone repo

     git clone https://github.com/MattDierick/f5-waf-elk-dashboards.git

 

ISSUE "fw"

     firewall-cmd --zone=public --add-port=9200/tcp

     firewall-cmd --zone=public --add-port=9200/tcp --permanent

 

ISSUE "memmory"

     vi /etc/sysctl.conf

          vm.max_map_count=262144

     sysctl -p

 

ISSUE "SE LINUX"

     setenforce 0

 

Launch

     cd f5-waf-elk-dashboards

     vi logstash/conf.d/30-waf-logs-full-logstash.conf

           hosts => ['localhost:9200']

     docker-compose up -d

 

Services:

     - log: 5144

     - GUI: 5601

 

Import dashboard

     - Management > Saved Objects > Import *.ndjson
     - Or via API call
     
     KIBANA_URL=http://10.1.1.8:5601
     jq -s . kibana/overview-dashboard.ndjson | jq '{"objects": . }' | \
        curl -k --location --request POST "$KIBANA_URL/api/kibana/dashboards/import" \
        --header 'kbn-xsrf: true' \
        --header 'Content-Type: text/plain' -d @- \
        | jq

     jq -s . kibana/false-positives-dashboard.ndjson | jq '{"objects": . }' | \
        curl -k --location --request POST "$KIBANA_URL/api/kibana/dashboards/import" \
        --header 'kbn-xsrf: true' \
        --header 'Content-Type: text/plain' -d @- \
        | jq
        
     jq -s . kibana/cryoptonice-dashboard.ndjson | jq '{"objects": . }' | \
        curl -k --location --request POST "$KIBANA_URL/api/kibana/dashboards/import" \
        --header 'kbn-xsrf: true' \
        --header 'Content-Type: text/plain' -d @- \
        | jq
 

source:

     - https://github.com/MattDierick/f5-waf-elk-dashboards

     - https://github.com/ansible/awx/blob/devel/INSTALL.md#docker-compose

     - https://docs.docker.com/compose/install/

 

TROUBLESHOOTING

     DEBUG tcpdump

           $ docker build -t tcpdump - <<EOF

           FROM ubuntu

           RUN apt-get update && apt-get install -y tcpdump

           CMD tcpdump -i eth0

           EOF

 

           $ docker run -it --net=container:925021d0f1ee tcpdump tcpdump -X port 5144

 

     DEBUG container

           $ docker exec -it 925021d0f1ee bash
