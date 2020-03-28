---
title: Självstudiekurs - Hantera webbtrafik med Azure Application Gateway med Ansible
description: Lär dig hur du använder Ansible för att skapa och konfigurera en Azure Application Gateway för att hantera webbtrafik
keywords: ansible, azure, devops, bash, playbook, application gateway, load balancer, web traffic
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 07f75e39b8c6f592ecd4c48697527493b1109bb9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156608"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Självstudiekurs: Hantera webbtrafik med Azure Application Gateway med Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Application Gateway](/azure/application-gateway/overview) är en webbtrafikbelastningsutjämningsfaktor som gör att du kan hantera trafik till dina webbprogram. Baserat på källans IP-adress och port dirigerar traditionella belastningsutjämnare trafik till en mål-IP-adress och port. Application Gateway ger dig en finare kontrollnivå där trafiken kan dirigeras baserat på webbadressen. Du kan till exempel `images` definiera att om url:ens sökväg dirigeras trafiken till en viss uppsättning servrar (kallas pool) som konfigurerats för avbildningar.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurera ett nätverk
> * Skapa två Azure-containerinstanser med HTTPD-avbildningar
> * Skapa en programgateway som körs med Azure-containerinstanserna i serverdelspoolen

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Spelbokskoden i det här avsnittet skapar en Azure-resursgrupp. En resursgrupp är en logisk behållare där Azure-resurser har konfigurerats.  

Spara följande spelbok som `rg.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Innan du kör spelboken läser du följande anmärkningar:

- Resursgruppsnamnet `myResourceGroup`är . Det här värdet används i hela självstudien.
- Resursgruppen skapas på `eastus` platsen.

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Skapa nätverksresurser

Spelbokskoden i det här avsnittet skapar ett virtuellt nätverk så att programgatewayen kan kommunicera med andra resurser.

Spara följande spelbok som `vnet_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

Innan du kör spelboken läser du följande anmärkningar:

* Avsnittet `vars` innehåller de värden som används för att skapa nätverksresurserna. 
* Du måste ändra dessa värden för din specifika miljö.

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Skapa servrar

Playbook-koden i det här avsnittet skapar två Azure-behållarinstanser med HTTPD-avbildningar som ska användas som webbservrar för programgatewayen.  

Spara följande spelbok som `aci_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

Spelbokskoden i det här avsnittet `myAppGateway`skapar en programgateway med namnet .  

Spara följande spelbok som `appgw_create.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

Innan du kör spelboken läser du följande anmärkningar:

* `appGatewayIP`definieras i `gateway_ip_configurations` blocket. Det krävs en undernätsreferens för gatewayens IP-konfiguration.
* `appGatewayBackendPool`definieras i `backend_address_pools` blocket. En programgateway måste ha minst en serverdelsadresspool.
* `appGatewayBackendHttpSettings`definieras i `backend_http_settings_collection` blocket. Den anger att port 80 och ett HTTP-protokoll används för kommunikation.
* `appGatewayHttpListener`definieras i `backend_http_settings_collection` blocket. Det är den standardlyssnare som är associerad med appGatewayBackendPool.
* `appGatewayFrontendIP`definieras i `frontend_ip_configurations` blocket. Det tilldelar myAGPublicIPAddress till appGatewayHttpListener.
* `rule1`definieras i `request_routing_rules` blocket. Det är den standardhanteringsregel som är associerad med appGatewayHttpListener.

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook appgw_create.yml
```

Det kan ta flera minuter att skapa programgatewayen.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. I avsnittet [Skapa en resursgrupp](#create-a-resource-group) anger du en plats. Notera dess värde.

1. I avsnittet [Skapa nätverksresurser](#create-network-resources) anger du domänen. Notera dess värde.

1. För test-URL:en genom att ersätta följande `http://<domain>.<location>.cloudapp.azure.com`mönster med platsen och domänen: .

1. Bläddra till test-URL:en.

1. Om du ser följande sida fungerar programgatewayen som förväntat.

    ![Ett lyckat test av en fungerande programgateway](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort de resurser som skapas i den här artikeln. 

Spara följande kod `cleanup.yml`som:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansible i Azure](/azure/ansible/)