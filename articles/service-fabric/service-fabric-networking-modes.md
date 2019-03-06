---
title: Konfigurera nätverk lägen för Azure Service Fabric-behållartjänster | Microsoft Docs
description: Lär dig hur du ställer in olika nätverk lägen som stöds av Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: aljo, subramar
ms.openlocfilehash: cc4860c43b6d1a1f5b89060a3d3045df804152c0
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404424"
---
# <a name="service-fabric-container-networking-modes"></a>Nätverkslägen för behållare för Service Fabric

Ett Azure Service Fabric-kluster för container services använder **nat** nätverk läge som standard. När mer än en behållartjänst lyssnar på samma port och nat-läge som används, kan det uppstå distributionsfel. För att stödja flera behållartjänster som lyssnar på samma port, Service Fabric erbjuder **öppna** nätverk läge (version 5.7 och senare). I öppet läge har varje behållartjänst en intern dynamiskt tilldelade IP-adress som har stöd för flera tjänster som lyssnar på samma port.  

Om du har ett container service med en statisk slutpunkt i din tjänstmanifestet kan du skapa och ta bort nya tjänster med hjälp av öppet läge utan distributionsfel. Samma docker-compose.yml-fil kan också användas med statisk port-mappningar för att skapa flera tjänster.

När en behållartjänst startar om eller flyttas till en annan nod i klustret, ändras IP-adressen. Därför rekommenderar vi inte använder dynamiskt tilldelade IP-adressen för att upptäcka behållartjänster. Endast den namngivning i Service Fabric- eller DNS-tjänsten ska användas för identifiering av tjänst. 

>[!WARNING]
>Azure kan totalt 65,356 IP-adresser per virtuellt nätverk. Summan av antalet noder och antalet container service-instanser (som använder öppet läge) får inte överskrida 65,356 IP-adresser inom ett virtuellt nätverk. Högdensitet scenarier rekommenderar vi nat nätverk läge. Dessutom kan andra beroenden, till exempel belastningsutjämnaren har andra [begränsningar](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits) att tänka på. För närvarande upp till 50 IP-adresser per nod har testats och verifierats stabil. 
>

## <a name="set-up-open-networking-mode"></a>Konfigurera öppna nätverk läge

1. Ställ in Azure Resource Manager-mallen. I den **fabricSettings** avsnittet klusterresursen, aktiverar DNS-tjänsten och IP-providern: 

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
    
2. Konfigurera nätverk profilavsnittet för Virtual Machine Scale Sets-resursen. På så sätt kan flera IP-adresser som ska konfigureras på varje nod i klustret. I följande exempel ställer in fem IP-adresser per nod för Windows/Linux Service Fabric-kluster. Du kan ha fem instanser av tjänsten lyssnar på port på varje nod. Registrera fem IP-adresser i Azure Load Balancer-Serverdelsadresspool för att du har fem IP-adresser som är tillgänglig från Azure Load Balancer, som visas nedan.  Du måste också lägga till variabler längst upp i din mall i variables-avsnittet.

    Lägg till det här avsnittet i variabler:

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
    ```
    
    Lägg till det här avsnittet till resursen i Virtual Machine Scale Sets:

    ```json   
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
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
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
 
3. Windows-kluster kan du konfigurera en regel för Azure Nätverkssäkerhetsgrupp (NSG) som öppnas port UDP/53 för det virtuella nätverket med följande värden:

   |Inställning |Värde | |
   | --- | --- | --- |
   |Prioritet |2000 | |
   |Namn |Custom_Dns  | |
   |Källa |VirtualNetwork | |
   |Mål | VirtualNetwork | |
   |Tjänst | DNS (UDP/53) | |
   |Åtgärd | Tillåt  | |
   | | |

4. Ange nätverk i applikationsmanifestet för varje tjänst: `<NetworkConfig NetworkType="Open">`. **Öppna** nätverk läge resultat i tjänsten hämtar en dedikerad IP-adress. Om ett läge inte har angetts, tjänsten som standard **nat** läge. I följande manifest exempel den `NodeContainerServicePackage1` och `NodeContainerServicePackage2` tjänster kan varje lyssnar på samma port (båda tjänsterna lyssnar på `Endpoint1`). När du anger öppet nätverk läge `PortBinding` konfigurationer kan inte anges.

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

    Du kan blanda och matcha olika lägen för nätverk för tjänster i ett program för ett Windows-kluster. Vissa tjänster kan använda öppet läge medan andra använder nat-läge. När en tjänst är konfigurerad för att använda nat-läge, måste som tjänsten lyssnar på porten vara unikt.

    >[!NOTE]
    >I Linux-kluster stöds blanda nätverkslägen för olika tjänster inte. 
    >

5. När den **öppna** läge väljs den **Endpoint** definition i tjänstmanifestet uttryckligen måste peka på kodpaketet som motsvarar slutpunkten, även om tjänstpaketet har endast en kod paketet i den. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. För Windows, gör en omstart av virtuell dator att öppna nätverks återskapas. Det här är att minimera underliggande problemet i nätverksstacken. Standardinställningen är att återskapa nätverket. Om det här beteendet måste stängas av, kan följande konfiguration användas följt av en config-uppgradering.

```json
"fabricSettings": [
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "SkipContainerNetworkResetOnReboot",
                            "value": "true"
                    }
                    ]
                }
            ],          
 ``` 
 
## <a name="next-steps"></a>Nästa steg
* [Förstå Service Fabric-programmodellen](service-fabric-application-model.md)
* [Läs mer om Service Fabric tjänstmanifestresurser](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Distribuera en Windows-behållare till Service Fabric i Windows Server 2016](service-fabric-get-started-containers.md)
* [Distribuera en Docker-behållare till Service Fabric i Linux](service-fabric-get-started-containers-linux.md)
