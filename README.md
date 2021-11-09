# terraform-aws-ecs-fargate

Terraform module to create AWS ECS FARGATE services.

## Terraform versions

Terraform >= 0.13.

## Usage

### ECS Fargate Service

```hcl
module "backend" {
  source = "git::https://github.com/dmytro-dorofeiev/terraform-aws-ecs-fargate-module"

  name_prefix             = "backend"
  service_name            = "backend"
  vpc_id                  = local.vpc_id
  private_subnet_ids      = local.vpc_private_subnets
  cluster_id              = data.aws_ecs_cluster.default.arn
  desired_count           = 1
  lb_arn                  = "none"
  task_container_protocol = "TCP"
  task_container_image    = "nginx:latest"
  task_definition_cpu     = 1024
  task_definition_memory  = 2048
  task_container_port     = 80
  task_container_secrets  = module.task_container_secrets.dynamic_secrets_json
  load_balanced           = false

  ssm_allowed_parameters = "${var.environmnet}/*"
  kms_key_arn            = data.aws_kms_key.default.arn

  health_check = {
    healthy_threshold   = 2
    unhealthy_threshold = 2
    interval            = 10
  }

  tags = local.common_tags
}
```

<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
## Requirements

| Name | Version |
|------|---------|
| <a name="requirement_terraform"></a> [terraform](#requirement\_terraform) | >= 0.13.0 |
| <a name="requirement_aws"></a> [aws](#requirement\_aws) | >= 3.13 |

## Providers

| Name | Version |
|------|---------|
| <a name="provider_aws"></a> [aws](#provider\_aws) | >= 3.13 |

## Modules

No modules.

## Resources

| Name | Type |
|------|------|
| [aws_cloudwatch_log_group.main](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/cloudwatch_log_group) | resource |
| [aws_ecs_service.service](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ecs_service) | resource |
| [aws_ecs_task_definition.task](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ecs_task_definition) | resource |
| [aws_iam_role.execution](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role) | resource |
| [aws_iam_role.task](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role) | resource |
| [aws_iam_role_policy.log_agent](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role_policy) | resource |
| [aws_iam_role_policy.read_repository_credentials](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role_policy) | resource |
| [aws_iam_role_policy.task_execution](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role_policy) | resource |
| [aws_iam_role_policy.tasks_execution_ssm](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role_policy) | resource |
| [aws_lb_target_group.task](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/lb_target_group) | resource |
| [aws_security_group.ecs_service](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group) | resource |
| [aws_security_group_rule.egress_service](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group_rule) | resource |
| [aws_caller_identity.current](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/caller_identity) | data source |
| [aws_iam_policy_document.read_repository_credentials](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) | data source |
| [aws_iam_policy_document.task_assume](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) | data source |
| [aws_iam_policy_document.task_execution_permissions](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) | data source |
| [aws_iam_policy_document.task_permissions](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) | data source |
| [aws_iam_policy_document.tasks_execution_ssm](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) | data source |
| [aws_kms_key.secretsmanager_key](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/kms_key) | data source |
| [aws_region.current](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/region) | data source |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_capacity_provider_strategy"></a> [capacity\_provider\_strategy](#input\_capacity\_provider\_strategy) | (Optional) The capacity\_provider\_strategy configuration block. This is a list of maps, where each map should contain "capacity\_provider ", "weight" and "base" | `list(any)` | `[]` | no |
| <a name="input_cluster_id"></a> [cluster\_id](#input\_cluster\_id) | The Amazon Resource Name (ARN) that identifies the cluster. | `string` | n/a | yes |
| <a name="input_container_name"></a> [container\_name](#input\_container\_name) | Optional name for the container to be used instead of name\_prefix. | `string` | `""` | no |
| <a name="input_create_repository_credentials_iam_policy"></a> [create\_repository\_credentials\_iam\_policy](#input\_create\_repository\_credentials\_iam\_policy) | Set to true if you are specifying `repository_credentials` variable, it will attach IAM policy with necessary permissions to task role. | `bool` | `false` | no |
| <a name="input_deployment_controller_type"></a> [deployment\_controller\_type](#input\_deployment\_controller\_type) | Type of deployment controller. Valid values: CODE\_DEPLOY, ECS. | `string` | `"ECS"` | no |
| <a name="input_deployment_maximum_percent"></a> [deployment\_maximum\_percent](#input\_deployment\_maximum\_percent) | The upper limit of the number of running tasks that can be running in a service during a deployment | `number` | `200` | no |
| <a name="input_deployment_minimum_healthy_percent"></a> [deployment\_minimum\_healthy\_percent](#input\_deployment\_minimum\_healthy\_percent) | The lower limit of the number of running tasks that must remain running and healthy in a service during a deployment | `number` | `50` | no |
| <a name="input_deregistration_delay"></a> [deregistration\_delay](#input\_deregistration\_delay) | (Optional) The amount time for Elastic Load Balancing to wait before changing the state of a deregistering target from draining to unused. The range is 0-3600 seconds. The default value is 300 seconds | `number` | `60` | no |
| <a name="input_desired_count"></a> [desired\_count](#input\_desired\_count) | The number of instances of the task definitions to place and keep running. | `number` | `1` | no |
| <a name="input_force_new_deployment"></a> [force\_new\_deployment](#input\_force\_new\_deployment) | Enable to force a new task deployment of the service. This can be used to update tasks to use a newer Docker image with same image/tag combination (e.g. myimage:latest), roll Fargate tasks onto a newer platform version. | `bool` | `false` | no |
| <a name="input_health_check"></a> [health\_check](#input\_health\_check) | A health block containing health check settings for the target group. Overrides the defaults. | `map(string)` | n/a | yes |
| <a name="input_health_check_grace_period_seconds"></a> [health\_check\_grace\_period\_seconds](#input\_health\_check\_grace\_period\_seconds) | Seconds to ignore failing load balancer health checks on newly instantiated tasks to prevent premature shutdown, up to 7200. Only valid for services configured to use load balancers. | `number` | `60` | no |
| <a name="input_kms_key_arn"></a> [kms\_key\_arn](#input\_kms\_key\_arn) | kms key to decrypt ssm | `string` | n/a | yes |
| <a name="input_lb_arn"></a> [lb\_arn](#input\_lb\_arn) | Arn for the LB for which the service should be attach to. | `string` | n/a | yes |
| <a name="input_load_balanced"></a> [load\_balanced](#input\_load\_balanced) | Whether the task should be loadbalanced. | `bool` | `true` | no |
| <a name="input_log_retention_in_days"></a> [log\_retention\_in\_days](#input\_log\_retention\_in\_days) | Number of days the logs will be retained in CloudWatch. | `number` | `30` | no |
| <a name="input_logs_kms_key"></a> [logs\_kms\_key](#input\_logs\_kms\_key) | The KMS key ARN to use to encrypt container logs. | `string` | `""` | no |
| <a name="input_name_prefix"></a> [name\_prefix](#input\_name\_prefix) | A prefix used for naming resources. | `string` | n/a | yes |
| <a name="input_placement_constraints"></a> [placement\_constraints](#input\_placement\_constraints) | (Optional) A set of placement constraints rules that are taken into consideration during task placement. Maximum number of placement\_constraints is 10. This is a list of maps, where each map should contain "type" and "expression" | `list(any)` | `[]` | no |
| <a name="input_platform_version"></a> [platform\_version](#input\_platform\_version) | The platform version on which to run your service. Only applicable for launch\_type set to FARGATE. | `string` | `"LATEST"` | no |
| <a name="input_private_subnet_ids"></a> [private\_subnet\_ids](#input\_private\_subnet\_ids) | A list of private subnets inside the VPC | `list(string)` | n/a | yes |
| <a name="input_propogate_tags"></a> [propogate\_tags](#input\_propogate\_tags) | Specifies whether to propagate the tags from the task definition or the service to the tasks. The valid values are SERVICE and TASK\_DEFINITION. | `string` | `"TASK_DEFINITION"` | no |
| <a name="input_proxy_configuration"></a> [proxy\_configuration](#input\_proxy\_configuration) | (Optional) The proxy configuration details for the App Mesh proxy. This is a list of maps, where each map should contain "container\_name", "properties" and "type" | `list(any)` | `[]` | no |
| <a name="input_repository_credentials"></a> [repository\_credentials](#input\_repository\_credentials) | name or ARN of a secrets manager secret (arn:aws:secretsmanager:region:aws\_account\_id:secret:secret\_name) | `string` | `""` | no |
| <a name="input_repository_credentials_kms_key"></a> [repository\_credentials\_kms\_key](#input\_repository\_credentials\_kms\_key) | key id, key ARN, alias name or alias ARN of the key that encrypted the repository credentials | `string` | `"alias/aws/secretsmanager"` | no |
| <a name="input_service_name"></a> [service\_name](#input\_service\_name) | ecs service name | `string` | n/a | yes |
| <a name="input_service_registry_arn"></a> [service\_registry\_arn](#input\_service\_registry\_arn) | ARN of aws\_service\_discovery\_service resource | `string` | `""` | no |
| <a name="input_sg_name_prefix"></a> [sg\_name\_prefix](#input\_sg\_name\_prefix) | A prefix used for Security group name. | `string` | `""` | no |
| <a name="input_ssm_allowed_parameters"></a> [ssm\_allowed\_parameters](#input\_ssm\_allowed\_parameters) | path allowed for parameters | `string` | n/a | yes |
| <a name="input_tags"></a> [tags](#input\_tags) | A map of tags (key-value pairs) passed to resources. | `map(string)` | `{}` | no |
| <a name="input_target_group_name"></a> [target\_group\_name](#input\_target\_group\_name) | The name for the tasks target group | `string` | `""` | no |
| <a name="input_task_container_assign_public_ip"></a> [task\_container\_assign\_public\_ip](#input\_task\_container\_assign\_public\_ip) | Assigned public IP to the container. | `bool` | `false` | no |
| <a name="input_task_container_command"></a> [task\_container\_command](#input\_task\_container\_command) | The command that is passed to the container. | `list(string)` | `[]` | no |
| <a name="input_task_container_cpu"></a> [task\_container\_cpu](#input\_task\_container\_cpu) | Amount of CPU to reserve for the container. | `number` | `null` | no |
| <a name="input_task_container_environment"></a> [task\_container\_environment](#input\_task\_container\_environment) | The environment variables to pass to a container. | `map(string)` | `{}` | no |
| <a name="input_task_container_image"></a> [task\_container\_image](#input\_task\_container\_image) | The image used to start a container. | `string` | n/a | yes |
| <a name="input_task_container_memory"></a> [task\_container\_memory](#input\_task\_container\_memory) | The hard limit (in MiB) of memory for the container. | `number` | `null` | no |
| <a name="input_task_container_memory_reservation"></a> [task\_container\_memory\_reservation](#input\_task\_container\_memory\_reservation) | The soft limit (in MiB) of memory to reserve for the container. | `number` | `null` | no |
| <a name="input_task_container_port"></a> [task\_container\_port](#input\_task\_container\_port) | The port number on the container that is bound to the user-specified or automatically assigned host port | `number` | n/a | yes |
| <a name="input_task_container_protocol"></a> [task\_container\_protocol](#input\_task\_container\_protocol) | Protocol that the container exposes. | `string` | `"HTTP"` | no |
| <a name="input_task_container_secrets"></a> [task\_container\_secrets](#input\_task\_container\_secrets) | The secrets variables to pass to a container. | `list(map(string))` | `null` | no |
| <a name="input_task_container_working_directory"></a> [task\_container\_working\_directory](#input\_task\_container\_working\_directory) | The working directory to run commands inside the container. | `string` | `""` | no |
| <a name="input_task_definition_cpu"></a> [task\_definition\_cpu](#input\_task\_definition\_cpu) | Amount of CPU to reserve for the task. | `number` | `256` | no |
| <a name="input_task_definition_memory"></a> [task\_definition\_memory](#input\_task\_definition\_memory) | The soft limit (in MiB) of memory to reserve for the task. | `number` | `512` | no |
| <a name="input_task_health_check"></a> [task\_health\_check](#input\_task\_health\_check) | An optional healthcheck definition for the task | `object({ command = list(string), interval = number, timeout = number, retries = number, startPeriod = number })` | `null` | no |
| <a name="input_task_host_port"></a> [task\_host\_port](#input\_task\_host\_port) | The port number on the container instance to reserve for your container. | `number` | `0` | no |
| <a name="input_task_mount_points"></a> [task\_mount\_points](#input\_task\_mount\_points) | The mount points for data volumes in your container. Each object inside the list requires "sourceVolume", "containerPath" and "readOnly". For more information see https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html | `list(object({ sourceVolume = string, containerPath = string, readOnly = bool }))` | `null` | no |
| <a name="input_task_start_timeout"></a> [task\_start\_timeout](#input\_task\_start\_timeout) | Time duration (in seconds) to wait before giving up on resolving dependencies for a container. If this parameter is not specified, the default value of 3 minutes is used (fargate). | `number` | `null` | no |
| <a name="input_task_stop_timeout"></a> [task\_stop\_timeout](#input\_task\_stop\_timeout) | Time duration (in seconds) to wait before the container is forcefully killed if it doesn't exit normally on its own. The max stop timeout value is 120 seconds and if the parameter is not specified, the default value of 30 seconds is used. | `number` | `null` | no |
| <a name="input_volume"></a> [volume](#input\_volume) | (Optional) A set of volume blocks that containers in your task may use. This is a list of maps, where each map should contain "name", "host\_path", "docker\_volume\_configuration" and "efs\_volume\_configuration". Full set of options can be found at https://www.terraform.io/docs/providers/aws/r/ecs_task_definition.html | `list` | `[]` | no |
| <a name="input_vpc_id"></a> [vpc\_id](#input\_vpc\_id) | The VPC ID. | `string` | n/a | yes |
| <a name="input_wait_for_steady_state"></a> [wait\_for\_steady\_state](#input\_wait\_for\_steady\_state) | If true, Terraform will wait for the service to reach a steady state (like aws ecs wait services-stable) before continuing. | `bool` | `false` | no |

## Outputs

| Name | Description |
|------|-------------|
| <a name="output_execution_role_arn"></a> [execution\_role\_arn](#output\_execution\_role\_arn) | The Amazon Resource Name (ARN) specifying the ECS execution role. |
| <a name="output_execution_role_name"></a> [execution\_role\_name](#output\_execution\_role\_name) | The name of the ECS execution role. |
| <a name="output_log_group_name"></a> [log\_group\_name](#output\_log\_group\_name) | The name of the Cloudwatch log group for the task. |
| <a name="output_service_arn"></a> [service\_arn](#output\_service\_arn) | The Amazon Resource Name (ARN) that identifies the ECS service. |
| <a name="output_service_name"></a> [service\_name](#output\_service\_name) | The name of the service. |
| <a name="output_service_sg_id"></a> [service\_sg\_id](#output\_service\_sg\_id) | The Amazon Resource Name (ARN) that identifies the service security group. |
| <a name="output_target_group_arn"></a> [target\_group\_arn](#output\_target\_group\_arn) | The ARN of the Target Group used by Load Balancer. |
| <a name="output_target_group_arn_suffix"></a> [target\_group\_arn\_suffix](#output\_target\_group\_arn\_suffix) | The ARN of the Target Group used by Load Balancer. |
| <a name="output_target_group_name"></a> [target\_group\_name](#output\_target\_group\_name) | The Name of the Target Group used by Load Balancer. |
| <a name="output_task_definition_arn"></a> [task\_definition\_arn](#output\_task\_definition\_arn) | The Amazon Resource Name (ARN) of the task definition created |
| <a name="output_task_definition_name"></a> [task\_definition\_name](#output\_task\_definition\_name) | The name of the task definition created |
| <a name="output_task_role_arn"></a> [task\_role\_arn](#output\_task\_role\_arn) | The Amazon Resource Name (ARN) specifying the ECS service role. |
| <a name="output_task_role_name"></a> [task\_role\_name](#output\_task\_role\_name) | The name of the Fargate task service role. |
<!-- END OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
