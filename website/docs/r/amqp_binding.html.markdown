---
subcategory: "RabbitMQ (AMQP)"
layout: "alicloud"
page_title: "Alicloud: alicloud_amqp_binding"
sidebar_current: "docs-alicloud-resource-amqp-binding"
description: |-
  Provides a Alicloud RabbitMQ (AMQP) Binding resource.
---

# alicloud_amqp_binding

Provides a RabbitMQ (AMQP) Binding resource to bind tha exchange with another exchange or queue.

For information about RabbitMQ (AMQP) Binding and how to use it, see [What is Binding](https://www.alibabacloud.com/help/en/message-queue-for-rabbitmq/latest/createbinding).

-> **NOTE:** Available since v1.135.0.

## Example Usage

Basic Usage

```terraform
resource "alicloud_amqp_instance" "default" {
  instance_type  = "enterprise"
  max_tps        = 3000
  queue_capacity = 200
  storage_size   = 700
  support_eip    = false
  max_eip_tps    = 128
  payment_type   = "Subscription"
  period         = 1
}

resource "alicloud_amqp_virtual_host" "default" {
  instance_id       = alicloud_amqp_instance.default.id
  virtual_host_name = "tf-example"
}

resource "alicloud_amqp_exchange" "default" {
  auto_delete_state = false
  exchange_name     = "tf-example"
  exchange_type     = "DIRECT"
  instance_id       = alicloud_amqp_instance.default.id
  internal          = false
  virtual_host_name = alicloud_amqp_virtual_host.default.virtual_host_name
}

resource "alicloud_amqp_queue" "default" {
  instance_id       = alicloud_amqp_instance.default.id
  queue_name        = "tf-example"
  virtual_host_name = alicloud_amqp_virtual_host.default.virtual_host_name
}

resource "alicloud_amqp_binding" "default" {
  argument          = "x-match:all"
  binding_key       = alicloud_amqp_queue.default.queue_name
  binding_type      = "QUEUE"
  destination_name  = "tf-example"
  instance_id       = alicloud_amqp_instance.default.id
  source_exchange   = alicloud_amqp_exchange.default.exchange_name
  virtual_host_name = alicloud_amqp_virtual_host.default.virtual_host_name
}
```

## Argument Reference

The following arguments are supported:

* `argument` - (Optional, ForceNew) X-match Attributes. Valid Values: 
  * "x-match:all": Default Value, All the Message Header of Key-Value Pairs Stored in the Must Match. 
  * "x-match:any": at Least One Pair of the Message Header of Key-Value Pairs Stored in the Must Match. 
    
  **NOTE:** This Parameter Applies Only to Headers Exchange Other Types of Exchange Is Invalid. Other Types of Exchange Here Can Either Be an Arbitrary Value.
  
* `binding_key` - (Required, ForceNew) The Binding Key.
  * For a non-topic source exchange: The binding key can contain only letters, digits, hyphens (-), underscores (_), periods (.), and at signs (@).
    The binding key must be 1 to 255 characters in length.
  * For a topic source exchange: The binding key can contain letters, digits, hyphens (-), underscores (_), periods (.), and at signs (@). 
    If the binding key contains a number sign (#), the binding key must start with a number sign (#) followed by a period (.) or end with a number sign (#) that follows a period (.). 
    The binding key must be 1 to 255 characters in length.
    
* `binding_type` - (Required, ForceNew) The Target Binding Types. Valid values: `EXCHANGE`, `QUEUE`.
* `destination_name` - (Required, ForceNew) The Target Queue Or Exchange of the Name.
* `instance_id` - (Required, ForceNew) Instance Id.
* `source_exchange` - (Required, ForceNew) The Source Exchange Name.
* `virtual_host_name` - (Required, ForceNew) Virtualhost Name.

## Attributes Reference

The following attributes are exported:

* `id` - The resource ID of Binding. The value formats as `<instance_id>:<virtual_host_name>:<source_exchange>:<destination_name>`.

## Import

RabbitMQ (AMQP) Binding can be imported using the id, e.g.

```shell
terraform import alicloud_amqp_binding.example <instance_id>:<virtual_host_name>:<source_exchange>:<destination_name>
```
