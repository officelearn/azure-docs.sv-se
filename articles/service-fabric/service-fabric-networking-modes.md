---
title: "Konfigurera nätverk lägen för Azure Service Fabric-behållartjänster | Microsoft Docs"
description: "Lär dig hur du ställer in de olika lägena för nätverk som stöds av Azure Service Fabric."
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
ms.openlocfilehash: f8e3af4e183952aaac5a8320966aab035b90a1a7
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="service-fabric-container-networking-modes"></a>Service Fabric-behållaren nätverk lägen

Ett Azure Service Fabric-kluster för behållaren tjänster använder **nat** nätverk läge som standard. När flera behållartjänsten lyssnar på samma port och nat-läge som används, inträffa distributionsfel. För att stödja flera behållartjänster lyssnar på samma port, Service Fabric erbjuder **öppna** nätverk läge (version 5.7 och senare). I öppet läge har varje behållartjänsten en intern dynamiskt tilldelade IP-adress som har stöd för flera tjänster som lyssnar på samma port.  

Om du har en behållartjänsten med en statisk slutpunkt i ditt service manifest kan du skapa och ta bort nya tjänster med hjälp av öppningsläge utan distributionsfel. Samma docker-compose.yml-fil kan också användas med statisk portmappningar för att skapa flera tjänster.

När en behållartjänsten startar om eller flyttas till en annan nod i klustret, ändrar IP-adress. Därför rekommenderar vi inte använder dynamisk IP-adress för att upptäcka behållartjänster. Endast tjänsten Fabric Naming Service eller DNS-tjänsten ska användas för identifiering av tjänst. 

>[!WARNING]
>Azure tillåter att totalt 4 096 IP-adresser per virtuellt nätverk. Summan av antalet noder och antalet instanser av tjänsten behållare (som använder öppningsläge) får inte överstiga 4 096 IP-adresser inom ett virtuellt nätverk. För scenarier med hög densitet rekommenderar vi nätverk nat-läge.
>

## <a name="set-up-open-networking-mode"></a>Konfigurera nätverk öppningsläge

1. Ställ in Azure Resource Manager-mallen. I den **fabricSettings** aktiverar DNS-tjänsten och IP-Provider: 

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

2. Konfigurera avsnittet network profilen så att flera IP-adresser som ska konfigureras på varje nod i klustret. I följande exempel ställer in fem IP-adresser per nod för ett Windows-/ Linux Service Fabric-kluster. Du kan ha fem instanser av tjänsten lyssnar på port på varje nod.

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
 
3. För Windows-kluster, ställa in en regel för Azure Nätverkssäkerhetsgrupp (NSG) som öppnar porten UDP/53 för det virtuella nätverket med följande värden:

   |Inställning |Värde | |
   | --- | --- | --- |
   |Prioritet |2000 | |
   |Namn |Custom_Dns  | |
   |Källa |VirtualNetwork | |
   |Mål | VirtualNetwork | |
   |Tjänst | DNS (UDP/53) | |
   |Åtgärd | Tillåt  | |
   | | |

4. Ange det nätverk läget i programmanifestet för varje tjänst: `<NetworkConfig NetworkType="Open">`. **Öppna** nätverk läge leder till att tjänsten hämtar en dedicerad IP-adress. Om ett läge har inte angetts, tjänsten standard **nat** läge. I följande manifestet exempel den `NodeContainerServicePackage1` och `NodeContainerServicePackage2` tjänster kan varje lyssna på samma port (båda tjänsterna lyssnar på `Endpoint1`). När nätverk öppningsläge anges `PortBinding` konfigurationer kan inte anges.

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
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```

    Du kan blanda och matcha olika lägen för nätverk för tjänster i ett program för Windows-kluster. Vissa tjänster kan använda öppet läge medan andra använder nat-läge. När en tjänst är konfigurerad för att använda nat-läge, måste som tjänsten lyssnar på porten vara unika.

    >[!NOTE]
    >På Linux-kluster stöds blanda nätverk lägen för olika tjänster inte. 
    >

## <a name="next-steps"></a>Nästa steg
* [Förstå Service Fabric-programmodellen](service-fabric-application-model.md)
* [Lär dig mer om Service Fabric service manifest resurser](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-manifest-resources)
* [Distribuera en Windows-behållare till Service Fabric på Windows Server 2016](service-fabric-get-started-containers.md)
* [Distribuera en dockerbehållare till Service Fabric på Linux](service-fabric-get-started-containers-linux.md)
