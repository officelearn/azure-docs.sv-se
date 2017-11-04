---
title: "Konfigurera nätverk lägen för Azure Service Fabric-behållartjänster | Microsoft Docs"
description: "Lär dig hur du ställer in de olika lägena för nätverk som har stöd för Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 1ecded3af6396f50e67dc5d2a9ef8337699046ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-container-networking-modes"></a>Service Fabric-behållaren nätverk lägen

Nätverk läge erbjuds i Service Fabric-klustret för behållartjänster som standard används den `nat` nätverk läge. Med den `nat` nätverk läge, med mer än en behållare tjänsten lyssnar på samma port resultat i distributionsfel. För att lyssna på samma port med flera tjänster, Service Fabric stöder den `open` nätverk läge (version 5.7 eller högre). Med den `open` nätverk läge varje behållartjänst hämtar en dynamiskt tilldelad IP-adress som internt så att flera tjänster att lyssna på samma port.   

Därför med en enda typ med en statisk slutpunkt som definierats i service manifest nya tjänster kan skapas och tas bort utan distributionsfel med hjälp av den `open` nätverk läge. På liknande sätt kan använda samma `docker-compose.yml` fil med statisk portmappningar för att skapa flera tjänster.

Med dynamiskt tilldelade IP-Adressen för att identifiera tjänster inte rekommenderas eftersom IP-adress ändras när tjänsten startas om eller flyttas till en annan nod. Använd bara den **namngivningstjänst för Service Fabric** eller **DNS-tjänsten** för identifiering av tjänst. 


> [!WARNING]
> Endast totalt 4096 IP-adresser tillåts per virtuellt nätverk i Azure. Därför summan av antalet noder och antalet behållare tjänstinstanser (med `open` nätverk) får inte överstiga 4096 inom ett vNET. Dessa scenarier med hög densitet i `nat` nätverk läge rekommenderas.
>

## <a name="setting-up-open-networking-mode"></a>Konfigurera nätverk öppningsläge

1. Konfigurera Azure Resource Manager-mallen genom att aktivera DNS-tjänsten och IP-providern under `fabricSettings`. 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name":  "Trace/Etw", 
                    "parameters": [
                    {
                            "name": "Level",
                            "value": "5"
                    }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```

2. Konfigurera avsnittet network profilen så att flera IP-adresser som ska konfigureras på varje nod i klustret. I följande exempel ställer in fem IP-adresser per nod (du kan därför ha fem instanser av tjänsten lyssnar på porten på varje nod) för ett Windows-/ Linux Service Fabric-kluster.

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 

3. För Windows-kluster, ställa in en NSG regel öppna port UDP/53 för vNET med följande värden:

   | Prioritet |    Namn    |    Källa      |  Mål   |   Tjänst    | Åtgärd |
   |:--------:|:----------:|:--------------:|:--------------:|:------------:|:------:|
   |     2000 | Custom_Dns | VirtualNetwork | VirtualNetwork | DNS (UDP/53) | Tillåt  |


4. Ange det nätverk läget i appmanifestet för varje tjänst `<NetworkConfig NetworkType="open">`.  Läget `open` resulterar i tjänsten hämtar en dedicerad IP-adress. Om ett läge inte anges används som standard grundläggande `nat` läge. Därför i manifestet exemplet `NodeContainerServicePackage1` och `NodeContainerServicePackage2` kan varje lyssna på samma port (båda tjänsterna lyssnar på `Endpoint1`).

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="open"/>
           <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="open"/>
            <PortBinding ContainerPort="8910" EndpointRef="Endpoint1"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```
Du kan blanda och matcha olika lägen för nätverk för tjänster i ett program för Windows-kluster. Du kan därför ha vissa tjänster på `open` läge och vissa på `nat` nätverk läge. När en tjänst har konfigurerats med `nat`, den lyssnar på porten måste vara unika. Blanda nätverk lägen för olika tjänster stöds inte på Linux-kluster. 


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig om networking lägen som erbjuds av Service Fabric.  

* [Service Fabric programmodell](service-fabric-application-model.md)
* [Service Fabric service manifest resurser](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-manifest-resources)
* [Distribuera en Windows-behållare till Service Fabric på Windows Server 2016](service-fabric-get-started-containers.md)
* [Distribuera en dockerbehållare till Service Fabric på Linux](service-fabric-get-started-containers-linux.md)
