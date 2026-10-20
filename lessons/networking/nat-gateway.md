# AWS NAT Gateway

## Purpose

NAT stands for **Network Address Translation**. An AWS NAT Gateway allows resources in a private subnet to initiate connections to destinations outside their VPC, such as public Internet services, without assigning those resources public IP addresses.

For typical public-Internet egress, a NAT Gateway uses an Elastic IP address. When a private resource sends a request out, the NAT Gateway translates the source private IP address to its public address. Return traffic for connections initiated from the private side can flow back through the gateway. A NAT Gateway does not provide a general path for unsolicited connections initiated from the Internet to reach the private resource.

A NAT Gateway is not the same thing as a firewall. Route tables determine whether traffic is directed through the NAT Gateway, while Security Groups and other network controls determine which traffic is allowed.

## Example in Trail Races

Suppose the Spring Boot backend runs on an EC2 instance in a private subnet and has a private IP such as `10.0.2.15`. The backend might need to call a public third-party API or another external service. If it has no other suitable egress path, its subnet's route table can send Internet-bound traffic (`0.0.0.0/0`) to a NAT Gateway located in a public subnet. The public subnet's route table has a route to an Internet Gateway.

```text
Spring Boot backend (private subnet)
        │
        │ Internet-bound request
        ▼
NAT Gateway (public subnet, Elastic IP)
        │
        ▼
Internet Gateway
        │
        ▼
Public Internet service
```

The external service sees the NAT Gateway's public IP address as the source of the connection, rather than the backend's private IP address. The backend itself does not need a public IP address for this outbound connection.

## When Trail Races would need a NAT Gateway

A NAT Gateway may be useful if the backend must initiate connections to public Internet endpoints and there is no alternative route that meets the requirements.

It is not required just because the backend is in a private subnet:

- Backend-to-PostgreSQL communication inside the VPC does not require a NAT Gateway.
- Receiving API requests through an appropriately configured load balancer does not, by itself, require a NAT Gateway.
- Access to some AWS services can use VPC endpoints instead, depending on the service and endpoint type.
- A backend that only serves requests and communicates with private resources may not need general Internet egress at all.

## Routing and cost considerations

For a private subnet to use a NAT Gateway for IPv4 Internet egress, its route table typically includes a default route (`0.0.0.0/0`) pointing to the NAT Gateway. The NAT Gateway is placed in a public subnet whose route table has a default route to an Internet Gateway. The route alone does not replace Security Group rules or other access controls.

NAT Gateways incur charges, including charges for provisioned running time and data processed. They should not be added automatically to a small learning project. Before including one in Trail Races, identify which outbound connections the backend actually needs and compare suitable alternatives, including VPC endpoints where applicable. Cost optimization must not weaken required security controls.

## Key distinction

A NAT Gateway provides a way for private-subnet resources to initiate outbound connections without requiring their own public IP addresses. It is not necessary for every private subnet, and it is not needed for communication between the backend and a PostgreSQL database that is reachable privately within the VPC.
