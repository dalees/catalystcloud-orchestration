# Using this HOT template


## Create stack

  $ openstack stack create mystack -t autoscaling.yaml --parameter webserver_keypair=dale


## Update to new base image

  $ openstack stack update --existing --parameter webserver_image=ubuntu-20.04-x86_64 my-stack


## Scale ASG up

First, obtain our scale up webhook URL from the stack output.

  $ openstack stack show my-stack -f json | jq -r '.outputs[] | select( .output_key == "scale_up_webhook").output_value'
  https://api.catalystcloud.nz:8004/v1/df3529d70455d3e71ae95b224dc/stacks/my-stack/fb55b21d-96f6-4faa-88a9-1875fe1819a8/resources/scaleup_policy/signal

  $ openstack stack show my-stack -f json | jq -r '.outputs[] | select( .output_key == "scale_down_webhook").output_value'
  https://api.catalystcloud.nz:8004/v1/df3529d70455d3e71ae95b224dc/stacks/my-stack/fb55b21d-96f6-4faa-88a9-1875fe1819a8/resources/scaledown_policy/signal


Next, get a Keystone Token that has access to the stack.

  $ openstack token issue -c id -f value
  gAAAAABkIgAZ0eDsstoREDACTED0FE0eD2viKo_YyD-FPxHd68dSx32kDGr_l9C1D

Finally, using both the token and webhook URL, we can POST to either manually scale up or scale down the AutoScalingGroup.

  $ curl -i -X POST -H "X-Auth-Token: gAAAAABkIgAZ0eDsstoREDACTED0FE0eD2viKo_YyD-FPxHd68dSx32kDGr_l9C1D" https://api.osppd.por.catalystcloud.nz:8004/v1/df3529d70455d3e71ae95b224dc/stacks/my-stack/fb55b21d-96f6-4faa-88a9-1875fe1819a8/resources/scaleup_policy/signal
  HTTP/1.1 200 OK
  content-type: application/json
  content-length: 4
  x-openstack-request-id: req-3abc005d-a968-420b-bd3f-023072933236
  date: Mon, 27 Mar 2023 20:46:36 GMT


