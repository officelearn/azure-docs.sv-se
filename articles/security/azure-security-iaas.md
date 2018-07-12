---
title: Säkerhetsmetodtips för IaaS-arbetsbelastningar i Azure | Microsoft Docs
description: " Migrering av arbetsbelastningar till Azure IaaS ger dig möjligheter att omvärdera våra design "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2018
ms.author: barclayn
ms.openlocfilehash: 37620e70377e3f1fbeeeb73aaa294c5f54cf5b3d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38724101"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Rekommenderade säkerhetsmetoder för IaaS-arbetsbelastningar i Azure

I de flesta infrastruktur som en tjänst (IaaS)-scenarier, [virtuella Azure-datorer (VM)](https://docs.microsoft.com/azure/virtual-machines/) är huvudsakliga arbetsbelastningen för organisationer som använder molnbaserad databehandling. Detta är särskilt tydligt i [hybridscenarier](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) där organisationer vill långsamt migrera arbetsbelastningar till molnet. I sådana scenarier, följer du de [allmänna säkerhetsaspekter för IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx), och tillämpa rekommenderade säkerhetsmetoder för alla dina virtuella datorer.

Ditt ansvar för säkerhet baseras på vilken typ av tjänst i molnet. Följande diagram sammanfattar balans ansvar för både Microsoft och du:

![Ansvarsområden](./media/azure-security-iaas/sec-cloudstack-new.png)

Säkerhetskrav varierar beroende på ett antal faktorer, inklusive olika typer av arbetsbelastningar. Inte en av dessa metodtips skydda ensamt dina system. Som allt annat i security måste du välja lämpliga alternativ och se hur lösningarna kan kompletterar varandra genom att fylla luckor.

Den här artikeln beskrivs olika VM-säkerhetsmetoder, var och en har härletts från kunder och Microsofts egna direkt upplevelser med virtuella datorer.

De bästa metoderna är baserade på en enhälligt av åsikter och de fungerar med den aktuella Azure-plattformsfunktioner och egenskapsuppsättningar. Eftersom andras åsikter och tekniker kan ändras med tiden, den här artikeln kommer att uppdateras med dessa ändringar.

## <a name="use-privileged-access-workstations"></a>Använda arbetsstationer för privilegierad åtkomst

Organisationer ofta faller använder sig mot cyberattacker eftersom administratörer utför åtgärder vid användning av konton med utökade behörigheter. Detta kan inte vara resultatet av skadlig aktivitet, beror det på att befintliga konfiguration och processer för att den. De flesta av dessa användare är medveten om risken för dessa åtgärder från en konceptuell synvinkel men fortfarande välja att göra dem.

Gör saker som e-post- och Internetanvändning verka ofarliga försöker tillräckligt. Men de kan exponera upphöjd konton för att angripa med skadliga aktörer. Bläddra aktiviteter, särskilt utformad e-post eller andra metoder kan användas för att få åtkomst till ditt företag. Vi rekommenderar starkt att användningen av säker hantering av arbetsstationer (saw) för att utföra alla Azure administrationsuppgifter. Saw är ett sätt att minska exponeringen för oavsiktlig kompromettering.

Arbetsstationer för privilegierad åtkomst (Paw) ger ett dedikerat operativsystem för känsliga uppgifter – en som är skyddat mot internetattacker och hotvektorer. Separera känsliga uppgifter och konton från arbetsstationer för daglig användning och enheter ger starkt skydd. Den här separationen begränsar effekten av nätfiskeattacker, program och OS-sårbarheter, olika personifiering attacker och stöldattacker av autentiseringsuppgifter. (loggning av tangenttryckningar, Pass-the-Hash och Pass-the-Ticket)

PAW-metoden är en utökning av välkänt och praktiskt att använda ett individuellt tilldelat administrativt konto. Det administrativa kontot som är separat från ett vanligt användarkonto. En PAW som ger en säker arbetsstation för sådana känsliga kontona.

Mer information och implementering vägledning finns i [arbetsstationer för privilegierad åtkomst](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations).

## <a name="use-multi-factor-authentication"></a>Använd Multi-Factor Authentication

Tidigare användes perimeternätverket att styra åtkomsten till företagets data. I en moln- och mobilinriktad värld identiteten är kontrollplanet: du kan använda den för att kontrollera åtkomsten till IaaS-tjänster från valfri enhet. Du också använda den för att få insyn i och insyn i var och hur dina data som används. Skydda digital identiteten för ditt Azure-användare är Hörnstenen i för att skydda dina prenumerationer från identitetsstöld och andra cybercrimes.

En av de mest fördelaktigt steg som du kan vidta för att skydda ett konto är att aktivera tvåfaktorsautentisering. Tvåfaktorsautentisering är ett sätt att autentisera med hjälp av något utöver ett lösenord. Det hjälper dig att minska risken för åtkomst av någon som är ansvarig för att få någon annans lösenord.

[Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) hjälper dig att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål för en enkel inloggningsprocess. Du får stark autentisering med en rad enkla verifieringsalternativ – telefonsamtal, SMS eller mobilapp. Användare välja den metod som de föredrar.

Det enklaste sättet att använda Multi-Factor Authentication är Microsoft Authenticator-mobilappen som kan användas på mobila enheter som kör Windows, iOS och Android. Med den senaste versionen av Windows 10 och integrering av lokala Active Directory med Azure Active Directory (Azure AD), [Windows Hello för företag](../active-directory/active-directory-azureadjoin-passport-deployment.md) kan användas för sömlös enkel inloggning till Azure-resurser. I det här fallet används Windows 10-enhet som den andra faktorn för autentisering.

För konton som hanterar din Azure-prenumeration och för konton som kan logga in på virtuella datorer ger mycket bättre säkerhet än att använda endast ett lösenord om du använder multi-Factor Authentication. Andra former av tvåfaktorsautentisering fungerar lika bra, men distribuerar dem kan vara komplicerade om de inte redan är i produktion.

Följande skärmbild visar några av de tillgängliga alternativen för Azure Multi-Factor Authentication:

![Multi-Factor Authentication-alternativ](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>Begränsa och begränsa administrativ åtkomst

Det är viktigt att skydda de konton som kan hantera din Azure-prenumeration. Förlust av någon av dessa konton eliminerar värdet för alla andra steg som du kan vidta för att säkerställa sekretess och integritet hos dina data. Illustrerade som nyligen av den [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden) interna attacker utgör en enorm hot mot den övergripande säkerheten i alla organisationer.

Utvärdera enskilda användare för administrativ behörighet genom följande kriterier som liknar dessa:

- Utför de uppgifter som kräver administratörsbehörighet?
- Hur ofta utförs uppgifterna?
- Finns det en särskild anledning varför aktiviteterna inte kan utföras av en annan administratör åt dem?

Dokumentera alla andra kända alternativa metoder för att bevilja behörigheten och varför var och en inte godkändes.

Just-in-time-administration förhindrar onödig förekomsten av konton med utökade rättigheter under perioder när dessa rättigheter inte behövs. Konton ha utökade rättigheter för en begränsad tid så att administratörer kan utföra sitt arbete. Dessa rättigheter tas sedan bort i slutet av en förändring, eller när en uppgift är slutförd.

Du kan använda [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) för att hantera, övervaka och kontrollera åtkomst till i din organisation. Det hjälper dig att vara medveten om de åtgärder som enskilda användare utför i din organisation. Det medför också just-in-time-administration till Azure AD genom att introducera begreppet berättigade administratörer. Det här är personer som har konton som kan beviljas administratörsrättigheter. Dessa typer av användare kan gå igenom en aktiveringsprocess och beviljas administratörsrättigheter under en begränsad tid.

## <a name="use-devtest-labs"></a>Använda DevTest Labs

Azure för laborationer och utvecklingsmiljöer tar bort fördröjningar som introducerar inköp av maskinvara. Detta gör det möjligt för organisationer att få flexibilitet vid testning och utveckling. Brist på tidigare erfarenhet av Azure eller en önskan att påskynda antagandet kan å andra sidan leda administratören vara alltför Tillåtande med tilldelning av användarrättigheter. Den här risken kan oavsiktligt exponera organisationen till interna attacker. Vissa användare kan beviljas mycket mer åtkomst än vad de ska ha.

Den [Azure DevTest Labs](../devtest-lab/devtest-lab-overview.md) tjänsten använder [rollbaserad åtkomstkontroll i](../role-based-access-control/overview.md) (RBAC). Med RBAC kan du hålla isär uppgifter i ditt team i roller som ger endast åtkomstnivån krävs för att användarna att göra sina jobb. RBAC levereras med fördefinierade roller (ägare, lab-användare och deltagare). Du kan även använda dessa roller för att tilldela rättigheter till externa partner och förenklar samarbete.

Eftersom labb använder sig av RBAC, är det möjligt att skapa ytterligare, [anpassade roller](../lab-services/devtest-lab-grant-user-permissions-to-specific-lab-policies.md). DevTest Labs inte bara förenklar hanteringen av behörigheter, förenklar processen för att få miljöer som har etablerats. Det hjälper dig också hantera andra vanliga utmaningar för team som arbetar med utvecklings- och testmiljöer. Det krävs vissa förberedelser, men på lång sikt, det kan göra det enklare för ditt team.

Azure DevTest Labs-funktioner:

- Administrativ kontroll över alternativ som är tillgängliga för användare. Administratören kan centralt hantera tillåtna storlekar för Virtuella datorer, maximalt antal virtuella datorer, och när virtuella datorer startas och avsluta korrekt.
- Automatisering av skapandet av labb miljö.
- Kostnadsuppföljning.
- Förenklad distribution av virtuella datorer för temporära collaborative arbete.
- Självbetjäning som gör det möjligt för användare att tillhandahålla sina labb med hjälp av mallar.
- Hantera och begränsa förbrukningen.

![DevTest Labs](./media/azure-security-iaas/devtestlabs.png)

Utan extra kostnad är associerad med användningen av DevTest Labs. Skapandet av labs, principer, mallar och artefakter är kostnadsfri. Du betalar för enbart Azure-resurser används i dina labb, till exempel virtuella datorer, lagringskonton och virtuella nätverk.

## <a name="control-and-limit-endpoint-access"></a>Kontroll- och gräns-slutpunkt-åtkomst

Som är värd för labb eller produktionssystem i Azure innebär det att dina system måste vara tillgänglig från Internet. Som standard en ny Windows virtuell dator har den RDP-porten som är tillgänglig från Internet och en Linux-dator har öppna SSH-porten. Vidta åtgärder till gränsen exponerade slutpunkter krävs för att minimera risken för obehörig åtkomst.

Med hjälp av tekniker i Azure kan du begränsa åtkomsten till dessa administrativa slutpunkter. Du kan använda i Azure, [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) (NSG). När du använder Azure Resource Manager för distribution av NSG: er att begränsa åtkomst från alla nätverk till bara hanteringsslutpunkter (RDP / SSH). När du överväger NSG: er tror router ACL: er. Du kan använda dem för att begränsa nätverkskommunikation mellan olika segment av din Azure-nätverk. Detta liknar skapar nätverk i perimeternätverk eller andra isolerade nätverk. Granska de inte trafiken, men de hjälpa till med nätverkssegmentering.

Ett mer dynamiskt sätt för att begränsa åtkomsten till virtuella datorer är att använda Azure Security center [Just-in-time-administration](../security-center/security-center-just-in-time.md). Security center kan låsa virtuella datorer i Azure och ger åtkomst när det behövs. Processen fungerar genom att tillåta åtkomst till en användare som begär den när du har verifierat baserat på deras [Role-Based Access control](../role-based-access-control/role-assignments-portal.md) (RBAC) som de har tillräcklig behörighet. Azure Security center kommer sedan göra nödvändiga Nätverkssäkerhetsgrupper (NSG) som tillåter inkommande trafik.

### <a name="site-to-site-vpnvpn-gatewayvpn-gateway-howto-site-to-site-resource-manager-portalmd"></a>[Plats-till-plats-VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

En plats-till-plats-VPN utökar ditt lokala nätverk till molnet. Detta ger dig en andra möjlighet att använda NSG: er, eftersom du kan också ändra NSG: er för att inte tillåta åtkomst från var som helst andra än det lokala nätverket. Du kan sedan kräva att administration gör du genom att först ansluter till Azure-nätverket via VPN.

Alternativet för plats-till-plats-VPN kan vara mest attraktiva i fall där du är värd för produktionssystem som är nära integrerad med dina lokala resurser i Azure.

### <a name="point-to-sitevpn-gatewayvpn-gateway-howto-point-to-site-rm-psmd"></a>[Punkt-till-plats](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

I situationer där du vill hantera system som inte behöver åtkomst till lokala resurser. Dessa system kan isoleras i sina egna Azure-nätverk. Administratörer kan VPN i Azure som värd miljö från sina administrativ arbetsstation.

>[!NOTE]
>Du kan använda antingen VPN-alternativet för att konfigurera om ACL: er på NSG: er att inte tillåta åtkomst till av hanteringsslutpunkter från Internet.

### <a name="remote-desktop-gatewayactive-directoryauthenticationhowto-mfaserver-nps-rdgmd"></a>[Gateway för fjärrskrivbord](../active-directory/authentication/howto-mfaserver-nps-rdg.md)

Du kan använda Remote Desktop Gateway för säker anslutning till fjärrskrivbord-servrar över HTTPS, samtidigt som du använder mer detaljerade kontroller för dessa anslutningar.

Funktioner som du skulle ha åtkomst till om du vill inkludera:

- Alternativ för administratörer att begränsa antalet anslutningar till begäranden från specifika system.
- Autentisering med smartkort eller Azure Multi-Factor Authentication.
- Kontroll över vilka system som någon kan ansluta till via gatewayen.
- Kontroll över enheten och disk-omdirigering.

### <a name="vm-availability"></a>VM-tillgänglighet

Om en virtuell dator körs kritiska program som måste ha hög tillgänglighet, är det rekommenderar starkt att flera virtuella datorer används. För bättre tillgänglighet kan du skapa minst två virtuella datorer i den [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md).

[Azure Load Balancer](../load-balancer/load-balancer-overview.md) kräver också att belastningsutjämnade virtuella datorer tillhör samma tillgänglighetsuppsättning. Om dessa virtuella datorer måste kunna nås från Internet, måste du konfigurera en [internetuppkopplade belastningsutjämnare](../load-balancer/load-balancer-internet-overview.md).

## <a name="use-a-key-management-solution"></a>Använda en lösning för hantering av nycklar

Säker nyckelhantering är nödvändig för att skydda data i molnet. Med [Azure Key Vault](../key-vault/key-vault-whatis.md), på ett säkert sätt kan du lagra krypteringsnycklar och små hemligheter som lösenord i maskinvarusäkerhetsmoduler (HSM). För ytterligare säkerhet kan du importera eller generera nycklar i HSM-moduler.

Bearbetar Microsoft dina nycklar i FIPS 140-2 nivå 2 verifierade Maskinvarusäkerhetsmoduler (maskinvara och inbyggd programvara). Övervaka och granska nyckelanvändningen med Azure-loggning: skicka loggar vidare till Azure eller din säkerhetsinformation och händelsehantering (SIEM) system för extra analys och hotidentifiering.

Vem som helst med en Azure-prenumeration kan skapa och använda nyckelvalv. Även om Key Vault hjälper utvecklare och säkerhetsadministratörer, kan de implementeras och hanteras av en administratör som ansvarar för att hantera Azure-tjänster i en organisation.

## <a name="encrypt-virtual-disks-and-disk-storage"></a>Kryptera virtuella diskar och disklagring

[Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) adresser hot om datastöld eller exponering från obehörig åtkomst som uppnås genom att flytta en disk. Disken kan kopplas till ett annat system som ett sätt att kringgå andra säkerhetskontroller. Disk encryption använder [BitLocker](https://technet.microsoft.com/library/hh831713) i Windows och DM-Crypt i Linux för att kryptera operativsystem och dataenheter. Azure Disk Encryption kan integreras med Key Vault för att styra och hantera krypteringsnycklarna. Den är tillgänglig för standard virtuella datorer och virtuella datorer med premium storage.

Mer information finns i [Azure Disk Encryption i Windows och Linux IaaS-datorer](azure-security-disk-encryption.md).

[Azure Storage Service Encryption](../storage/common/storage-service-encryption.md) hjälper dig att skydda data i vila. Den är aktiverad på lagringskontonivån. Data krypteras när de skrivs i våra datacenter och dekrypteras automatiskt när du öppnar den. Det har stöd för följande scenarier:

- Kryptering för blockblobbar, tilläggsblobbar och sidblobbar
- Kryptering av arkiverade virtuella hårddiskar och mallar som gav oss till Azure från en lokal plats
- Kryptering av underliggande OS och datadiskar för virtuella IaaS-datorer som du skapade med hjälp av dina VHD: er

Innan du fortsätter med Azure Storage kryptering måste du vara medveten om två begränsningar:

- Det är inte tillgänglig på klassiska lagringskonton.
- Endast de data som skrivs när kryptering är aktiverat krypteras.

## <a name="use-a-centralized-security-management-system"></a>Använd ett hanteringssystem för centraliserad säkerhet

Servrarna måste övervakas för korrigering, konfiguration, händelser och aktiviteter som anses vara säkerhetsproblem. Du kan använda för att åtgärda dessa problem, [Security Center](https://azure.microsoft.com/services/security-center/) och [Operations Management Suite Security and Compliance](https://azure.microsoft.com/services/security-center/). Båda dessa alternativ utöver konfigurationen i operativsystemet. Du får även övervakning av konfigurationen av den underliggande infrastrukturen som konfiguration och användning av virtuell installation.

## <a name="manage-operating-systems"></a>Hantera operativsystem

I en IaaS-distribution är du fortfarande ansvarig för hantering av de system som du distribuerar, precis som alla andra servrar eller arbetsstationer i din miljö. Uppdatering, härdning, tilldelning av användarrättigheter och andra aktiviteter som rör underhållet av systemet är fortfarande ditt ansvar. För system som är nära integrerad med dina lokala resurser, kanske du vill använda samma verktyg och procedurer för att du använder en lokal för saker som antivirus, program mot skadlig kod, uppdatering och säkerhetskopiering.

### <a name="harden-systems"></a>Förstärka system

Alla virtuella datorer i Azure IaaS bör vara härdade så att de visar endast tjänstslutpunkter som krävs för de program som är installerade. För Windows-datorer, Följ rekommendationerna som Microsoft publicerar som baslinjer för den [Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx) lösning.

Security Compliance Manager är ett kostnadsfritt verktyg. Du kan använda den för att snabbt konfigurera och hantera stationära datorer, traditionella datacenter och privata och offentliga moln med hjälp av en Grupprincip och System Center Configuration Manager.

Security Compliance Manager innehåller redo att distribuera principer och Desired Configuration Management configuration packs som testas. Dessa baslinjer baseras på [Microsofts säkerhetsvägledning](https://technet.microsoft.com/library/cc184906.aspx) rekommendationer och branschens bästa praxis. De hjälper dig att hantera konfigurationsförändringar, adress efterlevnadskrav, och minska hot.

Du kan använda Security Compliance Manager för att importera den aktuella konfigurationen av datorerna med hjälp av två olika metoder. Först måste importera du Active Directory-baserad grupprinciper. Dessutom kan du importera konfigurationen av en ”golden master” referensdatorn med hjälp av den [LocalGPO verktyget](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) för säkerhetskopiering av den lokala grupprincipen. Du kan sedan importera den lokala grupprincipen till Security Compliance Manager.

Jämför dina standarder för branschens bästa praxis, anpassa dem och skapa nya principer och konfigurationspaket för Desired Configuration Management. Baslinjer har publicerats för alla operativsystem som stöds, inklusive Windows 10 Anniversary Update och Windows Server 2016.


### <a name="install-and-manage-antimalware"></a>Installera och hantera program mot skadlig kod

Du kan använda ett tillägg för program mot skadlig kod för att skydda dina virtuella datorer och molntjänster för miljöer som finns separat från din produktionsmiljö. Den kan integreras med [Azure Security Center](../security-center/security-center-intro.md).

[Microsoft Antimalware](azure-security-antimalware.md) innehåller funktioner som realtidsskydd, schemalagd genomsökning, korrigering av skadlig kod, Signaturuppdateringar, uppdateringar, exempel reporting event samling, och [stöd för PowerShell](https://msdn.microsoft.com/library/dn771715.aspx).

![Azure mot skadlig kod](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>Installera de senaste säkerhetsuppdateringarna 

Vissa av de första arbetsbelastningarna som kunder flyttar till Azure är labs och externa system. Om dina virtuella datorer i Azure vara värd för program eller tjänster som måste vara tillgänglig på Internet, vara vaksam om uppdateringar. Korrigera sig utöver operativsystemet. Okorrigerade säkerhetsproblem på program från tredje part kan också leda till problem som kan undvikas om bra uppdateringshantering är på plats.

### <a name="deploy-and-test-a-backup-solution"></a>Distribuera och testa en lösning för säkerhetskopiering

Precis som säkerhetsuppdateringar måste en säkerhetskopia som ska hanteras på samma sätt som du hanterar andra åtgärder. Det här gäller för system som ingår i din produktionsmiljö utöka till molnet. Testning och utveckling system måste följa säkerhetskopieringsstrategier som gör det möjligt för återställning som liknar vad användare har blivit vana vid, baserat på deras upplevelse med lokala miljöer.

Produktionsarbetsbelastningar flyttade till Azure ska integrera med befintliga säkerhetskopieringslösningar när det är möjligt. Du kan också använda [Azure Backup](../backup/backup-azure-arm-vms.md) att hjälpa med dina behov för säkerhetskopiering.

## <a name="monitor"></a>Övervaka

### <a name="security-centersecurity-centersecurity-center-intromd"></a>[Security Center](../security-center/security-center-intro.md)

Security center tillhandahåller kontinuerlig utvärdering av säkerhetsläget hos dina Azure-resurser att identifiera potentiella säkerhetsrisker. Genom en lista med rekommendationer får du hjälp att ställa in de kontrollfunktioner som behövs.

Exempel:

- Skadlig kod för att identifiera och ta bort skadlig programvara.
- Konfigurera nätverkssäkerhetsgrupper och regler för trafiken till virtuella datorer.
- Etablerar brandväggar för webbprogram för att skydda mot attacker som är inriktade på dina webbprogram.
- Distribuera saknade systemuppdateringar.
- Åtgärda OS-konfigurationer som inte överensstämmer med rekommenderade baslinjer.

Följande bild visar några av de alternativ som du kan aktivera i Security Center.

![Azure Security Center-principer](./media/azure-security-iaas/security-center-policies.png)

### <a name="operations-management-suiteoperations-management-suiteoperations-management-suite-overviewmd"></a>[Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) 

Operations Management Suite är en Microsoft molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda dina lokala och molnbaserade infrastruktur. Eftersom Operations Management Suite implementeras som en molnbaserad tjänst, kan du distribuera den snabbt och med minimal investering i infrastrukturresurser.

Nya funktioner levereras automatiskt, vilket sparar dig från löpande underhåll och Uppgraderingskostnader. Operations Management Suite är integrerat med System Center Operations Manager. Den har olika komponenter för att bättre hantera dina Azure-arbetsbelastningar, inklusive en [säkerhet och efterlevnad](../operations-management-suite/oms-security-getting-started.md) modulen.

Du kan använda funktioner för säkerhet och efterlevnad i Operations Management Suite för att visa information om dina resurser. Informationen är uppdelad i fyra huvudkategorier:

- **Säkerhetsdomäner**: Utforska ytterligare säkerhetsposter över tid. Åt utvärdering av skadlig kod, uppdatera utvärdering, nätverksinformation för säkerhet, identitet och åtkomst och datorer med säkerhetshändelser. Dra nytta av snabb åtkomst till instrumentpanelen i Azure Security Center.
- **Anmärkningsvärda problem**: snabbt identifiera antalet aktiva problem och problemens allvarlighetsgrad.
- **Identifieringar (förhandsversion)**: identifiera attack mönster genom att visualisera säkerhetsvarningar när de visar mot dina resurser.
- **Hotinformation**: identifiera attack mönster genom att visualisera det totala antalet servrar med utgående skadlig IP-trafik, typen av hot och en karta som visar var dessa IP-adresser kommer från.
- **Vanliga säkerhetsfrågor**: visa en lista över de vanligaste säkerhetsfrågorna som du kan använda för att övervaka din miljö. När du klickar på någon av dessa frågor i **Search** bladet öppnas och visar resultatet för frågan.

Följande skärmbild visar ett exempel på information som kan visa i Operations Management Suite.

![Säkerheten för Operations Management Suite](./media/azure-security-iaas/oms-security-baseline.png)

### <a name="monitor-vm-performance"></a>Övervaka prestanda för virtuell dator

Resursen missbruk kan vara ett problem när VM-processer använder fler resurser än vad de ska. Prestandaproblem med en virtuell dator kan leda till avbrott i tjänsten, som bryter mot reglerna security tillgänglighet. Därför är det viktigt att övervaka åtkomst till virtuell dator inte bara reaktivt när ett problem uppstår, men även proaktivt mot baslinjeprestanda mätt under normal drift.

Genom att analysera [Azure diagnostisk loggfiler](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/), du kan övervaka dina VM-resurser och identifiera eventuella problem som kan påverka datorns prestanda och tillgänglighet. Azure Diagnostics-tillägget tillhandahåller funktioner för övervakning och diagnostik på Windows-baserade virtuella datorer. Du kan aktivera dessa funktioner genom att inkludera tillägget som en del av den [Azure Resource Manager-mall](../virtual-machines/windows/extensions-diagnostics-template.md).

Du kan också använda [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) att få insyn i dina resursers hälsotillstånd.

Organisationer som inte övervakar prestanda för virtuella datorer kan inte avgöra om vissa ändringar i prestandamönster är normal eller onormalt. Om den virtuella datorn använder fler resurser än normalt kan sådana avvikelser tyda på potentiella från en extern resurs eller en komprometterad process som körs på den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

* [Azure-säkerhetsteamets blogg](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)
* [Azure-säkerhetsmetodtips och mönster](security-best-practices-and-patterns.md)
