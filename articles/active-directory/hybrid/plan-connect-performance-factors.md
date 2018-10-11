---
title: Faktorer som påverkar prestandan för Azure AD Connect
description: Det här dokumentet beskriver hur olika faktorer påverkar Azure AD Connect etablering motorn. Dessa faktorer hjälper organisationer att planera sina Azure AD Connect-distribution för att kontrollera att den uppfyller deras krav för katalogsynkronisering.
services: active-directory
author: billmath
manager: mtillman
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.openlocfilehash: 7cf0e2b211f9d34f6d8f4fe89a230d8a2e97512a
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069021"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Faktorer som påverkar prestandan för Azure AD Connect

Azure AD Connect synkroniserar Active Directory till Azure AD. Den här servern är en kritisk komponent för att flytta dina användaridentiteter till molnet. De främsta faktorerna som påverkar prestanda för en Azure AD Connect är:

| **Designfaktor**| **Definition** |
|:-|-|
| Topologi| Distributionen av de slutpunkter och komponenterna i Azure AD Connect måste hantera i nätverket. |
| Skala| Antalet objekt som användare, grupper och organisationsenheter som ska hanteras av Azure AD Connect. |
| Maskinvara| Maskinvaran (fysisk eller virtuell) för Azure AD Connect och beroende prestanda kapaciteten för varje maskinvarukomponent inklusive processor, minne, nätverk och hårddiskkonfigurationen. |
| Konfiguration| Hur Azure AD Connect processer katalogerna och information. |
| Läsa in| Frekvens för objektet ändras. Belastningar som kan variera under en timme, dag eller vecka. Beroende på komponenten, kan du behöva utforma för hög belastning eller genomsnittlig belastning. |

Syftet med det här dokumentet är att beskriva prestandaöverväganden påverka prestanda för etablering motorn av Azure AD Connect. De andra komponenterna i Azure AD Connect, till exempel [Azure AD Connect health](how-to-connect-health-agent-install.md) och agenter inte som beskrivs här.

> [!IMPORTANT]
> Microsoft stöder inte ändring eller Azure AD Connect utöver de åtgärder som är formellt dokumenterade. Sådana åtgärder kan göra att Azure AD Connect-synkroniseringen hamnar i ett inkonsekvent tillstånd eller ett tillstånd som inte stöds. Därför kan Microsoft inte tillhandahålla teknisk support för sådana distributioner.

## <a name="azure-ad-connect-component-factors"></a>Azure AD Connect-komponenten faktorer

Följande diagram visar en övergripande arkitekturen för att etablera motor som ansluter till en enda skog, även om flera skogar stöds. Den här arkitekturen visar hur de olika komponenterna samverkar med varandra.

![AzureADConnentInternal](media/plan-connect-performance-factors/AzureADConnentInternal.png)

Etablering motorn ansluter till varje Active Directory-skog och till Azure AD. Processen med att läsa information från varje katalog kallas för Import. Exportera avser uppdaterar katalogerna från etablering-motorn. Synkronisera utvärderar reglerna för hur objekt som flödar i etablering motorn. För en grundligare genomgång av kan du referera till [Azure AD Connect-synkronisering: Förstå arkitekturen](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture).

Azure AD Connect använder följande mellanlagringsområden, regler och processer för att tillåta synkroniseringen från Active Directory till Azure AD:

* **Connector utrymme (CS)** -objekt från varje ansluten katalog (CD), de faktiska katalogerna mellanlagras här först innan de kan bearbetas av motorn för etablering av. Azure AD har sin egen CS och varje skog som du ansluter till har sin egen CS.
* **Metaversum (MV)** -objekt som måste synkroniseras är skapar här baserat på Synkroniseringsregler för. Objekt måste finnas i MV innan de kan fylla i objekt och attribut till andra anslutna katalogerna. Det finns bara en MV.
* **Synkronisera regler** -de bestämma vilka objekt ska skapas (planerat) eller ansluten (domänansluten) för objekt i MV. Sync-regler kan du också bestämma vilka attributvärden ska kopieras eller omvandlas till och från katalogerna.
* **Körningsprofiler** -samlar processen först kopiera objekt och deras attributvärden enligt reglerna för synkronisering mellan mellanlagringsområden och anslutna kataloger.

Det finns olika körning av profiler för att optimera prestanda för etablering motorn. De flesta organisationer använder standard-scheman och körningsprofiler för normal drift, men vissa organisationer kan behöva [ändra schemat](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) eller utlösa andra körningsprofiler att serva ovanliga situationer. Följande körningsprofiler är tillgängliga:

### <a name="initial-sync-profile"></a>Den inledande synkroniseringen profil

Profil för inledande synkronisering är processen med att läsa de anslutna katalogerna som en Active Directory-skog för första gången. Sedan sker en analys på alla poster i motorn synkroniseringsdatabasen. Den första cykeln skapar nya objekt i Azure AD och tar extra tid att slutföra om Active Directory-skogar är stora. Den första synkroniseringen omfattar följande steg:

1. Fullständig import på alla kopplingar
2. Fullständig synkronisering på alla kopplingar
3. Exportera på alla kopplingar

### <a name="delta-sync-profile"></a>Delta sync-profil

För att optimera synkroniseringen detta körningsprofil endast bearbeta ändringarna (skapar, tar bort och uppdaterar) för objekt i dina anslutna kataloger, sedan den senaste synkroniseringen. Som standard körs delta synkronisering profilen var 30: e minut. Organisationer bör sträva efter att den tid det tar att under 30 minuter för att kontrollera att Azure AD är uppdaterad. För att övervaka hälsotillståndet för Azure AD Connect, använda den [hälsoövervakning agenten](how-to-connect-health-sync.md) att se eventuella problem med processen. Delta synkronisering profilen innehåller följande steg:

1. Deltaimport på alla kopplingar
2. Deltasynkronisering på alla kopplingar
3. Exportera på alla kopplingar

En typisk företags organisation delta sync scenario är:

- ~ 1% av objekt tas bort
- ~ 1% av objekt skapas
- ~ 5% av objekt ändras

Din ändringstakt kan variera beroende på hur ofta organisationen uppdaterar användare i Active Directory. Högre förändringstakten kan exempelvis uppstå med säsongsberoende anställa och minskar användaren.

### <a name="full-sync-profile"></a>Fullständig synkronisering profil

En cykel för fullständig synkronisering krävs om du har gjort någon av följande konfigurationsändringar:



- Ökat omfånget för objekt eller attribut som ska importeras från anslutna kataloger. Till exempel när du lägger till en domän eller Organisationsenhet till import-omfång.
- Ändrat sync-regler. Till exempel när du skapar en ny regel för att fylla i en användares rubrik i Azure AD från extension_attribute3 i Active Directory. Den här uppdateringen kräver att etablering motorn ompröva alla befintliga användare att uppdatera sina titlar för att tillämpa ändringen framöver.

Följande åtgärder som ingår i en fullständig synkroniseringscykel:

1. Fullständig import på alla kopplingar
2. Fullständig/Deltasynkronisering på alla kopplingar
3. Exportera på alla kopplingar

> [!NOTE]
> Noggrann planering krävs när du gör massuppdateringar för många objekt i din Active Directory eller Azure AD. Massinläsning uppdateringarna kommer orsaka delta synkroniseringen tar längre tid när du importerar, eftersom många objekt har ändrats. Lång import kan inträffa även om Massuppdatering inte påverka synkroniseringen. Till exempel tilldela licenser till många användare i Azure AD gör en lång import cykel från Azure AD, men ska inte resultera i attributändringar i Active Directory.

### <a name="synchronization"></a>Synkronisering

Process-runtime synkronisering har följande konsekvenser för prestanda:

* Synkroniseringen har enkel threaded, vilket innebär att etablering motorn inte göra någon parallell bearbetning av körningsprofilerna anslutna kataloger, objekt eller attribut.
* Importeringen ökar linjärt med antalet objekt som synkroniseras. Till exempel om 10 000 objekt tar 10 minuter att importera, tar sedan 20 000 objekt ungefär 20 minuter på samma server.
* Exporten är också linjär.
* Synkroniseringen kommer att växa exponentiellt baserat på antalet objekt med referenser till andra objekt. Medlemskap i gruppen och kapslade grupper har den huvudsakliga prestandapåverkan eftersom deras medlemmar refererar till objekt eller andra grupper. Dessa referenser måste hitta och refererar till objekt i MV att slutföra synkroniseringscykel.

### <a name="filtering"></a>Filtrering

Storleken på Active Directory-topologi som du vill importera är den främsta faktor som påverkar prestanda och totala tid de interna komponenterna av etablering engine tar.

[Filtrering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) bör användas för att minska objekt till den synkroniserade. Detta förebygger onödiga objekt från att bearbetas och exporteras till Azure AD. Efter prioritet är de följande metoder för filtrering tillgängliga:



- **Domänbaserade filtreringen** – Använd det här alternativet för att välja specifika domäner som synkroniseras till Azure AD. Du måste lägga till och ta bort domäner från synkroniseringskonfiguration för motorn när du gör ändringar i din lokala infrastruktur när du har installerat Azure AD Connect-synkronisering.
- **Organisation enhet (OU) filtrering** -använder organisationsenheter för att fokusera på specifika objekt i Active Directory-domäner för etablering i Azure AD. OU-filtrering är den andra rekommenderas filtrering mekanism, eftersom den använder enkla LDAP attributbegränsade frågor för att importera en mindre deluppsättning objekt från Active Directory.
- **Attributfiltrering per objekt** -använder attributvärdena för objekt för att bestämma om specifikt objekt i Active Directory har etablerats i Azure AD. Attributfiltrering är perfekt för att finjustera dina filter när domänen och OU-filtrering inte uppfyller kraven för filtrering. Attributfiltrering kan inte minska tid som import men minska synkronisering och exportera gånger.
- **Gruppbaserad filtrering** -använder grupp för att bestämma om objekt som ska etableras i Azure AD. Gruppbaserad filtrering är endast lämpliga för att testa situationer och rekommenderas inte för produktion, på grund av extra arbete krävs för att söka gruppmedlemskap under synkroniseringscykel.

Många beständiga [bortkopplingsobjekt](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) i din Active Directory-CS kan orsaka längre synkroniseringstiden, eftersom etablering motorn tillkommer varje disconnector-objekt för möjliga anslutning i synkroniseringscykel. Använd någon av följande rekommendationer om du vill lösa det här problemet:



- Placera bortkopplingsobjekt utanför omfånget för import med hjälp av domän eller OU-filtrering.
- Projekt/join objekt som ska MV och ange den [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) attribut är lika med sant för att förhindra att etableringen av de här objekten i Azure AD CS.

> [!NOTE]
> Användare kan bli förvirrad eller behörighetsproblem kan uppstå när för många objekt filtreras. Till exempel i en hybrid Exchange online-implementering visas användare med lokala postlådor fler användare i sin globala adresslistan än användare med postlådor i Exchange online. I annat fall kan en användare vill bevilja åtkomst i en molnapp till en annan användare som inte tillhör omfånget för en filtrerad uppsättning objekt.

### <a name="attribute-flows"></a>Attributflöden

Attributflöden är en process för att kopiera och omvandla attributvärden för objekt från en ansluten katalog till en annan ansluten katalog. Definieras som en del av sync-regler. Till exempel när en användarens telefonnummer har ändrats i din Active Directory, kommer telefonnummer i Azure AD att uppdateras. Organisationer kan [ändra attributflöden](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-change-the-configuration) till suite olika krav. Du bör du kopiera de befintliga attributflöden innan du ändrar dem.

Enkel omdirigeringar, som flödar in ett attributvärde till ett annat attribut inte har betydande prestandapåverkan. Ett exempel på en omdirigering flödar ett mobiltelefonnummer i Active Directory till arbetstelefonnummer i Azure AD.

Omvandla attributvärden kan ha en prestandapåverkan på synkroniseringen. Omvandla attributvärden innehåller ändra, formatera, sammanfoga eller subtrahera värdena i attribut.

Organisationer kan förhindra att vissa attribut för att flöda till Azure AD, men det kommer inte påverka prestanda för etablering motorn.

> [!NOTE]
> Ta inte bort oönskade attributflöden i dina Synkroniseringsregler. Det rekommenderas du i stället inaktivera dem, eftersom borttagna regler återskapas under Azure AD Connect-uppgraderingar.

## <a name="azure-ad-connect-dependency-factors"></a>Azure AD Connect beroende faktorer

Prestanda för Azure AD Connect är beroende av prestandan hos de anslutna kataloger det importerar och exporterar till. Till exempel storleken på Active Directory som behövs för att importera eller svarstiden i nätverk till Azure AD-tjänsten. SQL-databasen etablering motorn använder också påverkar prestandan i synkroniseringscykel.

### <a name="active-directory-factors"></a>Active Directory-faktorer

Som tidigare nämnts påverkar antalet objekt som ska importeras prestanda avsevärt. Den [maskinvara och krav för Azure AD Connect](how-to-connect-install-prerequisites.md) beskriver specifik maskinvara nivåer, baserat på storleken på distributionen. Azure AD Connect har endast stöd för specifika topologier som beskrivs i [topologier för Azure AD Connect](plan-connect-topologies.md). Det finns inga prestandaoptimering och rekommendationer för topologier med stöds inte.

Kontrollera att din Azure AD Connect-servern uppfyller maskinvarukraven baserat på storleken på din Active Directory du vill importera. Felaktiga eller långsam nätverksanslutning mellan Azure AD Connect-servern och Active Directory-domänkontrollanter kan sakta ned din import.

### <a name="azure-ad-factors"></a>Azure AD-faktorer

Azure AD använder begränsning för att skydda Molntjänsten från denial of service-attacker. Azure AD har för närvarande en begränsning på högst 7 000 skrivningar per 5 minuter (84,000 per timme). Exempelvis kan du begränsas följande åtgärder:



- Azure AD Connect-exporten till Azure AD.
- PowerShell-skript eller program som uppdaterar Azure AD direkt till och med i bakgrunden, till exempel dynamiska gruppmedlemskap.
- Användare som uppdaterar sina egna identity-poster, till exempel registrera för MFA eller SSPR (återställning av lösenord).
- Åtgärder i det grafiska användargränssnittet.

Planera för distribution och underhåll aktiviteter, och kontrollera att din Azure AD Connect-synkroniseringscykel inte påverkas av nätverksbegränsningar. Om du har en stor anställningsstrategi våg där du kan skapa tusentals användaridentiteter, det kan orsaka uppdateringar till dynamiska gruppmedlemskap, licensiering tilldelningar och lösenordsåterställning via självbetjäning registreringar. Är det bättre att sprida dessa skrivningar över flera timmar eller några få dagar.

### <a name="sql-database-factors"></a>SQL database faktorer

Storleken på Active Directory-topologin datakälla kommer att påverka dina SQL database-prestanda. Följ den [maskinvarukrav](how-to-connect-install-prerequisites.md) för SQLServer-databas och beakta följande rekommendationer:



- Organisationer med fler än 100 000 användare kan minska nätverksfördröjningar genom att samordna SQL-databas och etablering-motorn på samma server.
- På grund av hög disken indata och utdata använda (I/O) kraven för synkroniseringen, Solid tillstånd-hårddiskar (SSD) för SQL-databas av etablering engine för bästa resultat bör om möjligt, Överväg att RAID 0 eller RAID 1-konfigurationer.
- Inte gör en fullständig synkronisering i förebyggande syfte; orsakar onödiga omsättning och längre svarstider.

## <a name="conclusion"></a>Sammanfattning

Beakta följande rekommendationer för att optimera prestanda för din Azure AD Connect-implementering:



- Använd den [rekommenderad maskinvarukonfiguration](how-to-connect-install-prerequisites.md) baserat på storleken på din implementering för Azure AD Connect-servern.
- När du uppgraderar Azure AD Connect i storskaliga distributioner måste du överväga att använda [svänga migreringsmetod](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration), för att kontrollera att du har minst driftstopp och bästa möjliga tillförlitlighet. 
- Använda SSD för SQL-databas för bästa skriva prestanda.
- Filtrera Active Directory-omfång så att endast objekt som etableras i Azure AD, med domänen, Organisationsenheten eller attributfiltrering.
- Om du behöver ändra standardreglerna attributet flow först kopiera regeln, ändra sedan kopian och inaktivera den ursprungliga regeln. Kom ihåg att köra en fullständig synkronisering.
- Planera tillräcklig tid för den första fullständiga synkroniseringen körningsprofil.
- Strävar efter att slutföra synkroniseringscykel delta i 30 minuter. Om delta synkronisering profilen inte Slutför i 30 minuter, ändra synkroniseringsfrekvensen standard om du vill inkludera en fullständig delta synkroniseringscykel.
- Övervaka din [Azure AD Connect health för synkronisering](how-to-connect-health-agent-install.md) i Azure AD.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
