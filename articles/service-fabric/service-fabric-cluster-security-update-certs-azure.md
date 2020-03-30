---
title: Hantera certifikat i ett Azure Service Fabric-kluster
description: I artikeln beskrivs hur du lägger till nya certifikat, överrullningscertifikat och tar bort certifikat till eller från ett Service Fabric-kluster.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: a3c92e1b39261af32085e4d9b6cb2462d5c0eb64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458346"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Lägga till eller ta bort certifikat för ett Service Fabric-kluster i Azure
Vi rekommenderar att du bekantar dig med hur Service Fabric använder X.509-certifikat och är bekant med [klustersäkerhetsscenarierna](service-fabric-cluster-security.md). Du måste förstå vad ett klustercertifikat är och vad som används för, innan du fortsätter vidare.

Azure Service Fabrics SDK:s standardbeteende för certifikatinläsning är att distribuera och använda det definierade certifikatet med utgångsdatum längst in i framtiden. oberoende av deras primära eller sekundära konfigurationsdefinition. Att falla tillbaka till det klassiska beteendet är en inte rekommenderad avancerad åtgärd och kräver att inställningsparametervärdet "UseSecondaryIfNewer" ställs in på false i `Fabric.Code` konfigurationen.

Med tjänstinfrastruktur kan du ange två klustercertifikat, en primär och en sekundär, när du konfigurerar certifikatsäkerhet när klustret skapas, förutom klientcertifikat. Se skapa [ett azure-kluster via portal](service-fabric-cluster-creation-via-portal.md) eller skapa ett [azure-kluster via Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) för information om hur du konfigurerar dem vid skapandet. Om du bara anger ett klustercertifikat vid skapandetid används det som primärt certifikat. När klustret har skapats kan du lägga till ett nytt certifikat som ett sekundärt.

> [!NOTE]
> För ett säkert kluster behöver du alltid minst ett giltigt (inte återkallat och inte utgånget) klustercertifikat (primärt eller sekundärt) som distribueras (om inte, klustret slutar fungera). 90 dagar innan alla giltiga certifikat upphör att gälla genererar systemet en varningsspårning och även en varningshälsohändelse på noden. Det finns för närvarande ingen e-post eller något annat meddelande som Service Fabric skickar ut på den här artikeln. 
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Lägga till ett sekundärt klustercertifikat med portalen
Sekundärt klustercertifikat kan inte läggas till via Azure-portalen, med Azure powershell. Processen beskrivs senare i det här dokumentet.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Ta bort ett klustercertifikat med portalen
För ett säkert kluster behöver du alltid minst ett giltigt certifikat (inte återkallat och inte upphört att gälla). Certifikatet som distribueras längst in i det framtida utgångsdatumet används och om du tar bort det stoppas klustret. se till att endast ta bort certifikatet som har upphört att gälla, eller ett oanvänt certifikat som löper ut den tidigaste.

Om du vill ta bort ett oanvänt klustersäkerhetscertifikat navigerar du till avsnittet Säkerhet och väljer alternativet Ta bort från snabbmenyn på det oanvända certifikatet.

Om din avsikt är att ta bort certifikatet som är markerat primärt måste du distribuera ett sekundärt certifikat med ett utgångsdatum längre in i framtiden än det primära certifikatet, vilket aktiverar det automatiska överrullningsbeteendet. ta bort det primära certifikatet när den automatiska överrullningen har slutförts.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Lägga till ett sekundärt certifikat med Powershell i Resurshanteraren
> [!TIP]
> Det finns nu ett bättre och enklare sätt att lägga till ett sekundärt certifikat med hjälp av [cmdleten Add-AzServiceFabricClusterCertificate.](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) Du behöver inte följa resten av stegen i det här avsnittet.  Du behöver inte heller den mall som ursprungligen användes för att skapa och distribuera klustret när du använder cmdleten [Add-AzServiceFabricClusterCertificate.](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate)

De här stegen förutsätter att du är bekant med hur Resource Manager fungerar och har distribuerat minst ett Service Fabric-kluster med hjälp av en Resource Manager-mall och har den mall som du använde för att konfigurera klustret till hands. Det antas också att du är bekväm med att använda JSON.

> [!NOTE]
> Om du letar efter en exempelmall och parametrar som du kan använda för att följa med eller som utgångspunkt, sedan ladda ner den från denna [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Redigera mallen Resurshanteraren

För att underlätta att följa med innehåller exempel 5-VM-1-NodeTypes-Secure_Step2.JSON alla redigeringar vi kommer att göra. provet finns tillgängligt på [git-repo.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample)

**Se till att följa alla steg**

1. Öppna den Resource Manager-mall som du använde för att distribuera klustret åt dig. (Om du har hämtat exemplet från föregående repa använder du 5-VM-1-NodeTypes-Secure_Step1.JSON för att distribuera ett säkert kluster och sedan öppna mallen).

2. Lägg till **två nya parametrar** "secCertificateThumbprint" och "secCertificateUrlValue" av typen "string" i parameteravsnittet i mallen. Du kan kopiera följande kodavsnitt och lägga till det i mallen. Beroende på mallens källa kanske du redan har definierat dessa, om så går du vidare till nästa steg. 
 
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

3. Gör ändringar i **Microsoft.ServiceFabric/clusters-resursen** – Leta reda på resursdefinitionen "Microsoft.ServiceFabric/clusters" i mallen. Under egenskaperna för den definitionen hittar du "Certificate" JSON-taggen, som ska se ut ungefär som följande JSON-utdrag:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Lägg till en ny tagg "tumavtryckKonsekund" och ge den ett värde "[parameters('secCertificateThumbprint')]".  

    Så nu resursdefinitionen ska se ut så här (beroende på din källa till mallen, kan det inte vara exakt som utdrag nedan). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Om du vill **rulla över certifikatet**anger du det nya certifikatet som primärt och flyttar den aktuella primära som sekundär. Detta resulterar i att ditt nuvarande primära certifikat övervältras till det nya certifikatet i ett distributionssteg.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Gör ändringar i **alla** **microsoft.Compute/virtualMachineScaleSets-resursdefinitioner** – Leta reda på resursdefinitionen för Microsoft.Compute/virtualMachineScaleSets. Bläddra till "utgivaren": "Microsoft.Azure.ServiceFabric", under "virtualMachineProfile".

    I publiceringsinställningarna för Service Fabric bör du se något liknande.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Lägga till de nya certposterna i den
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    Fastigheterna ska nu se ut så här
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Om du vill **rulla över certifikatet**anger du det nya certifikatet som primärt och flyttar den aktuella primära som sekundär. Detta resulterar i att ditt nuvarande certifikat övervältras till det nya certifikatet i ett distributionssteg.     

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

    Fastigheterna ska nu se ut så här    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Gör ändringar i **alla** **microsoft.compute/virtualMachineScaleSets-resursdefinitioner** – Leta reda på resursdefinitionen för Microsoft.Compute/virtualMachineScaleSets. Bläddra till "vaultCertificates": , under "OSProfile". det ska se ut ungefär så här.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Lägg till secCertificateUrlValue i den. Använd följande kodavsnitt:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Nu ska den resulterande Json se ut ungefär så här.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Kontrollera att du har upprepade steg 4 och 5 för alla nodetyper/Microsoft.Compute/virtualMachineScaleSets-resursdefinitioner i mallen. Om du missar en av dem installeras inte certifikatet på den virtuella datorns skalningsuppsättning och du får oförutsägbara resultat i klustret, inklusive att klustret går nedåt (om du inte får några giltiga certifikat som klustret kan använda för säkerhet. Så dubbelkolla, innan du fortsätter vidare.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Redigera mallfilen så att den återspeglar de nya parametrarna som du har lagt till ovan
Om du använder exemplet från [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample) att följa med, kan du börja göra ändringar i exemplet 5-VM-1-NodeTypes-Secure.parameters_Step2.JSON 

Redigera parameterparametern Resource Manager Template File, lägg till de två nya parametrarna för secCertificateThumbprint och secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Distribuera mallen till Azure

- Du är nu redo att distribuera mallen till Azure. Öppna en kommandotolk för Azure PS version 1+.
- Logga in på ditt Azure-konto och välj den specifika azure-prenumerationen. Detta är ett viktigt steg för personer som har tillgång till mer än en azure-prenumeration.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

Testa mallen innan du distribuerar den. Använd samma resursgrupp som klustret för närvarande har distribuerats till.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Distribuera mallen till resursgruppen. Använd samma resursgrupp som klustret för närvarande har distribuerats till. Kör kommandot New-AzResourceGroupDeployment. Du behöver inte ange läget, eftersom standardvärdet är **inkrementellt**.

> [!NOTE]
> Om du ställer in Läge till Slutför kan du av misstag ta bort resurser som inte finns i mallen. Så använd den inte i det här scenariot.
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Här är ett ifyllt exempel på samma powershell.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

När distributionen är klar ansluter du till klustret med det nya certifikatet och utför vissa frågor. Om du kan göra. Sedan kan du ta bort det gamla certifikatet. 

Om du använder ett självsignerat certifikat ska du inte glömma att importera dem till ditt lokala TrustedPeople-cert-arkiv.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
För snabb referens här är kommandot för att ansluta till ett säkert kluster 

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
För snabb referens här är kommandot för att få kluster hälsa

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>Distribuera klientcertifikat till klustret.

Du kan använda samma steg som beskrivs i föregående steg 5 för att få certifikaten distribuerade från en keyvault till noderna. Du behöver bara definiera och använda olika parametrar.


## <a name="adding-or-removing-client-certificates"></a>Lägga till eller ta bort klientcertifikat

Förutom klustercertifikaten kan du lägga till klientcertifikat för att utföra hanteringsåtgärder i ett Service Fabric-kluster.

Du kan lägga till två typer av klientcertifikat - Admin eller Skrivskyddad. Dessa kan sedan användas för att styra åtkomsten till administratörsåtgärderna och frågeåtgärderna i klustret. Som standard läggs klustercertifikaten till i listan över tillåtna administratörscertifikat.

Du kan ange valfritt antal klientcertifikat. Varje tillägg/borttagning resulterar i en konfigurationsuppdatering av Service Fabric-klustret


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Lägga till klientcertifikat – administratör eller skrivskyddad via portal

1. Navigera till avsnittet Säkerhet och välj knappen +Autentisering ovanpå säkerhetsavsnittet.
2. I avsnittet Lägg till autentisering väljer du "Autentiseringstyp" - "Skrivskyddad klient" eller "Administratörsklient"
3. Välj nu auktoriseringsmetoden. Detta anger för Service Fabric om det ska slå upp det här certifikatet med hjälp av ämnesnamnet eller tumavtrycket. I allmänhet är det inte en god säkerhetspraxis att använda auktoriseringsmetoden för ämnesnamn. 

![Lägg till klientcertifikat][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Radering av klientcertifikat - Administratör eller skrivskyddad med hjälp av portalen

Om du vill ta bort ett sekundärt certifikat från att användas för klustersäkerhet navigerar du till avsnittet Säkerhet och väljer alternativet Ta bort på snabbmenyn på det specifika certifikatet.

## <a name="adding-application-certificates-to-a-virtual-machine-scale-set"></a>Lägga till programcertifikat i en skalningsuppsättning för virtuella datorer

Information om hur du distribuerar ett certifikat som du använder för dina program till klustret finns [i det här exemplet powershell-skriptet](scripts/service-fabric-powershell-add-application-certificate.md).

## <a name="next-steps"></a>Nästa steg
Läs de här artiklarna för mer information om klusterhantering:

* [Uppgraderingsprocessen för Service Fabric Cluster och förväntningar från dig](service-fabric-cluster-upgrade.md)
* [Konfigurera rollbaserad åtkomst för klienter](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


