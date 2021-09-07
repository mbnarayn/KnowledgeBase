# Azure Knowledge Base
***
## Azure Service Endpoint
Service Endpoints were the first service introduced to allow locking down of multi-tenant services. Service Endpoints allow you to restrict access to your PaaS resources to traffic coming from your Azure Virtual Network. With Service Endpoints, the PaaS service is still separate to your vNet and traffic is leaving your virtual network to access the PaaS service. However, the PaaS service is configured to be able to identify traffic coming from your virtual network and allow that, without the need to configure public IP’s on your vNet to allow filtering.

Service Endpoints work by enabling a subnet, or subnets, on your virtual network to support Service Endpoints. Once this is done, you can configure your PaaS resource to only accept traffic from those subnets. There is no requirement to do any IP filtering or NAT translation; you tell the PaaS resource which vNet and Subnet to allow traffic from. When Service Endpoints are enabled, the PaaS resource sees traffic coming from your vNets private IP, not its public IP.

Another advantage of using service endpoints is that traffic is routed to the Azure resources optimally. Even if you have UDRs on your vNet to route internet traffic back on-premises or through a firewall device, using a Service Endpoint means traffic is sent directly to the Azure Resource.

- Resource maintains a public IP address
- IP resolution is via Microsoft DNS
- Service Endpoints are not directly accessible from on-premises networks

***
## Azure Private Link

Azure Private Endpoint is a network interface that connects you privately and securely to a service powered by Azure Private Link. Private Endpoint uses a private IP address from your VNet, effectively bringing the service into your VNet.

Azure Private Link enables you to access Azure PaaS Services (for example, Azure Storage and SQL Database) and Azure hosted customer-owned/partner services over a private endpoint in your virtual network.

- Private Endpoint - Private Endpoint is the NIC with the Private IP on a Subnet
- Private Link Resource - Private Link Resource is the service that is being presented via a Private Endpoint. Azure Blob Storage is an example of a Private Link Resource.
- Private Link - Private Link is the component that connects the Private Endpoint to the Private Link Resource
- Private Link Service - Azure Private Link service is the reference to your own service that is powered by Azure Private Link. Your service that is running behind Azure Standard Load Balancer can be enabled for Private Link access so that consumers to your service can access it privately from their own VNets. Your customers can create a private endpoint inside their VNet and map it to this service.
- Private Link Alias - After you create a Private Link service, Azure will generate a globally unique named moniker called "alias" based on the name you provide for your service. You can share either the alias or resource URI of your service with your customers offline. Consumers can start a Private Link connection using the alias or the resource URI.

### Private Endpoint Considerations
- Optionally block public access completely with the firewall
- Internal DNS resolves to private IP
- NSGs do not apply to the NIC of Private Endpoints. Outbound NSG rules can be added to other resources to block internal traffic if needed.

### Build a Private Link Service
An Azure Private Link service allows you to share your services privately with other Azure customers. You need to expose your services on a standard load balancer to be able to create a Private Link Service.

After creating the Standard Load Balancer you would create the Private Link service. When you create this service, you need to provide a subnet for NAT IP addresses. You can have between 1 and 8 NAT addresses.

The service then outputs an alias and URI. You need to pass this alias or URI to your customer to enable them to create the Private Link endpoint in their VNET.
Once your customer has that alias or URI, they can create the Private Link endpoint in their VNET. You as a service provider need to approve their request to access your service via Private Link. You could configure auto-approve if you so desire.
And that’s what it takes to build a Private Link service.

### Azure Private Link Demo

https://www.youtube.com/watch?v=vVDql7IKneg
