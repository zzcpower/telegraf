# ECS Input Plugin

ECS, Fargate compatible, input plugin which uses the [ECS v2 metadata and stats API](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-metadata-endpoint-v2.html)
endpoints to gather stats on running containers in a Task.

The telegraf container must be run in the same Task as the workload it is inspecting.

This is similar to (and reuses a few pieces of) the [Docker](../docker/README.md) input plugin, with some ECS specific modifications for AWS metadata and stats formats.


### Configuration: 

```toml
# Read metrics about ECS containers
[[inputs.ecs]]
  # endpoint_url = http://
  ## Containers to include and exclude. Globs accepted.
  ## Note that an empty array for both will include all containers
  container_name_include = []
  container_name_exclude = []

  ## Container states to include and exclude. Globs accepted.
  ## When empty only containers in the "running" state will be captured.
  # container_status_include = []
  # container_status_exclude = []

  ## ecs labels to include and exclude as tags.  Globs accepted.
  ## Note that an empty array for both will include all labels as tags
  ecs_label_include = [ "com.amazonaws.ecs.*" ]
  ecs_label_exclude = []

  ## Timeout for docker list, info, and stats commands
  timeout = "5s"
```

#### Environment Configuration

The ECS client can optionally also be configured with the following env vars:

```
ECS_TIMEOUT
```


### Example Output:

```
ecs_task_status,cluster=test,family=nginx,host=c4b301d4a123,revision=2,task_arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a revision="2",desired_status="RUNNING",known_status="RUNNING",limit_cpu=0.5,limit_mem=512 1542641488000000000
ecs_container_mem,cluster=test,com.amazonaws.ecs.cluster=test,com.amazonaws.ecs.container-name=~internal~ecs~pause,com.amazonaws.ecs.task-arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a,com.amazonaws.ecs.task-definition-family=nginx,com.amazonaws.ecs.task-definition-version=2,family=nginx,host=c4b301d4a123,id=e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba,name=~internal~ecs~pause,revision=2,task_arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a active_anon=40960i,active_file=8192i,cache=790528i,pgpgin=1243i,total_pgfault=1298i,total_rss=40960i,limit=1033658368i,max_usage=4825088i,hierarchical_memory_limit=536870912i,rss=40960i,total_active_file=8192i,total_mapped_file=618496i,usage_percent=0.05349543109392212,container_id="e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba",pgfault=1298i,pgmajfault=6i,pgpgout=1040i,total_active_anon=40960i,total_inactive_file=782336i,total_pgpgin=1243i,usage=552960i,inactive_file=782336i,mapped_file=618496i,total_cache=790528i,total_pgpgout=1040i 1542642001000000000
ecs_container_cpu,cluster=test,com.amazonaws.ecs.cluster=test,com.amazonaws.ecs.container-name=~internal~ecs~pause,com.amazonaws.ecs.task-arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a,com.amazonaws.ecs.task-definition-family=nginx,com.amazonaws.ecs.task-definition-version=2,cpu=cpu-total,family=nginx,host=c4b301d4a123,id=e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba,name=~internal~ecs~pause,revision=2,task_arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a usage_in_kernelmode=0i,throttling_throttled_periods=0i,throttling_periods=0i,throttling_throttled_time=0i,container_id="e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba",usage_percent=0,usage_total=26426156i,usage_in_usermode=20000000i,usage_system=2336100000000i 1542642001000000000
ecs_container_cpu,cluster=test,com.amazonaws.ecs.cluster=test,com.amazonaws.ecs.container-name=~internal~ecs~pause,com.amazonaws.ecs.task-arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a,com.amazonaws.ecs.task-definition-family=nginx,com.amazonaws.ecs.task-definition-version=2,cpu=cpu0,family=nginx,host=c4b301d4a123,id=e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba,name=~internal~ecs~pause,revision=2,task_arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a container_id="e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba",usage_total=26426156i 1542642001000000000
ecs_container_net,cluster=test,com.amazonaws.ecs.cluster=test,com.amazonaws.ecs.container-name=~internal~ecs~pause,com.amazonaws.ecs.task-arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a,com.amazonaws.ecs.task-definition-family=nginx,com.amazonaws.ecs.task-definition-version=2,family=nginx,host=c4b301d4a123,id=e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba,name=~internal~ecs~pause,network=eth0,revision=2,task_arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a rx_errors=0i,rx_packets=36i,tx_errors=0i,tx_bytes=648i,container_id="e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba",rx_dropped=0i,rx_bytes=5338i,tx_packets=8i,tx_dropped=0i 1542642001000000000
ecs_container_net,cluster=test,com.amazonaws.ecs.cluster=test,com.amazonaws.ecs.container-name=~internal~ecs~pause,com.amazonaws.ecs.task-arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a,com.amazonaws.ecs.task-definition-family=nginx,com.amazonaws.ecs.task-definition-version=2,family=nginx,host=c4b301d4a123,id=e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba,name=~internal~ecs~pause,network=eth5,revision=2,task_arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a rx_errors=0i,tx_packets=9i,rx_packets=26i,tx_errors=0i,rx_bytes=4641i,tx_dropped=0i,tx_bytes=690i,container_id="e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba",rx_dropped=0i 1542642001000000000
ecs_container_net,cluster=test,com.amazonaws.ecs.cluster=test,com.amazonaws.ecs.container-name=~internal~ecs~pause,com.amazonaws.ecs.task-arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a,com.amazonaws.ecs.task-definition-family=nginx,com.amazonaws.ecs.task-definition-version=2,family=nginx,host=c4b301d4a123,id=e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba,name=~internal~ecs~pause,network=total,revision=2,task_arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a rx_dropped=0i,rx_bytes=9979i,rx_errors=0i,rx_packets=62i,tx_bytes=1338i,container_id="e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba",tx_packets=17i,tx_dropped=0i,tx_errors=0i 1542642001000000000
ecs_container_blkio,cluster=test,com.amazonaws.ecs.cluster=test,com.amazonaws.ecs.container-name=~internal~ecs~pause,com.amazonaws.ecs.task-arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a,com.amazonaws.ecs.task-definition-family=nginx,com.amazonaws.ecs.task-definition-version=2,device=253:1,family=nginx,host=c4b301d4a123,id=e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba,name=~internal~ecs~pause,revision=2,task_arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a io_service_bytes_recursive_sync=790528i,io_service_bytes_recursive_total=790528i,io_serviced_recursive_sync=10i,io_serviced_recursive_write=0i,io_serviced_recursive_async=0i,io_serviced_recursive_total=10i,container_id="e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba",io_service_bytes_recursive_read=790528i,io_service_bytes_recursive_write=0i,io_service_bytes_recursive_async=0i,io_serviced_recursive_read=10i 1542642001000000000
ecs_container_blkio,cluster=test,com.amazonaws.ecs.cluster=test,com.amazonaws.ecs.container-name=~internal~ecs~pause,com.amazonaws.ecs.task-arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a,com.amazonaws.ecs.task-definition-family=nginx,com.amazonaws.ecs.task-definition-version=2,device=253:2,family=nginx,host=c4b301d4a123,id=e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba,name=~internal~ecs~pause,revision=2,task_arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a io_service_bytes_recursive_sync=790528i,io_service_bytes_recursive_total=790528i,io_serviced_recursive_async=0i,io_serviced_recursive_total=10i,container_id="e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba",io_service_bytes_recursive_read=790528i,io_service_bytes_recursive_write=0i,io_service_bytes_recursive_async=0i,io_serviced_recursive_read=10i,io_serviced_recursive_write=0i,io_serviced_recursive_sync=10i 1542642001000000000
ecs_container_blkio,cluster=test,com.amazonaws.ecs.cluster=test,com.amazonaws.ecs.container-name=~internal~ecs~pause,com.amazonaws.ecs.task-arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a,com.amazonaws.ecs.task-definition-family=nginx,com.amazonaws.ecs.task-definition-version=2,device=253:4,family=nginx,host=c4b301d4a123,id=e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba,name=~internal~ecs~pause,revision=2,task_arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a io_service_bytes_recursive_write=0i,io_service_bytes_recursive_sync=790528i,io_service_bytes_recursive_async=0i,io_service_bytes_recursive_total=790528i,io_serviced_recursive_async=0i,container_id="e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba",io_service_bytes_recursive_read=790528i,io_serviced_recursive_read=10i,io_serviced_recursive_write=0i,io_serviced_recursive_sync=10i,io_serviced_recursive_total=10i 1542642001000000000
ecs_container_blkio,cluster=test,com.amazonaws.ecs.cluster=test,com.amazonaws.ecs.container-name=~internal~ecs~pause,com.amazonaws.ecs.task-arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a,com.amazonaws.ecs.task-definition-family=nginx,com.amazonaws.ecs.task-definition-version=2,device=202:26368,family=nginx,host=c4b301d4a123,id=e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba,name=~internal~ecs~pause,revision=2,task_arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a io_serviced_recursive_read=10i,io_serviced_recursive_write=0i,io_serviced_recursive_sync=10i,io_serviced_recursive_async=0i,io_serviced_recursive_total=10i,container_id="e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba",io_service_bytes_recursive_sync=790528i,io_service_bytes_recursive_total=790528i,io_service_bytes_recursive_async=0i,io_service_bytes_recursive_read=790528i,io_service_bytes_recursive_write=0i 1542642001000000000
ecs_container_blkio,cluster=test,com.amazonaws.ecs.cluster=test,com.amazonaws.ecs.container-name=~internal~ecs~pause,com.amazonaws.ecs.task-arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a,com.amazonaws.ecs.task-definition-family=nginx,com.amazonaws.ecs.task-definition-version=2,device=total,family=nginx,host=c4b301d4a123,id=e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba,name=~internal~ecs~pause,revision=2,task_arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a io_serviced_recursive_async=0i,io_serviced_recursive_read=40i,io_serviced_recursive_sync=40i,io_serviced_recursive_write=0i,io_serviced_recursive_total=40i,io_service_bytes_recursive_read=3162112i,io_service_bytes_recursive_write=0i,io_service_bytes_recursive_async=0i,container_id="e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba",io_service_bytes_recursive_sync=3162112i,io_service_bytes_recursive_total=3162112i 1542642001000000000
ecs_container_meta,cluster=test,com.amazonaws.ecs.cluster=test,com.amazonaws.ecs.container-name=~internal~ecs~pause,com.amazonaws.ecs.task-arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a,com.amazonaws.ecs.task-definition-family=nginx,com.amazonaws.ecs.task-definition-version=2,family=nginx,host=c4b301d4a123,id=e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba,name=~internal~ecs~pause,revision=2,task_arn=arn:aws:ecs:aws-region-1:012345678901:task/a1234abc-a0a0-0a01-ab01-0abc012a0a0a limit_mem=0,type="CNI_PAUSE",container_id="e6af031b91deb3136a2b7c42f262ed2ab554e2fe2736998c7d8edf4afe708dba",docker_name="ecs-nginx-2-internalecspause",limit_cpu=0,known_status="RESOURCES_PROVISIONED",image="amazon/amazon-ecs-pause:0.1.0",image_id="",desired_status="RESOURCES_PROVISIONED" 1542642001000000000
```

### Notes: 
- the amazon-ecs-agent (though it _is_ a container running on the host) is not present in the metadata/stats endpoints.