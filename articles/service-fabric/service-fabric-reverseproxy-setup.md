---
title: Azure Service Fabric konfigurera omvänd proxy
description: Förstå hur du konfigurerar och konfigurerar den omvända proxytjänsten för ett Azure Service Fabric-program.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: pepogors
ms.openlocfilehash: 131440036896d323cbf821d7a220328456e1db36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645454"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Konfigurera omvänd proxy i Azure Service Fabric
Omvänd proxy är en valfri Azure Service Fabric-tjänst som hjälper mikrotjänster som körs i ett Service Fabric-kluster att identifiera och kommunicera med andra tjänster som har http-slutpunkter. Mer information finns [i Omvänd proxy i Azure Service Fabric](service-fabric-reverseproxy.md). Den här artikeln visar hur du konfigurerar omvänd proxy i klustret. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Aktivera omvänd proxy med Azure-portalen

Azure-portalen är ett alternativ för att aktivera omvänd proxy när du skapar ett nytt Service Fabric-kluster. Du kan inte uppgradera ett befintligt kluster för att använda omvänd proxy via portalen. 

Om du vill konfigurera omvänd proxy när du [skapar ett kluster med Azure-portalen](./service-fabric-cluster-creation-via-portal.md)kontrollerar du att du gör följande:

1. I **steg 2: Klusterkonfiguration**väljer du **Aktivera omvänd proxy**under **Nodtyp.**

   ![Aktivera omvänd proxy på portalen](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. (Valfritt) Om du vill konfigurera säker omvänd proxy måste du konfigurera ett SSL-certifikat. I **steg 3: Säkerhet**väljer du **Anpassad**under Konfigurationstyp under **Configure cluster security settings** **Konfigurationstyp**. Välj sedan **Inkludera ett SSL-certifikat för omvänd proxy** under Omvänd **proxy-SSL-certifikat**och ange dina certifikatuppgifter.

   ![Konfigurera säker omvänd proxy på portalen](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Om du väljer att inte konfigurera den omvända proxyn med ett certifikat när du skapar klustret kan du göra det senare via resource manager-mallen för klustrets resursgrupp. Mer information finns i [Aktivera omvänd proxy via Azure Resource Manager-mallar](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Aktivera omvänd proxy via Azure Resource Manager-mallar

För kluster på Azure kan du använda Azure Resource Manager-mallen för att aktivera omvänd proxy i Service Fabric. Du kan aktivera omvänd proxy när du skapar klustret eller aktiverar det genom att uppdatera klustret vid ett senare tillfälle. 

För ett nytt kluster kan du [skapa en anpassad Resource Manager-mall](service-fabric-cluster-creation-via-arm.md) eller använda en exempelmall. 

Du hittar exempel på Resource Manager-mallar som kan hjälpa dig att konfigurera säker omvänd proxy för ett Azure-kluster i [exempelmallarna för omvänd omvänd proxy](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample) på GitHub. Se [Konfigurera HTTPS Reverse Proxy i ett säkert kluster](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) i README-filen för instruktioner och mallar som ska användas för att konfigurera säker omvänd proxy med ett certifikat och för att hantera certifikatöverrullning.

För ett befintligt kluster kan du exportera Resource Manager-mallen för klustrets resursgrupp med hjälp av [Azure-portalen,](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell)eller [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli).

När du har en Resource Manager-mall kan du aktivera omvänd proxy med följande steg:

1. Definiera en port för omvänd proxy i [avsnittet Parametrar](../azure-resource-manager/templates/template-syntax.md) i mallen.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Ange porten för vart och ett av nodtyperna i avsnittet [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [Resource type](../azure-resource-manager/templates/template-syntax.md).

    Porten identifieras med parameternamnet reverseProxyEndpointPort.

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
3. Om du vill konfigurera SSL-certifikat på porten för omvänd proxy lägger du till certifikatet i egenskapen ***reverseProxyCertificate*** i avsnittet **Microsoft.ServiceFabric/clusters** [Resource type](../resource-group-authoring-templates.md).

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

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Stöd för ett omvänt proxycertifikat som skiljer sig från klustercertifikatet
 Om det omvända proxycertifikatet skiljer sig från certifikatet som skyddar klustret, ska det tidigare angivna certifikatet installeras på den virtuella datorn och läggas till i åtkomstkontrollistan (ACL) så att Service Fabric kan komma åt det. Detta kan göras i avsnittet [**Microsoft.Compute/virtualMachineScaleSets**](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) [Resource type](../resource-group-authoring-templates.md). För installation lägger du till certifikatet i osProfile. Avsnittet tillägg i mallen kan uppdatera certifikatet i åtkomstkontrollistan.

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
> När du använder certifikat som skiljer sig från klustercertifikatet för att aktivera omvänd proxy i ett befintligt kluster installerar du det omvända proxycertifikatet och uppdaterar åtkomstkontrollistan i klustret innan du aktiverar omvänd proxy. Slutför [Azure Resource Manager-malldistributionen](service-fabric-cluster-creation-via-arm.md) med hjälp av de inställningar som nämndes tidigare innan du startar en distribution för att aktivera omvänd proxy i steg 1-3.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Aktivera omvänd proxy på fristående kluster

För fristående kluster aktiverar du omvänd proxy i filen ClusterConfig.json. Du kan aktivera omvänd proxy när klustret skapas eller genom att uppgradera konfigurationen för ett befintligt kluster. Mer information om de inställningar som är tillgängliga i ClusterConfig.json-filer finns i [Fristående klusterinställningar](./service-fabric-cluster-manifest.md).

I följande steg visas de inställningar som ska användas för att aktivera omvänd proxy och, eventuellt, för att skydda den omvända proxyn med ett X.509-certifikat. 

1. Om du vill aktivera omvänd proxy anger du värdet **reverseProxyEndpointPort** för nodtypen under **egenskaper** i klusterkonfigurationen. Följande JSON visar inställningen av den omvända proxyslutpunktsporten till 19081 för noder med typen "NodeType0":

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
2. (Valfritt) Konfigurera ett certifikat i **säkerhetsavsnittet** under **egenskaper**för en säker omvänd proxy. 
   - För en utvecklings- eller testmiljö kan du använda inställningen **ReverseProxyCertificate:**

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
   - För en produktionsmiljö rekommenderas inställningen **ReverseProxyCertificateCommonNamess:**

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

   Mer information om hur du konfigurerar och hanterar certifikat för ett fristående kluster och mer information om hur du konfigurerar certifikat som används för att skydda omvänd proxy finns i [X509 certifikatbaserad säkerhet](./service-fabric-windows-cluster-x509-security.md).

När du har ändrat filen ClusterConfig.json för att aktivera omvänd proxy följer du instruktionerna i [Uppgradera klusterkonfigurationen](service-fabric-cluster-config-upgrade-windows-server.md) för att skicka ändringarna till klustret.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Exponera omvänd proxy på en offentlig port via Azure Load Balancer

Om du vill åtgärda den omvända proxyn från utanför ett Azure-kluster konfigurerar du Azure Load Balancer-regler och en Azure Health Probe för den omvända proxyporten. Dessa steg kan utföras med hjälp av Azure-portalen eller Resource Manager-mallen när som helst efter att du har skapat klustret. 

> [!WARNING]
> När du konfigurerar den omvända proxyporten i Belastningsutjämnaren är alla mikrotjänster i klustret som exponerar en HTTP-slutpunkt adresserbara utanför klustret. Det innebär att mikrotjänster som är avsedda att vara interna kan upptäckas av en bestämd obehörig användare. Detta kan medföra allvarliga sårbarheter som kan utnyttjas. till exempel:
>
> * En obehörig användare kan starta en denial of service-attack genom att upprepade gånger anropa en intern tjänst som inte har en tillräckligt härdad attackyta.
> * En obehörig användare kan leverera felformaterade paket till en intern tjänst som resulterar i oavsiktligt beteende.
> * En tjänst som är avsedd att vara intern kan returnera privat eller känslig information som inte är avsedd att exponeras för tjänster utanför klustret, vilket innebär att den här känsliga informationen exponeras för en obehörig användare. 
>
> Se till att du till fullo förstår och mildrar de potentiella säkerhetskonsekvenserna för klustret och apparna som körs på det, innan du gör den omvända proxyporten offentlig. 
>

Om du vill exponera omvänd proxy offentligt för ett fristående kluster beror det sätt på vilket du gör det på vilket system som är värd för klustret och ligger utanför den här artikelns omfattning. Den föregående varningen om att exponera omvänd proxy offentligt gäller dock fortfarande.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Exponera omvänd proxy med Azure-portalen 

1. Klicka på resursgruppen för klustret på Azure-portalen och klicka sedan på belastningsutjämnaren för klustret.
2. Om du vill lägga till en hälsoavsökning för den omvända proxyporten klickar du på **Hälsoavsökningar**i den vänstra rutan i belastningsutjämnarfönstret under **INSTÄLLNINGAR**. Klicka sedan på **Lägg till** högst upp i fönstret Hälsa avsökningar och ange information om den omvända proxyporten och klicka sedan på **OK**. Som standard är den omvända proxyporten 19081, såvida du inte ändrade den när du skapade klustret.

   ![Konfigurera hälsoavsökning för omvänd proxy](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Om du vill lägga till en belastningsutjämnad regel för att visa den omvända proxyporten klickar du på **Belastningsutjämningsregler**i den vänstra rutan i belastningsutjämnarfönstret under **INSTÄLLNINGAR.** Klicka sedan på **Lägg till** högst upp i fönstret Belastningsutjämningsregler och ange information om den omvända proxyporten. Se till att du ställer in **portvärdet** på den port som du vill att omvänd proxy ska exponeras på, **serverdportvärdet** till den port som du angav när du aktiverade omvänd proxy och **hälsoavsökningsvärdet** till hälsoavsökningen som du konfigurerade i föregående steg. Ange andra fält efter behov och klicka på **OK**.

   ![Konfigurera belastningsutjämnadsregel för omvänd proxy](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Exponera omvänd proxy via Resource Manager-mallar

Följande JSON refererar till samma mall som används i [Aktivera omvänd proxy via Azure Resource Manager-mallar](#enable-reverse-proxy-via-azure-resource-manager-templates). I det avsnittet i dokumentet finns information om hur du skapar en Resource Manager-mall eller exporterar en mall för ett befintligt kluster.  Ändringarna görs i avsnittet [**Microsoft.Network/loadBalancers**](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) [Resource type](../resource-group-authoring-templates.md).

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


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Anpassa omvänt proxybeteende med hjälp av inställningarna för tyg

Du kan anpassa beteendet för omvänd proxy via infrastrukturinställningar i resource manager-mallen för kluster som finns i Azure eller i Filen ClusterConfig.json för fristående kluster. Inställningar som styr omvänd proxyfunktion finns i avsnittet [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) i avsnittet **fabricSettings** under avsnittet **klusteregenskaper.** 

Du kan till exempel ange värdet för **StandardhttpRequestTimeout** för att ställa in tidsgränsen för begäranden till omvänd proxy till 180 sekunder som i följande JSON:

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

Mer information om hur du uppdaterar infrastrukturinställningar för Azure-kluster finns i [Anpassa klusterinställningar med Hjälp av Resource Manager-mallar](service-fabric-cluster-config-upgrade-azure.md). För fristående kluster finns i [Anpassa klusterinställningar för fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md). 

Flera infrastrukturinställningar används för att skapa säker kommunikation mellan omvänd proxy och tjänster. Detaljerad information om dessa inställningar finns i [Ansluta till en säker tjänst med omvänd proxy](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Nästa steg
* [Konfigurera vidarebefordran för att skydda HTTP-tjänsten med omvänd proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* Alternativ för omvänd proxykonfiguration finns i [avsnittet ApplicationGateway/Http i Anpassa klusterinställningar för Service Fabric](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).
