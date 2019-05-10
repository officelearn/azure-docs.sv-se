---
title: Självstudie – hantera webbtrafik med Azure Application Gateway med Ansible | Microsoft Docs
description: Lär dig hur du använder Ansible för att skapa och konfigurera en Azure Application Gateway för att hantera webbtrafik
keywords: ansible, azure, devops, bash, playbook, application gateway, load balancer, web traffic
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 9f8ed3e1da72db3e1b13d5d2aef1cce8fc3922a2
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231250"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Självstudier: Hantera webbtrafik med Azure Application Gateway med Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Application Gateway](/azure/application-gateway/overview) är en lastbalanserare för webbtrafik som gör det möjligt för dig att hantera trafik till dina webbappar. Traditionell belastningsutjämnare dirigera baserat på källans IP-adress och port kan trafik till en IP-måladress och port. Application Gateway får du en mer detaljerad nivå av kontroll där trafiken kan dirigeras baserat på URL: en. Till exempel kan du definiera att om `images` är URL: er sökväg, trafik dirigeras till en specifik uppsättning servrar (kallas en pool) som konfigurerats för avbildningar.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurera ett nätverk
> * Skapa två Azure-containerinstanser med HTTPD-avbildningar
> * Skapa en programgateway som körs med Azure-containerinstanserna i serverdelspoolen

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Spelboken koden i det här avsnittet skapar en Azure-resursgrupp. En resursgrupp är en logisk behållare där Azure resurser konfigureras.  

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

Se följande information innan du kör spelboken:

- Resursgruppens namn är `myResourceGroup`. Det här värdet används i hela självstudien.
- Resursgruppen har skapats i den `eastus` plats.

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Skapa nätverksresurser

Spelboken koden i det här avsnittet skapar ett virtuellt nätverk för att aktivera application gateway kan kommunicera med andra resurser.

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

Se följande information innan du kör spelboken:

* Den `vars` avsnittet innehåller de värden som används för att skapa nätverksresurser. 
* Du måste ändra dessa värden för din miljö.

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Skapa servrar

Spelboken koden i det här avsnittet skapar två Azure container instances med HTTPD avbildningar som ska användas som webbservrar för application gateway.  

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

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

Spelboken koden i det här avsnittet skapar en Programgateway med namnet `myAppGateway`.  

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

Se följande information innan du kör spelboken:

* `appGatewayIP` har definierats i den `gateway_ip_configurations` block. Det krävs en undernätsreferens för gatewayens IP-konfiguration.
* `appGatewayBackendPool` har definierats i den `backend_address_pools` block. En programgateway måste ha minst en serverdelsadresspool.
* `appGatewayBackendHttpSettings` har definierats i den `backend_http_settings_collection` block. Det anger att port 80 och en HTTP-protokollet används för kommunikation.
* `appGatewayHttpListener` har definierats i den `backend_http_settings_collection` block. Det är den standardlyssnare som är associerad med appGatewayBackendPool.
* `appGatewayFrontendIP` har definierats i den `frontend_ip_configurations` block. Det tilldelar myAGPublicIPAddress till appGatewayHttpListener.
* `rule1` har definierats i den `request_routing_rules` block. Det är den standardhanteringsregel som är associerad med appGatewayHttpListener.

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook appgw_create.yml
```

Det kan ta flera minuter att skapa programgatewayen.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. I den [skapa en resursgrupp](#create-a-resource-group) avsnittet ska du ange en plats. Observera värdet.

1. I den [skapa nätverksresurser](#create-network-resources) avsnittet ska du ange domänen. Observera värdet.

1. För test-URL: en genom att ersätta följande mönster med plats och domän: `http://<domain>.<location>.cloudapp.azure.com`.

1. Gå till test-URL.

1. Om du ser följande sida fungerar programgatewayen som förväntat.

    ![Ett lyckat test av en fungerande programgateway](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort de resurser som skapades i den här artikeln. 

Spara följande kod som `cleanup.yml`:

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

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansible i Azure](/azure/ansible/)