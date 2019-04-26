---
title: Azure Service Fabric konfigurera omvänd proxy | Microsoft Docs
description: Förstå hur du skapar och konfigurerar Service Fabric omvänd proxy.
services: service-fabric
documentationcenter: na
author: jimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/13/2018
ms.author: v-jamebr
ms.openlocfilehash: 7f1b6f955dd3f59f6c17403b536cf99d666aab08
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60393018"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Installera och konfigurera omvänd proxy i Azure Service Fabric
Omvänd proxy är en valfri Azure Service Fabric-tjänst som hjälper dig att mikrotjänster som körs i ett Service Fabric-kluster identifiera och kommunicera med andra tjänster som har http-slutpunkter. Mer information finns i [omvänd proxy i Azure Service Fabric](service-fabric-reverseproxy.md). Den här artikeln visar hur du skapar och konfigurerar omvänd proxy i klustret. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Aktivera omvänd proxy med hjälp av Azure portal

Azure portal innehåller ett alternativ för att aktivera omvänd proxy när du skapar ett nytt Service Fabric-kluster. Du kan inte uppgradera ett befintligt kluster om du vill använda omvänd proxy via portalen. 

Konfigurera omvänd proxy när du [skapa ett kluster med Azure-portalen](./service-fabric-cluster-creation-via-portal.md), gör du följande:

1. I **steg 2: Klusterkonfiguration**under **konfiguration av nodtyp**väljer **aktivera omvänd proxy**.

   ![Aktivera omvänd proxy i portal](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. (Valfritt) Om du vill konfigurera säker omvänd proxy, måste du konfigurera ett SSL-certifikat. I **steg 3: Security**på **konfigurera säkerhetsinställningar för kluster**under **konfigurationstypen**väljer **anpassad**. Sedan, under **omvänd Proxy SSL-certifikat**väljer **innehåller ett SSL-certifikat för omvänd proxy** och ange information om ditt certifikat.

   ![Konfigurera säker omvänd proxy i portal](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Om du väljer att inte konfigurera omvänd proxy med ett certifikat när du skapar klustret, kan du göra det senare via Resource Manager-mall för klustrets resursgrupp. Mer information finns i [aktivera omvänd proxy via Azure Resource Manager-mallar](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Aktivera omvänd proxy via Azure Resource Manager-mallar

Du kan använda Azure Resource Manager-mallen för kluster i Azure för att aktivera omvänd proxy i Service Fabric. Du kan aktivera omvänd proxy när du skapar klustret eller aktivera det genom att uppdatera klustret vid ett senare tillfälle. 

För ett nytt kluster, kan du [skapar en anpassad mall i Resource Manager](service-fabric-cluster-creation-via-arm.md) eller så kan du använda en exempelmall. 

Du hittar exempel Resource Manager-mallar som kan hjälpa dig konfigurera säker omvänd proxy för ett Azure-kluster i den [säker omvänd Proxy-exempelmallar](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample) på GitHub. Referera till [konfigurera HTTPS omvänd Proxy i ett säkert kluster](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) i filen Viktigt för anvisningar och mallarna för att använda för att konfigurera säker omvänd proxy med ett certifikat och att hantera förnya certifikatet.

För ett befintligt kluster, kan du exportera Resource Manager-mallen för den klusterresursen gruppen med hjälp av den [Azure-portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell), eller [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli).

När du har en Resource Manager-mall kan aktivera du den omvända proxyn med följande steg:

1. Definiera en port för omvänd proxy i den [parameteravsnittet](../azure-resource-manager/resource-group-authoring-templates.md) för mallen.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Ange port för varje nodetype objekten i den [ **Microsoft.ServiceFabric/clusters** ](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [typ avsnittet](../azure-resource-manager/resource-group-authoring-templates.md).

    Porten som identifieras av parameternamn, reverseProxyEndpointPort.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Om du vill konfigurera SSL-certifikat på porten för omvänd proxy, lägga till certifikatet till den ***reverseProxyCertificate*** -egenskapen i den **Microsoft.ServiceFabric/clusters** [resurs Skriv avsnittet](../resource-group-authoring-templates.md).

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Stöd för ett certifikat för omvänd proxy som skiljer sig från klustercertifikatet
 Om certifikatet för omvänd proxy skiljer sig från det certifikat som skyddar klustret, bör sedan det tidigare angivna certifikatet vara installerad på den virtuella datorn och läggs till åtkomstkontrollistan (ACL) så att Service Fabric kan komma åt den. Detta kan göras den [ **Microsoft.Compute/virtualMachineScaleSets** ](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) [typ avsnittet](../resource-group-authoring-templates.md). Lägga till certifikatet i osProfile för installation. Tillägget avsnitt i mallen kan uppdatera certifikatet i Åtkomstkontrollistan.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> När du använder certifikat som skiljer sig från klustercertifikat för att aktivera omvänd proxy i ett befintligt kluster, installera certifikatet för omvänd proxy och uppdatera ACL i klustret innan du aktiverar omvänd proxy. Slutför den [Azure Resource Manager-mall](service-fabric-cluster-creation-via-arm.md) distribution med hjälp av inställningarna som vi nämnde tidigare innan du startar en distribution för att aktivera omvänd proxy i steg 1 – 3.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Aktivera omvänd proxy i fristående kluster

Fristående kluster kan aktivera du omvänd proxy i filen ClusterConfig.json. Du kan aktivera omvänd proxy i klustret har skapats eller genom att uppgradera konfigurationen för ett befintligt kluster. Läs mer om de tillgängliga inställningarna i ClusterConfig.json filer i [fristående klusterinställningar](./service-fabric-cluster-manifest.md).

Följande steg visar inställningarna för att använda för att aktivera omvänd proxy och eventuellt att skydda den omvända proxyn med ett X.509-certifikat. 

1. Om du vill aktivera omvänd proxy, ange den **reverseProxyEndpointPort** värde för nodtypen under **egenskaper** i konfigurationen för klustret. Följande JSON visar att ställa in den omvända proxyn slutpunktsport 19081 för noder med en typ av ”NodeType0”:

   ```json
       "properties": {
          ... 
           "nodeTypes": [
               {
                   "name": "NodeType0",
                   ...
                   "reverseProxyEndpointPort": "19081",
                   ...
               }
           ],
          ...
       }
   ```
2. (Valfritt) För en säker omvänd proxy, konfigurera ett certifikat i den **security** avsnittet **egenskaper**. 
   - För en miljö för utveckling eller testning, kan du använda den **ReverseProxyCertificate** inställningen:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificate": {
                          "Thumbprint": "[Thumbprint]",
                          "ThumbprintSecondary": "[Thumbprint]",
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```
   - För en produktionsmiljö, den **ReverseProxyCertificateCommonNames** inställningen rekommenderas:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificateCommonNames": {
                        "CommonNames": [
                            {
                              "CertificateCommonName": "[CertificateCommonName]"
                            }
                          ],
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```

   Läs mer om konfigurering och hantering av certifikat för ett fristående kluster, samt mer information om hur du konfigurerar certifikat som används för att skydda omvänd proxy i [X509 certifikatbaserad security](./service-fabric-windows-cluster-x509-security.md).

När du har ändrat din ClusterConfig.json-fil för att aktivera omvänd proxy, följer du anvisningarna i [uppgradera klusterkonfigurationen](service-fabric-cluster-config-upgrade-windows-server.md) till push-överföra ändringarna till ditt kluster.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Exponera omvänd proxy på en offentlig port via Azure Load Balancer

Ställ in Azure Load Balancer-regler och en Azure avsökning för omvänd proxy för att åtgärda den omvända proxyn från utanför ett Azure-kluster. De här stegen kan utföras med hjälp av Azure-portalen eller Resource Manager-mallen när som helst när du har skapat klustret. 

> [!WARNING]
> När du konfigurerar den omvända proxyn port i belastningsutjämnaren är alla mikrotjänster i klustret som Exponerar en HTTP-slutpunkt adresserbara från utanför klustret. Det innebär att mikrotjänster ska vara interna kanske kan identifieras av en bestämd skadliga användare. Detta medför potentiellt allvarliga säkerhetsrisker som kan utnyttjas; till exempel:
>
> * En obehörig användare kan starta en DoS-angrepp genom att anropa en intern tjänst som inte har en tillräckligt strikta attackyta upprepade gånger.
> * En obehörig användare kan leverera felaktiga paket till en intern tjänst, vilket resulterar i oönskat beteende.
> * En tjänst som är avsedd att vara intern kan returnera privat eller känslig information som är inte avsedd att vara exponerat för tjänster utanför klustret, därför exponera känslig information till en obehörig användare. 
>
> Kontrollera att helt förstå och minimera de potentiella aspekterna av säkerhet för ditt kluster och de appar som körs på den, innan du gör omvänd proxy offentliga. 
>

Om du vill exponera omvänd proxy offentligt för ett fristående kluster på sätt som du gör det beror på system som är värd för klustret och ligger utanför omfånget för den här artikeln. Föregående varningen om att exponera omvänd proxy offentligt, gäller dock fortfarande.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Exponera den omvända proxyn med hjälp av Azure portal 

1. Klicka på resursgruppen för ditt kluster på Azure-portalen och klicka sedan på belastningsutjämnaren för klustret.
2. Att lägga till en hälsotillstånd avsökning för omvänd proxy-port i den vänstra rutan i fönstret load balancer under **inställningar**, klickar du på **hälsoavsökningar**. Klicka sedan på **Lägg till** avsökningar fönstret överst på hälsotillståndet och ange information för omvänd proxy och klicka sedan på **OK**. Som standard är omvänd proxy 19081, om du inte ändrat det när du har skapat klustret.

   ![Konfigurera omvänd proxy hälsoavsökning](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Att lägga till en Load Balancer-regel för att exponera porten för omvänd proxy, i den vänstra rutan i fönstret load balancer under **inställningar**, klickar du på **belastningsutjämningsregler**. Klicka sedan på **Lägg till** överst i belastningen belastningsutjämning regler fönstret och ange information för omvänd proxy. Se till att ange den **Port** värdet till den port som du vill att den omvända proxyn exponerad på, den **serverdelsport** värdet till den port som du angav när du har aktiverat omvänd proxy och **hälsoavsökning** värdet till hälsoavsökning som du konfigurerade i föregående steg. Ange andra fält och klicka på **OK**.

   ![Konfigurera belastningsutjämningsregeln för omvänd proxy](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Exponera den omvända proxyn via Resource Manager-mallar

Följande JSON refererar till samma mall som används i [aktivera omvänd proxy via Azure Resource Manager-mallar](#enable-reverse-proxy-via-azure-resource-manager-templates). Se avsnittet i dokumentet för information om hur du skapar en Resource Manager-mall eller exportera en mall för ett befintligt kluster.  Ändringar till den [ **Microsoft.Network/loadBalancers** ](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) [typ avsnittet](../resource-group-authoring-templates.md).

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Anpassa omvänd proxy beteende med hjälp av fabric-inställningar

Du kan anpassa beteendet för omvänd proxy via fabric inställningarna i Resource Manager-mall för kluster som finns i Azure eller i filen ClusterConfig.json för fristående kluster. Inställningar som styr beteendet för omvänd proxy finns i den [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) i avsnittet den **fabricSettings** avsnittet under klustret **egenskaper** avsnittet. 

Du kan till exempel ange värdet för **DefaultHttpRequestTimeout** att ange timeout för begäranden till omvänd proxy till 180 sekunder som i följande JSON:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "DefaultHttpRequestTimeout",
                   "value": "180"
                 }
               ]
             }
           ],
           ...
   }
   ``` 

Mer information om att uppdatera infrastrukturinställningarna för Azure-kluster finns i [Anpassa inställningar för klustret med hjälp av Resource Manager-mallar](service-fabric-cluster-config-upgrade-azure.md). Fristående kluster, se [anpassa klusterinställningarna för fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md). 

Flera infrastrukturinställningarna används för att upprätta säker kommunikation mellan omvänd proxy och tjänster. Detaljerad information om dessa inställningar finns i [Anslut till en säker tjänst med omvänd proxy](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Nästa steg
* [Konfigurera vidarebefordran till säker HTTP-tjänsten med omvänd proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* Konfigurationsalternativ för omvänd proxy, se [ApplicationGateway/Http-avsnittet i inställningarna för Anpassa Service Fabric-kluster](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).
