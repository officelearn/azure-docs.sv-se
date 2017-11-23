---
title: "Komma igång med Microsoft Azure-säkerhet | Microsoft Docs"
description: "Den här artikeln innehåller en översikt över säkerhetsfunktionerna i Microsoft Azure och allmänna överväganden för organisationer som migrerar deras tillgångar till en molntjänstleverantör."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 8d8a0088-c85a-48e7-bd04-2bc7b78b0691
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: e1ee07f2284df925b8bbd9050de7ae40fa66bf65
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2017
---
# <a name="getting-started-with-microsoft-azure-security"></a>Kom igång med Microsoft Azure-säkerhet
När du skapar eller migrerar IT tillgångar till en molntjänstleverantör, måste den organisationens förmåga att skydda dina program och data med tjänster och de kontroller som de tillhandahåller för att hantera säkerheten för din molnbaserade tillgångar.

Allt i Azures infrastruktur, från anläggning till tillämpningar, är utformat för att fungera som värd för miljoner kunder samtidigt, och den tillhandahåller en säker grund som företaget kan använda sig av för att möta de interna säkerhetsbehoven. Dessutom erbjuder Azure dig en mängd olika konfigurerbara säkerhetsalternativ samt möjligheten att kontrollera dem, så att du kan anpassa säkerheten för att uppfylla de specifika behoven hos dina distributioner.

I den här översiktsartikeln om Azure-säkerhet ska vi titta på följande:

* Azure-tjänster och funktioner som du kan använda för att skydda dina data i Azure och tjänster.
* Hur Microsoft skyddar Azure-infrastrukturen för att skydda dina data och program.

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering
Det är mycket viktigt att kontrollera åtkomsten till IT-infrastruktur, data och program. Microsoft Azure har funktioner för dessa tjänster som Azure Active Directory (Azure AD), Azure Storage och stöd för flera standarder och API: er.

[Azure AD](../active-directory/active-directory-whatis.md) är en identitetsdatabas och motor som tillhandahåller autentisering, auktorisering och åtkomstkontroll för organisationens användare, grupper och objekt. Azure Active Directory erbjuder även utvecklare ett effektivt sätt att integrera identitetshantering i sina program. Standardiserade protokoll som [SAML 2.0](https://en.wikipedia.org/wiki/SAML_2.0), [WS-Federation](https://msdn.microsoft.com/library/bb498017.aspx), och [OpenID Connect](http://openid.net/connect/) gör möjligt logga in på plattformar som .NET, Java, Node.js och PHP.

Den REST-baserade Graph API:n gör det möjligt för utvecklarna att läsa och skriva till katalogen från valfri plattform. Med stöd för [OAuth 2.0](http://oauth.net/2/)utvecklare kan bygga mobila och webbprogram som integrerar med Microsoft och tredje parts webb-API: er och skapa egna säker web API: er. Klientbibliotek med öppen källkod är tillgängliga för .Net, Windows Store, iOS och Android, och ytterligare bibliotek är under utveckling.

### <a name="how-azure-enables-identity-and-access-management"></a>Möjligheter till identitets- och åtkomsthantering i Azure
Azure AD kan användas som en fristående molnkatalog för din organisation eller som en integrerad lösning med en befintlig lokal Active Directory. Bland integrationsfunktionerna finns katalogsynkronisering och enkel inloggning (SSO). Dessa utöka räckvidden för din befintliga lokala identiteter till molnet och förbättra upplevelsen administratörs- och.

Vissa andra funktioner för identitets- och åtkomsthantering är följande:

* Azure AD aktiverar [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) för SaaS-program, oavsett var de finns. Vissa program federeras med Azure AD och andra använder enkel inloggning med lösenord. Federerade program kan också använda användareetablering och valvlagring av lösenord.
* Åtkomst till data i [Azure Storage](https://azure.microsoft.com/services/storage/) styrs via autentisering. Varje lagringskonto har en primär nyckel ([lagringskontonyckel](https://msdn.microsoft.com/library/azure/ee460785.aspx), eller SAK) och en sekundär hemlig nyckel (signatur för delad åtkomst, eller SAS).
* Azure AD innehåller identitet som en tjänst via federation med hjälp av [Active Directory Federation Services](../active-directory/fundamentals-identity.md), synkronisering och replikering med lokala kataloger.
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) är tjänsten multifaktorautentisering som kräver att användare att verifiera inloggningar med hjälp av en mobilapp, telefonsamtal eller SMS. Den kan användas med Azure AD för att säkra lokala resurser med Azure Multi-Factor Authentication-servern, och anpassade program och kataloger med hjälp av SDK.
* [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) kan du ansluta virtuella Azure-datorer till en domän utan att distribuera domänkontrollanter. Du kan logga in på dessa virtuella datorer med företagets Active Directory-autentiseringsuppgifter och administrera domänanslutna virtuella datorer med hjälp av Grupprincip för att genomdriva baslinjer för säkerhet på alla dina virtuella Azure-datorer.
* [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) ger en hög tillgänglighet globala identity management-tjänst för konsumentinriktade program som kan skalas till flera hundra miljoner identiteter. Den kan integreras över mobila plattformar och webbaserade plattformar. Dina användare kan logga in på alla program via anpassningsbara upplevelser genom att använda sina befintliga sociala konton eller genom att skapa nya autentiseringsuppgifter.

## <a name="data-access-control-and-encryption"></a>Dataåtkomstkontroll och kryptering
Microsoft använder principerna för uppdelning av uppgifter (Separation of Duties) och [Minsta rättigheter](https://en.wikipedia.org/wiki/Principle_of_least_privilege) vid driften av Azure. Azure-supportteknikernas dataåtkomst kräver explicit behörighet från dig och beviljas på JIT-basis (Just-In-Time) som loggas och granskas och sedan återkallas när uppdraget har slutförts.

Azure tillhandahåller också flera funktioner för att skydda data under överföring och i vila. Här ingår kryptering för data, filer, program, tjänster, kommunikation och enheter. Du kan kryptera information innan du placerar den i Azure och lagrar också nycklar i ditt lokala datacenter.

![Microsoft Antimalware i Azure](./media/azure-security-getting-started/sec-azgsfig1.PNG)

### <a name="azure-encryption-technologies"></a>Azure-krypteringstekniker
Du kan samla in information om administrativ åtkomst till din prenumerationsmiljö genom att använda [Azure AD Reporting](../active-directory/active-directory-reporting-audit-events.md). Du kan konfigurera [BitLocker-diskkryptering](https://technet.microsoft.com/library/cc732774.aspx) på virtuella hårddiskar som innehåller känslig information i Azure.

Andra funktioner i Azure som hjälper dig att skydda dina data är följande:

* Programutvecklare kan bygga kryptering i program som de distribueras i Azure med hjälp av Windows [CryptoAPI](https://msdn.microsoft.com/library/ms867086.aspx) och .NET Framework.
* Helt styra nycklar med kryptering på klientsidan för Azure Blob storage. Lagringstjänsten ser aldrig nycklarna och kan inte dekryptera data.
* [Azure Rights Management (Azure RMS)](https://technet.microsoft.com/library/jj585026.aspx) (med den [RMS SDK](https://msdn.microsoft.com/library/dn758244.aspx)) ger fil- och datanivå kryptering och dataläcka förebyggande till policy-baserad hantering.
* Azure stöder [tabell-nivå och på kolumnnivå kryptering (TDE/r)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database.aspx) i SQL Server-datorer och den stöder från tredje part lokala nyckelhantering servrar i datacenter.
* Lagringskontonycklar, delade åtkomstsignaturer, certifikat och andra nycklar är unika för varje Azure-klient.
* Azure [StorSimple](http://www.microsoft.com/server-cloud/products/storsimple/overview.aspx) hybrid storage krypterar data via en 128-bitars offentliga/privata nyckelparet innan den skickas till Azure Storage.
* Azure stöder och använder flera krypteringsmekanismer för, inklusive SSL/TLS, IPsec och AES, beroende på datatyper, behållare och transporter.

## <a name="virtualization"></a>Virtualisering
Azure-plattformen använder en virtualiserad miljö. Användarinstanser fungerar som fristående virtuella datorer som inte har åtkomst till en fysisk värd-server och denna isolering tillämpas med hjälp av fysiska [behörighetsnivåer för processor (ring-0/ring-3)](https://en.wikipedia.org/wiki/Protection_ring).

Ring 0 är den mest privilegierade och 3 är den minst privilegierade. Gästoperativsystemet som körs i en mindre privilegierad Ring 1 och program körs i den lägsta möjliga Ring 3. Den här virtualiseringen av fysiska resurser leder till en tydlig uppdelning mellan gästoperativsystem och hypervisor, vilket resulterar i ytterligare säkerhetsuppdelning mellan dessa två.

Azure hypervisor-programmet fungerar som en micro kernel och överför alla maskinvara förfrågningar från virtuella gästdatorer till värden för bearbetning med hjälp av ett delat minne gränssnitt som heter VMBus. Detta förhindrar att användare erhåller rååtkomsten läs/skriv/kör till systemet och minskar risken med att dela systemresurser.

![Microsoft Antimalware i Azure](./media/azure-security-getting-started/sec-azgsfig2.PNG)

### <a name="how-azure-implements-virtualization"></a>Hur virtualisering implementeras i Azure
Azure använder en hypervisor-brandvägg (paketfilter) som är implementerad i hypervisor-programmet och konfigureras av en agent för fabric-styrenhet. Det skyddar klienter från obehörig åtkomst. Som standard all trafik blockeras när en virtuell dator skapas och fabric controller agenten konfigurerar sedan paketfilter att lägga till *regler och undantag* auktoriserade trafik ska tillåtas.

Det finns två typer av regler som är programmerade här:

* **Datorn konfiguration eller infrastruktur regler**: som standard all kommunikation är blockerad. Det finns undantag så att en virtuell dator skickar och tar emot DHCP och DNS-trafik. Virtuella datorer kan också skicka trafik till ”offentliga” internet och skicka trafik till andra virtuella datorer i klustret och Aktiveringsservern OS. De virtuella datorerna listan över tillåtna utgående mål innehåller inte Azure router undernät, Azure management tillbaka slutet och andra Microsoft-egenskaper.
* **Rollen konfigurationsfilen**: Detta definierar den inkommande åtkomstkontrollistor (ACL) baserat på klientens modell. Till exempel om en klient har en frontwebb på port 80 på en viss virtuell dator, Azure öppnas TCP-port 80 till alla IP-adresser om du konfigurerar en slutpunkt i den [Azure klassiska distributionsmodellen](../azure-resource-manager/resource-manager-deployment-model.md). Om den virtuella datorn har en bakre end eller worker roll som körs, öppnas arbetsrollen endast till den virtuella datorn i samma klientorganisation.

## <a name="isolation"></a>Isolering
Ett annat viktigt molnet säkerhetskrav är avgränsning för att förhindra obehörig och oavsiktliga överföring av information mellan distributioner i en delad arkitektur med flera innehavare.

Azure implementerar [nätverk åtkomstkontroll](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/) och ansvarsfördelning via VLAN-isolering, ACL: er och belastningsutjämning och IP-filter. Den begränsar externa trafiken inkommande portar och protokoll på virtuella datorer som du definierar. Azure implementerar nätverket filtrering för att förhindra falska trafik och begränsa inkommande och utgående trafik till betrodda platform-komponenter. Trafikflödesprinciper implementeras på gränsskyddsenheter som nekar trafik som standard.

![Microsoft Antimalware i Azure](./media/azure-security-getting-started/sec-azgsfig3.PNG)

NAT-funktionen (Network Address Translation) för nätverksöversättning används för att separera intern nätverkstrafik från extern trafik. Intern trafik kan inte omdirigeras externt. [Virtuella IP-adresser](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) som är externt dirigerbara översätts till [interna dynamiska IP](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx)-adresser som enbart är dirigerbara i Azure.

Externa trafiken till virtuella datorer i Azure är med en brandvägg via ACL: er på routrar, belastningsutjämnare, och nivå 3-växlar. Enbart vissa kända protokoll är tillåtna. ACL: er är på plats för att begränsa trafik från virtuella gästdatorer till andra VLAN-nätverk som används för hantering. Dessutom kan begränsar trafik filtreras via IP-filter på värden OS ytterligare trafik på båda datalager för länk och nätverk.

### <a name="how-azure-implements-isolation"></a>Hur Azure implementerar isolering
Azure-Infrastrukturkontrollanten ansvarar för att fördela infrastrukturresurser för att arbetsbelastningar-klient och den hanterar enkelriktad kommunikation mellan värden och virtuella datorer. Hypervisor-programmet i Azure tvingar minne och processen åtskillnad mellan virtuella datorer och den dirigerar nätverkstrafik på ett säkert sätt till innehavare för gäst-OS. Azure implementerar också isolering för klienter, lagring och virtuella nätverk.

* Varje Azure AD-klient är logiskt isolerad med hjälp av säkerhetsgränser.
* Azure storage-konton är unika för varje prenumeration och åtkomst måste autentiseras med hjälp av en lagringskontonyckel.
* Virtuella nätverk är logiskt isolerade genom en kombination av unika privata IP-adresser, brandväggar och IP-ACL: er. Belastningsutjämnarna dirigerar trafiken till lämpliga klienter baserat på slutpunktsdefinitionerna.

## <a name="virtual-networks-and-firewalls"></a>Virtuella nätverk och brandväggar
Den [distribuerade och virtuella nätverk](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx) i Azure hjälp att se till att privata nätverkstrafiken är logiskt isolerade från trafik på andra virtuella Azure-nätverk.

![Microsoft Antimalware i Azure](./media/azure-security-getting-started/sec-azgsfig4.PNG)

Din prenumeration kan innehålla flera isolerade privata nätverk (och inkluderar brandväggen, belastningsutjämning och nätverksadresser).

Azure tillhandahåller tre primära nivåer av nätverksuppdelning i varje Azure-kluster för att dela upp trafiken på ett logiskt sätt. [Virtuella lokala nätverk](https://azure.microsoft.com/services/virtual-network/) (VLAN) används för att separera kunden trafiken från resten av Azure-nätverk. Åtkomst till Azure-nätverket från utanför klustret begränsas med belastningsutjämnare.

Nätverkstrafiken till och från virtuella datorer måste gå via den virtuella växeln hypervisor. Komponenten IP-filter i roten OS isolerar den virtuella datorn i roten från gästen virtuella datorer och virtuella gästdatorer från varandra. Den genomför filtrering av trafik som begränsar kommunikationen mellan en klients noder och det offentliga Internet (baserat på kundens tjänstkonfiguration), skilja dem från andra klienter.

IP-adressfilter förhindrar virtuella gästdatorer från:

* Generera falska trafik.
* Ta emot trafik som inte är adresserad till dem.
* Dirigera trafik till skyddade infrastruktur slutpunkter.
* Skicka eller ta emot olämpliga broadcast-trafik.

Du kan placera de virtuella datorerna till [virtuella Azure-nätverk](https://azure.microsoft.com/documentation/services/virtual-network/). Dessa virtuella nätverk liknar de nätverk som du konfigurerar i lokala miljöer där de är vanligtvis är kopplade till en virtuell växel. Virtuella datorer som är anslutna till samma virtuella nätverk kan kommunicera med varandra utan ytterligare konfiguration. Du kan också konfigurera olika undernät i det virtuella nätverket.

Du kan använda följande tekniker för Azure Virtual Network till att skydda kommunikationen i ditt virtuella nätverk:

* [**Nätverkssäkerhetsgrupper (NSG: er)**](../virtual-network/virtual-networks-nsg.md). Du kan använda en NSG för trafiken till en eller flera instanser av virtuell dator i ditt virtuella nätverk. En NSG innehåller åtkomstkontrollregler som tillåter eller nekar trafik baserat på trafikriktningen, protokollet, källadressen och -porten, samt måladressen och -porten.
* [**Användardefinierade routning**](../virtual-network/virtual-networks-udr-overview.md). Du kan styra routning av paket via en virtuell installation genom att skapa användardefinierade vägar som anger next hop för paket som flödar till ett specifikt undernät att gå till en virtuell nätverksenhet säkerhet.
* [**IP-vidarebefordran**](../virtual-network/virtual-networks-udr-overview.md). En virtuell nätverkssäkerhetsinstallation måste kunna ta emot inkommande trafik som inte är adresserad till installationen i fråga. Om du vill att en virtuell dator tar emot trafik adresserad till andra mål, aktivera IP-vidarebefordring för den virtuella datorn.
* [**Tvingad tunneltrafik**](../vpn-gateway/vpn-gateway-about-forced-tunneling.md). Tvingad tunneltrafik kan du omdirigera eller ”force” alla Internet-bunden trafik som genereras av virtuella datorer i ett virtuellt nätverk tillbaka till din lokala plats via en plats-till-plats VPN-tunnel för kontroll och granskning
* [**Slutpunkts-ACL:**](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Du kan styra vilka datorer tillåts inkommande anslutningar från Internet till en virtuell dator på det virtuella nätverket genom att definiera endpoint ACL: er.
* [**Säkerhetslösningar för partnernätverk**](https://azure.microsoft.com/marketplace/). Det finns ett antal säkerhetslösningar från partnerföretag nätverk som du kan komma åt från Azure Marketplace.

### <a name="how-azure-implements-virtual-networks-and-firewalls"></a>Hur Azure implementerar virtuella nätverk och brandväggar
Azure implementerar filtrering av nätverkspaket brandväggar på alla värden och gästen virtuella datorer som standard. Windows OS-bilder från Azure Marketplace har också Windows-brandväggen är aktiverad som standard. Belastningsutjämnare i utkanten av Azure offentliga nätverk kontrollen kommunikation baserat på IP-ACL: er hanteras av kunden administratörer.

Om Azure flyttar data för en kund som en del av den ordinarie driften eller under en katastrofhändelse, sker detta i privata och krypterade kommunikationskanaler. Andra funktioner som används av Azure ska användas i virtuella nätverk och brandväggar är:

* **Intern värdbrandväggen**: Azure Service Fabric och Azure Storage körs på ett enhetligt operativsystem som har inga hypervisor. Därför är windows-brandväggen konfigurerad med de föregående två regeluppsättningar. Lagring körs internt för att optimera prestanda.
* **Värdbrandväggen**: värdbrandväggen är att skydda värdoperativsystemet som kör hypervisor-programmet. Reglerna programmerad att tillåta endast Service Fabric-styrenhet och gå att kommunicera med värdens operativsystem på en viss port. De övriga undantagen är att tillåta DHCP- och DNS-svar. Azure använder en konfigurationsfil för datorn som har mallen brandväggsregler för värd-OS. Själva värden är skyddat från externa angrepp av en Windows-brandväggen som är konfigurerad för att tillåta kommunikation endast från kända, autentiserad källor.
* **Gästen brandväggen**: replikerar regler i det virtuella växeln paketfiltret men programmerade i olika program (till exempel Windows-brandväggen typ av gäst-OS). Gästen virtuella brandväggen kan konfigureras för att begränsa kommunikationen till eller från den virtuella gästdatorn, även om kommunikationen tillåts av konfigurationer på värden IP-Filter. Exempelvis kan du använda gäst virtuella brandväggen för att begränsa kommunikationen mellan två av ditt Vnet som har konfigurerats för att ansluta till varandra.
* **Storage-brandväggen (VB)**: brandväggen på lagring klientdelen filtrerar trafik endast på portarna 80/443 och andra nödvändiga verktyget portar. Brandväggen på lagring serverdelen begränsar kommunikation med kommer endast från frontend-lagringsservrar.
* **Virtuell nätverksgateway**: den [Azure virtuell nätverksgateway](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) fungerar som gateway mellan platser ansluter dina arbetsbelastningar i Azure-nätverk till lokala platser. Det krävs för att ansluta till lokala platser via [IPSec-tunnlar för plats-till-plats-VPN-](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), eller via [ExpressRoute](../expressroute/expressroute-introduction.md) kretsar. För IPsec/IKE VPN-tunnlar gateway utföra IKE-handskakningar på och upprätta IPsec S2S VPN-tunnlar mellan virtuella nätverk och lokala platser. Virtuella nätverksgatewayerna också avsluta [punkt-till-plats VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

## <a name="secure-remote-access"></a>Säker fjärråtkomst
Data som lagras i molnet måste ha tillräckliga skydd aktiverade för att förhindra kryphål och upprätthålla sekretessen och integriteten under överföringen. Detta omfattar nätverkskontroller som integreras med en organisations principbaserade, granskningsbara identitets- och åtkomsthanteringsmekanismer.

Med inbyggd kryptografisk teknik kan du kryptera kommunikation inom och mellan distributioner mellan Azure-regioner, samt från Azure till lokala datacenter. Administratörsåtkomst till virtuella datorer via [fjärrskrivbordssessioner](../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json), [fjärransluten Windows PowerShell](http://blogs.technet.com/b/heyscriptingguy/archive/2013/09/07/weekend-scripter-remoting-the-cloud-with-windows-azure-and-powershell.aspx), och Azure-portalen krypteras alltid.

Om du vill utöka ditt lokala datacenter till molnet på ett säkert sätt, Azure tillhandahåller både [plats-till-plats VPN](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) och [punkt-till-plats VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md), plus dedicerade länkar med [ExpressRoute](../expressroute/expressroute-introduction.md) (anslutningar till virtuella Azure-nätverk via VPN krypteras).

### <a name="how-azure-implements-secure-remote-access"></a>Hur Azure implementerar säker fjärråtkomst
Anslutningar till Azure-portalen måste alltid autentiseras och de kräver SSL/TLS. Du kan konfigurera hanteringscertifikat för att aktivera säker hantering. Branschstandard säkerhet protokoll som [SSTP](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx) och [IPsec](https://en.wikipedia.org/wiki/IPsec) stöds fullt ut.

Med [Azure ExpressRoute](../expressroute/expressroute-introduction.md) kan du skapa privata anslutningar mellan Azures datacenter och infrastruktur som finns lokalt eller i en samplaceringsmiljö. ExpressRoute-anslutningar går inte via offentligt Internet. De erbjuder flera tillförlitlighet, högre hastighet, lägre latens och högre säkerhet än vanliga Internetbaserade länkar. I vissa fall kan överföra data mellan lokala platser och Azure med hjälp av ExpressRoute-anslutningar kan också ge betydande kostnadsfördelar.

## <a name="logging-and-monitoring"></a>Loggning och övervakning
Azure tillhandahåller autentiserade loggning av säkerhetsmässigt händelser som genererar en granskningslogg och den har utformats för att vara motståndskraftiga mot angrepp. Detta inkluderar Systeminformation, till exempel säkerhetshändelseloggar i Azure-infrastrukturen virtuella datorer och Azure AD. Övervakning av säkerhet omfattar att samla in händelser, t.ex ändringar i DHCP- eller DNS-serveradresser; Det gjordes ett försök åtkomst till portar, protokoll eller IP-adresser som har blockerats avsiktligt; ändringar i principen eller brandvägg säkerhetsinställningar. kontot eller grupp att skapa; och oväntade processer eller installation av drivrutiner.

![Microsoft Antimalware i Azure](./media/azure-security-getting-started/sec-azgsfig5.PNG)

Granskningsloggar som registrerar åtkomst och aktiviteter för behöriga användare, behöriga och obehöriga åtkomstförsök, systemundantag och informationssäkerhetshändelser bevaras under en angiven tidsperiod. Du kan själv bestämma om du vill lagra loggarna eftersom du konfigurerar logginsamlingen och -lagringen utifrån dina egna behov.

### <a name="how-azure-implements-logging-and-monitoring"></a>Hur Azure implementerar loggning och övervakning
Azure distribuerar hanteringsagenter (MA) och Azure Security Monitor-agenter (ASM) till varje beräknings-, lagrings- eller infrastrukturnod som hanteras, oavsett om de är interna eller virtuella. Varje hanteringsagent har konfigurerats för att autentisera till ett teamlagringskonto för tjänsten med ett certifikat som hämtas från Azure-certifikatarkivet och vidarebefordrar förkonfigurerade diagnostik- och händelsedata till lagringskontot. Dessa agenter distribueras inte till kundens virtuella datorer.

Azure-administratörer använder loggar via en webbportal för autentiserad och kontrollerad åtkomst till loggarna. En administratör kan parsa, filtrera, korrelera och analysera loggar. Azure-tjänstteamets lagringskonton för loggar skyddas från direkt administratörsåtkomst för att förhindra manipulation av loggen.

Microsoft samlar in loggar från nätverksenheter som använder protokollet Syslog och värdservrar för fjärrskrivbordssessioner med hjälp av Microsoft Audit Collection Services (ACS). Dessa loggar placeras i en databas som aviseringar om misstänkta händelser genereras. Administratören kan komma åt och analysera dessa loggar.

[Azure-diagnostik](https://msdn.microsoft.com/library/azure/gg433048.aspx) är en funktion i Azure som du kan använda för att samla in diagnostikdata från program som körs i Azure. Detta är diagnostikdata för felsökning och felsökning, mäta prestanda, övervaka Resursanvändning, trafik analys, kapacitetsplanering och granskning. När diagnostiska data har samlats in kan de överföras till ett Azure Storage-konto för att sparas. Överföringar kan antingen schemaläggas eller på begäran.

## <a name="threat-mitigation"></a>Migrering av hot
Förutom isolering, kryptering och filtrering använder Azure ett antal hotminskningsmetoder och -processer för att skydda infrastruktur och tjänster. Dessa omfattar interna kontroller och tekniker som används för att identifiera och åtgärda avancerade hot, till exempel DDoS, privilegieeskalering och [OWASP topp 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

De processer för säkerhetskontroller och riskhantering som Microsoft har infört skyddar molninfrastrukturen och minskar risken för säkerhetsincidenter. Om en olycka inträffar är teamet säkerhet Incident Management (SIM) i Microsoft Online Services för säkerhet och kompatibilitet (OSSC) teamet redo att svara när som helst.

### <a name="how-azure-implements-threat-mitigation"></a>Hur Azure implementerar hotminskning
Azure har säkerhetsåtgärder för att implementera hot minskning och som hjälper kunder att minimera potentiella hot i sina miljöer. I följande lista sammanfattas hot minskning funktionerna som erbjuds av Azure:

* [Azure program mot skadlig kod](azure-security-antimalware.md) är aktiverad som standard på alla infrastrukturservrar. Du kan välja att den egna virtuella datorer.
* Microsoft har kontinuerlig övervakning av servrar, nätverk och program för att identifiera hot och förhindra kryphål. Automatiserade aviseringar meddelar administratörerna om avvikande beteenden så att de kan vidta åtgärder vid både interna och externa hot.
* Du kan distribuera säkerhetslösningar från tredje part i dina prenumerationer, till exempel web application brandväggar från [Barracuda](https://techlib.barracuda.com/ng54/deployonazure).
* Microsofts strategi för intrång testning innehåller ”[Red Teaming](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)”, som omfattar Microsoft-säkerhetsproffs att angripa (icke-kund) live produktionssystem i Azure för att testa försvar mot verkliga, avancerade, permanenta hot.
* Integrerade distributionssystem hanterar distribution och installation av säkerhetsuppdateringar över hela Azure-plattformen.

## <a name="next-steps"></a>Nästa steg
[Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/)

[Azure-säkerhetsteamets blogg](http://blogs.msdn.com/b/azuresecurity/)

[Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)

[Active Directory-bloggen](http://blogs.technet.com/b/ad/)
