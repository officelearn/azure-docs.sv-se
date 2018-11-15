---
title: Hantera certifikat i ett Azure Service Fabric-kluster | Microsoft Docs
description: Beskriver hur du lägger till nya certifikat, förnyelse av certifikat och ta bort certifikat till eller från ett Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2018
ms.author: aljo-microsoft
ms.openlocfilehash: aa5096b84f9bfe97784d6f80e4c203a1d8384404
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51687426"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Lägga till eller ta bort certifikat för Service Fabric-kluster i Azure
Vi rekommenderar att du bekanta dig med hur Service Fabric använder X.509-certifikat och att du läser den [Klustersäkerhetsscenarier](service-fabric-cluster-security.md). Du måste förstå vad ett klustercertifikat är och vad som används för, innan du forsätter.

Azure Service fabric SDK: ns certifikat belastningen standardbeteendet, är att distribuera och använda ett definierade certifikat med ett utgående datum längst bort i framtiden. oavsett deras primära eller sekundära configuration-definition. Återgång till klassiska beteendet är en icke rekommenderad avancerad åtgärd och kräver false i Fabric.Code konfigurationen att ställa in värdet för parametern ”UseSecondaryIfNever” inställningen.

Service fabric kan du ange två klustercertifikat, en primär och en sekundär när du konfigurerar Certifikatsäkerhet när klustret skapas, förutom klientcertifikat. Referera till [skapar ett azure-kluster via portalen](service-fabric-cluster-creation-via-portal.md) eller [skapar ett azure-kluster via Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) för information om hur du ställer in dem på Skapa tid. Om du anger bara ett klustercertifikat på Skapa tid, som används som primär certifikatet. När klustret har skapats, kan du lägga till ett nytt certifikat som en sekundär.

> [!NOTE]
> För ett säkert kluster behöver alltid du minst en giltig (inte återkallat och inte har upphört att gälla) kluster certifikat (primära eller sekundära) som distribuerats (om inte, de klustret slutar fungera). 90 dagar innan alla giltiga certifikat når upphör att gälla, genereras en varning-spårning och en varning hälsohändelse på noden. Det finns för närvarande ingen e-post eller andra meddelande som Service Fabric skickar i den här artikeln. 
> 
> 

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Lägg till ett sekundärt klustercertifikat med hjälp av portalen
Sekundära klustercertifikat kan inte läggas till via Azure portal, Använd Azure powershell. Processen beskrivs senare i det här dokumentet.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Ta bort ett klustercertifikat med hjälp av portalen
Ett säkert kluster behöver du alltid minst ett giltigt certifikat för (inte återkallat och inte har upphört att gälla). Certifikat som distribuerats med längst fram till det utgående datumet i framtiden kommer att används och tar bort meddelandet blir din klustret slutar att fungera; Se till att endast ta bort det certifikat som har upphört att gälla eller ett oanvända certifikat som upphör att gälla den tidigast.

Ta bort en oanvända klustersäkerhetscertifikatet, gå till avsnittet säkerhet och välj alternativet ”Ta bort” på snabbmenyn på oanvända certifikat.

Om din avsikt är att ta bort det certifikat som har markerats primära, måste du distribuerar ett sekundärt certifikat med ytterligare ett utgående datum i framtiden än det primära certifikatet att aktivera automatisk förnyelse av beteendet; ta bort det primära certifikatet när automatisk förnyelse har slutförts.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Lägg till ett sekundärt certifikat med hjälp av Powershell och Resource Manager
> [!TIP]
> Det är nu bättre och enklare sätt att lägga till ett sekundärt certifikat som använder den [Add-AzureRmServiceFabricClusterCertificate](/powershell/module/azurerm.servicefabric/add-azurermservicefabricclustercertificate) cmdlet. Du behöver inte följa resten av stegen i det här avsnittet.  Dessutom behöver inte den mall som ursprungligen använde för att skapa och distribuera klustret när du använder den [Add-AzureRmServiceFabricClusterCertificate](/powershell/module/azurerm.servicefabric/add-azurermservicefabricclustercertificate) cmdlet.

De här stegen förutsätter att du är bekant med så här fungerar Resource Manager och har distribuerat minst en Service Fabric-kluster med en Resource Manager-mall och har den mall som du använde för att konfigurera klustret till hands. Det förutsätts även att du är nöjd med JSON.

> [!NOTE]
> Om du letar efter en exempelmall och parametrar som du kan använda för att följa längs eller som en startpunkt, ladda ned det från den här [git-lagringsplats](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Redigera Resource Manager-mall

För att underlätta följa med innehåller exempel 5-VM-1-NodeTypes-Secure_Step2.JSON alla ändringar som vi kommer att göra. exemplet finns på [git-lagringsplats](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

**Se till att följa alla steg**

1. Öppna Resource Manager-mallen du använde för att distribuera du kluster. (Om du har hämtat exemplet från föregående lagringsplatsen, sedan distribuera ett säkert kluster med hjälp av 5-VM-1-NodeTypes-Secure_Step1.JSON och sedan öppna mallen).

2. Lägg till **två nya parametrar** ”secCertificateThumbprint” och ”secCertificateUrlValue” av skriver du ”string” i parameteravsnittet av mallen. Du kan kopiera följande kodfragment och lägga till den i mallen. Beroende på källan för din mall, kan du redan har dessa definierats, om så flytta till nästa steg. 
 
    ```json
       "secCertificateThumbprint": {
          "type": "string",
          "metadata": {
            "description": "Certificate Thumbprint"
          }
        },
        "secCertificateUrlValue": {
          "type": "string",
          "metadata": {
            "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
          }
        },
    
    ```

3. Gör ändringar i den **Microsoft.ServiceFabric/clusters** -resurs – Leta upp resursdefinitionen ”Microsoft.ServiceFabric/clusters” i mallen. Under Egenskaper för den definitionen hittar du ”certifikat” JSON-tagg som bör se ut ungefär som följande JSON-kodfragment:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Lägg till en ny tagg ”thumbprintSecondary” och ge den värdet ”[parameters('secCertificateThumbprint')]”.  

    Nu resursdefinitionen bör se ut som följande (beroende på din källa för mallen, den kanske inte är likadant som kodavsnittet nedan). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Om du vill **förnyar certifikatet**, ange nytt certifikat som primär och flytta den aktuella primärt som sekundär. Detta resulterar i förnyelse av din aktuella primära certifikatet till det nya certifikatet i ett distributionssteg.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Gör ändringar i **alla** den **Microsoft.Compute/virtualMachineScaleSets** resursdefinitionerna - hitta Microsoft.Compute/virtualMachineScaleSets resursdefinitionen. Bläddra till ”publisher”: ”Microsoft.Azure.ServiceFabric” under ”virtualMachineProfile”.

    Du bör se ut så här i inställningarna för Service Fabric-utgivare.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Lägga till de nya cert-posterna
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    Egenskaperna bör nu se ut så här
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Om du vill **förnyar certifikatet**, ange nytt certifikat som primär och flytta den aktuella primärt som sekundär. Detta resulterar i förnyelse av din aktuella certifikatet till det nya certifikatet i ett distributionssteg.     

    ```json
                   "certificate": {
                       "thumbprint": "[parameters('secCertificateThumbprint')]",
                       "x509StoreName": "[parameters('certificateStoreValue')]"
                         },
                   "certificateSecondary": {
                        "thumbprint": "[parameters('certificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    ```

    Egenskaperna bör nu se ut så här    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Gör ändringar i **alla** den **Microsoft.Compute/virtualMachineScaleSets** resursdefinitionerna - hitta Microsoft.Compute/virtualMachineScaleSets resursdefinitionen. Bläddra till ”vaultCertificates”:, under ”OSProfile”. Det bör se ut ungefär så här.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Lägg till secCertificateUrlValue till den. Använd följande kodavsnitt:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Nu den resulterande Json bör se ut ungefär så här.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Kontrollera att du har upprepas steg 4 och 5 för alla definitioner för Nodetypes/Microsoft.Compute/virtualMachineScaleSets-resurs i mallen. Om du missar något av dem får certifikatet inte installeras på att VM-skalningsuppsättningen och du har oväntade resultat i klustret, inklusive klustret slutar fungera (om du får inga giltiga certifikat som kan användas av klustret för säkerhet. Så dubbelkolla, innan du fortsätter.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Redigera din mallfil för att återspegla de nya parametrar som du just skapade
Om du använder exemplet från den [git-lagringsplats](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) om du vill följa med, kan du börja göra ändringar i det exemplet 5-VM-1-NodeTypes-Secure.parameters_Step2.JSON 

Redigera Resource Manager-mall parametern filen, lägga till två nya parametrar för secCertificateThumbprint och secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Distribuera mallen till Azure

- Du är nu redo att distribuera mallen till Azure. Öppna en kommandotolk för Azure PS version 1 +.
- Logga in på ditt Azure-konto och välj den specifika azure-prenumerationen. Det här är ett viktigt steg för dem som har åtkomst till fler än en azure-prenumeration.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subscription ID> 

```

Testa mallen innan du distribuerar den. Använd samma resursgrupp som klustret för närvarande har distribuerats till.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Distribuera mallen i resursgruppen. Använd samma resursgrupp som klustret för närvarande har distribuerats till. Kör kommandot New-AzureRmResourceGroupDeployment. Du behöver inte ange läget eftersom standardvärdet är **inkrementella**.

> [!NOTE]
> Om du anger läget till slutför du oavsiktligt ta bort resurser som inte ingår i din mall. Så Använd inte den i det här scenariot.
> 
> 

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Här är ett fyllts i samma powershell-exempel.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

När distributionen är klar kan du ansluta till klustret med det nya certifikatet och utföra några frågor. Om du kan göra. Sedan kan du ta bort det gamla certifikatet. 

Glöm inte att importera dem till din lokala certifikatarkivet för TrustedPeople om du använder ett självsignerat certifikat.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Snabbreferens här är kommandot för att ansluta till ett säkert kluster 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
Snabbreferens här är kommandot för att hämta klusterhälsa

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-application-certificates-to-the-cluster"></a>Distribution av certifikat för programmet till klustret.

Du kan använda samma steg som beskrivs i föregående steg 5 ha de certifikat som distribueras från en keyvault till noderna. Du behöver bara definiera och använda olika parametrar.


## <a name="adding-or-removing-client-certificates"></a>Att lägga till eller ta bort klientcertifikat

Förutom klustercertifikat, kan du lägga till klientcertifikat om du vill utföra hanteringsåtgärder på Service Fabric-kluster.

Du kan lägga till två typer av klientcertifikat - administratör eller skrivskyddad. Dessa kan sedan för att styra åtkomsten till admin-drift- och frågeåtgärder i klustret. Som standard läggs klustercertifikat till listan över tillåtna certifikat administratör.

Du kan ange valfritt antal klientcertifikat. Varje tillägg/borttagning resulterar i konfigurationen uppdaterades till Service Fabric-kluster


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Att lägga till klientcertifikat - administratör eller skrivskyddad via portalen

1. Gå till avsnittet säkerhet och välj den ”+ autentisering” knappen ovanpå säkerhets-avsnittet.
2. I avsnittet ”Lägg till autentisering” Välj ”autentiseringstyp' - 'Skrivskyddad client' eller 'Admin client'
3. Nu ska du välja metoden. Detta anger att Service Fabric om det ska sökas upp det här certifikatet med hjälp av ämnesnamnet eller tumavtrycket. I allmänhet är det inte en bra säkerhetsrutin att använda auktoriseringsmetoden för ämnesnamn. 

![Lägg till klientcertifikat][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Borttagning av klientcertifikat - administratör eller skrivskyddad med portalen

Ta bort ett sekundärt certifikat från att användas för Klustersäkerhet, gå till säkerhets-avsnittet och välj alternativet ”Ta bort” på snabbmenyn på specifika certifikat.

## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller mer information om hantering av:

* [Uppgraderingsprocessen för Service Fabric-kluster och förväntningar från dig](service-fabric-cluster-upgrade.md)
* [Konfigurera rollbaserad åtkomst för klienter](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


