---
title: Skapa ett Service Fabric-kluster i Azure-portalen
description: Lär dig hur du konfigurerar ett säkert Service Fabric-kluster i Azure med Azure-portalen och Azure Key Vault.
ms.topic: conceptual
ms.date: 09/06/2018
ms.openlocfilehash: e0cd3d5e5a37720134a5bce596bba211b375f19d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458325"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Skapa ett Service Fabric-kluster i Azure med Azure-portalen
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Det här är en steg-för-steg-guide som hjälper dig att konfigurera ett Service Fabric-kluster (Linux eller Windows) i Azure med Hjälp av Azure-portalen. Den här guiden går igenom följande steg:

* Skapa ett kluster i Azure via Azure-portalen.
* Autentisera administratörer med certifikat.

> [!NOTE]
> Om du vill ha mer avancerade säkerhetsalternativ, till exempel användarautentisering med Azure Active Directory och konfigurera certifikat för programsäkerhet, [skapar du klustret med Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Klustersäkerhet 
Certifikat används i Service Fabric till att autentisera och kryptera olika delar av ett kluster och de program som körs där. Mer information om hur du använder certifikat i Service Fabric finns i [Service Fabric cluster security scenarios][service-fabric-cluster-security] (Säkerhet för Service Fabric-kluster).

Om det är första gången du skapar ett tjänstinfrastrukturkluster eller distribuerar ett kluster för testarbetsbelastningar, kan du hoppa till nästa avsnitt (**Skapa kluster i Azure Portal**) och låta systemet generera certifikat som behövs för dina kluster som kör testarbetsbelastningar. Om du konfigurerar ett kluster för produktionsarbetsbelastningar fortsätter du att läsa.

#### <a name="cluster-and-server-certificate-required"></a>Kluster- och servercertifikat (obligatoriskt)
Det här certifikatet krävs för att skydda ett kluster och förhindra obehörig åtkomst till det. Det ger klustersäkerhet på ett par sätt:

* **Klusterautentisering:** Autentiserar nod-till-nodkommunikation för klusterfederation. Endast noder som kan bevisa sin identitet med det här certifikatet kan ansluta till klustret.
* **Autentisering av servrar:** Autentiserar slutpunkterna för klusterhantering till en hanteringsklient, så att hanteringsklienten vet att den talar med det verkliga klustret. Det här certifikatet tillhandahåller även TLS för HTTPS-hanterings-API:et och för Service Fabric Explorer via HTTPS.

För att kunna tjäna dessa syften måste certifikatet uppfylla följande krav:

* Certifikatet måste innehålla en privat nyckel.
* Certifikatet måste skapas för nyckelutbyte, som kan exporteras till en fil för personligt informationsutbyte (.pfx).
* Certifikatets **ämnesnamn måste matcha den domän** som används för att komma åt Service Fabric-klustret. Detta krävs för att tillhandahålla TLS för klustrets HTTPS-hanteringsslutpunkter och Service Fabric Explorer. Du kan inte hämta ett TLS/SSL-certifikat från `.cloudapp.azure.com` en certifikatutfärdarcertifikat för domänen. Skaffa ett anpassat domännamn för klustret. När du begär ett certifikat från en certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamnet som används för klustret.

#### <a name="client-authentication-certificates"></a>Certifikat för klientautentisering
Ytterligare klientcertifikat autentiserar administratörer för klusterhanteringsuppgifter. Service Fabric har två åtkomstnivåer: **admin** och **skrivskyddad användare**. Minst bör ett enda certifikat för administrativ åtkomst användas. För ytterligare åtkomst på användarnivå måste ett separat certifikat tillhandahållas. Mer information om åtkomstroller finns i [rollbaserad åtkomstkontroll för Service Fabric-klienter][service-fabric-cluster-security-roles].

Du behöver inte ladda upp klientautentiseringscertifikat till Key Vault för att fungera med Service Fabric. Dessa certifikat behöver bara tillhandahållas till användare som är auktoriserade för klusterhantering. 

> [!NOTE]
> Azure Active Directory är det rekommenderade sättet att autentisera klienter för klusterhanteringsåtgärder. Om du vill använda Azure Active Directory måste du [skapa ett kluster med Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Programcertifikat (valfritt)
Valfritt antal ytterligare certifikat kan installeras i ett kluster för programsäkerhetsändamål. Innan du skapar klustret bör du tänka på de programsäkerhetsscenarier som kräver att ett certifikat installeras på noderna, till exempel:

* Kryptering och dekryptering av programkonfigurationsvärden
* Kryptering av data mellan noder under replikering 

Programcertifikat kan inte konfigureras när [ett kluster skapas via Azure-portalen](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). Om du vill konfigurera programcertifikat vid klusterinställningstid måste du [skapa ett kluster med Azure Resource Manager][create-cluster-arm]. Du kan också lägga till programcertifikat i klustret när det har skapats.

## <a name="create-cluster-in-the-azure-portal"></a>Skapa kluster i Azure-portalen

Att skapa ett produktionskluster för att uppfylla dina programbehov innebär viss planering, för att hjälpa dig med det, rekommenderar vi starkt att du läser och förstår service [fabric-klusterplaneringsöverväganden.][service-fabric-cluster-capacity] 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Sök efter klusterresursen Service Fabric

Logga in på [Azure-portalen][azure-portal].
Klicka på **Skapa en resurs** om du vill lägga till en ny resursmall. Sök efter mallen Service Fabric Cluster på **Marketplace** under **Allt**.
Välj **Service Fabric Cluster** i listan.

![sök efter klustermall för Service Fabric på Azure-portalen.][SearchforServiceFabricClusterTemplate]

Navigera till bladet **Service Fabric Cluster** och klicka på **Skapa**.

**Klusterbladet Skapa service fabric** har följande fyra steg:

### <a name="1-basics"></a>1. Grunderna
![Skärmbild av hur du skapar en ny resursgrupp.][CreateRG]

I bladet Grunderna måste du ange grundläggande information för klustret.

1. Ange namnet på klustret.
2. Ange ett **användarnamn** och **lösenord** för fjärrskrivbord för de virtuella datorerna.
3. Se till att välja den **prenumeration** som du vill att klustret ska distribueras till, särskilt om du har flera prenumerationer.
4. Skapa en ny **resursgrupp**. Det är bäst att ge det samma namn som klustret, eftersom det hjälper till att hitta dem senare, särskilt när du försöker göra ändringar i distributionen eller ta bort klustret.
   
   > [!NOTE]
   > Även om du kan välja att använda en befintlig resursgrupp är det en bra idé att skapa en ny resursgrupp. Detta gör det enkelt att ta bort kluster och alla resurser som används.
   > 
   > 
5. Välj den **plats** där du vill skapa klustret. Om du planerar att använda ett befintligt certifikat som du redan har laddat upp till ett nyckelvalv måste du använda samma region som nyckelvalvet finns i. 

### <a name="2-cluster-configuration"></a>2. Klusterkonfiguration
![Skapa en nodtyp][CreateNodeType]

Konfigurera klusternoderna. Nodtyper definierar vm-storlekar, antalet virtuella datorer och deras egenskaper. Klustret kan ha mer än en nodtyp, men den primära nodtypen (den första som du definierar på portalen) måste ha minst fem virtuella datorer, eftersom det här är nodtypen där Service Fabric-systemtjänster placeras. Konfigurera inte **placeringsegenskaper** eftersom en standardplaceringsegenskap för "NodeTypeName" läggs till automatiskt.

> [!NOTE]
> Ett vanligt scenario för flera nodtyper är ett program som innehåller en frontend-tjänst och en backend-tjänst. Du vill placera frontend-tjänsten på mindre virtuella datorer (VM-storlekar som D2_V2) med portar öppna mot Internet och placera backend-tjänsten på större virtuella datorer (med vm-storlekar som D3_V2, D6_V2, D15_V2 och så vidare) utan Internet-öppna portar.
> 

1. Välj ett namn för nodtypen (1 till 12 tecken som bara innehåller bokstäver och siffror).
2. Den minsta **storleken på** virtuella datorer för den primära nodtypen styrs av den hållbarhetsnivå som du väljer för **klustret.** Standardnivån för hållbarhetsnivån är brons. Mer information om hållbarhet finns i [hur du väljer service fabric-klusterhållbarheten][service-fabric-cluster-durability].
3. Välj **storleken**på den virtuella datorn . Virtuella datorer i D-serien har SSD-enheter och rekommenderas starkt för tillståndskänsliga program. Använd inte någon VM SKU som har partiella kärnor eller har mindre än 10 GB tillgänglig diskkapacitet. Se dokumentet för att planera behandling av [tjänstinfrastrukturkluster][service-fabric-cluster-capacity] för att få hjälp med att välja storleken på den virtuella datorn.
4.  **Ennodskluster och tre nodkluster** är endast avsedda för testanvändning. De stöds inte för alla produktionsarbetsbelastningar som körs.
5. Välj den **ursprungliga vm-skalningsuppsättningskapaciteten** för nodtypen. Du kan skala upp eller ned antalet virtuella datorer i en nodtyp senare, men på den primära nodtypen är det minsta fem för produktionsarbetsbelastningar. Andra nodtyper kan ha minst en virtuell dator. Det minsta **antalet** virtuella datorer för den primära nodtypen styr **tillförlitligheten** i klustret.  
6. Konfigurera **anpassade slutpunkter**. I det här fältet kan du ange en kommaavgränsad lista över portar som du vill exponera via Azure Load Balancer till det offentliga Internet för dina program. Om du till exempel planerar att distribuera ett webbprogram till klustret anger du "80" här för att tillåta trafik på port 80 i klustret. Mer information om slutpunkter finns i [kommunicera med program][service-fabric-connect-and-communicate-with-services]
7. **Aktivera omvänd proxy**.  [Omvänd proxy för Service Fabric](service-fabric-reverseproxy.md) hjälper mikrotjänster som körs i ett Service Fabric-kluster att upptäcka och kommunicera med andra tjänster som har http-slutpunkter.
8. Tillbaka i **klusterkonfigurationsbladet,** under **+Visa valfria inställningar**, konfigurera **klusterdiagnostik**. Som standard är diagnostik aktiverat i klustret för att hjälpa till med felsökningsproblem. Om du vill inaktivera diagnostik ändra **statusväxlingsknappen** till **Av**. Det rekommenderas **inte** att stänga av diagnostiken. Om du redan har skapat Application Insights-projektet ger du dess nyckel så att programspårningarna dirigeras till det.
9. **Inkludera DNS-tjänst**.  [DNS-tjänsten](service-fabric-dnsservice.md) en valfri tjänst som gör att du kan hitta andra tjänster med HJÄLP av DNS-protokollet.
10. Välj det **uppgraderingsläge för fabric** som du vill ställa in klustret på. Välj **Automatisk**, om du vill att systemet automatiskt ska plocka upp den senaste tillgängliga versionen och försöka uppgradera klustret till det. Ställ in läget på **Manuell**, om du vill välja en version som stöds. Mer information om uppgraderingsläget För infrastruktur finns i [servicetygsklusteruppgraderingsdokumentet.][service-fabric-cluster-upgrade]

> [!NOTE]
> Vi stöder endast kluster som kör versioner av Service Fabric som stöds. Genom att välja **manuellt** läge tar du på dig ansvaret för att uppgradera klustret till en version som stöds.
> 

### <a name="3-security"></a>3. Säkerhet
![Skärmbild av säkerhetskonfigurationer på Azure-portalen.][BasicSecurityConfigs]

För att göra det enkelt för dig att konfigurera ett säkert testkluster har vi tillhandahållit alternativet **Grundläggande.** Om du redan har ett certifikat och har laddat upp det till [nyckelvalvet](/azure/key-vault/) (och aktiverat nyckelvalvet för distribution) använder du alternativet **Anpassad**

#### <a name="basic-option"></a>Grundläggande alternativ
Följ skärmarna för att lägga till eller återanvända ett befintligt nyckelvalv och lägg till ett certifikat. Tillägget av certifikatet är en synkron process och därför måste du vänta på att certifikatet ska skapas.

Motstå frestelsen att navigera bort från skärmen tills föregående process är klar.

![Skapa NyckelVault]

Nu när nyckelvalvet har skapats redigerar du åtkomstprinciperna för nyckelvalvet. 

![Skapa NyckelVault2]

Klicka på **principerna för redigera åtkomst**, sedan Visa avancerade **åtkomstprinciper** och aktivera åtkomst till Virtuella Azure-datorer för distribution. Vi rekommenderar att du också aktiverar malldistributionen. När du har gjort dina val, glöm inte att klicka på **knappen Spara** och stänga ute i **fönstret Åtkomstprinciper.**

![Skapa NyckelVault3]

Ange namnet på certifikatet och klicka på **OK**.

![Skapa NyckelVault4]

#### <a name="custom-option"></a>Anpassat alternativ
Hoppa över det här avsnittet om du redan har utfört stegen i **det grundläggande** alternativet.

![SäkerhetCustomOption]

Du behöver informationen om källnyckelvalvet, certifikat-URL:en och certifikatets tumavtryck för att slutföra säkerhetssidan. Om du inte har det praktiskt öppnar du ett annat webbläsarfönster och gör följande i Azure-portalen

1. Navigera till din nyckelvalvstjänst.
2. Välj fliken Egenskaper och kopiera "RESURS-ID" till "Source key vault" i det andra webbläsarfönstret 

    ![CertInfo0]

3. Välj nu fliken "Certifikat".
4. Klicka på certifikatets tumavtryck, som tar dig till sidan Versioner.
5. Klicka på de GUIDs du ser under den aktuella versionen.

    ![CertInfo1]

6. Du bör nu vara på skärmen som nedan. Kopiera det hexadecimala SHA-1-tumavtrycket till "Certificate thumbprint" i det andra webbläsarfönstret
7. Kopiera "Hemlig identifierare" till "Certifikat-URL" i andra webbläsarfönster.

    ![CertInfo2]

Markera rutan **Konfigurera avancerade inställningar** om du vill ange klientcertifikat för **administratörsklient** och **skrivskyddad klient**. I dessa fält anger du tumavtrycket för administratörsklientcertifikatet och tumavtrycket för ditt skrivskyddade användarklientcertifikat, om tillämpligt. När administratörer försöker ansluta till klustret beviljas de endast åtkomst om de har ett certifikat med ett tumavtryck som matchar tumavtrycksvärdena som anges här.  

### <a name="4-summary"></a>4. Sammanfattning

Nu är du redo att distribuera klustret. Innan du gör det laddar du ned certifikatet och tittar i den stora blå informationsrutan för länken. Se till att förvara certifikatet på ett säkert ställe. du behöver den för att ansluta till klustret. Eftersom certifikatet du hämtade inte har något lösenord rekommenderas att du lägger till ett.

Om du vill slutföra skapandet av kluster klickar du på **Skapa**. Du kan också hämta mallen.

![Sammanfattning]

Du kan se förloppet bland aviseringarna. (Klicka på "Bell"-ikonen nära statusfältet längst upp till höger på skärmen.) Om du klickade på **Fäst på Startboard** när du skapade **klustret visas Distribuera serviceinfrastrukturkluster** som är fäst på **Startkortet.** Denna process kommer att ta lite tid. 

För att utföra hanteringsåtgärder i klustret med Powershell eller CLI måste du ansluta till klustret, läs mer om hur du [ansluter till klustret](service-fabric-connect-to-secure-cluster.md).

## <a name="view-your-cluster-status"></a>Visa klusterstatus
![Skärmbild av klusterinformation på instrumentpanelen.][ClusterDashboard]

När klustret har skapats kan du granska klustret i portalen:

1. Gå till **Bläddra** och klicka på **Service Fabric Clusters**.
2. Leta reda på klustret och klicka på det.
3. Där kan du se information om klustret på instrumentpanelen, bland annat klustrets offentliga slutpunkter och en länk till Service Fabric Explorer.

Avsnittet **Nodövervakare** på klustrets instrumentpanelsblad anger antalet virtuella datorer som är felfria och inte felfria. Du hittar mer information om klustrets hälsomodell introduktion på [Service Fabric.][service-fabric-health-introduction]

> [!NOTE]
> Service Fabric kluster kräver ett visst antal noder att vara upp alltid för att upprätthålla tillgänglighet och bevara tillstånd - kallad "upprätthålla kvorum". Därför är det vanligtvis inte säkert att stänga av alla datorer i klustret om du inte först har utfört en [fullständig säkerhetskopia av ditt tillstånd][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Fjärranslutning till en skaladiseringsförekomst för virtuell dator eller en klusternod
Var och en av de nodetyper som du anger i klustret resulterar i en skaladator för virtuell dator som konfigureras. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Nästa steg
Nu har du ett säkert kluster med certifikat för hanteringsautentisering. Anslut [sedan till klustret](service-fabric-connect-to-secure-cluster.md) och lär dig hur du [hanterar programhemligheter](service-fabric-application-secret-management.md).  Lär dig också mer om [supportalternativ för Service Fabric.](service-fabric-support.md)

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/general/overview.md
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
[Skapa NyckelVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[Skapa NyckelVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[Skapa NyckelVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[Skapa NyckelVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[SäkerhetCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Sammanfattning]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
