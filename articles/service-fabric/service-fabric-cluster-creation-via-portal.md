---
title: Skapa Service Fabric-kluster i Azure portal | Microsoft Docs
description: Den här artikeln beskriver hur du ställer in en säker Service Fabric-kluster i Azure med Azure-portalen och Azure Key Vault.
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
ms.date: 02/09/2018
ms.author: chackdan
ms.openlocfilehash: 864952554b04d8d6bc14aad92d168298e554710e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Skapa ett Service Fabric-kluster i Azure med Azure-portalen
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Det här är en stegvis guide som vägleder dig genom stegen för att skapa ett Service Fabric-klustret (Windows eller Linux) i Azure med Azure-portalen. Den här guiden vägleder dig genom följande steg:

* Skapa ett kluster i Azure via Azure-portalen.
* Autentisera administratörer som använder certifikat.

> [!NOTE]
> För mer avancerade säkerhetsalternativ, till exempel autentisering med Azure Active Directory och hur du konfigurerar certifikat för programmet säkerhet [skapar ett kluster med Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Klustersäkerhet 
Certifikat används i Service Fabric till att autentisera och kryptera olika delar av ett kluster och de program som körs där. Mer information om hur du använder certifikat i Service Fabric finns [säkerhetsscenarier för Service Fabric-klustret][service-fabric-cluster-security].

Om det här är första gången du skapar ett service fabric-kluster eller distribuerar ett kluster för arbetsbelastningar för testning, du kan gå vidare till nästa avsnitt (**Skapa kluster i Azure Portal**) och att systemet ska generera certifikat som krävs för ditt kluster som kör arbetsbelastningar för testning. Om du konfigurerar ett kluster för produktionsarbetsbelastningar fortsätter du läsning.

#### <a name="cluster-and-server-certificate-required"></a>Kluster- och certifikat (krävs)
Detta certifikat krävs för att skydda ett kluster och förhindra obehörig åtkomst till den. Det ger säkerhet i klustret på ett par olika sätt:

* **Autentisering för klustret:** autentiserar nod till nod kommunikation för klustret. Endast noder som kan bevisa sin identitet med det här certifikatet kan ansluta till klustret.
* **Serverautentisering:** verifierar att klustret management slutpunkter management-klienten så att klienten management vet pratar till verkliga klustret. Det här certifikatet ger också SSL för HTTPS-hanterings-API och för Service Fabric Explorer via HTTPS.

Om du vill hantera dessa ändamål måste certifikatet uppfylla följande krav:

* Certifikatet måste innehålla en privat nyckel.
* Certifikatet måste skapas för nyckelutbyte, kan exporteras till en Personal Information Exchange (.pfx)-fil.
* Certifikatets **ämnesnamn måste matcha domänen** används för åtkomst till Service Fabric-klustret. Detta krävs för att tillhandahålla SSL för klustrets HTTPS management slutpunkter och Service Fabric Explorer. Du kan skaffa ett SSL-certifikat från en certifikatutfärdare (CA) för den `.cloudapp.azure.com` domän. Hämta ett anpassat domännamn för klustret. När du begär ett certifikat från en Certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamnet som används för klustret.

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

< /a ”Skapa kluster-portal” ></a>

## <a name="create-cluster-in-the-azure-portal"></a>Skapa kluster i Azure-portalen

Skapar ett produktionskluster för att uppfylla behoven för ditt program innebär lite planering, som hjälper dig med som, rekommenderas du att läsa och förstå [Service Fabric-kluster överväganden vid planering av] [ service-fabric-cluster-capacity] dokumentet. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Sök efter klusterresursen Service Fabric
![Sök efter mallen för Service Fabric-kluster på Azure portal.][SearchforServiceFabricClusterTemplate]

1. Logga in på [Azure Portal][azure-portal].
2. Klicka på **skapar du en resurs** att lägga till en ny resursmall för. Sök efter mallen Service Fabric-kluster i den **Marketplace** under **allt**.
3. Välj **Service Fabric-kluster** från listan.
4. Navigera till den **Service Fabric-kluster** bladet, klickar du på **skapa**,
5. Den **skapar Service Fabric-kluster** bladet har följande fyra steg:

#### <a name="1-basics"></a>1. Grundläggande inställningar
![Skärmbild som visar hur du skapar en ny resursgrupp.][CreateRG]

I bladet grundläggande behöver du ange grundläggande information för klustret.

1. Ange namnet på klustret.
2. Ange en **användarnamn** och **lösenord** Fjärrskrivbord för de virtuella datorerna.
3. Se till att välja den **prenumeration** som du vill klustret som ska distribueras till, särskilt om du har flera prenumerationer.
4. Skapa en **ny resursgrupp**. Det är bäst att ge det samma namn som klustret, eftersom det hjälper dig att hitta dem senare, särskilt när du försöker göra ändringar i din distribution eller ta bort klustret.
   
   > [!NOTE]
   > Även om du kan välja att använda en befintlig resursgrupp, är det en bra idé att skapa en ny resursgrupp. Detta gör det enkelt att ta bort kluster och alla resurser som används.
   > 
   > 
5. Välj den **region** som du vill skapa klustret. Om du planerar att använda ett befintligt certifikat som du redan har laddat upp till en nyckelvalvet, måste du använda samma region som ditt nyckelvalv. 

#### <a name="2-cluster-configuration"></a>2. Klusterkonfiguration
![Skapa en nodtyp][CreateNodeType]

Konfigurera klusternoderna. Nodtyper definiera storlek på Virtuella datorer, hur många virtuella datorer och deras egenskaper. Klustret kan ha fler än en nodtyp, men den primära nodtypen (den första som du definierar i portalen) måste ha minst fem virtuella datorer, eftersom det är nodtypen där Service Fabric systemtjänster placeras. Konfigurera inte **placeringsegenskaper** eftersom en standardegenskap placering av ”NodeTypeName” läggs till automatiskt.

> [!NOTE]
> Ett vanligt scenario för flera nodtyper är ett program som innehåller en frontend-tjänst och en backend tjänst. Du vill publicera frontend-tjänsten på mindre virtuella datorer (VM-storlekar som D2_V2) med öppna portar till Internet och placera backend-tjänst på större VM: ar (med VM-storlekar som D3_V2, D6_V2, D15_V2 och så vidare) utan Internet-riktade öppna portar.
> 
> 

1. Välj ett namn för din nodtypen (1 till 12 tecken som innehåller endast bokstäver och siffror).
2. Minst **storlek** för virtuella datorer för den primära noden typen drivs av den **hållbarhet** nivå som du väljer för klustret. Standardvärdet för hållbarhet skiktet är Brons. Mer information om hållbarhet finns [hur du väljer Service Fabric-kluster hållbarhet][service-fabric-cluster-durability].
3. Välj VM-storlek. D-serien virtuella datorer ha SSD-enheterna och rekommenderas för tillståndskänsliga program. Använd inte VM-SKU som har delvis kärnor eller vara mindre än 10 GB tillgängligt diskutrymme. Referera till [service fabric-kluster planera övervägande dokumentet] [ service-fabric-cluster-capacity] för hjälp att välja VM-storlek.
4. Välj antalet virtuella datorer för nodtypen. Du kan skala upp eller ned antalet virtuella datorer i en nodtyp vid ett senare tillfälle, men på den primära nodtypen är minst fem för produktionsarbetsbelastningar. Andra nodtyper kan ha minst en virtuell dator. Minst **nummer** för virtuella datorer för de primära noden typen enheterna den **tillförlitlighet** på klustret.  
5. **Enkla kluster med noder och kluster med tre noder** -dessa är avsedda för test-bruk. De stöds inte för alla produktionsarbetsbelastningar som körs.
6. Konfigurera anpassade slutpunkter. Det här fältet kan du ange en kommaavgränsad lista över portar som du vill exponera via Azure-belastningsutjämnaren till det offentliga Internet för dina program. Om du planerar att distribuera ett program i klustret, t.ex ”80” här för att tillåta trafik på port 80 till klustret. Läs mer på slutpunkter [kommunicera med program][service-fabric-connect-and-communicate-with-services]
7. Konfigurera klustret **diagnostik**. Som standard aktiveras diagnostik på klustret att underlätta felsökning av problem. Om du vill inaktivera diagnostik ändra den **Status** växla till **av**. Om du inaktiverar diagnostik är **inte** rekommenderas. Om du redan har Application Insights-projekt du ge dess nyckel så att programspårningar dirigeras till den.
8. Välj Fabric Uppgraderingsläge du vill ange ditt kluster till. Välj **automatisk**, om du vill att hämta den senaste tillgängliga versionen och försök att uppgradera ditt kluster till det automatiskt. Ange läget som **manuell**, om du vill välja en version som stöds. För mer information om infrastrukturen uppgradera läge finns det [service fabric-kluster-uppgradering dokumentet.][service-fabric-cluster-upgrade]

> [!NOTE]
> Vi stöder endast kluster som kör versioner av service Fabric stöds. Genom att välja den **manuell** läge du tar med ansvar att uppgradera ditt kluster till en version som stöds. > 
> 

#### <a name="3-security"></a>3. Säkerhet
![Skärmbild som visar säkerhetskonfigurationer på Azure-portalen.][BasicSecurityConfigs]

Att ställa in en säker testklustret för dig, har vi angett den **grundläggande** alternativet. Om du redan har ett certifikat och har överförts till din keyvault (och aktiverat nyckelvalvet för distribution), använder du den **anpassad** alternativet

##### <a name="basic-option"></a>Grundläggande autentisering
Följer du anvisningarna för att lägga till eller återanvända en befintlig keyvault och lägga till ett certifikat. För att lägga till certifikatet är en synkron process så du måste vänta tills certifikatet som ska skapas.


Stå emot frestelsen med att navigera bort från skärmen förrän den föregående processen har slutförts.

![CreateKeyVault]

Nu när certifikatet har lagts till din keyvault, visas följande skärm där du uppmanas att redigera principer för åtkomst för dina Keyvault. Klicka på den **redigera åtkomstprinciper för.** till.

![CreateKeyVault2]

Klicka på Avancerat åtkomstprinciper och ge åtkomst till virtuella datorer för distribution. Det rekommenderas att du aktiverar malldistribution samt. När du har gjort dina val Glöm inte att klicka på den **spara** knappen och Stäng av den **åtkomstprinciper** fönstret.

![CreateKeyVault3]

Du är nu redo att gå vidare till resten av processen skapa klustret.

![CreateKeyVault4]

##### <a name="custom-option"></a>Anpassade alternativet
Hoppa över det här avsnittet om du redan har utfört stegen i den **grundläggande** alternativet.

![SecurityCustomOption]

Du behöver CertificateThumbprint, SourceVault och CertificateURL information för att slutföra säkerhetssidan. Om du inte har den praktiska, öppna ett nytt webbläsarfönster och gör följande


1. Navigera till din keyvault, Välj certifikatet. 
2. Välj egenskapsfliken ”” och kopiera ”resurs-ID” till ”källa Key vault” på webbläsarfönstret rätt 

    ![CertInfo0]

3. Nu Välj fliken ”certifikat”.
4. Klicka på tumavtrycket för certifikatet, som tar dig till sidan versioner.
5. Klicka på det GUID som visas under den aktuella versionen.

    ![CertInfo1]

6. Du bör nu vara på skärmen nedan. Kopiera certifikatets tumavtryck till ”tumavtryck” på webbläsarfönstret rätt
7. Kopiera den ”Secret Identifier” till ”Certifikatwebbadress” på andra fönster i webbläsaren.


![CertInfo2]


Kontrollera den **konfigurera avancerade inställningar** om du vill ange klientcertifikat för **administratörsklient** och **skrivskyddad klient**. I dessa fält, anger du tumavtrycket för admin klientcertifikatet och tumavtrycket för klientcertifikatet skrivskyddade användaren, om tillämpligt. När administratörer försöker ansluta till klustret kan beviljas åtkomst endast om de har ett certifikat med ett tumavtryck som matchar tumavtrycket värden anges här.  

#### <a name="4-summary"></a>4. Sammanfattning

Nu är du redo att distribuera klustret. Innan du gör det kan hämta certifikatet, titta i stora blå informationsmeddelande rutan för länken. Se till att hålla cert på en säker plats. Du måste den ansluta till klustret. Eftersom det certifikat du hämtade inte har ett lösenord, är det bäst att du lägger till en.

Skapa ett kluster, klicka på **skapa**. Alternativt kan du hämta mallen. 

![Sammanfattning]

Du kan se förloppet bland aviseringarna. (Klicka på klockikonen nära statusfältet uppe till höger på skärmen.) Om du klickade på **Fäst på startsidan** när du skapade klustret ser du **Deploying Service Fabric Cluster** (Distribuerar Service Fabric-kluster) fäst på **startsidan**.

Du behöver ansluta till klustret för att kunna utföra hanteringsåtgärder på ditt kluster med Powershell eller CLI Läs mer om hur du vid [ansluter till ditt kluster](service-fabric-connect-to-secure-cluster.md).

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
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
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
[BasicSecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/BasicSecurityConfigs.PNG
[CreateKeyVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[CreateKeyVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[CreateKeyVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[SecurityCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Sammanfattning]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
