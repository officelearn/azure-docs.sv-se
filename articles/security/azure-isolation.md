---
title: Isolering i det offentliga Azure-molnet | Microsoft Docs
description: Läs mer om molnbaserad databehandling tjänster som omfattar ett brett urval av beräkningsinstanser och tjänster som kan skalas upp och ned automatiskt för att uppfylla behoven i ditt program eller enterprise.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 4ef312ebd6c329028a556778c24c5e0e41706056
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53311005"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Isolering i det offentliga Azure-molnet
##  <a name="introduction"></a>Introduktion
### <a name="overview"></a>Översikt
För att hjälpa Azure aktuella och potentiella kunder förstå och använda olika säkerhetsrelaterade funktioner som ingår i och med Azure-plattformen Microsoft har utvecklat en serie White Paper-faktablad, Security översikter, bästa praxis och Checklistor.
Ämnena variera beroende på bredden och djupet och uppdateras regelbundet. Det här dokumentet är en del av serien som sammanfattas i följande abstrakta avsnittet.

### <a name="azure-platform"></a>Azure-plattformen
Azure är en öppen och flexibel molntjänstplattform som har stöd för det bredaste urvalet av operativsystem, programmeringsspråk, ramverk, verktyg, databaser och enheter. Du kan till exempel:
- Kör Linux-behållare med Docker-integrering
- Skapa appar med JavaScript, Python, .NET, PHP, Java och Node.js; och
- Bygg serverdelar för iOS, Android och Windows enheter.

Microsoft Azure har stöd för samma teknik som miljontals utvecklare och IT-proffs redan använder och litar på.

När du bygger på, eller migrerar IT-tillgångar till en offentlig molntjänstleverantör du lita på den organisationens förmåga att skydda dina program och data med tjänsterna och de kontroller som de tillhandahåller för att hantera säkerheten för dina molnbaserade tillgångar.

Allt i Azures infrastruktur, från anläggning till tillämpningar, är utformat för att fungera som värd för miljoner kunder samtidigt, och den tillhandahåller en säker grund som företaget kan använda sig av för att möta de interna säkerhetsbehoven. Dessutom erbjuder Azure dig en mängd olika konfigurerbara säkerhetsalternativ samt möjligheten att kontrollera dem, så att du kan anpassa säkerheten för att uppfylla de specifika behoven hos dina distributioner. Det här dokumentet hjälper dig att uppfylla dessa krav.

### <a name="abstract"></a>Abstrakt

Microsoft Azure kan du köra program och virtuella datorer (VM) på delade fysiska infrastrukturen. En av särskilda ekonomiska motiveringarna i program som körs i en molnmiljö är möjligheten att distribuera kostnaden för delade resurser mellan flera kunder. Det här tillvägagångssättet för flera innehavare förbättrar effektiviteten av multiplexering resurser mellan olika kunder med låg kostnad. Tyvärr introducerar det också risken med att dela fysiska servrar och andra resurser i infrastrukturen att köra dina känsliga program och virtuella datorer som kan höra till en skadlig och potentiellt skadliga användare.

Den här artikeln beskrivs hur Microsoft Azure ger isolering mot både skadlig och icke-skadliga användare och fungerar som en vägledning för att skapa molnlösningar genom att erbjuda olika alternativ för isolering arkitekter. Detta white paper fokuserar på teknik av Azure-plattformen och kundinriktad säkerhetskontroller och försöker inte adress serviceavtal, modeller och DevOps-överväganden.

## <a name="tenant-level-isolation"></a>Nivån Klientisolering
En av de främsta fördelarna med molnbaserad databehandling är konceptet med en delad infrastruktur som är vanliga i ett stort antal kunder samtidigt, vilket leder till stordriftsfördelar. Detta kallas för flera innehavare. Microsoft arbetar kontinuerligt för att säkerställa att arkitekturen med flera innehavare av Microsoft Cloud Azure har stöd för säkerhet, sekretess, sekretess, integritet och tillgänglighet standarder.

På en arbetsplats i molnet kan en klientorganisation definieras som en klient eller organisation som äger och hanterar en specifik instans av molntjänsten. Med identitetsplattformen som tillhandahålls av Microsoft Azure, är en klient helt enkelt en dedikerad instans av Azure Active Directory (Azure AD) som din organisation tilldelas och äger när den registrerar sig för en Microsoft-molntjänst.

Varje Azure AD-katalog är separat och åtskild från andra Azure AD-kataloger. Precis som ett företags kontorsbyggnad är en säker resurs som är specifik för din organisation har även Azure AD-katalogen utformats för att vara en säker tillgång för exklusiv användning av din organisation. Azure AD-arkitekturen håller isär kunddata och identitetsinformation. Det innebär att användare och administratörer av en Azure AD-katalog inte oavsiktligt eller illvilligt kan komma åt data i en annan katalog.

### <a name="azure-tenancy"></a>Azure innehavare
Azure innehavare (Azure-prenumeration) refererar till en relation med ”kund/billing” och ett unikt [klient](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) i [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Nivån klientisolering i Microsoft Azure uppnås med hjälp av Azure Active Directory och [rollbaserade kontroller](https://docs.microsoft.com/azure/role-based-access-control/overview) erbjuds av den. Varje Azure-prenumeration är associerad med en katalog i Azure Active Directory (AD).

Användare, grupper och program från katalogen kan hantera resurser i Azure-prenumeration. Du kan tilldela behörigheter med Azure-portalen, Azure kommandoradsverktyg och Azure Management API: er. En Azure AD-klient är logiskt isolerad med hjälp av säkerhetsgränser så att ingen kund kan komma åt eller kompromettera delad klienter oavsiktligt eller illvilligt. Azure AD som körs på ”bare metal”-servrar som är isolerade i en fördelad nätverkssegment där filtrering av nätverkspaket värdnivå och Windows-brandväggen blockerar oönskad anslutningar och trafik.

- Åtkomst till data i Azure AD kräver användarautentisering via en säkerhetstokentjänst (STS). Information om användarens förekomsten, aktiverade tillstånd och rollen används av auktorisering systemet för att avgöra om den begärda åtkomsten till Målklienten har behörighet för den här användaren i den här sessionen.

![Azure innehavare](./media/azure-isolation/azure-isolation-fig1.png)


- Klienter är diskreta behållare och det finns ingen relation mellan dessa.

- Ingen åtkomst mellan klienter, såvida inte klientorganisationens administratör beviljar via federation eller etablera användarkonton från andra klienter.

- Fysisk åtkomst till servrar som utgör Azure AD-tjänsten och direktåtkomst till Azure Active Directorys backend-system är begränsad.

- Azure AD-användare har ingen åtkomst till fysiska tillgångar eller platser och är därför inte möjligt för dem att kringgå de logiska RBAC principkontroller anges efter.

För diagnostik- och underhållsbehov en driftsmodell som använder ett system för höjning av just-in-time-privilegier krävs och används. Azure AD Privileged Identity Management (PIM) introduceras konceptet berättigad administratör. [Berättigade administratörer](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) ska vara användare som behöver privilegierad åtkomst då och då, men inte varje dag. Rollen är inaktiv tills användaren behöver åtkomst. Därefter slutför användaren en aktiveringsprocess och blir aktiv administratör under en förinställd tidsperiod.

![Azure AD Privileged Identity Management](./media/azure-isolation/azure-isolation-fig2.png)

Azure Active Directory är värd för varje klient i en egen skyddad behållare med principer och behörigheter till och inom behållaren enbart ägs och hanteras av klienten.

Begreppet klient behållare är djupt förankrade i katalogtjänsten på alla nivåer från portaler hela vägen till beständig lagring.

Även om metadata från flera Azure Active Directory-klienter finns på samma fysiska disk, finns det ingen relation mellan behållare än det som definieras av katalogtjänsten, vilket i sin tur styrs av klientadministratören.

### <a name="azure-role-based-access-control-rbac"></a>Azure rollbaserad åtkomstkontroll (RBAC)
[Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) hjälper dig att dela olika komponenter som är tillgängliga i en Azure-prenumeration genom att tillhandahålla detaljerad åtkomsthantering för Azure. RBAC i Azure kan du särskilja uppgifter inom din organisation och bevilja åtkomst baserat på vad användare behöver utföra sitt arbete. Istället för att ge alla obegränsad behörighet i Azure-prenumeration eller resurser kan du tillåta enbart vissa åtgärder.

RBAC i Azure har tre grundläggande roller som gäller för alla typer av resurser:

- **Ägare** har fullständig åtkomst till alla resurser inklusive rätten att delegera åtkomst till andra.

- **Deltagare** kan skapa och hantera alla typer av Azure-resurser, men det går inte att bevilja åtkomst till andra.

- **Läsare** kan visa befintliga Azure-resurser.

![Rollbaserad åtkomstkontroll i Azure](./media/azure-isolation/azure-isolation-fig3.png)

Resten av RBAC-roller i Azure kan hanteringen av specifika Azure-resurser. Exempelvis kan gör rollen virtuell Datordeltagare att användaren att skapa och hantera virtuella datorer. Det ger dem åtkomst till Azure-nätverk eller undernät som den virtuella datorn ansluter till.

[RBAC inbyggda roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) listar rollerna som är tillgängliga i Azure. Den anger drift- och omfång som varje inbyggd roll som ger användare. Om du vill definiera egna roller för ännu mer kontroll, se hur du skapar [anpassade roller i Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Vissa andra funktioner för Azure Active Directory är:
- Azure AD kan SSO till SaaS-program, oavsett var de finns. Vissa program federeras med Azure AD och andra använder enkel inloggning med lösenord. Federerade program kan också använda etableringen av användare och [lösenordsvalv](https://www.techopedia.com/definition/31415/password-vault).

- Åtkomst till data i [Azure Storage](https://azure.microsoft.com/services/storage/) styrs via autentisering. Varje lagringskonto har en primär nyckel ([lagringskontonyckel](https://docs.microsoft.com/azure/storage/storage-create-storage-account), eller SAK) och en sekundär hemlig nyckel (signatur för delad åtkomst, eller SAS).

- Azure AD erbjuder identitet som en tjänst via federering med hjälp av [Active Directory Federation Services](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs), synkronisering och replikering med lokala kataloger.

- [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) är Multi-Factor authentication-tjänst som användare måste bekräfta inloggningar med hjälp av en mobilapp, telefonsamtal eller SMS. Det kan användas med Azure AD för att skydda lokala resurser med Azure Multi-Factor Authentication-servern och även med anpassade program och kataloger med hjälp av SDK.

- [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) kan du ansluta virtuella Azure-datorer till en Active Directory-domän utan att distribuera domänkontrollanter. Du kan logga in på dessa virtuella datorer med dina autentiseringsuppgifter för företagets Active Directory och administrera domänanslutna virtuella datorer med hjälp av Grupprincip för att upprätthålla säkerheten på alla virtuella datorer i Azure.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) är en tjänst för konsumentinriktade program med hög tillgänglighet globala Identitetshantering som kan skalas till hundratals miljoner identiteter. Den kan integreras över mobila plattformar och webbaserade plattformar. Konsumenterna kan logga in på alla dina program via anpassningsbara upplevelser med sina befintliga sociala konton eller genom att skapa autentiseringsuppgifter.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Isolering från Microsoft-administratörer & Databorttagning
Microsoft tar stark åtgärder för att skydda dina data från oönskad åtkomst eller använda av obehöriga personer. Dessa driftrutiner och säkerhetskontroller backas upp av den [villkoren för Online Services](https://aka.ms/Online-Services-Terms), som erbjuder avtalsenliga åtaganden som styr åtkomsten till dina data.

-   Microsoft-tekniker har inte åtkomst till dina data i molnet. I stället ges de åtkomst till under hantering av tillsyn, vid behov. Att åtkomsten är noggrant kontrolleras loggas och återkallas när den inte längre behövs.

-   Microsoft kan anlita andra företag som tillhandahåller begränsade tjänster för vår räkning. Underleverantörer som har åtkomst till kunddata endast för att leverera tjänsterna som vi har anlitat dem för att tillhandahålla och de är förbjudna att använda den i något annat syfte. Dessutom är de avtalsenligt bundna att underhålla konfidentialiteten för våra kunder.

Företagstjänster med granskad certifieringar, till exempel ISO/IEC 27001 kontrolleras regelbundet av Microsoft och utomstående revisionsbyråer, vilket gör stickprov för att intyga att åtkomsten endast för legitima affärsändamål. Du kan alltid komma åt din egen kundinformation när som helst och av någon anledning.

Om du tar bort alla data som Microsoft Azure tar bort data, inklusive alla cachelagrade eller sekundära kopior. För omfattade tjänster som borttagningen utförs inom 90 dagar efter slutet av kvarhållningsperioden. (Omfattade tjänster definieras under bearbetning av villkoren i vår [villkoren för Online Services](https://aka.ms/Online-Services-Terms).)

Om en diskenhet som används för lagring av drabbas av ett maskinvarufel, är det säkert [raderas eller förstörs](https://microsoft.com/en-us/trustcenter/privacy/you-own-your-data) innan Microsoft tillbaka det till tillverkaren för ersättning eller reparation. Data på enheten över för att säkerställa att data inte kan återställas på något sätt.

## <a name="compute-isolation"></a>Compute-isolering
Microsoft Azure tillhandahåller olika molnbaserad databehandling tjänster som omfattar ett brett urval av beräkningsinstanser och tjänster som kan skalas upp och ned automatiskt för att uppfylla behoven i ditt program eller enterprise. Dessa instanser och tjänsten erbjuder isolering på flera nivåer för att skydda data utan att offra flexibilitet i konfigurationen som kunderna kräver.

### <a name="isolated-virtual-machine-sizes"></a>Storlekar isolerade virtuella datorer
Azure Compute ger VM-storlekar som isoleras till en specifik maskinvarutyp och dedikerad till en enda kund.  Dessa VM-storlekar passar bäst för arbetsbelastningar som kräver en hög grad av isolering från andra kunder för arbetsbelastningar som innehåller element som efterlevnad och regelkrav.  Kunder kan också välja att dela upp resurser på dessa isolerade virtuella datorer med hjälp av ytterligare [Azure-stöd för kapslade virtuella datorer](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Använda en isolerad storlek garanterar att din virtuella dator är den enda som körs på den specifika serverinstansen.  De aktuella isolerade VM-erbjudandena omfattar:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2

Du kan läsa mer om varje isolerade storlek som är tillgängliga [här](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Hyper-V- och Root OS-isolering mellan rotens virtuella dator & virtuella gästdatorer
Azures plattform för databearbetning baseras på datorn virtualisering – vilket innebär att alla kund körs på en Hyper-V virtuell dator. Det finns ett hypervisor-program som körs direkt via maskinvaran och delar upp en nod i ett variabelnummer av gäst-datorer (VM) på varje Azure-nod (eller nätverksslutpunkten).


![Hyper-V- och Root OS-isolering mellan rotens virtuella dator & virtuella gästdatorer](./media/azure-isolation/azure-isolation-fig4.jpg)


Varje nod har även en särskild rot-dator som kör Värdoperativsystem. En kritisk gräns är isoleringen av rotens virtuella dator från virtuella gästdatorerna och virtuella gästdatorerna från varandra, hanteras av hypervisor-programmet och roten OS. Hypervisor-program/root OS-kopplingen använder Microsofts årtionden av operativsystemets säkerhet genom och nyare learning från Microsofts Hyper-V för stark isolering av virtuella gästdatorer.

Azure-plattformen använder en virtualiserad miljö. Användarinstanser fungerar som fristående virtuella datorer som inte har åtkomst till en fysisk värdserver.

Azure hypervisor-programmet fungerar som en mikrokernel och skickar alla maskinvaruförfrågningar från virtuella gästdatorer till värden för bearbetning med hjälp av ett delat Minnesgränssnitt som kallas VMBus. Detta förhindrar att användare erhåller rååtkomsten läs/skriv/kör till systemet och minskar risken med att dela systemresurser.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Avancerade VM placeringsalgoritmen och skydd från sida kanal attacker
Alla mellan VM-attack omfattar två steg: placera en angripare-kontrollerade virtuell dator på samma värddator som en av drabbade virtuella datorer och tränga in isoleringsgräns att stjäla känslig drabbade information eller påverka prestandan för greed eller vandalism. Microsoft Azure tillhandahåller skydd på båda dessa steg med hjälp av en avancerad algoritm för placering av VM och bättre skydd mot alla kända sida kanal attacker inklusive resursfördelningen virtuella datorer.

### <a name="the-azure-fabric-controller"></a>Azure-Infrastrukturkontrollanten
Azure-Infrastrukturkontrollanten ansvarar för att fördela infrastrukturresurser till klientarbetsbelastningar och den hanterar enkelriktad kommunikation från värden till virtuella datorer. Virtuella datorer att placera algoritmen för Azure-infrastrukturkontrollanten är sofistikerade och nästan omöjligt att förutsäga som fysisk värdnivå.

![Azure-Infrastrukturkontrollanten](./media/azure-isolation/azure-isolation-fig5.png)

Azure-hypervisorn tillämpar minnes- och processuppdelning mellan virtuella datorer och den dirigerar nätverkstrafik på ett säkert sätt till gästoperativsystemens klienter. Detta eliminerar risken för och sida kanal attack på VM-nivå.

I Azure, rotens virtuella dator är speciell: den kör en förstärkt operativsystem kallas roten OS som är värd för en fabric-agent (FA). FAs används i sin tur att hantera gästagenterna (GA) i gästoperativsystem på kundens virtuella datorer. FAs också hantera lagringsnoder.

Insamling av Azure-hypervisorn rot OS/FA eller kundens virtuella datorer/GAs består av en beräkningsnod. FAs hanteras av en infrastrukturkontrollanten (FC), som finns utanför beräknings- och noder (beräknings- och kluster hanteras av separata FCs). Om en kund uppdaterar sina programkonfigurationsfilen medan den körs, i FC som kommunicerar med FA, som sedan kontaktar GAs, som meddelar tillämpningen av konfigurationsändringen. I händelse av ett maskinvarufel i FC automatiskt hitta tillgänglig maskinvara och starta om den virtuella datorn det.

![Azure-Infrastrukturkontrollanten](./media/azure-isolation/azure-isolation-fig6.jpg)

Kommunikation från en Infrastrukturkontrollanten till en agent är enkelriktade. Agenten implementerar en SSL-skyddad-tjänst som bara svarar på förfrågningar från styrenheten. Det kan inte initiera anslutningar till kontrollanten eller andra Privilegierade interna noder. FC behandlar alla svar som om de vore inte är betrodd.


![Infrastrukturkontrollant](./media/azure-isolation/azure-isolation-fig7.png)

Isolering sträcker sig från den roten virtuella datorn från virtuella Gästdatorerna och de virtuella Gästdatorerna från varandra. Compute-noderna är också isolerad från storage-noder för bättre skydd.


Hypervisor-programmet och värden OS tillhandahåller nätverkspaket - filter för att garantera att den inte är betrodd virtuella datorer inte kan generera falsk trafik eller ta emot trafik som inte är adresserad till dem, dirigera trafik till skyddade infrastrukturslutpunkter eller skicka och ta emot olämplig sändningstrafik.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Ytterligare regler som konfigurerats av Agent för Infrastrukturkontrollanten att isolera virtuella datorer
Som standard blockeras all trafik när en virtuell dator skapas och sedan konfigurerar infrastrukturkontrollantens agent paketfiltret för att lägga till regler och undantag och tillåta auktoriserad trafik.

Det finns två typer av regler som är programmerade:

-   **Dator-konfiguration eller infrastruktur regler:** Som standard blockerad all kommunikation. Det finns undantag så att en virtuell dator att skicka och ta emot DHCP- och DNS-trafik. Virtuella datorer kan även skicka trafik till internet ”offentliga” och skicka trafik till andra virtuella datorer i samma Azure Virtual Network och Aktiveringsservern OS. De virtuella datorerna innehåller för tillåtna utgående mål inte Azure-routerundernät, Azure-hantering och andra Microsoft-egenskaper.

-   **Konfigurationsfilen för rollen:** Detta definierar den inkommande åtkomstkontrollistor (ACL) baserat på klientens tjänstmodellen.

### <a name="vlan-isolation"></a>VLAN-isolering
Det finns tre VLAN i varje kluster:

![VLAN-isolering](./media/azure-isolation/azure-isolation-fig8.jpg)


-   Det huvudsakliga VLANET – sammanbinder obetrodda kunden noder

-   FC-VLAN – innehåller betrodda FCs och ge support för system

-   Enheten VLAN – innehåller betrott nätverk och andra enheter för infrastruktur

Kommunikation tillåts från FC-VLAN huvudsakliga VLAN, men det går inte att initiera från det huvudsakliga VLANET till FC-VLAN. Kommunikation blockeras även från det huvudsakliga VLANET till enheten VLAN. Detta säkerställer att även om en nod som kör Kundkod äventyras, inte kan den angrepp noder på FC eller VLAN-enhet.

## <a name="storage-isolation"></a>Storage-isolering
### <a name="logical-isolation-between-compute-and-storage"></a>Logisk isolering mellan beräkning och lagring
Som en del av dess grundläggande design separerar Microsoft Azure VM-baserad beräkning från storage. Den här separationen kan beräkning och lagring skalas oberoende av varandra, vilket gör det enklare att ge flera innehavare och isolering.

Därför kan körs Azure Storage på separata maskinvara med ingen nätverksanslutning till Azure Compute utom logiskt. [Detta](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf) innebär att när en virtuell disk skapas, inte tilldelat diskutrymme för sin hela kapacitet. Istället skapas en tabell som mappar adresser på den virtuella disken till delar på den fysiska disken och tabellen är tom från början. **Första gången en kund skriver data på den virtuella disken allokeras utrymmet på den fysiska disken och en pekare till den placeras i tabellen.**
### <a name="isolation-using-storage-access-control"></a>Åtkomstkontroll för isolering med lagring
**Åtkomstkontroll i Azure Storage** har en enkel modell för åtkomstkontroll. Varje Azure-prenumeration kan skapa en eller flera Lagringskonton. Varje Lagringskonto har en hemlig nyckel som används för att styra åtkomsten till alla data i det Lagringskontot.

![Åtkomstkontroll för isolering med lagring](./media/azure-isolation/azure-isolation-fig9.png)

**Åtkomst till Azure Storage-data (inklusive tabeller)** kan styras via en [SAS (Shared Access Signature)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) token, vilket ger begränsad åtkomst. SAS har skapats via en fråga-mall (URL) som signeras med den [SAK (Lagringskontonyckel)](https://msdn.microsoft.com/library/azure/ee460785.aspx). Som [signerade URL](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) kan ges till en annan process (som delegerats), som kan sedan fylla i informationen om frågan och kontrollera begäran av storage-tjänsten. En SAS kan du bevilja tidsbaserad åtkomst till klienter utan att avslöja lagringskontots hemlig nyckel.

SAS innebär att vi kan ge en klient begränsad behörighet till objekt i vår storage-konto under en viss tidsperiod och med en angiven uppsättning behörigheter. Vi kan ge dessa begränsade behörigheter utan att behöva dela åtkomstnycklarna för kontot.

### <a name="ip-level-storage-isolation"></a>IP-lagring på blocknivå isolering
Du kan fastställa brandväggar och definiera ett IP-adressintervall för dina betrodda klienter. Med ett IP-adressintervall, endast klienter som har en IP-adress inom det angivna intervallet kan ansluta till [Azure Storage](https://docs.microsoft.com/azure/storage/storage-security-guide).

IP-lagringsdata kan skyddas från obehöriga användare via en mekanism för nätverk som används för att allokera en dedikerad eller dedikerade tunnel för trafik till IP-lagring.

### <a name="encryption"></a>Kryptering
Azure erbjuder följande typer av kryptering för att skydda data:
-   Kryptering under överföring

-   Vilande kryptering

#### <a name="encryption-in-transit"></a>Kryptering under överföring
Kryptering under överföring är en mekanism för att skydda data när de skickas över nätverk. Med Azure Storage kan skydda du data med hjälp av:

-   [Kryptering på transportnivå](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), till exempel HTTPS när du överför data till eller från Azure Storage.

-   [Koppla kryptering](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), till exempel SMB 3.0-kryptering för Azure-filresurser.

-   [Client side encryption](https://docs.microsoft.com/azure/storage/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), att kryptera data innan den överförs till storage och att dekryptera data efter överföringen slut på minne.

#### <a name="encryption-at-rest"></a>Kryptering i vila
Många organisationer [datakryptering i viloläge](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) är ett obligatoriskt steg för sekretess, efterlevnad och datasuveränitet. Det finns tre Azure-funktioner som tillhandahåller kryptering av data som är ”i vila”:

-   [Kryptering av lagringstjänst](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-at-rest) kan du begära att lagringstjänsten automatiskt kryptera data när du skriver den till Azure Storage.

-   [Client side Encryption](https://docs.microsoft.com/azure/storage/storage-security-guide#client-side-encryption) tillhandahåller även funktionen för kryptering i vila.

-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) kan du kryptera OS-diskar och datadiskar som används av en virtuell IaaS-dator.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) för virtuella datorer (VMs) hjälper dig att uppfylla organisationens säkerhet och krav på efterlevnad genom att kryptera dina Virtuella datordiskar (inklusive start- och datadiskar) med nycklar och principer som du styr i [Azure Key Valvet](https://azure.microsoft.com/services/key-vault/).

Disk Encryption-lösningen för Windows är baserad på [Microsoft BitLocker-diskkryptering](https://technet.microsoft.com/library/cc732774.aspx), och Linux-lösningen baseras på [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Lösningen stöder följande scenarier för virtuella IaaS-datorer när de är aktiverade i Microsoft Azure:
-   Integrering med Azure Key Vault

-   Standard-nivån virtuella datorer: A, D, DS, G, GS och så vidare, serien virtuella IaaS-datorer

-   Aktivera kryptering på Windows och Linux IaaS-datorer

-   Inaktivera kryptering på operativsystem och enheter för Windows virtuella IaaS-datorer

-   Inaktivering av kryptering på enheter för virtuella Linux IaaS-datorer

-   Aktivera kryptering på den virtuella IaaS-datorer som kör Windows-klientoperativsystem

-   Aktivera kryptering på volymer med monteringssökvägar

-   Aktivera kryptering på virtuella Linux-datorer som är konfigurerade med disk-striping (RAID) med hjälp av [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   Aktivera kryptering på virtuella Linux-datorer med hjälp av [LVM (Logical Volume Manager)](https://msdn.microsoft.com/library/windows/desktop/bb540532) för datadiskar

-   Aktivera kryptering på Windows virtuella datorer som konfigureras med hjälp av lagringsutrymmen

-   Alla offentliga Azure-regioner som stöds

Lösningen stöder inte följande scenarier, funktioner och -teknik i version:

-   Basic-nivån virtuella IaaS-datorer

-   Inaktivering av kryptering på en OS-enhet för virtuella Linux IaaS-datorer

-   Virtuella IaaS-datorer som skapas med hjälp av klassiska metod för skapande av virtuell dator

-   Integrering med din lokala nyckelhanteringstjänst

-   Azure Files (delade filsystem), Network File System (NFS), dynamiska volymer och Windows-datorer som är konfigurerade med programvarubaserade RAID-system

## <a name="sql-azure-database-isolation"></a>SQL Azure Database isolering
SQL Database är en relationsdatabastjänst i Microsoft Cloud som är baserad på den marknadsledande Microsoft SQL Server-motorn och som kan hantera verksamhetskritiska arbetsbelastningar. SQL Database erbjuder förutsägbar isolering på kontonivå, geografi / region och baserade på nätverk – allt med nästan obefintlig administration.

### <a name="sql-azure-application-model"></a>Modell för SQL Azure-program

[Microsoft SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started) databasen är en molnbaserad relationsdatabastjänst byggd på SQL Server-teknik. Det ger en tjänst med mycket tillgänglig, skalbar och flera innehavare av Microsoft i molnet.

Från ett program innehåller perspektiv SQL Azure följande hierarki: Varje nivå har en-till-många inneslutning av undernivåer.

![Modell för SQL Azure-program](./media/azure-isolation/azure-isolation-fig10.png)

Är Microsoft Azure-plattformen begrepp att associera hantering av fakturor och -konto och prenumeration.

Logiska servrar och databaser finns SQL Azure-specifika begrepp och hanteras med hjälp av SQL Azure, förutsatt att OData- och TSQL-gränssnitt eller via SQL Azure-portalen som integreras i Azure-portalen.

SQL Azure-servrar inte är fysiska eller Virtuella datorinstanser, i stället de är samlingar av databaser, delning av principer för hantering och säkerhet, som lagras i så kallade ”logiska master” databas.

![SQL Azure](./media/azure-isolation/azure-isolation-fig11.png)

Logiska master-databaser är:

-   SQL-inloggningar som används för att ansluta till servern

-   Brandväggsregler

Fakturerings- och användningsrelaterade information för SQL Azure-databaser från samma logiska server inte garanteras att finnas på samma fysiska instans i SQL Azure-kluster i stället program måste ange namnet på mål-databasen när du ansluter.

Från ett kundperspektiv skapas en logisk server i en geo-grafiska region medan det faktiska skapandet av servern sker i ett kluster i regionen.

### <a name="isolation-through-network-topology"></a>Isolering via nätverkets topologi

När en logisk server skapas och dess DNS-namn har registrerats, DNS-namnet pekar på den så kallade ”Gateway VIP”-adressen i specifika datacenter där servern placerades.

Vi har en samling av tillståndslösa gateway-tjänsterna bakom VIP (virtuell IP-adress). I allmänhet delta gatewayer när det finns samordning krävs mellan flera datakällor (master-databasen, användardatabas osv.). Gateway-tjänster implementerar följande:
-   **Proxy är TDS-anslutning.** Detta inkluderar hitta användardatabas i backend-klustret, implementera inloggningssekvensen och sedan vidarebefordra TDS-paket till serverdelen och tillbaka.

-   **Databashantering.** Detta inkluderar att implementera en samling av arbetsflöden för CREATE/ALTER/DROP databasoperationer. Databasåtgärderna kan anropas av kontroll TDS-paket eller explicit OData APIs.

-   CREATE/ALTER/DROP inloggning/användaråtgärder

-   Logisk server hanteringsåtgärder via OData-API

![Isolering via nätverkets topologi](./media/azure-isolation/azure-isolation-fig12.png)

Nivån bakom gateway kallas ”backend”. Det här är alla data ska lagras på ett sätt som med hög tillgänglighet. Varje datadel anses tillhöra en ”partition” eller ”redundansenheten”, var och en av de behöver minst tre repliker. Replikerna lagras och replikeras av SQL Server-motorn och hanteras av ett system för växling vid fel kallas ofta ”infrastruktur”.

I allmänhet backend-system inte kommunicera utgående till andra system som en säkerhetsåtgärd. Detta är reserverat till system i klientdelen (gateway)-nivå. Datorer för gateway-nivån har begränsad behörighet på backend-Server-datorer för att minimera risken för angrepp som en mekanism för skydd på djupet.

### <a name="isolation-by-machine-function-and-access"></a>Isolering av dator-funktion och åtkomst
SQL Azure (består av tjänster som körs på en annan dator funktioner. SQL Azure är uppdelad i ”serverdel” Molndatabas och ”front-end” (Gateway/hantering) miljöer med den allmänna principen trafik endast växla till backend-server och inte ut. Frontend-miljö kan kommunicera med omvärlden av andra tjänster och generellt sett endast har begränsad behörighet i backend-server (tillräckligt för att anropa startpunkter som behövs för att anropa).

## <a name="networking-isolation"></a>Isolering av nätverk
Azure-distribution har flera lager av isolering av nätverk. Följande diagram visar olika lager i Azure ger kunderna isolering av nätverk. Dessa lager är både inbyggd i själva Azure-plattformen och kunddefinierad funktioner. Inkommande från Internet, Azure DDoS ger isolering mot storskaliga attacker mot Azure. Nästa lager av isolering är kunddefinierad offentliga IP-adresser (slutpunkter), som används för att avgöra vilken trafik kan passera Molntjänsten till det virtuella nätverket. Intern Azure virtuell nätverksisolering säkerställer fullständig isolering från alla andra nätverk och att det bara flödar trafiken genom Användarkonfigurerad sökvägar och metoder. Dessa sökvägar och metoder är nästa lager där NSG: er, UDR och virtuella nätverksenheter kan användas för att skapa isoleringsgränser för att skydda programdistributioner i skyddat nätverk.

![Isolering av nätverk](./media/azure-isolation/azure-isolation-fig13.png)

**Trafikisolering:** En [virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) är gränsen för isolering av trafik på Azure-plattformen. Virtuella datorer (VM) i ett virtuellt nätverk kan inte kommunicera direkt till virtuella datorer i ett annat virtuellt nätverk, även om båda virtuella nätverken har skapats av samma kund. Isolering är en viktig egenskap som ser till kundens virtuella datorer och kommunikation förblir privata inom ett virtuellt nätverk.

[Undernät](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#subnets) erbjuder ett extra lager av isolering med i virtuella nätverk som baseras på IP-intervall. IP-adresser i det virtuella nätverket, du kan dela upp ett virtuellt nätverk i flera undernät av organisations- och säkerhetsskäl. VM:ar och PaaS-rollinstanser som distribuerats till undernät (samma eller olika) inom ett VNet, kan kommunicera med varandra utan övrig konfiguration. Du kan också konfigurera [nätverkssäkerhetsgrupp (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#network-security-groups-nsg) att tillåta eller neka nätverkstrafik till en VM-instans som är baserat på regler som konfigurerats i åtkomstkontrollistan (ACL) för NSG. NSG:er kan antingen associeras med undernät eller individuella VM-instanser inom det undernätet. När en NSG är associerad med ett undernät, tillämpas ACL-reglerna på alla VM-instanser i det undernätet.

## <a name="next-steps"></a>Nästa steg

- [Isolering av nätverk som alternativ för virtuella datorer i Windows Azure-nätverk](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)

Detta inkluderar klassiska frontend och backend-scenariot där datorer i ett visst backend-nätverk eller undernätverk kan endast vissa klienter eller andra datorer att ansluta till en viss slutpunkt som baseras på en godkänd lista över IP-adresser.

- [Compute-isolering](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure tillhandahåller en olika molnbaserad databehandling tjänster som omfattar ett brett urval av beräkningsinstanser och tjänster som kan skalas upp och ned automatiskt för att uppfylla behoven i ditt program eller enterprise.

- [Storage-isolering](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure separerar kund VM-baserad beräkning från storage. Den här separationen kan beräkning och lagring skalas oberoende av varandra, vilket gör det enklare att ge flera innehavare och isolering. Därför kan körs Azure Storage på separata maskinvara med ingen nätverksanslutning till Azure Compute utom logiskt. Alla förfrågningar som körs via HTTP eller HTTPS baserat på kundens val.

