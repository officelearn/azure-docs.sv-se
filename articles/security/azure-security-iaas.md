---
title: "Security best practices för IaaS arbetsbelastningar i Azure | Microsoft Docs"
description: " Migrering av arbetsbelastningar till Azure IaaS ger möjligheter att omvärdera vårt Designer "
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
ms.date: 08/29/2017
ms.author: barclayn
ms.openlocfilehash: f93211d289553b7a8afbe8c17fa4847f3d4585a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Rekommenderade säkerhetsmetoder för IaaS-arbetsbelastningar i Azure

Eftersom du startade tänka på att flytta arbetsbelastningar till Azure-infrastrukturen som en tjänst (IaaS), insåg du förmodligen att vissa förutsättningar är bekant. Du kanske redan har erfarenhet att skydda virtuella miljöer. När du flyttar till Azure IaaS du tillämpa dina kunskaper i att skydda virtuella miljöer och använder en ny uppsättning med alternativ för att skydda dina tillgångar.

Låt oss börja med säger att vi inte ska räknar med att få lokala resurser som 1: 1-till Azure. Nya utmaningar och nya alternativ gör att en möjlighet att omvärdera befintliga deigns, verktyg och processer.

Ditt ansvar för säkerhet baserat på vilken typ av tjänst i molnet. I följande tabell sammanfattas balans mellan ansvar för både Microsoft och du:


![Ansvarsområden](./media/azure-security-iaas/sec-cloudstack-new.png)


Diskuterar vi några av alternativ som är tillgängliga i Azure som kan hjälpa dig att uppfylla organisationens säkerhetskrav. Tänk på att säkerhetskrav kan variera för olika typer av arbetsbelastningar. Inte ett av tipsen skydda ensamt dina system. Som något annat säkerheten har att välja lämpliga alternativ och se hur lösningarna kan kompletterar varandra genom att fylla luckor.

## <a name="use-privileged-access-workstations"></a>Använd arbetsstationer med privilegierad åtkomst

Organisationer sig ofta faller till cyberattacks eftersom administratörer utför åtgärder när du använder konton med utökade behörigheter. Vanligtvis det här är inte medvetet göra men eftersom befintliga konfiguration och processer som tillåter den. De flesta av dessa användare är medveten om risken för dessa åtgärder från en konceptuell synvinkel men fortfarande vill göra dem.

Gör saker som e-post- och Internetanvändning verkar vara ofarliga försöker tillräckligt. Men de kan det hända att utökade konton att angripa med skadliga aktörer. Bläddra aktiviteter, särskilt utformad e-post eller andra metoder kan användas för åtkomst till ditt företag. Vi rekommenderar användning av säker hantering av arbetsstationer (sågar) för att utföra alla administrationsåtgärder på Azure. Sågar är ett sätt att minska risken för oavsiktliga angrepp.

Privilegierad åtkomst arbetsstationer (PAWs) innehåller ett dedikerat operativsystem känsliga--en som är skyddat från Internet-attacker och hotvektorer. Avgränsa dessa känsliga uppgifter och konton från daglig användning arbetsstationer och enheter ger starkt skydd. Den här separationen begränsar effekten av nätfiskeattacker, program och OS-säkerhetsrisker, olika personifiering attacker och attacker för stöld av autentiseringsuppgifter. (formulärmanipulation loggning, Pass-the-Hash och Pass-the-Ticket)

Metoden PAW är en utökning av väletablerade och praktiskt att använda ett enskilt tilldelade administrativt konto. Det administrativa kontot som är separat från ett standardanvändarkonto. En PAW innehåller en pålitlig arbetsstation för de känsliga kontona.

Mer information och implementering vägledning finns [arbetsstationer med privilegierad åtkomst](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations).

## <a name="use-multi-factor-authentication"></a>Använda Multifaktorautentisering

Tidigare användes nätverk-perimeter styra åtkomsten till företagets data. I molnet första, mobila först världen, identiteten är kontrollplan: du kan använda den för att styra åtkomsten till IaaS-tjänster från valfri enhet. Du också använda den för att hämta synlighet och insyn i var och hur data som används. Skydda dina Azure användare digitala identitet är en hörnsten för att skydda dina prenumerationer från identitetsstöld och andra cybercrimes.

Ett av de mest praktiskt stegen som du kan vidta för att skydda ett konto är att aktivera tvåfaktorsautentisering. Tvåfaktorsautentisering är ett sätt att autentisera med hjälp av något förutom ett lösenord. Det hjälper till att minska risken för åtkomst av någon som är ansvarig för att hämta lösenordet.

[Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) hjälper dig att skydda åtkomst till data och program och uppfyller efterfrågan från användarna för en process för enkel inloggning. Den ger stark autentisering med ett antal alternativ för enkel verifiering--telefonsamtal, textmeddelande eller mobilapp. Användare välja den metod som de själva föredrar.

Det enklaste sättet att använda Multi-Factor Authentication är Microsoft Authenticator-mobilappen som kan användas på mobila enheter som kör Windows, iOS och Android. Med den senaste versionen av Windows 10 och integreringen med lokala Active Directory med Azure Active Directory (Azure AD), [Windows Hello för företag](../active-directory/active-directory-azureadjoin-passport-deployment.md) kan användas för sömlös enkel inloggning till Azure-resurser. I det här fallet används Windows 10-enheten som andra faktor för autentisering.

För konton som hanterar din Azure-prenumeration och för konton som kan logga in på virtuella datorer ger med hjälp av Multi-Factor Authentication dig mycket bättre säkerhet än att använda endast ett lösenord. Andra former av tvåfaktorsautentisering fungerar lika bra, men distribuerar dem kan vara komplicerade om de inte redan är i produktion.

Följande skärmbild visar några av de tillgängliga alternativen för Azure Multi-Factor Authentication:

![Multi-Factor Authentication-alternativ](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>Begränsa och begränsa administrativ åtkomst

Det är mycket viktigt att skydda de konton som kan hantera Azure-prenumerationen. Röjande av någon av dessa konton negeras värdet för alla andra åtgärder som du kan vidta för att säkerställa sekretess och integritet för dina data. Som nyligen illustrerade av den [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden) interna attacker utgör ett stort hot mot den övergripande säkerheten för en organisation.

Utvärdera personer för administratörsbehörighet genom följande kriterier som liknar dessa:

- Utför de uppgifter som kräver administratörsbehörighet?
- Hur ofta utförs uppgifterna?
- Finns det en särskild anledning varför aktiviteter inte kan utföras av en annan administratör åt?

Dokumentera alla andra kända alternativa sätt att bevilja behörigheten och varför var inte är godkända.

Användning av just-in-time-administration förhindrar onödiga förekomsten av konton med förhöjd behörighet under perioder när de rättigheterna som inte behövs. Konton ha utökade rättigheter för en begränsad tid så att administratörer kan utföra sina uppgifter. Dessa rättigheter tas sedan bort i slutet av ett SKIFT eller när en aktivitet har slutförts.

Du kan använda [Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md) för att hantera, övervaka och kontrollera åtkomst till i din organisation. Det hjälper dig att vara medveten om de åtgärder som enskilda användare i din organisation. Medför det också just-in-time-administration till Azure AD genom att introducera begreppet berättigade administratörer. Dessa är personer som har konton som kan beviljas administratörsrättigheter. Dessa typer av användare kan gå igenom ett aktiveringen och beviljas administratörsrättigheter för en begränsad tid.


## <a name="use-devtest-labs"></a>Använd DevTest Labs

Med Azure för labs och utvecklingsmiljöer kan organisationer att bli smidigare i testning och utveckling genom att ta bort fördröjningar som introducerar maskinvara inköp. Tyvärr leda bristande tidigare erfarenhet av Azure eller viljan att påskynda antagandet att administratören kan vara alltför Tillåtande med tilldelning av användarrättigheter. Den här risken kan oavsiktligt exponera organisationen interna attacker. Vissa användare kan beviljas mycket mer åtkomst än de ska ha.

Den [Azure DevTest Labs](../devtest-lab/devtest-lab-overview.md) tjänsten använder [rollbaserad åtkomstkontroll i](../active-directory/role-based-access-control-what-is.md) (RBAC). Med RBAC kan särskilja du uppgifter i din grupp i roller som ger endast åtkomstnivån krävs för att användarna sköta sitt arbete. RBAC innehåller fördefinierade roller (ägare, lab-användare och deltagare). Du kan även använda dessa roller för att tilldela rättigheter till externa partners och förenklar samarbete.

Eftersom DevTest Labs använder RBAC, är det möjligt att skapa extra [anpassade roller](../devtest-lab/devtest-lab-grant-user-permissions-to-specific-lab-policies.md). DevTest Labs inte bara förenklar hanteringen av behörigheter, det gör enklare att komma miljöer som har etablerats. Du kan också hantera andra vanliga utmaningar team som jobbar på utvecklings- och testmiljöer. Det krävs vissa förberedelser, men på lång sikt den ska göra det enklare för din grupp.

Azure DevTest Labs funktionerna inkluderar:

- Administrativ kontroll över alternativ som är tillgängliga för användare. Administratören kan centralt hantera t.ex. tillåtna storlekar på VM, maximalt antal virtuella datorer och virtuella datorer startas och stängs av.
- Automatisering labb miljö skapas.
- Kostnadsuppföljning.
- Förenklad distribution av virtuella datorer för tillfälliga samarbete.
- Självbetjäning som gör det möjligt för användare att tillhandahålla sina övningar med hjälp av mallar.
- Hantera och begränsa förbrukning.

![Med DevTest Labs för att skapa ett labb](./media/azure-security-iaas/devtestlabs.png)

Utan extra kostnad är associerad med användningen av DevTest Labs. Skapandet av labs, principer, mallar och artefakter är kostnadsfri. Du betalar endast Azure-resurser används i din labs, till exempel virtuella datorer, lagringskonton och virtuella nätverk.



## <a name="control-and-limit-endpoint-access"></a>Kontroll- och limit endpoint-åtkomst

Värd för labs eller produktionssystem i Azure innebär att dina system måste vara tillgänglig från Internet. Som standard en ny Windows virtuell dator har RDP-porten tillgänglig från Internet och en virtuell Linux-dator har öppna SSH-porten. Vidtar åtgärder för att begränsa exponeras slutpunkter är nödvändiga för att minska risken för obehörig åtkomst.

Med hjälp av tekniker i Azure kan du begränsa åtkomsten till dessa administrativa slutpunkter. Du kan använda i Azure, [nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-nsg.md) (NSG: er). När du använder Azure Resource Manager för distribution av NSG: er att begränsa åtkomst från alla nätverk till bara hanteringsslutpunkter (RDP eller SSH). När du funderar NSG: er tror router ACL: er. Du kan använda dem för att begränsa nätverkskommunikationen mellan olika segment av dina Azure-nätverk. Detta liknar skapa nätverk i perimeternätverk eller andra isolerade nätverk. De inspektera inte trafik, men de att med nätverkssegmentering.


I Azure, kan du konfigurera en [plats-till-plats VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) från ditt lokala nätverk. En plats-till-plats-VPN utökar ditt lokala nätverk till molnet. Detta ger dig en annan möjlighet att använda NSG: er, eftersom du kan också ändra NSG: er för att inte tillåta åtkomst varifrån som helst annat än det lokala nätverket. Sedan kan du kräva att administration görs genom att först ansluta till Azure-nätverk via VPN.

Plats-till-plats VPN-alternativet kan vara mest bra i fall där du är värd för produktionssystem som är nära integrerad med din lokala resurser i Azure.

Du kan också använda den [punkt-till-plats](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md) alternativet i situationer där du vill hantera datorer som behöver åtkomst till lokala resurser. Dessa system kan isoleras i sina egna virtuella Azure-nätverket. Administratörer kan VPN i Azure värdbaserade miljön från sina administrativa arbetsstation.

>[!NOTE]
>Du kan använda antingen VPN-alternativet för att konfigurera om åtkomstkontrollistor NSG: er till inte tillåter åtkomst till hanteringsslutpunkter från Internet.

Ett annat alternativ vara värt att ta hänsyn till är en [fjärrskrivbordsgateway](../multi-factor-authentication/multi-factor-authentication-get-started-server-rdg.md) distribution. Du kan använda den här distributionen för säker anslutning till fjärrskrivbord servrar via HTTPS, samtidigt som du använder mer detaljerade kontroller att anslutningarna.

Funktioner som du vill ha åtkomst till om du vill inkludera:

- Alternativ för administratörer att begränsa antalet anslutningar på begäranden från specifika system.
- Autentisering med smartkort eller Azure Multi-Factor Authentication.
- Kontroll över vilka system som någon kan ansluta till via gatewayen.
- Kontroll över enheten och disk-omdirigering.

## <a name="use-a-key-management-solution"></a>Använda en lösning för hantering av nycklar

Säker nyckelhantering är nödvändig för att skydda data i molnet. Med [Azure Key Vault](../key-vault/key-vault-whatis.md), på ett säkert sätt kan du lagra krypteringsnycklar och hemligheter små som lösenord i maskinvarusäkerhetsmoduler (HSM). För ytterligare säkerhet kan du importera eller generera nycklar i HSM-moduler.

Microsoft processer dina nycklar i FIPS 140-2 Level 2 verifierade HSM (maskinvara och inbyggd programvara). Övervaka och granska nyckelanvändning med Azure loggning: skicka loggar till Azure eller Security Information and Event Management SIEM ()-system för ytterligare analys och hotidentifiering identifiering.

Vem som helst med en Azure-prenumeration kan skapa och använda nyckelvalv. Även om Key Vault hjälper utvecklare och säkerhetsadministratörer, kan de implementeras och hanteras av en administratör som ansvarar för att hantera Azure-tjänster i en organisation.


## <a name="encrypt-virtual-disks-and-disk-storage"></a>Kryptera virtuella diskar och disklagring

[Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) adresser hot om datastöld eller exponering från obehörig åtkomst uppnås genom att flytta en disk. Disken kan kopplas till ett annat system som ett sätt att kringgå andra säkerhetskontroller. Disken använder kryptering [BitLocker](https://technet.microsoft.com/library/hh831713) i Windows och DM-Crypt i Linux för att kryptera operativsystem och dataenheter. Azure Disk Encryption integreras med Key Vault styra och hantera krypteringsnycklarna. Den är tillgänglig för standard virtuella datorer och virtuella datorer med premium-lagring.

Mer information finns i [Azure Disk Encryption i Windows och Linux IaaS-VM](azure-security-disk-encryption.md).

[Azure Storage Service-kryptering](../storage/common/storage-service-encryption.md) hjälper dig att skydda dina data i vila. Det är aktiverat på kontonivå lagring. Den krypterar data som skrivs i våra datacenter och dekrypteras automatiskt när du öppnar den. Den stöder följande scenarion:

- Kryptering av blockblobbar, tilläggsblobbar och sidblobbar
- Kryptering av arkiverade virtuella hårddiskar och mallar som ansluts till Azure från lokala
- Kryptering av underliggande Operativsystemet och datadiskarna för IaaS-VM som du skapat med hjälp av de virtuella hårddiskarna

Innan du fortsätter med Azure Storage kryptering ska du vara medveten om två begränsningar:

- Det är inte tillgänglig på klassiska lagringskonton.
- Endast de data som skrivs när kryptering är aktiverat krypteras.

## <a name="use-a-centralized-security-management-system"></a>Använd ett hanteringssystem för centraliserad säkerhet

Servrarna måste övervakas för korrigering, konfiguration, händelser och aktiviteter som anses vara säkerhetsfrågor. Du kan använda för att åtgärda dessa problem [Security Center](https://azure.microsoft.com/services/security-center/) och [Operations Management Suite säkerhet och efterlevnad](https://azure.microsoft.com/services/security-center/). Båda dessa alternativ utöver konfigurationen i operativsystemet. De ger också övervakning av konfigurationen av den underliggande infrastrukturen som nätverkskonfigurationen och Använd virtuell installation.

## <a name="manage-operating-systems"></a>Hantera operativsystem

I en IaaS-distribution som fortfarande ansvarig för hantering av datorer som du distribuerar, precis som alla andra servrar eller arbetsstationer i din miljö. Korrigering, härdning, tilldelning av användarrättigheter och andra aktiviteter som rör underhållet av systemet är fortfarande ditt ansvar. För system som är nära integrerad med din lokala resurser, kanske du vill använda samma verktyg och procedurer för att du använder lokala för sådant som antivirus program mot skadlig kod, uppdatering och säkerhetskopiering.

### <a name="harden-systems"></a>Skydda system
Alla virtuella datorer i Azure IaaS bör vara härdat så att de exponerar endast slutpunkter som krävs för de program som är installerade. För Windows-datorer, följer du Microsoft publicerar som baslinjer för den [Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx) lösning.

Security Compliance Manager är ett kostnadsfritt verktyg. Du kan använda den för att snabbt konfigurera och hantera stationära datorer, traditionella datacenter och privata och offentliga moln med hjälp av Grupprincip och System Center Configuration Manager.

Security Compliance Manager innehåller redo att distribuera principer och Desired Configuration Management configuration packs som testas. Dessa baslinjer baseras på [Microsoft säkerhetsvägledning](https://technet.microsoft.com/en-us/library/cc184906.aspx) rekommendationer och industrin bästa praxis. De hjälper dig att hantera konfigurationsavvikelser, adress efterlevnadskrav och minska säkerhetshot.

Du kan använda Security Compliance Manager för att importera den aktuella konfigurationen av datorerna med hjälp av två olika metoder. Du kan först importera Active Directory-baserade grupprinciper. Därefter kan du importera konfigurationen av en ”golden master” referensdatorn med hjälp av den [LocalGPO verktyget](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) att säkerhetskopiera den lokala grupprincipen. Du kan sedan importera den lokala grupprincipen i Security Compliance Manager.

Jämför din standarder till branschens bästa praxis, anpassa dem och skapa nya principer och Desired Configuration Management-konfigurationspaket. Baslinjer har publicerats för alla operativsystem som stöds, inklusive Windows 10 årsdagar Update och Windows Server 2016.


### <a name="install-and-manage-antimalware"></a>Installera och hantera program mot skadlig kod

Du kan använda ett tillägg för program mot skadlig kod för att skydda dina virtuella datorer och molntjänster i miljöer som hanteras separat från din produktionsmiljö. Den kan integreras med [Azure Security Center](../security-center/security-center-intro.md).


[Microsoft Antimalware](azure-security-antimalware.md) innehåller funktioner som realtidsskydd, schemalagd genomsökning, åtgärder mot skadlig kod, Signaturuppdateringar, uppdateringar, exempel reporting undantag händelseinsamling och [stöd för PowerShell](https://msdn.microsoft.com/library/dn771715.aspx).

![Azure program mot skadlig kod](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>Installera de senaste säkerhetsuppdateringarna
De första arbetsbelastningarna som kunder flyttar till Azure är labs och externa system. Om dina Azure-värdbaserade virtuella datorer värd för program eller tjänster som måste vara tillgänglig för Internet måste vara vaksam om korrigering. Korrigera sig utöver operativsystemet. Okorrigerade säkerhetsproblem på program från tredje part kan det leda till problem som kan undvikas om bra uppdateringshantering är på plats.

### <a name="deploy-and-test-a-backup-solution"></a>Distribuera och testa en lösning för säkerhetskopiering

Precis som säkerhetsuppdateringar måste en säkerhetskopia som ska hanteras på samma sätt som du hanterar alla andra åtgärder. Detta gäller för system som ingår i din produktionsmiljö utöka till molnet. Testning och utveckling system måste följa säkerhetskopieringsstrategier som tillhandahåller funktioner som liknar vad användare är vana, utifrån sina erfarenheter lokala miljöer.

Produktionsarbetsbelastningar flyttas till Azure ska integrera med befintliga säkerhetskopieringslösningar när det är möjligt. Du kan också använda [Azure Backup](../backup/backup-azure-arm-vms.md) för att adressera dina behov för säkerhetskopiering.


## <a name="monitor"></a>Övervaka

[Security Center](../security-center/security-center-intro.md) ger pågående utvärdering av säkerhetstillståndet hos dina Azure-resurser att identifiera potentiella säkerhetsrisker. Genom en lista med rekommendationer får du hjälp att ställa in de kontrollfunktioner som behövs.

Exempel:

- Etablera program mot skadlig kod för att identifiera och ta bort skadlig programvara.
- Konfigurera nätverkssäkerhetsgrupper och regler för trafiken till virtuella datorer.
- Etablering brandväggar för att skydda mot angrepp riktade webbaserade program webbaserade program.
- Systemuppdateringar som fattas.
- OS-konfigurationer som inte stämmer överens med rekommenderade baslinjer-adressering.

Följande bild visar några av de alternativ som du kan aktivera i Security Center.

![Principer för Azure Security Center](./media/azure-security-iaas/security-center-policies.png)

[Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) är en Microsoft molnbaserade IT lösning som hjälper dig att hantera och skydda dina lokala och molnet infrastruktur. Eftersom Operations Management Suite implementeras som en molnbaserad tjänst kan du distribuera den snabbt och med minimal investeringar i infrastrukturresurser.

Nya funktioner som levereras automatiskt, vilket sparar dig från löpande underhållet och uppgradera kostnader. Operations Management Suite är integrerat med System Center Operations Manager. Det har olika komponenter som hjälper dig att hantera dina Azure arbetsbelastningar, inklusive en [säkerhet och efterlevnad](../operations-management-suite/oms-security-getting-started.md) modul.

Du kan använda funktionerna för säkerhet och efterlevnad i Operations Management Suite för att visa information om dina resurser. Informationen är indelad i fyra huvudsakliga kategorier:

- **Säkerhetsdomäner**: Utforska ytterligare säkerhetsposter över tid. Kontroll av skadlig kod kan uppdatera assessment, information om nätverk, identitet och åtkomst och datorer med säkerhetshändelser. Dra nytta av snabb åtkomst till instrumentpanelen i Azure Security Center.
- **Anmärkningsvärda problem**: snabbt identifiera antalet aktiva problem och allvarlighetsgrad för de här problemen.
- **Identifieringar (förhandsgranskning)**: identifiera attack mönster av visualisera säkerhetsaviseringar när de görs mot dina resurser.
- **Hot intelligence**: identifiera attack mönster av visualisering av det totala antalet servrar med utgående skadlig IP-trafik, skadliga hot typ och en karta som visar var dessa IP-adresser kommer från.
- **Vanliga säkerhetsfrågor**: visa en lista över de vanligaste säkerhetsfrågor som du kan använda för att övervaka din miljö. När du klickar på någon av dessa frågor i **Sök** blad öppnas och visar resultatet för frågan.

Följande skärmbild visar ett exempel på den information som kan visa Operations Management Suite.

![Operations Management Suite baslinjer för säkerhet](./media/azure-security-iaas/oms-security-baseline.png)

## <a name="next-steps"></a>Nästa steg

* [Azure-säkerhetsteamets blogg](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)
* [Azure säkerhetsmetoder och mönster](security-best-practices-and-patterns.md)
