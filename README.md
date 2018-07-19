# Elastic Stack BOSH deployment


```
cat <<EOF > logstash.conf
input {
  tcp {
     port => 5514
  }
}
output {
  stdout {
    codec => json_lines
  }
}
EOF
```

* minimal deployment

```
bosh -d elastic-stack deploy elastic-stack.yml \
     -l versions.yml \
     --var-file logstash.conf=logstash.conf \
     --no-redact
```

* clustered deployment

```
bosh -d elastic-stack deploy elastic-stack.yml \
     -l versions.yml \
     -o ops-files/vm_types.yml \
     -o ops-files/disk_types.yml \
     -o ops-files/instances.yml \
     -o ops-files/networks.yml \
     -o ops-files/azs.yml \
     -o ops-files/elasticsearch-add-lb.yml \
     -o ops-files/elasticsearch-add-data-nodes.yml \
     -o ops-files/elasticsearch-add-plugins-master.yml \
     -o ops-files/elasticsearch-add-plugins-data.yml \
     -o ops-files/logstash-add-lb.yml \
     -o ops-files/logstash-readiness-probe.yml \
     -o ops-files/kibana-https-and-basic-auth.yml \
     -o ops-files/kibana-https-add-lb.yml \
     --var-file logstash.conf=logstash.conf \
     -v elasticsearch_master_instances=3 \
     -v elasticsearch_master_vm_type=minimal \
     -v elasticsearch_master_disk_type=5120 \
     -v elasticsearch_master_network=default \
     -v elasticsearch_master_azs="[z1, z2, z3]" \
     -v elasticsearch_data_instances=2 \
     -v elasticsearch_data_vm_type=minimal \
     -v elasticsearch_data_disk_type=5120 \
     -v elasticsearch_data_network=default \
     -v elasticsearch_data_azs="[z1, z2, z3]" \
     -v logstash_instances=2 \
     -v logstash_vm_type=minimal \
     -v logstash_disk_type=5120 \
     -v logstash_network=default \
     -v logstash_azs="[z1, z2, z3]" \
     -v logstash_readiness_probe_http_port=0 \
     -v logstash_readiness_probe_tcp_port=5514 \
     -v kibana_instances=1 \
     -v kibana_vm_type=minimal \
     -v kibana_network=default \
     -v kibana_azs="[z1, z2, z3]" \
     --no-redact
```
