# Using this HOT template


## Create stack

  $ openstack stack create mystack -t autoscaling.yaml -e env.yaml


## Update to new base image

  $ openstack stack update --existing --parameter webserver_image_id=0f995296-d6de-4f56-b5a1-e0bf090e7c6a my-stack

