---
title: Hantera certifikat i ett Azure Service Fabric-kluster | Microsoft Docs
description: Beskriver hur du lägger till nya certifikat, förnyelsecertifikat, och ta bort certifikat till eller från ett Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/23/2018
ms.author: chackdan
ms.openlocfilehash: 16758cc85b552e82d3daa63893558e1048bcefb8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Lägg till eller ta bort certifikat för Service Fabric-kluster i Azure
Vi rekommenderar att du bekanta dig med hur Service Fabric använder X.509-certifikat och känna till de [kluster säkerhetsscenarier](service-fabric-cluster-security.md). Du måste förstå vad ett certifikat för klustret och som används för, innan du fortsätter ytterligare.

Service fabric kan du ange två certifikat för klustret, en primär och en sekundär när du konfigurerar Certifikatsäkerhet när klustret skapas, förutom klientcertifikat. Referera till [skapar ett azure-kluster via portalen](service-fabric-cluster-creation-via-portal.md) eller [skapar ett azure-kluster via Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) för information om att ställa in dem på Skapa tid. Om du anger bara ett certifikat för kluster på Skapa tid, som används som primär certifikatet. Du kan lägga till ett nytt certifikat som en sekundär när klustret skapades.

> [!NOTE]
> För en säker klustret behöver alltid du minst en giltig (inte återkallat och inte har upphört att gälla) kluster-certifikat (primära eller sekundära) distribuerat (om inte, i klustret slutar fungera). 90 dagar innan alla giltiga certifikat når förfallodatum, genereras en varning-spårning och en varningshändelse hälsa på noden. Det finns för närvarande ingen e-post eller andra notification Service Fabric skickar ut på den här artikeln. 
> 
> 

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Lägga till ett sekundärt kluster-certifikat med hjälp av portalen
Sekundära kluster certifikat kan inte läggas till via Azure portal, Använd Azure powershell. Processen beskrivs senare i det här dokumentet.

## <a name="swap-the-cluster-certificates-using-the-portal"></a>Växla klustercertifikat med hjälp av portalen
När du har distribuerat ett sekundärt kluster-certifikat har om du vill byta ut de primära och sekundära, gå till avsnittet säkerhet och alternativet 'Växlingen med primära' på snabbmenyn för att växla sekundära cert med primärt certifikat.

![Växla certifikat][Delete_Swap_Cert]

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Ta bort ett certifikat för kluster med hjälp av portalen
För en säker kluster behöver alltid du minst en giltig (inte återkallat och inte har upphört att gälla) certifikat (primära eller sekundära) distribueras om inte klustret slutar fungera.

Ta bort ett sekundärt certifikat används för klustret säkerhet, gå till avsnittet och väljer alternativet ”Ta bort' snabbmenyn på sekundärt certifikat.

Om din avsikt är att ta bort certifikat som har markerats primära, måste växla med sekundärt först och sedan ta bort sekundärt när uppgraderingen har slutförts.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Lägg till ett sekundärt certifikat med hjälp av hanteraren för filserverresurser Powershell
> [!TIP]
> Det är nu bättre och enklare sätt att lägga till ett sekundärt certifikat med hjälp av den [Lägg till AzureRmServiceFabricClusterCertificate](/powershell/module/azurerm.servicefabric/add-azurermservicefabricclustercertificate) cmdlet. Du behöver inte utföra resten av stegen i det här avsnittet.  Dessutom behöver du inte har använts för att skapa och distribuera klustret när du använder mallen i [Lägg till AzureRmServiceFabricClusterCertificate](/powershell/module/azurerm.servicefabric/add-azurermservicefabricclustercertificate) cmdlet.

Dessa instruktioner förutsätter att du känner till hur Resource Manager fungerar och har distribuerat minst en Service Fabric-kluster med hjälp av en Resource Manager-mall och har den mall som du använde för att konfigurera klustret praktiska. Det förutsätts även att du är nöjd med JSON.

> [!NOTE]
> Om du letar efter en exempelmall och parametrar som du kan använda för att följa längs eller som en startpunkt sedan ladda ned det från den här [git-lagringsplatsen](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Redigera Resource Manager-mall

För att underlätta följande längs innehåller exempel 5-VM-1-nodetypes får-Secure_Step2.JSON alla ändringar som vi kommer att göra. exemplet finns på [git-lagringsplatsen](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

**Se till att du har följt alla steg**

1. Öppna Resource Manager-mallen du använde för att distribuera kluster. (Om du har hämtat provet från föregående lagringsplatsen kan sedan distribuera ett kluster för säker med hjälp av 5-VM-1-nodetypes får-Secure_Step1.JSON och öppna sedan mallen).

2. Lägg till **två nya parametrar** ”secCertificateThumbprint” och ”secCertificateUrlValue” för typ ”sträng” i parameteravsnittet i mallen. Du kan kopiera följande kodavsnitt och lägga till den i mallen. Beroende på källan till mallen kanske du redan dessa definieras om därför gå vidare till nästa steg. 
 
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

3. Göra ändringar i den **Microsoft.ServiceFabric/clusters** resurs, leta upp resursdefinitionen ”Microsoft.ServiceFabric/clusters” i mallen. Under Egenskaper för den definitionen av du hittar ”certifikat” JSON-tagg som ska se ut ungefär som följande kodavsnitt i JSON:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Lägg till en ny tagg ”thumbprintSecondary” och ge det värdet ”[parameters('secCertificateThumbprint')]”.  

    Nu resursdefinitionen bör se ut som följande (beroende på din källa för mallen, det är inte exakt samma sätt som kodfragmentet nedan). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Om du vill **rulla över cert**, ange det nya certifikatet som primär och flytta den aktuella primärt som sekundär. Detta resulterar i förnyelsen av ditt aktuella primära certifikat till det nya certifikatet i ett steg i distributionen.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Göra ändringar i **alla** den **Microsoft.Compute/virtualMachineScaleSets** resursdefinitionerna - hitta Microsoft.Compute/virtualMachineScaleSets resursdefinitionen. Bläddra till ”publisher”: ”Microsoft.Azure.ServiceFabric” under ”virtualMachineProfile”.

    Du bör se ut så här i inställningarna för Service Fabric-utgivare.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Lägga till nya cert-poster
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    Egenskaperna bör nu se ut så här
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Om du vill **rulla över cert**, ange det nya certifikatet som primär och flytta den aktuella primärt som sekundär. Detta resulterar i förnyelsen av ditt aktuella certifikat till det nya certifikatet i ett steg i distributionen.     

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

5. Göra ändringar i **alla** den **Microsoft.Compute/virtualMachineScaleSets** resursdefinitionerna - hitta Microsoft.Compute/virtualMachineScaleSets resursdefinitionen. Bläddra till ”vaultCertificates”:, under ”OSProfile”. Det bör se ut ungefär så här.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Lägg till secCertificateUrlValue till den. Använd följande utdrag:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Resulterande Json bör nu se ut så här.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Kontrollera att du har upprepas steg 4 och 5 för alla definitioner för Nodetypes/Microsoft.Compute/virtualMachineScaleSets-resurs i mallen. Om du missar någon av dem får certifikatet inte installeras på att skaluppsättning för virtuell dator och du har oförutsägbara resultat i klustret, inklusive klustret gå (om du får inga giltiga certifikat som klustret kan använda för säkerhet. Så dubbelkolla, innan du fortsätter.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Redigera mallfilen för att återspegla de nya parametrar som du just skapade
Om du använder prov från den [git-lagringsplatsen](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) om du vill följa med kan du börja göra ändringar i exempel 5-VM-1-nodetypes får-Secure.paramters_Step2.JSON 

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
- Logga in på ditt Azure-konto och välj den specifika azure-prenumerationen. Det här är ett viktigt steg för avdelningen som har tillgång till fler än en azure-prenumeration.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subcription ID> 

```

Testa mallen innan du distribuerar den. Använd samma resursgrupp som klustret för närvarande har distribuerats till.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Distribuera mallen till resursgruppen. Använd samma resursgrupp som klustret för närvarande har distribuerats till. Kör kommandot New AzureRmResourceGroupDeployment. Du behöver inte ange läget eftersom standardvärdet är **inkrementella**.

> [!NOTE]
> Om du anger läget till slutför du av misstag ta bort resurser som inte finns i mallen. Därför inte använda den i det här scenariot.
> 
> 

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Här följer ett exempel som har fyllts i samma PowerShell.

```powershell
$ResouceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResouceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResouceGroup2

```

När distributionen är klar kan ansluta till klustret med det nya certifikatet och utföra några frågor. Om du ska kunna göra. Du kan ta bort det gamla certifikatet. 

Glöm inte att importera dem till din lokala certifikatarkivet för TrustedPeople om du använder ett självsignerat certifikat.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Här är kommandot för att ansluta till en säker kluster för Snabbreferens 

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
Här är kommandot för att hämta kluster för Snabbreferens

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-application-certificates-to-the-cluster"></a>Distribution av programmet certifikat till klustret.

Du kan använda samma steg som beskrivs i föregående steg 5 för att få certifikat distribueras från en keyvault till noder. Du behöver bara ange och använda olika parametrar.


## <a name="adding-or-removing-client-certificates"></a>Tillägg eller borttagning av klientcertifikat

Förutom klustercertifikat kan du lägga till klientcertifikat för att kunna utföra hanteringsåtgärder på ett Service Fabric-kluster.

Du kan lägga till två typer av certifikat - administratör eller skrivskyddat. De kan sedan för att styra åtkomsten till administratörsåtgärder och fråga åtgärder på klustret. Klustercertifikat har lagts till listan över tillåtna Admin certifikat som standard.

Du kan ange valfritt antal klientcertifikat. Varje tillägg/borttagning resulterar i en konfiguration-uppdatering till Service Fabric-kluster


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Lägga till klientcertifikat - administratör eller skrivskyddad via portalen

1. Gå till avsnittet säkerhet och välj den ”+ autentisering” knappen ovanpå Säkerhetsavsnittet.
2. Välj 'Autentiseringstypen' - 'Skrivskyddad client' eller 'Admin client' i avsnittet ”Lägg till autentisering”
3. Nu Välj metod för auktorisering. Detta anger för Service Fabric om det ska sökas upp det här certifikatet med hjälp av ämnesnamnet eller tumavtrycket. Det är i allmänhet inte en bra säkerhetsrutin att använda metoden auktorisering för ämnesnamn. 

![Lägg till klientcertifikat][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Borttagning av klientcertifikat - administratören eller skrivskyddad med hjälp av portalen

Ta bort ett sekundärt certifikat används för klustret säkerhet, gå till avsnittet och väljer alternativet ”Ta bort' snabbmenyn på specifika certifikat.

## <a name="next-steps"></a>Nästa steg
Läs artiklarna mer information om hantering av kluster:

* [Uppgraderingsprocessen för Service Fabric-kluster och förväntningar från dig](service-fabric-cluster-upgrade.md)
* [Konfigurera rollbaserad åtkomst för klienter](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Delete_Swap_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_09.PNG
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


