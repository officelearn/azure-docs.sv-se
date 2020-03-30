---
title: Konfigurera nätverkslägen för behållartjänster
description: Lär dig hur du konfigurerar de olika nätverkslägen som stöds av Azure Service Fabric.
author: athinanthny
ms.topic: conceptual
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: ba1fa92559d39a481008d1dd18036e4232be1bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639810"
---
# <a name="service-fabric-container-networking-modes"></a>Nätverkslägen för Service Fabric-behållare

Ett Azure Service Fabric-kluster **nat** för behållartjänster använder nat-nätverksläge som standard. När mer än en behållartjänst lyssnar på samma port och nat-läge används kan distributionsfel uppstå. Service Fabric **erbjuder** öppet nätverksläge (version 5.7 och senare) för att stödja att flera behållartjänster lyssnar på samma port. I öppet läge har varje behållartjänst en intern, dynamiskt tilldelad IP-adress som stöder flera tjänster som lyssnar på samma port.  

Om du har en behållartjänst med en statisk slutpunkt i tjänstmanifestet kan du skapa och ta bort nya tjänster med hjälp av Öppet läge utan distributionsfel. Samma docker-compose.yml-fil kan också användas med statiska portmappningar för att skapa flera tjänster.

När en behållartjänst startas om eller flyttas till en annan nod i klustret ändras IP-adressen. Därför rekommenderar vi inte att du använder den dynamiskt tilldelade IP-adressen för att identifiera behållartjänster. Endast tjänst fabric-namngivningstjänsten eller DNS-tjänsten ska användas för identifiering av tjänsten. 

>[!WARNING]
>Azure tillåter totalt 65 356 IP-adresser per virtuellt nätverk. Summan av antalet noder och antalet behållartjänstinstanser (som använder öppet läge) kan inte överstiga 65 356 IP-adresser i ett virtuellt nätverk. För scenarier med hög densitet rekommenderar vi nat-nätverksläge. Dessutom har andra beroenden, till exempel belastningsutjämnaren, andra [begränsningar](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) att beakta. För närvarande har upp till 50 IPs per nod testats och visat sig vara stabila. 
>

## <a name="set-up-open-networking-mode"></a>Konfigurera öppet nätverksläge

1. Konfigurera Azure Resource Manager-mallen. Aktivera DNS-tjänsten och IP-providern i avsnittet **fabricSettings** i klusterresursen: 

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
    
2. Ställ in avsnittet nätverksprofil i skalauppsättningen för virtuella datorer. Detta gör att flera IP-adresser kan konfigureras på varje nod i klustret. I följande exempel anges fem IP-adresser per nod för ett Windows/Linux Service Fabric-kluster. Du kan ha fem tjänstinstanser som lyssnar på porten på varje nod. Om du vill att de fem IPs ska vara tillgängliga från Azure Load Balancer registrerar du de fem IPs i Azure Load Balancer Backend-adresspoolen som visas nedan.  Du måste också lägga till variablerna överst i mallen i variabelavsnittet.

    Lägg till det här avsnittet i Variabler:

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
    
    Lägg till det här avsnittet i skalauppsättningen för virtuella datorer:

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
 
3. Endast för Windows-kluster konfigurerar du en NSG-regel (Azure Network Security Group) som öppnar port UDP/53 för det virtuella nätverket med följande värden:

   |Inställning |Värde | |
   | --- | --- | --- |
   |Prioritet |2000 | |
   |Namn |Custom_Dns  | |
   |Källa |VirtualNetwork | |
   |Mål | VirtualNetwork | |
   |Tjänst | DNS (UDP/53) | |
   |Åtgärd | Tillåt  | |
   | | |

4. Ange nätverksläge i programmanifestet för `<NetworkConfig NetworkType="Open">`varje tjänst: . **Öppet** nätverksläge resulterar i att tjänsten får en dedikerad IP-adress. Om ett läge inte anges är tjänsten **nat** som standard nat-läge. I följande manifestexempel kan `NodeContainerServicePackage1` var och `NodeContainerServicePackage2` en av tjänsterna lyssna `Endpoint1`på samma port (båda tjänsterna lyssnar på). När öppna nätverksläge har `PortBinding` angetts kan det inte anges konfigurationer.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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

    Du kan blanda och matcha olika nätverkslägen mellan tjänster i ett program för ett Windows-kluster. Vissa tjänster kan använda öppet läge medan andra använder nat-läge. När en tjänst är konfigurerad för att använda nat-läge måste porten som tjänsten lyssnar på vara unik.

    >[!NOTE]
    >På Linux-kluster stöds inte blandningsnätverkslägen för olika tjänster. 
    >

5. När **läget Öppna** är markerat bör **slutpunktsdefinitionen** i tjänstmanifestet uttryckligen peka på det kodpaket som motsvarar slutpunkten, även om servicepaketet bara har ett kodpaket i sig. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. För Windows kommer en omstart av den virtuella datorn att återskapa det öppna nätverket. Detta för att minska ett underliggande problem i nätverksstacken. Standardbeteendet är att återskapa nätverket. Om det här problemet måste inaktiveras kan följande konfiguration användas följt av en konfigurationsuppgradering.

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
* [Läs mer om servicemanifestresurser för Tjänsten Service](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Distribuera en Windows-behållare till Service Fabric på Windows Server 2016](service-fabric-get-started-containers.md)
* [Distribuera en Docker-behållare till Service Fabric på Linux](service-fabric-get-started-containers-linux.md)
