---
title: Isolering i Det offentliga Azure-molnet | Microsoft-dokument
description: Lär dig hur Azure isolerar både skadliga och icke-skadliga användare och erbjuder olika isoleringsalternativ till arkitekter.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: TomSh
ms.openlocfilehash: c6e74e7992326d2a4b8fe24510742422b005c2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280318"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Isolering i Det offentliga Azure-molnet
Med Azure kan du köra program och virtuella datorer (VMs) på delad fysisk infrastruktur. En av de främsta ekonomiska motiven för att köra program i en molnmiljö är möjligheten att fördela kostnaden för delade resurser mellan flera kunder. Denna praxis med flera innehavare förbättrar effektiviteten genom multiplexering resurser mellan olika kunder till låga kostnader. Tyvärr introducerar det också risken för att dela fysiska servrar och andra infrastrukturresurser för att köra dina känsliga program och virtuella datorer som kan tillhöra en godtycklig och potentiellt illvillig användare.

I den här artikeln beskrivs hur Azure isolerar både skadliga och icke-skadliga användare och fungerar som en guide för att skapa molnlösningar genom att erbjuda olika isoleringsalternativ till arkitekter.

## <a name="tenant-level-isolation"></a>Isolering på klientnivå
En av de främsta fördelarna med cloud computing är begreppet en gemensam, gemensam infrastruktur över många kunder samtidigt, vilket leder till stordriftsfördelar. Detta koncept kallas multi-hyresrätt. Microsoft arbetar kontinuerligt med att se till att arkitekturen för flera innehavare i Microsoft Cloud Azure stöder standarder för säkerhet, sekretess, sekretess och tillgänglighet.

På en arbetsplats i molnet kan en klientorganisation definieras som en klient eller organisation som äger och hanterar en specifik instans av molntjänsten. Med identitetsplattformen som tillhandahålls av Microsoft Azure är en klient helt enkelt en dedikerad instans av Azure Active Directory (Azure AD) som din organisation tar emot och äger när den registrerar sig för en Microsoft-molntjänst.

Varje Azure AD-katalog är separat och åtskild från andra Azure AD-kataloger. Precis som ett företags kontorsbyggnad är en säker resurs som är specifik för din organisation har även Azure AD-katalogen utformats för att vara en säker tillgång för exklusiv användning av din organisation. Azure AD-arkitekturen håller isär kunddata och identitetsinformation. Det innebär att användare och administratörer av en Azure AD-katalog inte oavsiktligt eller illvilligt kan komma åt data i en annan katalog.

### <a name="azure-tenancy"></a>Azure hyresrätt
Azure-arrende (Azure-prenumeration) refererar till en "kund/fakturering" relation och en unik [klient i](../../active-directory/develop/quickstart-create-new-tenant.md) Azure [Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Isolering på klientnivå i Microsoft Azure uppnås med hjälp av Azure Active Directory och [rollbaserade kontroller](../../role-based-access-control/overview.md) som erbjuds av den. Varje Azure-prenumeration är associerad med en AD-katalog (Azure Active Directory).

Användare, grupper och program från den katalogen kan hantera resurser i Azure-prenumerationen. Du kan tilldela dessa åtkomsträttigheter med hjälp av Azure-portalen, Azure-kommandoradsverktyg och Azure Management API:er. En Azure AD-klient är logiskt isolerad med hjälp av säkerhetsgränser så att ingen kund kan komma åt eller kompromettera medhyresgäster, antingen uppsåtligt eller oavsiktligt. Azure AD körs på "bare metal"-servrar isolerade på ett segregerat nätverkssegment, där paketfiltrering på värdnivå och Windows-brandväggen blockerar oönskade anslutningar och trafik.

- Åtkomst till data i Azure AD kräver användarautentisering via en säkerhetstokentjänst (STS). Information om användarens existens, aktiverat tillstånd och roll används av auktoriseringssystemet för att avgöra om den begärda åtkomsten till målklienten är auktoriserad för den här användaren i den här sessionen.

![Azure hyresrätt](./media/isolation-choices/azure-isolation-fig1.png)


- Klienter är diskreta behållare och det finns inget samband mellan dessa.

- Ingen åtkomst mellan klienter om inte klientadministratören beviljar den via federation eller etablering av användarkonton från andra klienter.

- Fysisk åtkomst till servrar som utgör Azure AD-tjänsten och direkt åtkomst till Azure AD:s backend-system är begränsad.

- Azure AD-användare har ingen åtkomst till fysiska tillgångar eller platser, och därför är det inte möjligt för dem att kringgå de logiska RBAC-principkontroller som anges nedan.

För diagnostik- och underhållsbehov krävs och används en operativ modell som använder ett just-in-time-behörighetshöjningssystem. Azure AD Privileged Identity Management (PIM) introducerar konceptet med en kvalificerad administratör. [Berättigade administratörer](../../active-directory/privileged-identity-management/pim-configure.md) bör vara användare som behöver privilegierad åtkomst då och då, men inte varje dag. Rollen är inaktiv tills användaren behöver åtkomst. Därefter slutför användaren en aktiveringsprocess och blir aktiv administratör under en förinställd tidsperiod.

![Azure AD Privileged Identity Management](./media/isolation-choices/azure-isolation-fig2.png)

Azure Active Directory är värd för varje klient i sin egen skyddade behållare, med principer och behörigheter till och inom behållaren som endast ägs och hanteras av klienten.

Begreppet klientbehållare är djupt invanda i katalogtjänsten på alla lager, från portaler hela vägen till beständig lagring.

Även när metadata från flera Azure Active Directory-klienter lagras på samma fysiska disk finns det ingen relation mellan behållarna förutom vad som definieras av katalogtjänsten, vilket i sin tur dikteras av klientadministratören.

### <a name="azure-role-based-access-control-rbac"></a>Azure-rollbaserad åtkomstkontroll (RBAC)
[Azure Role-Based Access Control (RBAC)](../../role-based-access-control/overview.md) hjälper dig att dela olika komponenter som är tillgängliga i en Azure-prenumeration genom att tillhandahålla finkornig åtkomsthantering för Azure. Med Azure RBAC kan du segregera uppgifter inom organisationen och bevilja åtkomst baserat på vad användarna behöver för att utföra sina jobb. I stället för att ge alla obegränsade behörigheter i Azure-prenumeration eller resurser kan du bara tillåta vissa åtgärder.

Azure RBAC har tre grundläggande roller som gäller för alla resurstyper:

- **Ägaren** har full tillgång till alla resurser, inklusive rätten att delegera åtkomst till andra.

- **Deltagare** kan skapa och hantera alla typer av Azure-resurser men kan inte bevilja åtkomst till andra.

- **Läsaren** kan visa befintliga Azure-resurser.

![Rollbaserad åtkomstkontroll i Azure](./media/isolation-choices/azure-isolation-fig3.png)

Resten av RBAC-rollerna i Azure tillåter hantering av specifika Azure-resurser. Till exempel tillåter rollen Virtuell datordeltagare att en användare skapar och hanterar virtuella datorer. Det ger dem inte åtkomst till Det virtuella Azure-nätverket eller undernätet som den virtuella datorn ansluter till.

[RBAC-inbyggda roller](../../role-based-access-control/built-in-roles.md) listar de roller som är tillgängliga i Azure. Den anger de åtgärder och omfattning som varje inbyggd roll beviljar användare. Om du vill definiera dina egna roller för ännu mer kontroll kan du se hur du skapar [anpassade roller i Azure RBAC](../../role-based-access-control/custom-roles.md).

Några andra funktioner för Azure Active Directory är:
- Azure AD aktiverar SSO för SaaS-program, oavsett var de finns. Vissa program federeras med Azure AD och andra använder enkel inloggning med lösenord. Federerade program kan också stödja användaretablering och [lösenordsvalv.](https://www.techopedia.com/definition/31415/password-vault)

- Åtkomst till data i [Azure Storage](https://azure.microsoft.com/services/storage/) styrs via autentisering. Varje lagringskonto har en primärnyckel[(lagringskontonyckel](../../storage/common/storage-create-storage-account.md)eller SAK) och en sekundär hemlig nyckel (signaturen för delad åtkomst eller SAS).

- Azure AD tillhandahåller identitet som en tjänst via federation med hjälp av [Active Directory Federation Services](../../active-directory/hybrid/how-to-connect-fed-azure-adfs.md), synkronisering och replikering med lokala kataloger.

- [Azure Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md) är multifaktorautentiseringstjänsten som kräver att användare verifierar inloggningar med hjälp av en mobilapp, telefonsamtal eller sms. Den kan användas med Azure AD för att skydda lokala resurser med Azure Multi-Factor Authentication server, och även med anpassade program och kataloger med hjälp av SDK.

- [Med Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) kan du ansluta till virtuella Azure-datorer till en Active Directory-domän utan att distribuera domänkontrollanter. Du kan logga in på dessa virtuella datorer med företagets Active Directory-autentiseringsuppgifter och administrera domänanslutna virtuella datorer med hjälp av grupprinciper för att framtvinga säkerhetsbaslinjer på alla dina virtuella Azure-datorer.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) tillhandahåller en högtillgänglig global identitetshanteringstjänst för konsumentinriktade program som skalas till hundratals miljoner identiteter. Den kan integreras över mobila plattformar och webbaserade plattformar. Dina konsumenter kan logga in på alla dina program via anpassningsbara upplevelser genom att använda sina befintliga sociala konton eller genom att skapa autentiseringsuppgifter.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Isolering från Microsoft-administratörer & dataradering
Microsoft vidtar kraftfulla åtgärder för att skydda dina data från olämplig åtkomst eller användning av obehöriga personer. Dessa operativa processer och kontroller backas upp av [onlinetjänstvillkoren](https://aka.ms/Online-Services-Terms), som erbjuder avtalsenliga åtaganden som styr åtkomsten till dina data.

-   Microsoft-tekniker har inte standardåtkomst till dina data i molnet. I stället beviljas de tillträde, under ledningstillsyn, endast när det är nödvändigt. Den åtkomsten kontrolleras noggrant och loggas och återkallas när den inte längre behövs.

-   Microsoft kan anlita andra företag för att tillhandahålla begränsade tjänster för dess räkning. Underleverantörer får endast komma åt kunddata för att leverera de tjänster som vi har anlitat dem för att tillhandahålla, och de är förbjudna att använda dem för något annat ändamål. Vidare är de enligt avtal skyldiga att upprätthålla sekretessen för våra kunders information.

Företagstjänster med granskade certifieringar som ISO/IEC 27001 kontrolleras regelbundet av Microsoft och ackrediterade revisionsföretag, som utför exempelgranskningar för att styrka denna åtkomst, endast för legitima affärsändamål. Du kan alltid komma åt dina egna kunddata när som helst och av vilken anledning som helst.

Om du tar bort data tar Microsoft Azure bort data, inklusive cachelagrade kopior eller säkerhetskopior. För in-scope-tjänster sker den borttagningen inom 90 dagar efter lagringsperiodens. (In-scope-tjänster definieras i avsnittet Databehandlingsvillkor i våra [villkor för onlinetjänster](https://aka.ms/Online-Services-Terms).)

Om en hårddisk som används för lagring drabbas av ett maskinvarufel [raderas eller förstörs](https://microsoft.com/trustcenter/privacy/you-own-your-data) den på ett säkert sätt innan Microsoft returnerar den till tillverkaren för utbyte eller reparation. Data på enheten skrivs över för att säkerställa att data inte kan återställas på något sätt.

## <a name="compute-isolation"></a>Beräkna isolering
Microsoft Azure tillhandahåller olika molnbaserade datortjänster som innehåller ett brett urval av beräkningsinstanser & tjänster som kan skalas upp och ned automatiskt för att uppfylla behoven hos ditt program eller företag. Dessa beräkningsinstanser och tjänster erbjuder isolering på flera nivåer för att skydda data utan att offra flexibiliteten i konfigurationen som kunderna kräver.

### <a name="isolated-virtual-machine-sizes"></a>Isolerade storlekar för virtuella datorer

[!INCLUDE [virtual-machines-common-isolation](../../../includes/virtual-machines-common-isolation.md)]

### <a name="dedicated-hosts"></a>Dedikerade värdar
Förutom de isolerade värdar som beskrivs i föregående avsnitt erbjuder Azure även dedikerade värdar. Dedikerade värdar i Azure är en tjänst som tillhandahåller fysiska servrar som kan vara värd för en eller flera virtuella datorer och som är dedikerade till en enda Azure-prenumeration. Dedikerade värdar tillhandahåller maskinvaruisolering på den fysiska servernivån. Inga andra virtuella datorer kommer att placeras på dina värdar. Dedikerade värdar distribueras i samma datacenter och delar samma nätverk och underliggande lagringsinfrastruktur som andra, icke-isolerade värdar. Mer information finns i den detaljerade översikten över [Azure-dedikerade värdar](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts).

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Hyper-V-& root OS-isolering mellan virtuella rot-&-datorer med gäst
Azures beräkningsplattform baseras på datorvirtualisering, vilket innebär att all kundkod körs på en virtuell Hyper-V-dator. På varje Azure-nod (eller nätverksslutpunkt) finns det en Hypervisor som körs direkt över maskinvaran och delar upp en nod i ett variabelt antal virtuella gästdatorer (VIRTUELLA datorer).


![Hyper-V-& root OS-isolering mellan virtuella rot-&-datorer med gäst](./media/isolation-choices/azure-isolation-fig4.jpg)


Varje nod har också en speciell rot-VM, som kör värdoperativsystemet. En kritisk gräns är isoleringen av roten VM från gäst-virtuella datorer och gäst-virtuella datorer från varandra, som hanteras av hypervisorn och rotoperativsystemet. Hypervisor/root OS-parkopplingen utnyttjar Microsofts årtionden av säkerhetserfarenhet av operativsystem och nyare inlärning från Microsofts Hyper-V för att ge stark isolering av virtuella gäst-datorer.

Azure-plattformen använder en virtualiserad miljö. Användarinstanser fungerar som fristående virtuella datorer som inte har åtkomst till en fysisk värdserver.

Azure hypervisor fungerar som en mikrokärna och skickar alla begäranden om maskinvaruåtkomst från gäst virtuella datorer till värden för bearbetning med hjälp av ett delat minnesgränssnitt som kallas VMBus. Detta förhindrar att användare erhåller rååtkomsten läs/skriv/kör till systemet och minskar risken med att dela systemresurser.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Avancerad VM-placeringsalgoritm & skydd mot sidokanalattacker
Varje attack mellan virtuella datorer innebär två steg: att placera en motståndare-kontrollerad virtuell dator på samma värd som en av offret virtuella datorer, och sedan bryta isoleringsgränsen för att antingen stjäla känslig offer information eller påverka dess prestanda för girighet eller vandalism. Microsoft Azure skyddar i båda stegen med hjälp av en avancerad VM-placeringsalgoritm och skydd mot alla kända sidokanalattacker, inklusive bullriga start-och returdrar.

### <a name="the-azure-fabric-controller"></a>Azure Fabric-styrenheten
Azure Fabric Controller ansvarar för att allokera infrastrukturresurser till klientarbetsbelastningar och hanterar enkelriktad kommunikation från värden till virtuella datorer. Den virtuella placeringsalgoritmen för Azure-infrastrukturstyrenheten är mycket sofistikerad och nästan omöjlig att förutsäga som fysisk värdnivå.

![Azure Fabric-styrenheten](./media/isolation-choices/azure-isolation-fig5.png)

Azure hypervisor framtvingar minne och processseparation mellan virtuella datorer, och det dirigerar nätverkstrafik till gästoperatorer. Detta eliminerar risken för och sidokanalattack på VM-nivå.

I Azure är roten VM speciell: den kör ett härdat operativsystem som kallas roten OS som är värd för en fabric agent (FA). FAs används i sin tur för att hantera gästagenter (GA) inom gäst-OSes på kund-virtuella datorer. FAs hanterar också lagringsnoder.

Samlingen av Azure hypervisor, root OS/FA och kund-VIRTUELLA datorer/GAs består av en beräkningsnod. FAs hanteras av en infrastrukturstyrenhet (FC), som finns utanför beräknings- och lagringsnoder (beräknings- och lagringskluster hanteras av separata FCs). Om en kund uppdaterar programmets konfigurationsfil medan den körs kommunicerar FC med ank. I händelse av ett maskinvarufel hittar FC automatiskt tillgänglig maskinvara och startar om den virtuella datorn där.

![Azure Fabric Controller](./media/isolation-choices/azure-isolation-fig6.jpg)

Kommunikation från en Fabric Controller till en agent är enkelriktad. Agenten implementerar en SSL-skyddad tjänst som bara svarar på begäranden från styrenheten. Det går inte att initiera anslutningar till styrenheten eller andra privilegierade interna noder. FC behandlar alla svar som om de inte var betrodda.


![Tyg Controller](./media/isolation-choices/azure-isolation-fig7.png)

Isoleringen sträcker sig från rotdassen från virtuella gästdämpare och gästdassss från varandra. Beräkningsnoder är också isolerade från lagringsnoder för ökat skydd.


Hypervisorn och värdoperativsystemet tillhandahåller nätverkspaket - filter för att säkerställa att ej betrodda virtuella datorer inte kan generera förfalskad trafik eller ta emot trafik som inte är adresserad till dem, dirigera trafik till skyddade infrastrukturslutpunkter eller skicka/ta emot olämplig sändningstrafik.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Ytterligare regler som konfigurerats av Fabric Controller Agent för att isolera VM
Som standard blockeras all trafik när en virtuell dator skapas och sedan konfigurerar fabric controller-agenten paketfiltret för att lägga till regler och undantag för att tillåta auktoriserad trafik.

Det finns två kategorier av regler som är programmerade:

-   **Regler för maskinkonfiguration eller infrastruktur:** Som standard är all kommunikation blockerad. Det finns undantag för att tillåta att en virtuell dator skickar och tar emot DHCP- och DNS-trafik. Virtuella datorer kan också skicka trafik till det "offentliga" internet och skicka trafik till andra virtuella datorer inom samma Virtuella Azure-nätverk och aktiveringsservern för operativsystemet. De virtuella datorernas lista över tillåtna utgående destinationer innehåller inte Undernät för Azure-router, Azure-hantering och andra Microsoft-egenskaper.

-   **Konfigurationsfil för roll:** Detta definierar de inkommande åtkomstkontrollistorna (ACL: er) baserat på klientens tjänstmodell.

### <a name="vlan-isolation"></a>VLAN-isolering
Det finns tre VLAN i varje kluster:

![VLAN-isolering](./media/isolation-choices/azure-isolation-fig8.jpg)


-   Huvud-VLAN – kopplar samman ej betrodda kundnoder

-   FC VLAN – innehåller betrodda NÄTVERKS- och stödsystem

-   Enheten VLAN – innehåller betrodda nätverk och andra infrastrukturenheter

Kommunikation är tillåten från FC VLAN till huvud-VLAN, men kan inte initieras från huvud-VLAN till FC VLAN. Kommunikationen blockeras också från huvud-VLAN till enheten VLAN. Detta säkerställer att även om en nod som kör kundkod äventyras, kan den inte attackera noder på antingen FC eller enhetenS VPN.

## <a name="storage-isolation"></a>Isolering av lagring
### <a name="logical-isolation-between-compute-and-storage"></a>Logisk isolering mellan beräkning och lagring
Som en del av sin grundläggande design separerar Microsoft Azure VM-baserad beräkning från lagring. Den här separationen gör det möjligt för beräkning och lagring att skalas oberoende av sig, vilket gör det enklare att tillhandahålla flerrätter och isolering.

Därför körs Azure Storage på separat maskinvara utan nätverksanslutning till Azure Compute förutom logiskt. Det innebär att när en virtuell disk skapas allokeras diskutrymmet inte för hela dess kapacitet. I stället skapas en tabell som mappar adresser på den virtuella disken till områden på den fysiska disken och den tabellen är till en början tom. **Första gången en kund skriver data på den virtuella disken allokeras utrymme på den fysiska disken och en pekare till den placeras i tabellen.**
### <a name="isolation-using-storage-access-control"></a>Isolering med hjälp av lagringsåtkomstkontroll
**Åtkomstkontroll i Azure Storage** har en enkel åtkomstkontrollmodell. Varje Azure-prenumeration kan skapa ett eller flera lagringskonton. Varje lagringskonto har en enda hemlig nyckel som används för att kontrollera åtkomsten till alla data i det lagringskontot.

![Isolering med hjälp av lagringsåtkomstkontroll](./media/isolation-choices/azure-isolation-fig9.png)

**Åtkomst till Azure Storage-data (inklusive tabeller)** kan styras via en [SAS-token (Shared Access Signature),](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) som ger begränsad åtkomst. SAS skapas via en frågemall (URL), signerad med [SAK (Storage Account Key)](https://msdn.microsoft.com/library/azure/ee460785.aspx). Den [signerade WEBBADRESSEN](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) kan ges till en annan process (det vill ha delegerat), som sedan kan fylla i information om frågan och göra begäran om lagringstjänsten. Med en SAS kan du bevilja tidsbaserad åtkomst till klienter utan att avslöja lagringskontots hemliga nyckel.

SAS innebär att vi kan bevilja en klient begränsade behörigheter, till objekt i vårt lagringskonto under en angiven tidsperiod och med en angiven uppsättning behörigheter. Vi kan bevilja dessa begränsade behörigheter utan att behöva dela dina kontoåtkomstnycklar.

### <a name="ip-level-storage-isolation"></a>Isolering av lagring på IP-nivå
Du kan upprätta brandväggar och definiera ett IP-adressintervall för dina betrodda klienter. Med ett IP-adressintervall kan endast klienter som har en IP-adress inom det definierade intervallet ansluta till [Azure Storage](../../storage/blobs/security-recommendations.md).

IP-lagringsdata kan skyddas från obehöriga användare via en nätverksmekanism som används för att allokera en dedikerad eller dedikerad tunnel av trafik till IP-lagring.

### <a name="encryption"></a>Kryptering
Azure erbjuder följande typer av kryptering för att skydda data:
-   Kryptering under överföring

-   Vilande kryptering

#### <a name="encryption-in-transit"></a>Kryptering vid överföring
Kryptering under överföring är en mekanism för att skydda data när den överförs över nätverk. Med Azure Storage kan du skydda data med:

-   [Kryptering på transportnivå](../../storage/blobs/security-recommendations.md), till exempel HTTPS när du överför data till eller från Azure Storage.

-   [Trådkryptering](../../storage/blobs/security-recommendations.md), till exempel SMB 3.0-kryptering för Azure File-resurser.

-   [Kryptering på klientsidan](../../storage/blobs/security-recommendations.md)för att kryptera data innan de överförs till lagring och för att dekryptera data efter att de har överförts från lagring.

#### <a name="encryption-at-rest"></a>Kryptering i vila
För många organisationer är [datakryptering i vila](isolation-choices.md) ett obligatoriskt steg mot datasekretess, efterlevnad och datasuveränitet. Det finns tre Azure-funktioner som tillhandahåller kryptering av data som är "i vila":

-   [Med storage service-kryptering](../../storage/blobs/security-recommendations.md) kan du begära att lagringstjänsten automatiskt krypterar data när du skriver den till Azure Storage.

-   [Kryptering på klientsidan](../../storage/blobs/security-recommendations.md) ger också funktionen kryptering i vila.

-   [Med Azure Disk Encryption](../azure-security-disk-encryption-overview.md) kan du kryptera os-diskar och datadiskar som används av en virtuell IaaS-dator.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) for virtual machines (VMs) hjälper dig att hantera organisatoriska säkerhets- och efterlevnadskrav genom att kryptera dina VM-diskar (inklusive start- och datadiskar) med nycklar och principer som du styr i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Diskkrypteringslösningen för Windows är baserad på [Microsoft BitLocker-diskkryptering](https://technet.microsoft.com/library/cc732774.aspx)och Linux-lösningen är baserad på [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Lösningen stöder följande scenarier för virtuella IaaS-datorer när de är aktiverade i Microsoft Azure:
-   Integrering med Azure Key Vault

-   Virtuella datorer på standardnivå: virtuella datorer på standardnivå: virtuella datorer i serie A, D, DS, G, GS och så vidare, serie IaaS virtuella datorer

-   Aktivera kryptering på virtuella Windows- och Linux IaAS-datorer

-   Inaktivera kryptering på operativsystem och dataenheter för virtuella Datorer i Windows IaaS

-   Inaktivera kryptering på dataenheter för virtuella Linux IaaS-datorer

-   Aktivera kryptering på virtuella IaaS-datorer som kör Windows-klientoperatorsystem

-   Aktivera kryptering på volymer med monteringsbanor

-   Aktivera kryptering på virtuella Linux-datorer som konfigureras med diskremsning (RAID) med [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   Aktivera kryptering på virtuella Linux-datorer med hjälp av [LVM(Logisk volymhanterare)](https://msdn.microsoft.com/library/windows/desktop/bb540532) för datadiskar

-   Aktivera kryptering på virtuella Windows-datorer som konfigureras med hjälp av lagringsutrymmen

-   Alla offentliga Azure-regioner stöds

Lösningen stöder inte följande scenarier, funktioner och teknik i versionen:

-   Virtuella datorer på grundläggande nivå IaaS

-   Inaktivera kryptering på en OS-enhet för virtuella Linux IaaS-datorer

-   Virtuella IaaS-datorer som skapas med den klassiska metoden för att skapa virtuella datorer

-   Integrering med din lokala nyckelhanteringstjänst

-   Azure Files (delat filsystem), NFS (Network File System), dynamiska volymer och virtuella Windows-datorer som är konfigurerade med programvarubaserade RAID-system

## <a name="sql-azure-database-isolation"></a>Isolering av SQL Azure-databas
SQL Database är en relationsdatabastjänst i Microsoft Cloud som är baserad på den marknadsledande Microsoft SQL Server-motorn och som kan hantera verksamhetskritiska arbetsbelastningar. SQL Database erbjuder förutsägbar dataisolering på kontonivå, geografi/region baserat och baserat på nätverk– allt med nära nolladministration.

### <a name="sql-azure-application-model"></a>SQL Azure-programmodell

[Microsoft SQL Azure](../../sql-database/sql-database-single-database-get-started.md) Databasen är en molnbaserad relationsdatabastjänst som bygger på SQL Server-teknik. Det ger en mycket tillgänglig, skalbar databastjänst med flera innehavare som microsoft i molnet erbjuder.

Ur ett programperspektiv ger SQL Azure följande hierarki: Varje nivå har en-till-många-inneslutning av nivåer under.

![SQL Azure-programmodell](./media/isolation-choices/azure-isolation-fig10.png)

Kontot och prenumerationen är Microsoft Azure-plattformskoncept för att associera fakturering och hantering.

Logiska servrar och databaser är SQL Azure-specifika begrepp och hanteras med hjälp av SQL Azure, förutsatt OData och TSQL-gränssnitt eller via SQL Azure-portal som är integrerad i Azure-portalen.

SQL Azure-servrar är inte fysiska instanser eller VM-instanser, utan de är samlingar av databaser, delningshantering och säkerhetsprinciper, som lagras i så kallad "logisk huvuddatabas".

![SQL Azure](./media/isolation-choices/azure-isolation-fig11.png)

Logiska huvuddatabaser inkluderar:

-   SQL-inloggningar som används för att ansluta till servern

-   Brandväggsregler

Fakturerings- och användningsrelaterad information för SQL Azure-databaser från samma logiska server är inte garanterat att vara på samma fysiska instans i SQL Azure-klustret, i stället måste program ange måldatabasnamnet vid anslutning.

Ur ett kundperspektiv skapas en logisk server i ett geografiskt område medan det faktiska skapandet av servern sker i ett av klustren i regionen.

### <a name="isolation-through-network-topology"></a>Isolering via nätverkstopologi

När en logisk server skapas och dess DNS-namn registreras pekar DNS-namnet på den så kallade "Gateway VIP"-adressen i det specifika datacenter där servern placerades.

Bakom VIP (virtuell IP-adress) har vi en samling statslösa gatewaytjänster. I allmänhet engagerar sig gateways när det behövs samordning mellan flera datakällor (huvuddatabas, användardatabas osv.). Gateway-tjänster implementerar följande:
-   **TDS-anslutningsproxy.** Detta inkluderar att hitta användardatabasen i serverdelsklustret, implementera inloggningssekvensen och sedan vidarebefordra TDS-paketen till serverdelen och tillbaka.

-   **Databashantering.** Detta inkluderar att implementera en samling arbetsflöden för att göra CREATE/ALTER/DROP-databasåtgärder. Databasåtgärderna kan anropas genom att antingen sniffa TDS-paket eller explicita OData-API:er.

-   SKAPA/ÄNDRA/DROP-inloggning/användaråtgärder

-   Logiska serverhanteringsåtgärder via OData API

![Isolering via nätverkstopologi](./media/isolation-choices/azure-isolation-fig12.png)

Nivån bakom gateways kallas "back-end". Det är här alla data lagras på ett mycket tillgängligt sätt. Varje del av data sägs tillhöra en "partition" eller "redundansenhet", var och en av dem har minst tre repliker. Repliker lagras och replikeras av SQL Server-motorn och hanteras av ett redundanssystem som ofta kallas "tyg".

I allmänhet kommunicerar backend-systemet inte utgående till andra system som en säkerhetsåtgärd. Detta är reserverat för systemen på frontend-nivån (gateway).This is reserved to the systems in the front-end (gateway) tier. Gateway-nivådatorerna har begränsade privilegier på backend-datorerna för att minimera attackytan som en försvars-djupgående mekanism.

### <a name="isolation-by-machine-function-and-access"></a>Isolering efter maskinfunktion och åtkomst
SQL Azure (består av tjänster som körs på olika datorfunktioner. SQL Azure är indelat i "serverdel" Cloud Database och "front-end" (Gateway/Management) miljöer, med den allmänna principen om trafik bara går in i backen end och inte ut. Frontend-miljön kan kommunicera med omvärlden av andra tjänster och i allmänhet har endast begränsade behörigheter i serverdelen (tillräckligt för att anropa de ingångspunkter den behöver åberopa).

## <a name="networking-isolation"></a>Isolering av nätverk
Azure-distributionen har flera lager av nätverksisolering. Följande diagram visar olika lager av nätverksisolering Azure tillhandahåller kunder. Dessa lager är både inbyggda i Själva Azure-plattformen och kunddefinierade funktioner. Azure DDoS är inkommande från Internet och ger isolering mot storskaliga attacker mot Azure. Nästa isoleringslager är kunddefinierade offentliga IP-adresser (slutpunkter), som används för att avgöra vilken trafik som kan passera molntjänsten till det virtuella nätverket. Inbyggd Azure-isolering av virtuella nätverk säkerställer fullständig isolering från alla andra nätverk och att trafiken endast flödar via användarkonfigurerade sökvägar och metoder. Dessa sökvägar och metoder är nästa lager, där NSGs, UDR och virtuella nätverksenheter kan användas för att skapa isoleringsgränser för att skydda programdistributionerna i det skyddade nätverket.

![Isolering av nätverk](./media/isolation-choices/azure-isolation-fig13.png)

**Isolering av trafik:** Ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md) är trafikisoleringsgränsen på Azure-plattformen. Virtuella datorer (VMs) i ett virtuellt nätverk kan inte kommunicera direkt till virtuella datorer i ett annat virtuellt nätverk, även om båda virtuella nätverk skapas av samma kund. Isolering är en kritisk egenskap som säkerställer att virtuella kunddr och kommunikation förblir privat i ett virtuellt nätverk.

[Undernät](../../virtual-network/virtual-networks-overview.md) erbjuder ytterligare ett lager av isolering med i virtuellt nätverk baserat på IP-intervall. IP-adresser i det virtuella nätverket kan du dela upp ett virtuellt nätverk i flera undernät för organisation och säkerhet. VM:ar och PaaS-rollinstanser som distribuerats till undernät (samma eller olika) inom ett VNet, kan kommunicera med varandra utan övrig konfiguration. Du kan också konfigurera [NSG (Network Security Group)](../../virtual-network/virtual-networks-overview.md) för att tillåta eller neka nätverkstrafik till en VM-instans baserat på regler som konfigurerats i åtkomstkontrollista (ACL) för NSG. NSG:er kan antingen associeras med undernät eller individuella VM-instanser inom det undernätet. När en NSG är associerad med ett undernät, tillämpas ACL-reglerna på alla VM-instanser i det undernätet.

## <a name="next-steps"></a>Efterföljande moment

- Lär dig mer om [alternativ för nätverksisolering för datorer i Virtuella Windows Azure-nätverk](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/). Detta inkluderar det klassiska frontend- och backend-scenariot där datorer i ett visst backend-nätverk eller undernätverk endast kan tillåta vissa klienter eller andra datorer att ansluta till en viss slutpunkt baserat på en tillåtslista över IP-adresser.

- Lär dig mer om [isolering av virtuella datorer i Azure](../../virtual-machines/windows/isolation.md). Azure Compute erbjuder storlekar för virtuella datorer som är isolerade till en viss maskinvarutyp och dedikerade till en enskild kund.
