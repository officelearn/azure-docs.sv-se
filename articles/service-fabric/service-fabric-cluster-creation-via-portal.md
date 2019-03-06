---
title: Skapa ett Service Fabric-kluster i Azure portal | Microsoft Docs
description: Lär dig hur du ställer in ett säkert Service Fabric-kluster i Azure med Azure-portalen och Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2018
ms.author: aljo
ms.openlocfilehash: 80a4014cb371f3c37c01fa1cf2d20041caf17184
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448229"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Skapa ett Service Fabric-kluster i Azure med hjälp av Azure-portalen
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Det här är en stegvis guide som vägleder dig genom stegen för att konfigurera ett Service Fabric-kluster (Linux eller Windows) i Azure med Azure portal. Den här guiden vägleder dig genom följande steg:

* Skapa ett kluster i Azure via Azure portal.
* Autentisera administratörer som använder certifikat.

> [!NOTE]
> För mer avancerade säkerhetsalternativ, till exempel autentisering av användare med Azure Active Directory och ställer in certifikat för programsäkerhet, [skapa klustret med Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Klustersäkerhet 
Certifikat används i Service Fabric till att autentisera och kryptera olika delar av ett kluster och de program som körs där. Mer information om hur du använder certifikat i Service Fabric finns i [säkerhetsscenarier för Service Fabric-kluster][service-fabric-cluster-security].

Om det här är första gången du skapar ett service fabric-kluster eller distribuerar ett kluster för arbetsbelastningar för testning kan du hoppa över till nästa avsnitt (**Skapa kluster i Azure Portal**) och har systemet ska generera certifikat som krävs för dina kluster som kör arbetsbelastningar för testning. Om du är hur du konfigurerar ett kluster för produktionsarbetsbelastningar och sedan fortsätta läsa.

#### <a name="cluster-and-server-certificate-required"></a>Klustret och server-certifikat (krävs)
Detta certifikat krävs för att skydda ett kluster och förhindra obehörig åtkomst till den. Den tillhandahåller Klustersäkerhet i ett par olika sätt:

* **Autentisering för klustret:** Autentiserar kommunikation från nod till nod för klustret federation. Endast de noder som kan bevisa sin identitet med det här certifikatet kan ansluta till klustret.
* **Server-autentisering:** Autentiserar slutpunkterna för klusterhantering i en Hanteringsklient så att hanteringsklienten vet att det pratar med verkligt kluster. Det här certifikatet ger också SSL för HTTPS-hanterings-API och för Service Fabric Explorer över HTTPS.

Certifikatet måste uppfylla följande krav för att du har följande syften:

* Certifikatet måste innehålla en privat nyckel.
* Certifikatet måste skapas för nyckelutbyte, kan exporteras till en Personal Information Exchange (.pfx)-fil.
* Certifikatets **ämnesnamn måste matcha domänen** används för åtkomst till Service Fabric-klustret. Detta krävs för att tillhandahålla SSL för klustrets HTTPS-hanteringsslutpunkter och Service Fabric Explorer. Du kan inte hämta ett SSL-certifikat från en certifikatutfärdare (CA) för den `.cloudapp.azure.com` domän. Hämta ett anpassat domännamn för klustret. När du begär ett certifikat från en Certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamnet som används för klustret.

#### <a name="client-authentication-certificates"></a>Certifikat för klientautentisering
Ytterligare klientcertifikat autentisera administratörer för hanteringsuppgifter för klustret. Service Fabric har två åtkomstnivåer: **admin** och **skrivskyddade användaren**. Åtminstone ska ett enda certifikat för administrativ åtkomst användas. För ytterligare användarnivå åtkomst måste du tillhandahålla ett separat intyg. Mer information om roller för åtkomst finns i [rollbaserad åtkomstkontroll för Service Fabric-klienter][service-fabric-cluster-security-roles].

Du behöver inte ladda upp certifikat för klientautentisering till Key Vault för att arbeta med Service Fabric. Dessa certifikat behöver bara ska distribueras till användare som har behörighet för klusterhantering. 

> [!NOTE]
> Azure Active Directory är det rekommenderade sättet att autentisera klienter för klusterhanteringsåtgärder. Om du vill använda Azure Active Directory, måste du [skapa ett kluster med Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certifikat för programmet (valfritt)
Valfritt antal ytterligare certifikat kan installeras på ett kluster av säkerhetsskäl för programmet. Tänk på applikationssäkerhets-scenarier som kräver ett certifikat installeras på noderna, till exempel innan du skapar klustret:

* Kryptering och dekryptering av konfigurationsvärden för programmet
* Kryptering av data mellan noderna under replikering 

Programcertifikat får inte vara konfigurerat när [skapar ett kluster via Azure portal](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). För att konfigurera programmet certifikat vid kluster konfigurationen, måste du [skapa ett kluster med Azure Resource Manager][create-cluster-arm]. Du kan också lägga till programcertifikat till klustret när den har skapats.

## <a name="create-cluster-in-the-azure-portal"></a>Skapa kluster i Azure portal

Skapar ett produktionskluster för att passa dina programbehov innebär lite planering, som hjälper dig med som, vi rekommenderar starkt att du har läst och förstått det [Service Fabric-kluster saker att tänka på] [ service-fabric-cluster-capacity] dokumentet. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Sök efter Service Fabric-klusterresursen

Logga in på [Azure Portal][azure-portal].
Klicka på **skapa en resurs** att lägga till en ny resursmall. Sök efter Service Fabric-kluster-mallen i den **Marketplace** under **allt**.
Välj **Service Fabric-kluster** i listan.

![Sök efter Service Fabric-kluster-mall i Azure-portalen.][SearchforServiceFabricClusterTemplate]

Navigera till den **Service Fabric-kluster** bladet och klickar på **skapa**.

Den **skapa Service Fabric-kluster** bladet har följande fyra steg:

### <a name="1-basics"></a>1. Grundläggande inställningar
![Skärmbild för att skapa en ny resursgrupp.][CreateRG]

I bladet grundläggande inställningar som du behöver ange grundläggande information för klustret.

1. Ange namnet på klustret.
2. Ange en **användarnamn** och **lösenord** för fjärrskrivbord för de virtuella datorerna.
3. Se till att välja den **prenumeration** som du vill att ditt kluster kan distribueras till, särskilt om du har flera prenumerationer.
4. Skapa en ny **resursgrupp**. Det är bäst att ge det samma namn som klustret, eftersom det hjälper dig att hitta dem senare, särskilt när du försöker göra ändringar i din distribution eller ta bort ditt kluster.
   
   > [!NOTE]
   > Även om du kan välja att använda en befintlig resursgrupp, är det en bra idé att skapa en ny resursgrupp. Detta gör det enkelt att ta bort kluster och alla resurser som används.
   > 
   > 
5. Välj den **plats** som du vill skapa klustret. Om du planerar att använda ett befintligt certifikat som du redan har överfört till ett nyckelvalv, måste du använda samma region som ditt nyckelvalv. 

### <a name="2-cluster-configuration"></a>2. Klusterkonfiguration
![Skapa en nodtyp][CreateNodeType]

Konfigurera klusternoderna. Nodtyper definierar storlekarna, hur många virtuella datorer och deras egenskaper. Klustret kan innehålla fler än en nodtyp, men den primära nodtypen (den första som du definierar i portalen) måste ha minst fem virtuella datorer, eftersom det här är nodtyp där Service Fabric-systemtjänster placeras. Konfigurera inte **placeringsegenskaper** eftersom en standardegenskap placering av ”NodeTypeName” har lagts till automatiskt.

> [!NOTE]
> Ett vanligt scenario för flera nodtyper är ett program som innehåller en frontend-tjänst och en backend tjänst. Du vill placera frontend-tjänst på mindre virtuella datorer (VM-storlekar som D2_V2) med portar öppna till Internet och placera backend-tjänst i större datorer (med VM-storlekar som D3_V2, D6_V2, D15_V2 och så vidare) utan öppna portar för webbservergrupper på Internet.
> 

1. Välj ett namn för nodtypen (1 och 12 tecken som innehåller endast bokstäver och siffror).
2. Minst **storlek** av virtuella datorer för den primära noden typ drivs av den **hållbarhetsnivå** du väljer för klustret. Standardvärdet för hållbarhetsnivån är Brons. Mer information om hållbarhet finns [hur du väljer Service Fabric-kluster hållbarhet][service-fabric-cluster-durability].
3. Välj den **VM-storlek**. Virtuella datorer i D-serien har SSD-enheterna och rekommenderas för tillståndskänsliga program. Använd inte VM-SKU som har delvis kärnor eller mindre än 10 GB av tillgängligt diskutrymme. Referera till [service fabric-kluster planeringsdokument övervägande] [ service-fabric-cluster-capacity] för hjälp med att välja virtuella datorstorlek.
4.  **Enkel kluster med noder och kluster med tre noder** är avsedda för testning. De stöds inte för alla arbetsbelastningar som körs i produktion.
5. Välj den **kapacitet för inledande VM-skalningsuppsättningar** för nodtypen. Du kan skala upp eller ned antalet virtuella datorer i en nodtyp vid ett senare tillfälle, men på den primära nodtypen är minst fem för produktionsarbetsbelastningar. Andra nodtyper kan ha minst en virtuell dator. Minst **nummer** av virtuella datorer för de primära noden typ enheterna den **tillförlitlighet** på klustret.  
6. Konfigurera **anpassade slutpunkter**. Det här fältet kan du ange en kommaavgränsad lista över portar som du vill exponera via Azure-belastningsutjämnaren till det offentliga Internet för dina program. Exempel: Om du planerar att distribuera ett program i klustret, ange ”80” här för att tillåta trafik på port 80 i klustret. Läs mer på slutpunkter [kommunicerar med program][service-fabric-connect-and-communicate-with-services]
7. **Aktivera omvänd proxy**.  Den [Service Fabric omvänd proxy](service-fabric-reverseproxy.md) hjälper till att mikrotjänster som körs i ett Service Fabric-kluster identifiera och kommunicera med andra tjänster som har http-slutpunkter.
8. I den **klusterkonfiguration** bladet under **+ visa valfria inställningar**, konfigurera klustret **diagnostik**. Som standard aktiveras diagnostik på ditt kluster som hjälper till med felsökning av problem. Om du vill inaktivera diagnostik ändra den **Status** växla till **av**. Om du inaktiverar diagnostik är **inte** rekommenderas. Om du redan har Application Insights-projekt som skapas kan du ge dess nyckel så att programspårningar dirigeras till den.
9. **Inkludera DNS-tjänsten**.  Den [DNS-tjänsten](service-fabric-dnsservice.md) en valfri tjänst som hjälper dig att hitta andra tjänster med hjälp av DNS-protokollet.
10. Välj den **Fabric Uppgraderingsläge** du vill ställa in ditt kluster. Välj **automatisk**om du vill att systemet kan automatiskt hämta den senaste tillgängliga versionen och försök att uppgradera klustret till den. Ange läget till **manuell**, om du vill välja en version som stöds. För mer information om infrastrukturen uppgradera läge finns i den [uppgradering av Service Fabric kluster dokumentet.][service-fabric-cluster-upgrade]

> [!NOTE]
> Vi stöder endast kluster som kör versioner som stöds av Service Fabric. Genom att välja den **manuell** läge du vidtar ansvar att uppgradera klustret till en version som stöds.
> 

### <a name="3-security"></a>3. Säkerhet
![Skärmbild av säkerhetskonfigurationer på Azure-portalen.][BasicSecurityConfigs]

Om du vill se hur du konfigurerar ett testkluster med säker enkelt för dig har vi samlat den **grundläggande** alternativet. Om du redan har ett certifikat och har laddat upp den till din [nyckelvalv](/azure/key-vault/) och aktiverat nyckelvalvet för distribution, sedan använda den **anpassad** alternativet

#### <a name="basic-option"></a>Grundläggande alternativ
Följ anvisningarna för att lägga till eller återanvända ett befintligt nyckelvalv och Lägg till ett certifikat. Att lägga till certifikatet är en synkron process och så du måste vänta tills certifikatet som ska skapas.

Motstå frestelsen genom att gå från skärmen tills den föregående processen har slutförts.

![CreateKeyVault]

Nu när nyckelvalvet har skapats kan du redigera åtkomstprinciper för nyckelvalvet. 

![CreateKeyVault2]

Klicka på den **redigera åtkomstprinciper**, sedan **visa avancerade åtkomstprinciper** och aktivera åtkomst till Azure Virtual Machines för distribution. Vi rekommenderar att du aktiverar malldistributionen samt. När du har gjort dina val, Glöm inte att klicka på den **spara** knappen och Stäng av den **åtkomstprinciper** fönstret.

![CreateKeyVault3]

Ange namnet på certifikatet och klicka på **OK**.

![CreateKeyVault4]

#### <a name="custom-option"></a>Anpassat alternativ
Hoppa över det här avsnittet om du redan har utfört stegen i den **grundläggande** alternativet.

![SecurityCustomOption]

Du behöver källnyckelvalvet, certifikatets Webbadress och information om tumavtryck för certifikat för att slutföra sidan. Om du inte har det gör praktiskt, öppna ett nytt webbläsarfönster och i Azure-portalen du följande

1. Gå till key vault-tjänsten.
2. Välj fliken ”Egenskaper” och kopiera ”resurs-ID” till ”källnyckelvalvet” på andra webbläsarfönstret 

    ![CertInfo0]

3. Nu, Välj fliken ”certifikat”.
4. Klicka på tumavtrycket för certifikatet, som tar dig till sidan versioner.
5. Klicka på de GUID som visas under den aktuella versionen.

    ![CertInfo1]

6. Du bör nu finnas på skärmen nedan. Kopiera den hexadecimala SHA-1-tumavtryck till ”certifikatets tumavtryck” på andra webbläsarfönstret
7. Kopiera hemlig identifierare till ”certifikatets Webbadress” på andra webbläsarfönster.

    ![CertInfo2]

Kontrollera den **konfigurera avancerade inställningar** om du vill ange klientcertifikat för **administratörsklient** och **skrivskyddad klient**. I de här fälten, anger du tumavtrycket för klientcertifikat för administratör och tumavtrycket för klientcertifikatet skrivskyddade användaren, om tillämpligt. När administratörer försöker ansluta till klustret, ges de åtkomst endast om de har ett certifikat med ett tumavtryck som matchar tumavtrycket värden anges här.  

### <a name="4-summary"></a>4. Sammanfattning

Nu är du redo att distribuera klustret. Innan du gör du genom att hämta certifikatet, titta i stora blå informationsmeddelande rutan för länken. Se till att välja certifikatet på en säker plats. Du behöver den för att ansluta till klustret. Eftersom det certifikat som du har laddat ned inte har ett lösenord, vi rekommenderar att du lägger till en.

För att slutföra skapa ett kluster, klickar du på **skapa**. Du kan också hämta mallen.

![Sammanfattning]

Du kan se förloppet bland aviseringarna. (Klicka på klockikonen nära statusfältet uppe till höger på skärmen.) Om du klickade på **Fäst på startsidan** när du skapade klustret ser du **Deploying Service Fabric Cluster** (Distribuerar Service Fabric-kluster) fäst på **startsidan**. Den här processen kommer ta lite tid. 

För att kunna utföra hanteringsåtgärder på ditt kluster med Powershell eller CLI, måste du ansluta till ditt kluster, Läs mer om hur du på [ansluter till ditt kluster](service-fabric-connect-to-secure-cluster.md).

## <a name="view-your-cluster-status"></a>Visa klusterstatus för
![Skärmbild av klusterinformation i instrumentpanelen.][ClusterDashboard]

När klustret har skapats kan visa du ditt kluster i portalen:

1. Gå till **Bläddra** och klicka på **Service Fabric-kluster**.
2. Leta upp ditt kluster och klicka på den.
3. Där kan du se information om klustret på instrumentpanelen, bland annat klustrets offentliga slutpunkter och en länk till Service Fabric Explorer.

Den **noden övervakaren** avsnittet på klustrets instrumentpanel-bladet anger hur många virtuella datorer som är felfria och inte felfri. Du hittar mer information om klustrets hälsa i [Service Fabric health modellen introduktion][service-fabric-health-introduction].

> [!NOTE]
> Service Fabric-kluster kräver ett visst antal noder krävs för att alltid att bibehålla tillgänglighet och bevara tillstånd - kallas ”upprätthålla kvorum”. Därför är det vanligtvis inte säkert att Stäng alla datorer i klustret, såvida inte du först har utfört en [fullständig säkerhetskopiering av din delstat][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Fjärransluta till en Virtual Machine Scale Sets instans eller en klusternod
Var och en av NodeTypes som du anger i klustret resultaten i Virtual Machine Scale Sets komma konfiguration. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Nästa steg
Nu har du ett säkert kluster som använder certifikat för hantering av autentisering. Nästa [ansluta till klustret](service-fabric-connect-to-secure-cluster.md) och lär dig hur du [hantera programhemligheter](service-fabric-application-secret-management.md).  Lär dig även om [Service Fabric-supportalternativ](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
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
