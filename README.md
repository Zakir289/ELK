# kibana

### Install Logstash
echo 'deb http://packages.elasticsearch.org/logstash/1.5/debian stable main' | sudo tee /etc/apt/sources.list.d/logstash.list


Update your apt package database:
sudo apt-get update


Install Logstash with this command:
sudo apt-get install logstash


Configure Logstash

Logstash configuration files are in the JSON-format, and reside in /etc/logstash/conf.d. 
The configuration consists of three sections: inputs, filters, and outputs.
Let's create a configuration file called 02filebeat-input.conf 
sudo vi /etc/logstash/conf.d/02filebeat-input.conf
Insert the following input configuration:
input {
  file {
    path => "/home/zakir/Downloads/access.log"
    start_position => "beginning"
  }
}
Save and quit. 
/home/zakir/Downloads/access.log → we need to specify the path for the logs
Now let's create a configuration file called 10-syslog.conf, where we will add a filter for syslog messages:
sudo vi /etc/logstash/conf.d/10-syslog.conf
filter {
  if [path] =~ "access" {
    mutate { replace => { "type" => "apache_access" } }
    grok {
      match => { "message" => "%{COMBINEDAPACHELOG}" }
    }
  }
  date {
    match => [ "timestamp" , "dd/MMM/yyyy:HH:mm:ss Z" ]
  }
}

Save and quit. 
Lastly, we will create a configuration file called 30-elasticsearch-output.conf:
sudo vi /etc/logstash/conf.d/30-elasticsearch-output.conf


Insert the following output configuration:
output {
  elasticsearch {
    hosts => ["localhost:9200"]
  }
  stdout { codec => rubydebug }
}

Save and exit. This output basically configures Logstash to store the logs in Elasticsearch.
With this configuration, Logstash will also accept logs that do not match the filter
Restart Logstash to put our configuration changes into effect:
sudo service logstash restart


Now that our Logstash Server is ready


Installing Elasticsearch
Run the following command to import the Elasticsearch 
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add 
Update your apt package database:
sudo apt-get update
Create the Elasticsearch source list:
echo "deb http://packages.elastic.co/elasticsearch/1.7/debian stable main" | sudo tee -a /etc/apt/sources.list.d/elasticsearch-1.7.list


Install Elasticsearch with this command:
sudo apt-get -y install elasticsearch


Configure the elastic search:
sudo vi /etc/elasticsearch/elasticsearch.yml


Find the line that specifies network.host, uncomment it, and replace its value with "localhost" so it looks like this:
elasticsearch.yml excerpt (updated)
network.host: localhost


Save and exit elasticsearch.yml.
Now start Elasticsearch:
sudo service elasticsearch restart


Development Environment Setup
Clone the kibana repo 
git clone https://github.com/elastic/kibana.git kibana
cd kibana
Install the version of node.js listed in the .node-version file (this is made easy with tools like nvm and avn)
nvm install "$(cat .node-version)"
Install dependencies
npm install

Start the development server.
npm start

Finally elastic search will be running under the port 9200
localhost:9200
kibana will be running under 5601
localhost:5601
We can configure the port numbers accordingly


The main objective here include kibana into a site for angular.js application
I will be doing using Iframe

<iframe src="http://localhost:5601" frameborder="0" scrolling="yes" width="100%" height="1000" ></iframe>

