# CAW-aws-terraform-module

This repository contains aws terraform modules . we only include the particular module terraform code in their folder. you can set the naming and tagging convention according to your needs.

## Usage

```hcl
resource "aws_ec2_client_vpn_endpoint" "this-vpn-client-endpoint" {
  
  description            = "terraform-vpn-client-endpoint"
  server_certificate_arn = var.vpn_server_certificate_arn
  client_cidr_block      = "1.0.0.0/16"
  vpn_port = 443

  authentication_options {
    type                       = "certificate-authentication"
    root_certificate_chain_arn = var.vpn_server_certificate_arn
  }
  
  split_tunnel = true
  vpc_id = module.vpc.vpc_id
  session_timeout_hours = 8 # 
  security_group_ids = [module.db-vpn-sg.security_group_id]#put the vpn security group id#
  connection_log_options {
    enabled               = false
    # cloudwatch_log_group  = aws_cloudwatch_log_group.lg.name
    # cloudwatch_log_stream = aws_cloudwatch_log_stream.ls.name
  }
  depends_on = [
    module.db-vpn-sg
  ]
  tags = {
  terraform = true 
  }
}
```

## VPN NETWORK ASSOCIATION

A VPN (Virtual Private Network) is a network technology that allows users to create a secure and encrypted connection to another network over the internet. When you connect to a VPN, your device creates a virtual tunnel between your device and the VPN server, encrypting all data that travels between them. so once you done with creating vpn client endpoint you have to associate the subnet where you want to allow a user to connect through vpn .

```hcl
resource "aws_ec2_client_vpn_network_association" "this-vpn-subnet-1a" {
  client_vpn_endpoint_id = aws_ec2_client_vpn_endpoint.this-vpn-client-endpoint.id
  subnet_id              = data.aws_subnets.public-subnets.ids[0]#Put the subnet id of aws resource which you want to secure from vpn#
}
```

```hcl
data "aws_subnets" "public-subnets" {
  filter {
    name   = "tag:Name"
    values = [var.database_subnet_names[0], var.database_subnet_names[1]]
  }
  depends_on = [
    module.vpc
  ]
}
```

## VPN AUTHORIZATION RULE

A VPN (Virtual Private Network) authorization rule is a set of policies and rules that define who can access the VPN network and what they can do once they are connected. Authorization rules are typically configured by the network administrator and are used to ensure that only authorized users can access the VPN network and its resources.

Authorization rules can be based on a variety of criteria, including user credentials, device type, network location, and time of day. For example, an authorization rule may specify that only users with a specific role or group membership can connect to the VPN network, or that only devices with specific security settings are allowed to connect.

Once a user or device is authorized to connect to the VPN network, the authorization rule can also specify what resources they are allowed to access. This can include specific network segments, servers, or applications, as well as specific actions that are allowed, such as file sharing or printing.

```hcl

resource "aws_ec2_client_vpn_authorization_rule" "this-vpn-authorization" {
  client_vpn_endpoint_id = aws_ec2_client_vpn_endpoint.this-vpn-client-endpoint.id
  target_network_cidr    = var.vpc_cidr_block#Put the vpc cidr block which we will establish vpn connection to that particular cidr#
  authorize_all_groups   = true
}

```

## Contributing

Report issues/questions/feature requests on in the [issues](https://github.com/cawstudios/CAW-aws-terraform-modules/issues/new) section.

Full contributing [guidelines are covered here](https://drive.google.com/open?id=1CNRzDrllOFaVGT2GjIkqwM2vcXJ2rnmlF0Kx2ah1Ho0&usp=chrome_ntp).

<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
## Requirements

| Name | Version |
|------|---------|
| <a name="requirement_terraform"></a> [terraform](#requirement\_terraform) | >= 1.0 |
| <a name="requirement_aws"></a> [aws](#requirement\_aws) | >= 4.40 |

## Providers

| Name | Version |
|------|---------|
| <a name="provider_aws"></a> [aws](#provider\_aws) | >= 4.40 |

## Modules

No modules.
## Resources

| Name | Type |
|------|------|
| [aws_ec2_client_vpn_authorization_rule](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ec2_client_vpn_authorization_rule) | resource |
| [aws_ec2_client_vpn_endpoint](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ec2_client_vpn_endpoint) | resource |
| [aws_ec2_client_vpn_network_association](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ec2_client_vpn_network_association) | resource |
| [aws_ec2_client_vpn_route](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ec2_client_vpn_route) | resource |
| [aws_ec2_client_vpn_endpoint](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/default_route_table) | resource |


## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_authentication_options"></a> [authentication\options](#input\_authentication\_options) | Information about the authentication method to be used to authenticate clients. | `block` | `` | yes |
| <a name="input_client_cidr_block"></a> [client\cidr\block](#input\_client\_cidr\_block) |  The IPv4 address range, in CIDR notation, from which to assign client IP addresses. The address range cannot overlap with the local CIDR of the VPC in which the associated subnet is located, or the routes that you add manually. The address range cannot be changed after the Client VPN endpoint has been created | `string` | `[]` | yes |
| <a name="input_client_connect_options"></a> [client\connect\options](#input\_client\_connect\_options) | The options for managing connection authorization for new client connections. | `block` | `` | no |
| <a name="input_client_login_banner_options"></a> [client\login\banner\options](#input\_client\_login\_banner\_options) | Options for enabling a customizable text banner that will be displayed on AWS provided clients when a VPN session is established. | `block` | `` | no |
| <a name="input_connection_log_options"></a> [connection\log\options](#input\_connection\_log\_options) |  Information about the client connection logging options. | `block` | `` | yes |
| <a name="input_description"></a> [description](#input\_description) | A brief description of the Client VPN endpoint. | `string` | `` | no |
| <a name="input_dns_servers"></a> [dns\servers](#input\_dns\_servers) |  Information about the DNS servers to be used for DNS resolution. A Client VPN endpoint can have up to two DNS servers. If no DNS server is specified, the DNS address of the connecting device is used. | `list(string)` | `` | no |


## Outputs
