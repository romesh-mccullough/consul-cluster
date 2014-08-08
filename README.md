consul-cluster
==============

Templates for launching [Consul][1] clusters.

# Vagrant
Requires Vagrant 1.6 or above.
```sh
git clone git@github.com:romesh-mccullough/consul-cluster.git
cd consul-cluster/vagrant

vagrant plugin install vagrant-auto_network
vagrant plugin install vagrant-hostmanager
vagrant plugin install vagrant-berkshelf

vagrant up
```
By default, this will create 3 Consul servers running on Ubuntu 12.04 add them to your local hosts file.  They will be given the hostnames *consul0*, *consul1*, and *consul2*

*consul0* will be started in bootstrap mode and run the web ui.  *consul1* and *consul2* will automatically register with consul0 and form the cluster.  

If you want to play around with manually joining servers set *AUTO_JOIN = false* at the top of the Vagrantfile.  You can then:
```sh
vagrant ssh consul1
consul join consul0
exit

vagrant ssh consul2
consul join consul0
exit
```

Once the cluster has started you can access the web ui by going to [http://consul0:8500/ui](http://consul0:8500/ui)


## Basic Usage

Check out these guides for more detail:
* [http://www.consul.io/docs/guides/external.html][2]
* [http://www.consul.io/intro/getting-started/kv.html][3]
* [http://www.consul.io/docs/agent/http.html][4]
* [http://www.consul.io/docs/agent/dns.html][5]

#### Register a new service with the HTTP API

```sh
curl -X PUT -d '{"Datacenter": "vagrant-dc", "Node": "google", "Address": "www.google.com",
"Service": {"Service": "search", "Port": 80}}' http://consul0:8500/v1/catalog/register
```
#### Set a key value pair with the HTTP API
```sh
curl -X PUT -d 'bar' http://consul0:8500/v1/kv/foo
```
#### Use the DNS interface
```sh
dig @consul0 -p 8600 search.service.consul.
```

#### Query the HTTP API
* For all services
```sh
curl consul0:8500/v1/catalog/services
```
* For all nodes in a service
```sh
curl consul0:8500/v1/catalog/service/search
curl consul0:8500/v1/catalog/service/consul
```
* For a key
```sh
curl -X PUT -d 'bar' http://consul0:8500/v1/kv/foo
```

  [1]: http://www.consul.io "Consul"
  [2]: http://www.consul.io/docs/guides/external.html
  [3]: http://www.consul.io/intro/getting-started/kv.html
  [4]: http://www.consul.io/docs/agent/http.html
  [5]: http://www.consul.io/docs/agent/dns.html
