---
title: Hantera webbtrafik med Azure Application Gateway med hjälp av Ansible (förhandsversion)
description: Lär dig hur du använder Ansible för att skapa och konfigurera en Azure Application Gateway för att hantera webbtrafik
ms.service: ansible
keywords: ansible, azure, devops, bash, spelbok, azure application gateway, belastningsutjämnare, webbtrafik
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: e3c165c87d6c179141f2ddd44f00f0f62a84b285
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50912874"
---
# <a name="manage-web-traffic-with-azure-application-gateway-by-using-ansible-preview"></a>Hantera webbtrafik med Azure Application Gateway med hjälp av Ansible (förhandsversion)

[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/) är en lastbalanserare för webbtrafik som gör det möjligt för dig att hantera trafik till dina webbappar.

Ansible hjälper till att automatisera distributionen och konfigurationen av resurser i din miljö. Den här artikeln visar hur du använder Ansible för att skapa en programgateway. Du får även lära dig att använda gatewayen för att hantera trafik till två webbservrar som körs i Azure-containerinstanser.

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa två Azure-containerinstanser med HTTPD-avbildningar
> * Skapa en programgateway som körs med Azure-containerinstanserna i serverdelspoolen

## <a name="prerequisites"></a>Nödvändiga komponenter

- **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.7 krävs för att köra följande exempelspelböcker i den här självstudien. Du kan installera Ansible 2.7 RC genom att köra `sudo pip install ansible[azure]==2.7.0rc2`. När Ansible 2.7 har släppts behöver du inte ange versionen.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.  

I följande exempel skapas en resursgrupp med namnet **myResourceGroup** på platsen **eastus**.

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

Spara den här spelboken som *rg.yml*. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Skapa nätverksresurser

Skapa först ett virtuellt nätverk så att programgatewayen kan kommunicera med andra resurser.

I följande exempel skapar vi ett virtuellt nätverk med namnet **myVNet**, ett undernät med namnet **myAGSubnet** och en offentlig IP-adress med namnet **myAGPublicIPAddress** med en domän med namnet **mydomain**.

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

Spara den här spelboken som *vnet_create.yml*. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Skapa servrar

Följande exempel visar hur du skapar två Azure-containerinstanser med HTTPD-avbildningar som ska användas som webbservrar för programgatewayen.  

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

Spara den här spelboken som *aci_create.yml*. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

Följande exempel skapar en programgateway med namnet **myAppGateway** med konfigurationer för serverdel, klientdel och HTTP.  

* **appGatewayIP** definieras i blocket **gateway_ip_configurations**. Det krävs en undernätsreferens för gatewayens IP-konfiguration.
* **appGatewayBackendPool** definieras i blocket **backend_address_pools**. En programgateway måste ha minst en serverdelsadresspool.
* **appGatewayBackendHttpSettings** definieras i blocket **backend_http_settings_collection**. Det anger att port 80 och ett HTTP-protokoll används för kommunikation.
* **appGatewayHttpListener** definieras i blocket **backend_http_settings_collection**. Det är den standardlyssnare som är associerad med appGatewayBackendPool.
* **appGatewayFrontendIP** definieras i blocket **frontend_ip_configurations**. Det tilldelar myAGPublicIPAddress till appGatewayHttpListener.
* **rule1** definieras i blocket **request_routing_rules**. Det är den standardhanteringsregel som är associerad med appGatewayHttpListener.

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

Spara den här spelboken som *appgw_create.yml*. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:

```bash
ansible-playbook appgw_create.yml
```

Det kan ta flera minuter att skapa programgatewayen.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

I exempelspelboken för nätverksresurser skapade du domänen **mydomain** i **eastus**. Gå till `http://mydomain.eastus.cloudapp.azure.com` i webbläsaren. Om du ser följande sida fungerar programgatewayen som förväntat.

![Ett lyckat test av en fungerande programgateway](media/ansible-create-configure-application-gateway/applicationgateway.PNG)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver de här resurserna kan du ta bort dem genom att köra följande kod. Exemplet tar bort en resursgrupp med namnet **myResourceGroup**.

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

Spara den här spelboken som *rg_delete*.yml. Om du vill köra spelboken använder du kommandot **ansible-playbook** så här:

```bash
ansible-playbook rg_delete.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansible i Azure](https://docs.microsoft.com/azure/ansible/)
