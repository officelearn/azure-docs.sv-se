---
title: Isolera i det offentliga Azure-molnet | Microsoft Docs
description: Läs mer om molnbaserad databearbetning tjänster som omfattar ett brett urval av compute-instanser och tjänster som kan skalas upp och ned automatiskt så att den passar ditt program-eller enterprise.
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
ms.openlocfilehash: 353762f33da8e5d48f6b70df3b790287eeab7ff9
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="isolation-in-the-azure-public-cloud"></a>Isolera i det offentliga Azure-molnet
##  <a name="introduction"></a>Introduktion
### <a name="overview"></a>Översikt
För att hjälpa Azure aktuella och potentiella kunder förstå och använda olika säkerhetsrelaterade funktioner tillgängliga i och omgivande Azure-plattformen Microsoft har utvecklat en serie faktablad, säkerhet översikter, bästa praxis och Checklistor.
Avsnitten intervallet vad gäller bredd och djup och uppdateras regelbundet. Det här dokumentet är en del av serien som sammanfattas i följande avsnitt som abstrakt.

### <a name="azure-platform"></a>Azure-plattformen
Azure är en öppen och flexibla molntjänstplattform som stöder bredaste valet av operativsystem, programmeringsspråk, ramverk, verktyg, databaser, och enheter. Du kan till exempel:
- Kör Linux behållare med Docker-integration.
- Skapa appar med JavaScript, Python, .NET, PHP, Java och Node.js; och
- Build-servrar för iOS, Android och Windows enheter.

Microsoft Azure stöder att samma tekniker miljontals utvecklare och IT-proffs redan förlitar sig på och litar på.

När du bygger på, eller migrera IT tillgångar, en offentlig molntjänstleverantör, måste den organisationens förmåga att skydda dina program och data med tjänster och de kontroller som de tillhandahåller för att hantera säkerheten för din molnbaserade tillgångar.

Allt i Azures infrastruktur, från anläggning till tillämpningar, är utformat för att fungera som värd för miljoner kunder samtidigt, och den tillhandahåller en säker grund som företaget kan använda sig av för att möta de interna säkerhetsbehoven. Dessutom erbjuder Azure dig en mängd olika konfigurerbara säkerhetsalternativ samt möjligheten att kontrollera dem, så att du kan anpassa säkerheten för att uppfylla de specifika behoven hos dina distributioner. Det här dokumentet hjälper dig att möta dessa krav.

### <a name="abstract"></a>Abstrakt

Microsoft Azure kan du köra program och virtuella maskiner (VMs) på delad fysisk infrastruktur. En av särskilda ekonomiska motiveringen till program som körs i en molnmiljö är möjligheten att distribuera kostnaderna för delade resurser mellan flera kunder. Det här tillvägagångssättet för flera innehavare ökar effektiviteten genom multiplexering resurser mellan olika kunder låg kostnad. Tyvärr introducerar även risken för delning av fysiska servrar och andra infrastrukturresurser för för att köra känsliga program och virtuella datorer som kan höra till en skadlig och potentiellt skadliga användare.

Den här artikeln beskrivs hur Microsoft Azure tillhandahåller isolering mot skadliga såväl som icke-skadliga användare och fungerar som en guide för att bygga om molnlösningar genom att erbjuda olika alternativ för isolering arkitekter. Faktabladet fokuserar på tekniken av Azure-plattformen och kunden vända säkerhetskontroller och försöker inte adressen SLA: er, prissättning modeller och DevOps överväganden.

## <a name="tenant-level-isolation"></a>Nivån Klientisolering
En av de främsta fördelarna med cloud computing är konceptet för en delad infrastruktur som är vanliga i ett stort antal kunder samtidigt, vilket leder till stordriftsfördelar. Detta kallas för flera innehavare. Microsoft arbetar kontinuerligt för att se till att arkitekturen med flera innehavare för Microsoft Cloud Azure stöder säkerhet, sekretess, sekretess, integritet och tillgänglighet standarder.

På en arbetsplats i molnet kan en klientorganisation definieras som en klient eller organisation som äger och hanterar en specifik instans av molntjänsten. Med identitetsplattformen som tillhandahålls av Microsoft Azure är en klient helt enkelt en dedikerad instans av Azure Active Directory (AD Azure) som din organisation tilldelas och äger när den registrerar sig för en Microsoft-molntjänst.

Varje Azure AD-katalog är separat och åtskild från andra Azure AD-kataloger. Precis som ett företags kontorsbyggnad är en säker resurs som är specifik för din organisation har även Azure AD-katalogen utformats för att vara en säker tillgång för exklusiv användning av din organisation. Azure AD-arkitekturen håller isär kunddata och identitetsinformation. Det innebär att användare och administratörer av en Azure AD-katalog inte oavsiktligt eller illvilligt kan komma åt data i en annan katalog.

### <a name="azure-tenancy"></a>Azure innehavare
Azure innehavare (Azure-prenumeration) refererar till en relation med ”kund/billing” och en unik [klient](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) i [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Nivån klientisolering i Microsoft Azure uppnås med hjälp av Azure Active Directory och [rollbaserad kontroller](https://docs.microsoft.com/azure/role-based-access-control/overview) erbjuds av den. Varje Azure-prenumeration är associerad med en katalog i Azure Active Directory (AD).

Användare, grupper och program från katalogen kan hantera resurser i Azure-prenumerationen. Du kan tilldela behörigheter med Azure-portalen, Azure kommandoradsverktyg och Azure Management-API: er. En Azure AD-klient är logiskt isolerade med säkerhetsgränser så att ingen kund kan komma åt eller kompromettera samtidigt hyresgäster medvetet eller av misstag. Azure AD som körs på ”bare metal”-servrar isolerade i ett separat nätverkssegment där värdnivå paketfilter och Windows-brandväggen blockerar oönskade anslutningar och trafik.

- Åtkomst till data i Azure AD kräver användarautentisering via en säkerhetstokentjänst (STS). Information om användarens existens aktiverat läge och rollen används av systemets tillstånd för att avgöra om den begärda åtkomsten till mål-klienten har behörighet för den här användaren i den här sessionen.

![Azure innehavare](./media/azure-isolation/azure-isolation-fig1.png)


- Klienter är diskreta behållare och det finns ingen relation mellan dessa.

- Ingen åtkomst över innehavare om innehavaradministration beviljar via federation eller etablering användarkonton från andra klienter.

- Fysisk åtkomst till servrar som ingår i Azure AD-tjänsten och direkt åtkomst till Azure AD backend-system är begränsad.

- Azure AD-användare har ingen åtkomst till fysiska tillgångar eller platser och är därför inte möjligt att kringgå de logiska RBAC principkontroller anges efter.

För diagnostik- och behov av underhåll, krävs en funktionsmodell som använder en just-in-time-privilegium höjning system och användas. Azure AD Privileged Identity Management (PIM) introducerar konceptet för en berättigad-administratör. [Berättigad administratörer](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) ska vara användare som behöver privilegierad åtkomst till då och då, men inte varje dag. Rollen är inaktiv tills användaren behöver åtkomst, och sedan de slutföra aktiveringsprocessen och bli administratör active för en förinställd tidsperiod.

![Azure AD Privileged Identity Management](./media/azure-isolation/azure-isolation-fig2.png)

Azure Active Directory är värd för varje innehavare i sin egen skyddade behållaren med principer och behörigheter till och i behållare enbart ägs och hanteras av klienten.

Begreppet klient behållare är djupt ingrained i katalogtjänsten på alla skikt från portaler ända till beständig lagring.

Även om metadata från flera Azure Active Directory-klienter finns på samma fysiska disk, finns det ingen relation mellan behållare än vad som definieras av katalogtjänsten, som i sin tur styrs av klientorganisationsadministratören.

### <a name="azure-role-based-access-control-rbac"></a>Azure rollbaserad åtkomstkontroll (RBAC)
[Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) hjälper dig att dela olika komponenter som är tillgängliga i en Azure-prenumeration genom att tillhandahålla detaljerad åtkomsthantering för Azure. Azure RBAC gör att du kan särskilja uppgifter i din organisation och bevilja åtkomst baserat på vad användarna behöver för att utföra sitt arbete. Istället för att ge alla obegränsad behörighet i Azure-prenumeration eller resurser, kan du tillåta endast vissa åtgärder.

Azure RBAC har tre grundläggande roller som gäller för alla typer av resurser:

- **Ägare** har fullständig åtkomst till alla resurser som bland annat att delegera åtkomst till andra.

- **Deltagare** kan skapa och hantera alla typer av Azure-resurser, men det går inte att bevilja åtkomst till andra.

- **Läsaren** kan visa befintliga Azure-resurser.

![Azures rollbaserade åtkomstkontroll](./media/azure-isolation/azure-isolation-fig3.png)

Resten av rollerna i Azure RBAC kan hanteringen av specifika Azure-resurser. Till exempel tillåter virtuella deltagarrollen användaren att skapa och hantera virtuella datorer. Det ger dem åtkomst till Azure-nätverk eller undernät som den virtuella datorn ansluter till.

[Inbyggda RBAC-roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) lista roller i Azure. Det anger åtgärder och scope som varje inbyggd roll som ger användare. Om du behöver för att definiera egna roller för ännu mer kontroll, se hur du skapar [anpassade roller i Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Vissa funktioner för Azure Active Directory är:
- Azure AD aktiverar enkel inloggning för SaaS-program, oavsett var de finns. Vissa program federeras med Azure AD och andra använder enkel inloggning med lösenord. Federerade program kan också använda användaretablering och [lösenordsvalv](https://www.techopedia.com/definition/31415/password-vault).

- Åtkomst till data i [Azure Storage](https://azure.microsoft.com/services/storage/) styrs via autentisering. Varje lagringskonto har en primär nyckel ([lagringskontonyckel](https://docs.microsoft.com/azure/storage/storage-create-storage-account), eller SAK) och en sekundär hemlig nyckel (signatur för delad åtkomst, eller SAS).

- Azure AD innehåller identitet som en tjänst via federation med hjälp av [Active Directory Federation Services](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs), synkronisering och replikering med lokala kataloger.

- [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) är tjänsten multifaktorautentisering som kräver att användare att verifiera inloggningar med hjälp av en mobilapp, telefonsamtal eller SMS. Den kan användas med Azure AD för att säkra lokala resurser med Azure Multi-Factor Authentication-servern, och anpassade program och kataloger med hjälp av SDK.

- [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) kan du ansluta virtuella datorer i Azure Active Directory-domän utan att distribuera domänkontrollanter. Du kan logga in på dessa virtuella datorer med företagets Active Directory-autentiseringsuppgifter och administrera domänanslutna virtuella datorer med hjälp av Grupprincip för att genomdriva baslinjer för säkerhet på alla dina virtuella Azure-datorer.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) ger en hög tillgänglighet globala identity management-tjänst för konsumentinriktade program som kan skalas till flera hundra miljoner identiteter. Den kan integreras över mobila plattformar och webbaserade plattformar. Dina användare kan logga in på alla program via anpassningsbara upplevelser genom att använda sina befintliga sociala konton eller genom att skapa autentiseringsuppgifter.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Isolering från Microsoft administratörer & Databorttagning
Microsoft tar starkt åtgärder för att skydda dina data från oönskad åtkomst eller använda av obehöriga. Dessa operativa processer och kontroller backas upp av den [Online Services-villkoren](http://aka.ms/Online-Services-Terms), som erbjuder avtal åtaganden som styr åtkomsten till dina data.

-   Microsoft-tekniker har inte åtkomst till dina data i molnet. I stället ges de tillgång under hantering av tillsyn, bara när det behövs. Att åtkomsten är noggrant kontrolleras loggas och återkallas när den inte längre behövs.

-   Microsoft kan anlita andra företag att tillhandahålla begränsade tjänster i dess ställe. Underleverantörer kan komma åt kundens data endast för att leverera tjänsterna som vi har anlitat dem att tillhandahålla och de är förbjudna att använda informationen i något annat syfte. Dessutom är de enligt avtal bundna konfidentiell information för våra kunder.

Företagstjänster med granskad certifieringar, till exempel ISO/IEC 27001 kontrolleras regelbundet av Microsoft och auktoriserad audit företag, som utför exempel granskningar intyga den åtkomsten endast för legitima affärsändamål. Du kan alltid komma åt dina egna kunddata när som helst och av någon anledning.

Om du tar bort alla data som Microsoft Azure tar bort data, inklusive eventuella cachelagrade eller säkerhetskopiering kopior. Borttagningen sker för omfattas tjänster inom 90 dagar efter slutet av kvarhållningsperioden. (I omfånget tjänster definieras i avsnittet databearbetning villkoren i vår [Online Services-villkoren](http://aka.ms/Online-Services-Terms).)

Om en diskenhet som används för lagring av drabbas av ett maskinvarufel, är det på ett säkert sätt [raderas eller förstörs](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data) innan Microsoft skickar tillbaka det till tillverkare för ersättning eller reparation. Skrivs över data på enheten för att säkerställa att data inte kan återställas på något sätt.

## <a name="compute-isolation"></a>Compute-isolering
Microsoft Azure tillhandahåller olika molnbaserade databehandling tjänster som omfattar ett brett urval av compute-instanser och tjänster som kan skalas upp och ned automatiskt så att den passar ditt program-eller enterprise. Dessa beräkningar instans och tjänsten erbjuder isolering på flera nivåer för att skydda data utan att kompromissa flexibilitet i konfigurationen som kunder kräver.

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Hyper-V & rot OS-isolering mellan rot VM & gäst-VM
Azures beräkningsplattform baseras på datorn virtualisering, vilket innebär att alla kunden koden körs i en virtuell dator för Hyper-V. Det finns en Hypervisor som körs direkt via maskinvaran och delar en nod i variabelnummer av gäst virtuella maskiner (VMs) på varje nod i Azure (eller slutpunkter i nätverket).


![Hyper-V & rot OS-isolering mellan rot VM & gäst-VM](./media/azure-isolation/azure-isolation-fig4.jpg)


Varje nod har också en särskild rot VM, som körs på värd-OS. En kritisk gräns är isolering för rot-VM från gäst-VM och Gäst-VM från varandra, hanteras av hypervisor-programmet och roten OS. Länkning av hypervisor-program/root OS utnyttjar Microsofts åren av operativsystemet säkerhet erfarenhet och nyare learning från Microsoft Hyper-V att ge starkt isolering av gäst-VM.

Azure-plattformen använder en virtualiserad miljö. Användarinstanser fungerar som fristående virtuella datorer som inte har åtkomst till en fysisk värd-server och denna isolering tillämpas med behörighetsnivåer för fysisk processor (ring-0/ring-3).

Ring 0 är den mest privilegierade och 3 är den minst privilegierade. Gästoperativsystemet som körs i en mindre privilegierad Ring 1 och program körs i den lägsta möjliga Ring 3. Den här virtualiseringen av fysiska resurser leder till en tydlig uppdelning mellan gästoperativsystem och hypervisor, vilket resulterar i ytterligare säkerhetsuppdelning mellan dessa två.

Azure hypervisor-programmet fungerar som en micro kernel och överför alla maskinvara förfrågningar från virtuella gästdatorer till värden för bearbetning med hjälp av ett delat minne gränssnitt som heter VMBus. Detta förhindrar att användare erhåller rååtkomsten läs/skriv/kör till systemet och minskar risken med att dela systemresurser.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Avancerade VM placeringsalgoritmen och skydd mot attacker, kanal på klientsidan
Mellan VM angrepp består av två steg: att placera en angriparen-kontrollerade virtuell dator på samma värddator som en drabbade virtuella datorer och brott mot isoleringsgräns att stjäla känslig drabbade information eller påverka prestandan för greed eller vandalism. Microsoft Azure ger skydd på båda dessa steg med hjälp av en avancerad VM placeringsalgoritmen och skydd mot alla kända sida kanal attacker, inklusive störningar granne virtuella datorer.

### <a name="the-azure-fabric-controller"></a>Azure-Infrastrukturkontrollanten
Azure-Infrastrukturkontrollanten ansvarar för att fördela infrastrukturresurser för att arbetsbelastningar-klient och den hanterar enkelriktad kommunikation mellan värden och virtuella datorer. VM att placera algoritmen för Azure-infrastrukturkontrollanten är mycket avancerade och nästan omöjligt att förutsäga som fysisk värdnivå.

![Azure-Infrastrukturkontrollanten](./media/azure-isolation/azure-isolation-fig5.png)

Hypervisor-programmet i Azure tvingar minne och processen åtskillnad mellan virtuella datorer och den dirigerar nätverkstrafik på ett säkert sätt till innehavare för gäst-OS. Detta eliminerar möjligheten att och sidan kanal attack på VM-nivå.

I Azure, roten VM är speciell: ett strikt operativsystem kallas roten OS som är värd för en fabric-agent (MI) körs. FAs används i sin tur att hantera gästagenter (GA) gästen OS på kundens virtuella datorer. FAs också hantera lagringsnoder.

Insamling av Azure hypervisor rot OS/MI eller kundens virtuella datorer/GAs omfattar en beräkningsnod. FAs hanteras av en fabric-controller (FC) som finns utanför beräkning och lagring noder (beräkning och lagring klustren hanteras av separata FCs). Om en kund uppdaterar sina programkonfigurationsfilen medan den körs, i FC som kommunicerar med MI som sedan kontaktar GAs, som meddelar tillämpningen av konfigurationsändringen. I händelse av ett maskinvarufel i FC automatiskt hitta tillgänglig maskinvara och starta om den virtuella datorn det.

![Azure-Infrastrukturkontrollanten](./media/azure-isolation/azure-isolation-fig6.jpg)

Kommunikation från en Fabric-domänkontrollant till en agent är enkelriktad. Agenten implementerar en SSL-skyddad tjänst som bara svarar på förfrågningar från styrenheten. Det går inte att initiera anslutningar till styrenheten eller andra Privilegierade interna noder. FC behandlar alla svar som om de vore inte är betrodd.


![Fabric-styrenhet](./media/azure-isolation/azure-isolation-fig7.png)

Isolering sträcker sig från den Virtuella roten från gäst-VM och Gäst-VM från varandra. Compute-noderna är också isolerade från lagringsnoder för bättre skydd.


Hypervisor-programmet och värden OS ange nätverkspaket - filter för att garantera att den inte är betrodd virtuella datorer inte kan skapa falska trafik eller ta emot trafik som inte är adresserad till dem, dirigera trafik till skyddade infrastruktur slutpunkter eller skicka och ta emot olämpliga broadcast-trafik.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Ytterligare regler som konfigurerats av Fabric-styrenhet agenten att isolera VM
Som standard blockeras all trafik när en virtuell dator skapas och fabric controller agenten konfigurerar sedan paketfilter för att lägga till regler och undantag som tillåter auktoriserade trafik.

Det finns två typer av regler som är programmerade:

-   **Datorn konfiguration eller infrastruktur regler:** som standard all kommunikation är blockerad. Det finns undantag så att en virtuell dator skickar och tar emot DHCP och DNS-trafik. Virtuella datorer kan skicka trafik till ”offentliga” internet och skicka trafik till andra virtuella datorer i samma virtuella Azure-nätverk och Aktiveringsservern OS. De virtuella datorerna lista över tillåtna utgående mål innehåller inte Azure router undernät, hantering av Azure och andra Microsoft-egenskaper.

-   **Konfigurationsfilen för rollen:** detta definierar den inkommande åtkomstkontrollistor (ACL) baserat på klientens modell.

### <a name="vlan-isolation"></a>VLAN-isolering
Det finns tre VLAN i varje kluster:

![VLAN-isolering](./media/azure-isolation/azure-isolation-fig8.jpg)


-   Det huvudsakliga VLANET – anslutningar obetrodda kunden noder

-   FC-VLAN – innehåller betrodda FCs och ge support för system

-   Enheten VLAN – innehåller betrott nätverk och andra infrastruktur-enheter

Kommunikation tillåts från FC VLAN huvudsakliga VLAN, men kan inte initieras från det huvudsakliga VLANET till FC VLAN. Kommunikationen är också blockeras från det huvudsakliga VLANET till enheten VLAN. Detta säkerställer att även om en nod som kör Kundkod äventyras, kan den angrepp noder i FC eller VLAN-enhet.

## <a name="storage-isolation"></a>Isolering av lagring
### <a name="logical-isolation-between-compute-and-storage"></a>Logisk isolering mellan beräkning och lagring
Som en del av dess grundläggande design separerar Microsoft Azure VM-baserad beräkning från lagringsplatsen. Den här separationen kan beräkning och lagring kan skalas för sig., vilket gör det enklare att tillhandahålla flera innehavare och isolering.

Därför kör Azure Storage på separata maskinvara med ingen nätverksanslutning till Azure Compute utom logiskt. [Detta](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf) innebär att när en virtuell disk skapas inte tilldelat diskutrymme för den totala kapaciteten. I stället skapas en tabell som mappar adresser på den virtuella disken till områden på den fysiska disken och tabellen är tom från början. **Första gången en kund skriver data på den virtuella disken allokeras utrymmet på den fysiska disken och en pekare till den placeras i tabellen.**
### <a name="isolation-using-storage-access-control"></a>Åtkomstkontroll för isolering med lagring
**Åtkomstkontroll i Azure Storage** har en enkel modellen för åtkomstkontroll. Varje Azure-prenumeration kan skapa en eller flera Lagringskonton. Varje Lagringskonto har en hemlig nyckel som används för att styra åtkomsten till alla data i detta Lagringskonto.

![Åtkomstkontroll för isolering med lagring](./media/azure-isolation/azure-isolation-fig9.png)

**Åtkomst till Azure Storage-data (inklusive tabeller)** kan styras via en [SAS (signatur för delad åtkomst)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) token, vilket ger begränsad åtkomst. SAS har skapats via en fråga mall (URL) som signerats med den [SAK (Lagringskontonyckel)](https://msdn.microsoft.com/library/azure/ee460785.aspx). Som [signerade URL](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) kan ges till en annan process (som delegerats), vilket kan sedan fylla i information om frågan och begära storage-tjänst. En SAS kan du bevilja tidsbaserade åtkomst till klienter utan att avslöja storage-konto hemlig nyckel.

SAS innebär att vi ger en klient begränsade behörigheter till objekt i vår lagringskonto för en angiven tidsperiod och med en angiven uppsättning behörigheter. Vi kan bevilja dessa begränsade behörigheter utan att behöva dela åtkomstnycklarna för ditt konto.

### <a name="ip-level-storage-isolation"></a>IP-nivån lagring isolering
Du kan fastställa brandväggar och definiera ett intervall med IP-adresser för dina betrodda klienter. Med ett IP-adressintervall endast klienter som har en IP-adress inom det angivna intervallet kan ansluta till [Azure Storage](https://docs.microsoft.com/azure/storage/storage-security-guide).

IP-storage-data kan skyddas från obehöriga användare via ett nätverk mekanism som används för att allokera en dedikerad eller dedikerade tunnel av trafik till IP-lagring.

### <a name="encryption"></a>Kryptering
Azure erbjuder följande typer av kryptering för att skydda data:
-   Kryptering under överföring

-   Vilande kryptering

#### <a name="encryption-in-transit"></a>Kryptering under överföring
Kryptering under överföring är en mekanism för att skydda data när de skickas över nätverk. Du kan skydda data med hjälp av med Azure Storage:

-   [Kryptering på transportnivå](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), till exempel HTTPS när du överför data till eller från Azure Storage.

-   [Tråd kryptering](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), till exempel SMB 3.0-kryptering för Azure-filresurser.

-   [Kryptering på klientsidan](https://docs.microsoft.com/azure/storage/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), att kryptera data innan det överförs till lagringskontot och att dekryptera data efter överföringen slut på minne.

#### <a name="encryption-at-rest"></a>Kryptering i vila
I många organisationer [datakryptering i viloläge](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) är ett obligatoriskt steg mot data sekretess-, efterlevnads- och suveränitet. Det finns tre Azure-funktioner som tillhandahåller kryptering av data som är ”i vila”:

-   [Lagringstjänstens kryptering](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-at-rest) kan du begära att lagringstjänsten automatiskt kryptera data när du skriver till Azure Storage.

-   [Kryptering på klientsidan](https://docs.microsoft.com/azure/storage/storage-security-guide#client-side-encryption) innehåller också en funktion till kryptering i vila.

-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) kan du kryptera OS-diskar och datadiskar som används av en virtuell IaaS-dator.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) för virtuella datorer (VM) hjälper dig att organisationens säkerhets- och efterlevnadskrav genom att kryptera dina Virtuella diskar (inklusive start- och datadiskar) med nycklar och principer som du styr i [Azure nyckel Valvet](https://azure.microsoft.com/services/key-vault/).

Disk Encryption lösningen för Windows är baserad på [Microsoft BitLocker-diskkryptering](https://technet.microsoft.com/library/cc732774.aspx), och Linux-lösning baserad på [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Lösningen stöder följande scenarion för virtuella IaaS-datorer när de är aktiverade i Microsoft Azure:
-   Integrering med Azure Key Vault

-   Standardnivån VMs: A, D, DS, G, GS och så vidare, serien IaaS-VM

-   Aktivera kryptering på Windows och Linux virtuella IaaS-datorer

-   Om du inaktiverar kryptering på Operativsystemet och enheter för Windows IaaS-VM

-   Om du inaktiverar kryptering på dataenheter för Linux virtuella IaaS-datorer

-   Aktivera kryptering på virtuella IaaS-datorer som kör windowsklient-OS

-   Aktivera kryptering på volymer med monteringssökvägar

-   Aktivera kryptering på den virtuella Linux-datorer som är konfigurerade med disk-striping (RAID) med hjälp av [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   Aktivera kryptering på den virtuella Linux-datorer med hjälp av [LVM (logisk volymhanterare)](https://msdn.microsoft.com/library/windows/desktop/bb540532) för datadiskar

-   Aktivera kryptering på virtuella Windows-datorer som har konfigurerats med hjälp av lagringsutrymmen

-   Alla offentliga Azure-regioner som stöds

Lösningen stöder inte följande scenarier, funktioner och tekniken i versionen:

-   Grundnivån IaaS-VM

-   Om du inaktiverar kryptering på en OS-enhet för Linux virtuella IaaS-datorer

-   Virtuella IaaS-datorer som skapas med hjälp av klassiska metod för skapande av virtuell dator

-   Integrering med din lokala nyckelhanteringstjänst

-   Azure Files (delade filsystem), Network File System (NFS), dynamiska volymer och virtuella Windows-datorer som är konfigurerade med programvarubaserad RAID-system

## <a name="sql-azure-database-isolation"></a>SQL Azure Database isolering
SQL Database är en relationsdatabastjänst i Microsoft Cloud som är baserad på den marknadsledande Microsoft SQL Server-motorn och som kan hantera verksamhetskritiska arbetsbelastningar. SQL-databas erbjuder förutsägbar data isolering på kontot, geography / region och baserade på nätverk – allt detta med nästan obefintlig administration.

### <a name="sql-azure-application-model"></a>SQL Azure programmodell

[Microsoft SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started) databasen är en molnbaserad relationsdatabstjänst bygger på SQL Server-teknik. Det ger en hög tillgänglighet, skalbar, flera innehavare databastjänst hos Microsoft i molnet.

Ett program ur SQL Azure tillhandahåller följande hierarki: varje nivå har en-till-många inneslutning av nivåer nedan.

![SQL Azure programmodell](./media/azure-isolation/azure-isolation-fig10.png)

Konto och prenumeration är Microsoft Azure-plattformen begrepp att associera fakturering och hantering.

Logiska servrar och databaser är SQL Azure-specifika begrepp och hanteras med hjälp av SQL Azure angivna OData- och TSQL-gränssnitt eller via SQL Azure-portalen som integrerat i Azure-portalen.

SQL Azure-servrarna är inte fysiska eller Virtuella instanser, i stället de är samlingar av databaser, delning och hantering av säkerhetsprinciper som lagras i så kallade ”logiska master” databas.

![SQL Azure](./media/azure-isolation/azure-isolation-fig11.png)

Logiska master-databaser är:

-   SQL-inloggningar som används för att ansluta till servern

-   Brandväggsregler

Fakturerings- och användning-relaterad information för SQL Azure databaser från samma logiska server inte är säkert att vara på samma fysiska instansen i SQL Azure-klustret i stället program måste ange namnet på mål-databasen när du ansluter.

Ur customer skapas en logisk server i en grafisk geo region medan det faktiska skapandet av servern sker i ett kluster i regionen.

### <a name="isolation-through-network-topology"></a>Isolering via nätverkets topologi

När en logisk server skapas och DNS-namnet registreras i DNS-namn pekar på den så kallade ”Gateway” adressen i specifika datacenter där servern gjordes.

Vi har en samling tillståndslös gateway tjänster bakom VIP (virtuell IP-adress). I allmänhet delta gateways när samordning behövs mellan flera datakällor (master-databasen, användardatabas osv.). Gateway-tjänster implementera följande:
-   **Proxy för TDS-anslutningen.** Detta inkluderar att hitta användardatabas i backend-klustret, implementera inloggningssekvensen och vidarebefordran TDS-paket till serverdelen och tillbaka.

-   **Databashantering av.** Detta inkluderar att implementera en samling av arbetsflöden för CREATE/ALTER/DROP databasoperationer. Databasåtgärderna kan anropas av kontroll TDS-paket eller explicit OData APIs.

-   CREATE/ALTER/DROP inloggning åtgärder

-   Logisk server hanteringsåtgärder via OData-API

![Isolering via nätverkets topologi](./media/azure-isolation/azure-isolation-fig12.png)

Nivån bakom gateway kallas ”backend”. Detta är där alla data som lagras på ett sätt som hög tillgänglighet. Varje datadel sägs tillhör en ”partition” eller ”redundansenhet”, var och en av dem har minst tre repliker. Replikerna lagras och replikeras av SQL Server-motorn och hanteras av ett system för växling vid fel kallas ofta för ”infrastruktur”.

I allmänhet kommunicerar backend-systemet inte utgående till andra system som en säkerhetsåtgärd. Detta är reserverat på datorer i skiktet frontend (gateway). Skiktdatorer gateway har begränsad behörighet på backend-datorer för att minimera risken för angrepp som en mekanism för skydd på djupet.

### <a name="isolation-by-machine-function-and-access"></a>Isolering av datorn funktion och åtkomst
SQL Azure (består av tjänster som körs på en annan dator funktioner. SQL Azure är uppdelat i ”backend” moln databasen och ”frontend” (Gateway och hantering) miljöer med den allmänna principen trafik endast växla till backend- och inte ut. Frontend-miljö kan kommunicera med omvärlden av andra tjänster och i allmänhet endast har begränsad behörighet i serverdelen (tillräckligt för att anropa startpunkter som behövs för att anropa).

## <a name="networking-isolation"></a>Isolering av nätverk
Azure-distribution har flera lager för isolering av nätverk. Följande diagram visar olika lager i nätverksisolering som Azure tillhandahåller till kunder. Dessa lager är både intern i själva Azure-plattformen och kunddefinierade funktioner. Inkommande från Internet, Azure DDoS ger isolering mot storskaliga attacker mot Azure. Nästa säkerhetslager isolering är kunddefinierade offentliga IP-adresser (slutpunkter) som används för att avgöra vilken trafik kan passera Molntjänsten till det virtuella nätverket. Interna Azure virtuell nätverksisolering garanterar fullständig isolering från andra nätverk och att trafiken endast flödar via konfigurerade användaren sökvägar och metoder. Dessa sökvägar och metoder är nästa säkerhetslager där NSG: er, UDR och virtuella nätverksenheter kan användas för att skapa isoleringsgränser för att skydda application-distributioner i det skyddade nätverket.

![Isolering av nätverk](./media/azure-isolation/azure-isolation-fig13.png)

**Trafik isolering:** A [virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) är isoleringsgräns trafik på Azure-plattformen. Virtuella datorer (VM) i ett virtuellt nätverk kan inte kommunicera direkt med virtuella datorer i ett annat virtuellt nätverk, även om båda virtuella nätverken skapas av samma kund. Isolering är en kritisk egenskap som garanterar kundens virtuella datorer och kommunikation förblir privat inom ett virtuellt nätverk.

[Undernät](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#subnets) ger ett ytterligare lager med isolering med i det virtuella nätverket baserat på IP-intervall. IP-adresser i det virtuella nätverket, du kan dela ett virtuellt nätverk i flera undernät av organisations- och säkerhetsskäl. VM:ar och PaaS-rollinstanser som distribuerats till undernät (samma eller olika) inom ett VNet, kan kommunicera med varandra utan övrig konfiguration. Du kan också konfigurera [nätverkssäkerhetsgrupp (NSG: er)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#network-security-groups-nsg) att tillåta eller neka trafik till en VM-instans som är baserat på konfigurerad i åtkomstkontrollistan (ACL) för NSG-regler. NSG:er kan antingen associeras med undernät eller individuella VM-instanser inom det undernätet. När en NSG är associerad med ett undernät, tillämpas ACL-reglerna på alla VM-instanser i det undernätet.

## <a name="next-steps"></a>Nästa steg

- [Isolering av nätverk som alternativ för datorer i Windows Azure-nätverk](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)

Detta inkluderar det klassiska frontend och backend-scenario där datorer i ett visst backend-nätverk eller undernätverk kanske bara tillåter vissa klienter eller andra datorer att ansluta till en viss slutpunkt baserat på en godkänd lista över IP-adresser.

- [Compute-isolering](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure tillhandahåller en olika molnbaserade databehandling tjänster som omfattar ett brett urval av compute-instanser och tjänster som kan skalas upp och ned automatiskt så att den passar ditt program-eller enterprise.

- [Isolering av lagring](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure separerar kunden VM-baserad beräkning från lagringsplatsen. Den här separationen kan beräkning och lagring kan skalas för sig., vilket gör det enklare att tillhandahålla flera innehavare och isolering. Därför kör Azure Storage på separata maskinvara med ingen nätverksanslutning till Azure Compute utom logiskt. Alla förfrågningar kör via HTTP eller HTTPS enligt kundens val.

