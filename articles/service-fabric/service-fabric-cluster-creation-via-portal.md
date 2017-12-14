---
title: Skapa Service Fabric-kluster i Azure portal | Microsoft Docs
description: "Den här artikeln beskriver hur du ställer in en säker Service Fabric-kluster i Azure med Azure-portalen och Azure Key Vault."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/21/2017
ms.author: chackdan
ms.openlocfilehash: be880efdcf1276252c76f27c2f2fd99edd606caa
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Skapa ett Service Fabric-kluster i Azure med Azure-portalen
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Det här är en stegvis guide som vägleder dig genom stegen för att skapa en säker Service Fabric-kluster i Azure med Azure-portalen. Den här guiden vägleder dig genom följande steg:

* Konfigurera Key Vault för att hantera nycklar för säkerhet för klustret.
* Skapa ett skyddat kluster i Azure via Azure-portalen.
* Autentisera administratörer som använder certifikat.

> [!NOTE]
> För mer avancerade säkerhetsalternativ, till exempel autentisering med Azure Active Directory och hur du konfigurerar certifikat för programmet säkerhet [skapar ett kluster med Azure Resource Manager][create-cluster-arm].
> 
> 

En säker klustret är ett kluster som förhindrar obehörig åtkomst till hanteringsåtgärder, vilket innefattar distribution, uppgradera och ta bort program, tjänster och de data som de innehåller. Ett oskyddat klustret är ett kluster alla kan ansluta till när som helst och utföra hanteringsåtgärder. Även om det är möjligt att skapa ett oskyddade kluster, är det **rekommenderar vi att skapa en säker kluster**. Ett oskyddade kluster **går inte att senare säkra** -måste du skapa ett nytt kluster.

Begreppen är samma för att skapa skyddade kluster om klustren är Linux-kluster eller Windows-kluster. Mer information och hjälp skript för att skapa säkra Linux-kluster, se [att skapa skyddade kluster](service-fabric-cluster-creation-via-arm.md). De parametrar som erhålls genom helper skriptet som kan mata direkt i portalen enligt beskrivningen i avsnittet [skapa ett kluster i Azure portal](#create-cluster-portal).

## <a name="configure-key-vault"></a>Konfigurera Key Vault 
### <a name="log-in-to-azure"></a>Logga in på Azure
Den här guiden använder [Azure PowerShell][azure-powershell]. När du startar en ny PowerShell-session, logga in på ditt Azure-konto och välja din prenumeration innan du kör kommandon för Azure.

Logga in på ditt azure-konto:

```powershell
Login-AzureRmAccount
```

Välj din prenumeration:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

### <a name="set-up-key-vault"></a>Konfigurera Key Vault
Den här delen av guiden vägleder dig genom att skapa ett Nyckelvalv för Service Fabric-kluster i Azure och för Service Fabric-program. En fullständig guide på Key Vault finns i [Key Vault Kom igång med][key-vault-get-started].

Service Fabric använder X.509-certifikat för att skydda ett kluster. Azure Key Vault används för att hantera certifikat för Service Fabric-kluster i Azure. När ett kluster distribueras i Azure, Azure resursprovidern ansvarar för att skapa Service Fabric-kluster hämtar certifikat från Nyckelvalvet och installerar dem på klustret virtuella datorer.

Följande diagram illustrerar förhållandet mellan Nyckelvalvet, Service Fabric-kluster och Azure-resursprovider som använder certifikat som lagras i Nyckelvalvet när den skapar ett kluster:

![Certifikatinstallation][cluster-security-cert-installation]

#### <a name="create-a-resource-group"></a>Skapa en resursgrupp
Det första steget är att skapa en ny resursgrupp specifikt för Nyckelvalvet. Placera Nyckelvalvet i sin egen resursgruppen rekommenderas så att du kan ta bort beräkning och lagring resursgrupper – exempelvis den resursgrupp som har Service Fabric-kluster - utan att förlora dina nycklar och hemligheter. Den resursgrupp som har ditt Nyckelvalv måste vara i samma region som det kluster som använder den.

```powershell

    PS C:\Users\vturecek> New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet will be modified in a future release.

    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

#### <a name="create-key-vault"></a>Skapa Key Vault
Skapa ett Nyckelvalv i den nya resursgruppen. Nyckelvalvet **måste vara aktiverat för distribution** att Service Fabric-resursprovidern så att få certifikat från den och installera på klusternoder:

```powershell

    PS C:\Users\vturecek> New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment


    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```

Om du har en befintlig Key Vault kan aktivera du den för distribution med något av följande sätt:

##### <a name="azure-powershell"></a>Azure PowerShell

```powershell
PS C:\Users\vturecek> Set-AzureRmKeyVaultAccessPolicy -VaultName 'myvault' -EnabledForDeployment
```

##### <a name="azure-cli"></a>Azure CLI:

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```


### <a name="add-certificates-to-key-vault"></a>Lägg till certifikat Key Vault
Certifikat används i Service Fabric till att autentisera och kryptera olika delar av ett kluster och de program som körs där. Mer information om hur du använder certifikat i Service Fabric finns [säkerhetsscenarier för Service Fabric-klustret][service-fabric-cluster-security].

#### <a name="cluster-and-server-certificate-required"></a>Kluster- och certifikat (krävs)
Detta certifikat krävs för att skydda ett kluster och förhindra obehörig åtkomst till den. Det ger säkerhet i klustret på ett par olika sätt:

* **Autentisering för klustret:** autentiserar nod till nod kommunikation för klustret. Endast noder som kan bevisa sin identitet med det här certifikatet kan ansluta till klustret.
* **Serverautentisering:** verifierar att klustret management slutpunkter management-klienten så att klienten management vet pratar till verkliga klustret. Det här certifikatet ger också SSL för HTTPS-hanterings-API och för Service Fabric Explorer via HTTPS.

Om du vill hantera dessa ändamål måste certifikatet uppfylla följande krav:

* Certifikatet måste innehålla en privat nyckel.
* Certifikatet måste skapas för nyckelutbyte, kan exporteras till en Personal Information Exchange (.pfx)-fil.
* Certifikatets ämnesnamn måste matcha den domän som används för åtkomst till Service Fabric-klustret. Detta krävs för att tillhandahålla SSL för klustrets HTTPS management slutpunkter och Service Fabric Explorer. Du kan skaffa ett SSL-certifikat från en certifikatutfärdare (CA) för den `.cloudapp.azure.com` domän. Hämta ett anpassat domännamn för klustret. När du begär ett certifikat från en Certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamnet som används för klustret.

#### <a name="client-authentication-certificates"></a>Certifikat för klientautentisering
Ytterligare klientcertifikat autentisera administratörer för hanteringsuppgifter för klustret. Service Fabric har två åtkomstnivåer: **admin** och **skrivskyddade användaren**. Minst ska ett enda certifikat för administrativ åtkomst användas. För ytterligare användarrättigheter, måste ett separat certifikat tillhandahållas. Mer information om åtkomst roller finns [rollbaserad åtkomstkontroll för Service Fabric-klienter][service-fabric-cluster-security-roles].

Du behöver inte överför certifikat för klientautentisering till Key Vault att arbeta med Service Fabric. Dessa certifikat behöver bara anges för användare som har behörighet för hantering av kluster. 

> [!NOTE]
> Azure Active Directory är det rekommenderade sättet att autentisera klienter för klusterhanteringsåtgärder. Om du vill använda Azure Active Directory, måste du [skapa ett kluster med Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certifikat för programmet (valfritt)
Valfritt antal ytterligare certifikat kan installeras på ett kluster av säkerhetsskäl för programmet. Tänk på applikationssäkerhets-scenarier som kräver ett certifikat installeras på noderna, som innan du skapar klustret:

* Kryptering och dekryptering av konfigurationsvärden för programmet
* Kryptering av data mellan noderna vid replikering 

Certifikat för programmet kan inte konfigureras när du skapar ett kluster via Azure-portalen. Om du vill konfigurera programmet certifikat vid klustret konfigurationen måste du [skapa ett kluster med Azure Resource Manager][create-cluster-arm]. Du kan också lägga till programmet certifikat till klustret när den har skapats.

#### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Formatering certifikat för användning av Azure resource provider
Privata nyckelfiler (.pfx) kan läggas till och användas direkt via Nyckelvalvet. Azure-resursprovider kräver dock nycklar lagras i en särskild JSON-format som innehåller PFX som en Base64-kodad sträng och lösenordet för privata nyckeln. Om du vill anpassa dessa krav nycklar måste placeras i en JSON-sträng och sedan lagras som *hemligheter* i Nyckelvalvet.

För att underlätta processen, en PowerShell-modul är [finns på GitHub][service-fabric-rp-helpers]. Följ dessa steg om du vill använda modulen:

1. Hämta hela innehållet på lagringsplatsen till en lokal katalog. 
2. Importera modulen i PowerShell-fönstret:

```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

Den `Invoke-AddCertToKeyVault` kommandot i PowerShell-modulen automatiskt formaterar en certifikatets privata nyckel till en JSON-sträng och överförs till Nyckelvalvet. Använd den för att lägga till kluster-certifikat och några ytterligare certifikat Key Vault. Upprepa det här steget för några ytterligare certifikat som du vill installera i klustret.

```powershell
PS C:\Users\vturecek> Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet will be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault


Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

Dessa är alla Key Vault-krav för att konfigurera ett Service Fabric-kluster Resource Manager-mall som installerar certifikat för noden autentisering, hantering av slutpunktssäkerhet och autentisering och eventuella ytterligare programsäkerhet funktioner som använder X.509-certifikat. Nu ska du nu har följande inställningar i Azure:

* Key Vault resursgruppen.
  * Key Vault
    * Klustret certifikat för serverautentisering

< /a ”Skapa kluster-portal” ></a>

## <a name="create-cluster-in-the-azure-portal"></a>Skapa kluster i Azure-portalen
### <a name="search-for-the-service-fabric-cluster-resource"></a>Sök efter klusterresursen Service Fabric
![Sök efter mallen för Service Fabric-kluster på Azure portal.][SearchforServiceFabricClusterTemplate]

1. Logga in på [Azure Portal][azure-portal].
2. Klicka på **ny** att lägga till en ny resursmall för. Sök efter mallen Service Fabric-kluster i den **Marketplace** under **allt**.
3. Välj **Service Fabric-kluster** från listan.
4. Navigera till den **Service Fabric-kluster** bladet, klickar du på **skapa**,
5. Den **skapar Service Fabric-kluster** bladet har följande fyra steg.

#### <a name="1-basics"></a>1. Grundläggande inställningar
![Skärmbild som visar hur du skapar en ny resursgrupp.][CreateRG]

I bladet grundläggande behöver du ange grundläggande information för klustret.

1. Ange namnet på klustret.
2. Ange en **användarnamn** och **lösenord** Fjärrskrivbord för de virtuella datorerna.
3. Se till att välja den **prenumeration** som du vill klustret som ska distribueras till, särskilt om du har flera prenumerationer.
4. Skapa en **ny resursgrupp**. Det är bäst att ge det samma namn som klustret, eftersom det hjälper dig att hitta dem senare, särskilt när du försöker göra ändringar i din distribution eller ta bort klustret.
   
   > [!NOTE]
   > Även om du kan välja att använda en befintlig resursgrupp, är det en bra idé att skapa en ny resursgrupp. Detta gör det enkelt att ta bort kluster som du inte behöver.
   > 
   > 
5. Välj den **region** som du vill skapa klustret. Du måste använda samma region som ditt Nyckelvalv.

#### <a name="2-cluster-configuration"></a>2. Klusterkonfiguration
![Skapa en nodtyp][CreateNodeType]

Konfigurera klusternoderna. Nodtyper definiera storlek på Virtuella datorer, hur många virtuella datorer och deras egenskaper. Klustret kan ha fler än en nodtyp, men den primära nodtypen (den första som du definierar i portalen) måste ha minst fem virtuella datorer, eftersom det är nodtypen där Service Fabric systemtjänster placeras. Konfigurera inte **placeringsegenskaper** eftersom en standardegenskap placering av ”NodeTypeName” läggs till automatiskt.

> [!NOTE]
> Ett vanligt scenario för flera nodtyper är ett program som innehåller en frontend-tjänst och en backend tjänst. Du vill publicera frontend-tjänsten på mindre virtuella datorer (VM-storlekar som D2) med öppna portar till Internet, men du vill placera backend-tjänst på större VM: ar (med VM-storlekar som D4, D6, D15 och så vidare) utan Internet-riktade öppna portar.
> 
> 

1. Välj ett namn för din nodtypen (1 till 12 tecken som innehåller endast bokstäver och siffror).
2. Minst **storlek** för virtuella datorer för den primära noden typen drivs av den **hållbarhet** nivå som du väljer för klustret. Standardvärdet för hållbarhet skiktet är Brons. Mer information om hållbarhet finns [hur du väljer Service Fabric-kluster tillförlitlighet och hållbarhet][service-fabric-cluster-capacity].
3. Välj prisnivå och VM-storlek. D-serien virtuella datorer ha SSD-enheterna och rekommenderas för tillståndskänsliga program. Använd inte VM-SKU som har delvis kärnor eller vara mindre än 7 GB tillgängligt diskutrymme. 
4. Minst **nummer** för virtuella datorer för den primära noden typen drivs av den **tillförlitlighet** nivå som du väljer. Standardvärdet för tillförlitlighetsnivån är Silver. Mer information om tillförlitlighet finns [hur du väljer Service Fabric-kluster tillförlitlighet och hållbarhet][service-fabric-cluster-capacity].
5. Välj antalet virtuella datorer för nodtypen. Du kan skala upp eller ned antalet virtuella datorer i en nodtyp vid ett senare tillfälle, men på den primära nodtypen minst drivs av den nivå för tillförlitlighet som du har valt. Andra nodtyper kan ha minst 1 VM.
6. Konfigurera anpassade slutpunkter. Det här fältet kan du ange en kommaavgränsad lista över portar som du vill exponera via Azure-belastningsutjämnaren till det offentliga Internet för dina program. Om du planerar att distribuera ett program i klustret, t.ex ”80” här för att tillåta trafik på port 80 till klustret. Läs mer på slutpunkter [kommunicera med program][service-fabric-connect-and-communicate-with-services]
7. Konfigurera klustret **diagnostik**. Som standard aktiveras diagnostik på klustret att underlätta felsökning av problem. Om du vill inaktivera diagnostik ändra den **Status** växla till **av**. Om du inaktiverar diagnostik är **inte** rekommenderas.
8. Välj Fabric Uppgraderingsläge du vill ange ditt kluster till. Välj **automatisk**, om du vill att hämta den senaste tillgängliga versionen och försök att uppgradera ditt kluster till det automatiskt. Ange läget som **manuell**, om du vill välja en version som stöds.

> [!NOTE]
> Vi stöder endast kluster som kör versioner av service Fabric stöds. Genom att välja den **manuell** läge du tar med ansvar att uppgradera ditt kluster till en version som stöds. För mer information om infrastrukturen uppgradera läge finns det [service fabric-kluster-uppgradering dokumentet.][service-fabric-cluster-upgrade]
> 
> 

#### <a name="3-security"></a>3. Säkerhet
![Skärmbild som visar säkerhetskonfigurationer på Azure-portalen.][SecurityConfigs]

Det sista steget är att tillhandahålla information om certifikat för att skydda klustret med Key Vault och certifikatet information har skapat tidigare.

* Fylla i certifikatet för primär-fält med utdata från ladda upp den **klustret certifikat** till Nyckelvalvet med hjälp av den `Invoke-AddCertToKeyVault` PowerShell-kommando.

```powershell
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47
```

* Kontrollera den **konfigurera avancerade inställningar** om du vill ange klientcertifikat för **administratörsklient** och **skrivskyddad klient**. I dessa fält, anger du tumavtrycket för admin klientcertifikatet och tumavtrycket för klientcertifikatet skrivskyddade användaren, om tillämpligt. När administratörer försöker ansluta till klustret kan beviljas åtkomst endast om de har ett certifikat med ett tumavtryck som matchar tumavtrycket värden anges här.  

#### <a name="4-summary"></a>4. Sammanfattning

Skapa ett kluster, klicka på **sammanfattning** att se de konfigurationer som du har angett eller hämta en Azure Resource Manager-mall som används för att distribuera klustret. När du har angett obligatoriska inställningar i **OK** knappen blir grön och du kan starta klusterskapandeprocessen genom att klicka på den.

Du kan se förloppet bland aviseringarna. (Klicka på klockikonen nära statusfältet uppe till höger på skärmen.) Om du klickade på **fäst på startsidan** när du skapar klustret måste du se **distribuerar Service Fabric-kluster** Fäst den **starta** kort.

### <a name="view-your-cluster-status"></a>Visa klusterstatus för
![Skärmbild som visar information om kluster på instrumentpanelen.][ClusterDashboard]

När klustret har skapats kan du granska klustret i portalen:

1. Gå till **Bläddra** och på **Service Fabric-kluster**.
2. Leta upp ditt kluster och klicka på den.
3. Där kan du se information om klustret på instrumentpanelen, bland annat klustrets offentliga slutpunkter och en länk till Service Fabric Explorer.

Den **nod övervakaren** avsnitt på klustrets instrumentpanel blad anger hur många virtuella datorer som är felfria och inte felfri. Du hittar mer information om klustrets hälsa i [Service Fabric hälsa modellen introduktion][service-fabric-health-introduction].

> [!NOTE]
> Service Fabric-kluster kräver ett visst antal noder krävs för att alltid underhålla tillgänglighet och bevara tillstånd - kallas ”underhålla kvorum”. Därför det är vanligtvis inte säkert att stänga av alla datorer i klustret om du först har utfört en [fullständig säkerhetskopiering av din tillstånd][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Fjärranslutning till en Virtual Machine Scale Set-instans eller en klusternod
Var och en av nodetypes får du ange i klustret resultaten i en virtuell dator Skaluppsättning komma installation. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Nästa steg
Du har nu ett säker kluster som använder certifikat för autentisering för hantering. Nästa [ansluta till klustret](service-fabric-connect-to-secure-cluster.md) och lära dig hur du [hantera program hemligheter](service-fabric-application-secret-management.md).  Lär dig dessutom [Service Fabric supportalternativ](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
<!--[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md#remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node -->
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
