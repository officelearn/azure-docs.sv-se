---
title: Faktorer som påverkar prestandan för Azure AD Connect
description: I det här dokumentet beskrivs hur olika faktorer påverkar azure AD Connect-etableringsmotorn. Dessa faktorer hjälper organisationer att planera sin Azure AD Connect-distribution för att se till att den uppfyller deras synkroniseringskrav.
services: active-directory
author: billmath
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5518d516848ba7c006827faa41ff76bbca35d0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897051"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Faktorer som påverkar prestandan för Azure AD Connect

Azure AD Connect synkroniserar din Active Directory till Azure AD. Den här servern är en viktig komponent för att flytta dina användaridentiteter till molnet. De primära faktorer som påverkar prestanda för en Azure AD Connect är:

| **Designfaktor**| **Definition** |
|:-|-|
| Topologi| Distributionen av slutpunkter och komponenter Som Azure AD Connect måste hantera i nätverket. |
| Skalning| Antalet objekt som användare, grupper och OUs som ska hanteras av Azure AD Connect. |
| Maskinvara| Maskinvaran (fysisk eller virtuell) för Azure AD Connect och beroende prestandakapacitet för varje maskinvarukomponent, inklusive konfiguration av processorer, minne, nätverk och hårddisk. |
| Konfiguration| Hur Azure AD Connect bearbetar kataloger och information. |
| Läsa in| Frekvensen för objektet ändras. Lasterna kan variera under en timme, dag eller vecka. Beroende på komponenten kan du behöva designa för toppbelastning eller genomsnittlig belastning. |

Syftet med det här dokumentet är att beskriva de faktorer som påverkar prestanda för Azure AD Connect-etableringsmotorn. Stora eller komplexa organisationer (organisationer som etablerar mer än 100 000 objekt) kan använda rekommendationerna för att optimera implementeringen av Azure AD Connect, om de får några prestandaproblem som beskrivs här. De andra komponenterna i Azure AD Connect, till exempel [Azure AD Connect-hälso-](how-to-connect-health-agent-install.md) och agenter, omfattas inte här.

> [!IMPORTANT]
> Microsoft stöder inte att ändra eller använda Azure AD Connect utanför de åtgärder som formellt dokumenteras. Alla dessa åtgärder kan resultera i ett inkonsekvent eller icke-stödt tillstånd för Azure AD Connect-synkronisering. Därför kan Microsoft inte tillhandahålla teknisk support för sådana distributioner.

## <a name="azure-ad-connect-component-factors"></a>Komponentfaktorer för Azure AD Connect

Följande diagram visar en arkitektur på hög nivå för etableringsmotor som ansluter till en enda skog, även om flera skogar stöds. Den här arkitekturen visar hur de olika komponenterna interagerar med varandra.

![AzureADConnentInternal](media/plan-connect-performance-factors/AzureADConnentInternal.png)

Etableringsmotorn ansluter till varje Active Directory-skog och till Azure AD. Processen att läsa information från varje katalog kallas Importera. Export avser uppdatering av kataloger från etableringsmotorn. Sync utvärderar reglerna för hur objekten flödar inuti etableringsmotorn. För ett djupare kan du referera till [Azure AD Connect-synkronisering: Förstå arkitekturen](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture).

Azure AD Connect använder följande mellanlagringsområden, regler och processer för att tillåta synkronisering från Active Directory till Azure AD:

* **Anslutningsutrymme (CS)** - Objekt från varje ansluten katalog (CD), de faktiska katalogerna, är iscensatta här först innan de kan bearbetas av etableringsmotorn. Azure AD har sin egen CS och varje skog som du ansluter till har sin egen CS.
* **Metaverse (MV)** - Objekt som måste synkroniseras skapas här baserat på synkroniseringsreglerna. Objekt måste finnas i MV innan de kan fylla i objekt och attribut till andra anslutna kataloger. Det finns bara en MV.
* **Synkroniseringsregler** – De bestämmer vilka objekt som ska skapas (projiceras) eller anslutas (sammanfogas) till objekt i MV. Synkroniseringsreglerna bestämmer också vilka attributvärden som ska kopieras eller omvandlas till och från katalogerna.
* **Kör profiler** - Buntar ihop processstegen för att kopiera objekt och deras attributvärden enligt synkroniseringsreglerna mellan mellanlagringsområdena och anslutna kataloger.

Det finns olika körprofiler för att optimera etableringsmotorns prestanda. De flesta organisationer använder standardscheman och kör profiler för normala åtgärder, men vissa organisationer kan behöva [ändra schemat](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) eller utlösa andra körningsprofiler för att tillgodose ovanliga situationer. Följande körprofiler är tillgängliga:

### <a name="initial-sync-profile"></a>Första synkroniseringsprofil

Den inledande synkroniseringsprofilen är processen att läsa de anslutna katalogerna, som en Active Directory-skog, för första gången. Den gör sedan en analys av alla poster i synkroniseringsmotordatabasen. Den inledande cykeln skapar nya objekt i Azure AD och tar extra tid att slutföra om Active Directory-skogarna är stora. Den första synkroniseringen innehåller följande steg:

1. Fullständig import på alla kopplingar
2. Fullständig synkronisering på alla anslutningar
3. Exportera på alla kopplingar

### <a name="delta-sync-profile"></a>Delta-synkroniseringsprofil

För att optimera synkroniseringsprocessen bearbetar den här körningsprofilen endast ändringarna (skapar, tar bort och uppdaterar) av objekt i dina anslutna kataloger sedan den senaste synkroniseringsprocessen. Som standard körs deltasynkroniseringsprofilen var 30:e minut. Organisationer bör sträva efter att hålla den tid det tar att under 30 minuter, för att se till att Azure AD är uppdaterat. Om du vill övervaka hälsotillståndet för Azure AD Connect använder du [hälsoövervakningsagenten](how-to-connect-health-sync.md) för att se eventuella problem med processen. Deltasynkroniseringsprofilen innehåller följande steg:

1. Delta-import på alla kopplingar
2. Delta-synkronisering på alla kontakter
3. Exportera på alla kopplingar

Ett typiskt deltasynkroniseringsscenario för företagsorganisation är:

- ~1% av objekten tas bort
- ~1% av objekten skapas
- ~5% av objekten ändras

Din förändringstakt kan variera beroende på hur ofta organisationen uppdaterar användare i Active Directory. Till exempel kan högre förändringstakt uppstå med säsongsvariationer att anställa och minska arbetskraften.

### <a name="full-sync-profile"></a>Fullständig synkroniseringsprofil

En fullständig synkroniseringscykel krävs om du har gjort någon av följande konfigurationsändringar:



- Ökade omfattningen av de objekt eller attribut som ska importeras från de anslutna katalogerna. När du till exempel lägger till en domän eller organisationsenhet i importomfattningen.
- Har gjort ändringar i synkroniseringsreglerna. När du till exempel skapar en ny regel för att fylla i en användares titel i Azure AD från extension_attribute3 i Active Directory. Den här uppdateringen kräver att etableringsmotorn undersöker alla befintliga användare för att uppdatera sina titlar för att tillämpa ändringen framöver.

Följande åtgärder ingår i en fullständig synkroniseringscykel:

1. Fullständig import på alla kopplingar
2. Fullständig/Delta-synkronisering på alla kontakter
3. Exportera på alla kopplingar

> [!NOTE]
> Noggrann planering krävs när du gör massuppdateringar till många objekt i Active Directory eller Azure AD. Massuppdateringar gör att deltasynkroniseringsprocessen tar längre tid när du importerar, eftersom många objekt har ändrats. Långa importer kan hända även om massuppdateringen inte påverkar synkroniseringsprocessen. Om du till exempel tilldelar licenser till många användare i Azure AD skapas en lång importcykel från Azure AD, men inga attributändringar i Active Directory.

### <a name="synchronization"></a>Synkronisering

Synkroniseringsprocessen har följande prestandaegenskaper:

* Synkronisering är enkel trådad, vilket innebär att etableringsmotorn inte gör någon parallell bearbetning av körningsprofiler för anslutna kataloger, objekt eller attribut.
* Importtiden växer linjärt med antalet objekt som synkroniseras. Om till exempel 10 000 objekt tar 10 minuter att importera tar det cirka 20 000 objekt på samma server.
* Exporten är också linjär.
* Synkroniseringen växer exponentiellt baserat på antalet objekt med referenser till andra objekt. Gruppmedlemskap och kapslade grupper har den huvudsakliga prestandaeffekten, eftersom deras medlemmar refererar till användarobjekt eller andra grupper. Dessa referenser måste hittas och refereras till faktiska objekt i MV för att slutföra synkroniseringscykeln.

### <a name="filtering"></a>Filtrering

Storleken på den Active Directory-topologi som du vill importera är den viktigaste faktorn som påverkar prestanda och den totala tiden som de interna komponenterna i etableringsmotorn tar.

[Filtrering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) bör användas för att minska objekten till synkroniserade. Det förhindrar att onödiga objekt bearbetas och exporteras till Azure AD. I prioritetsordning finns följande metoder för filtrering:



- **Domänbaserad filtrering** – använd det här alternativet för att välja specifika domäner som ska synkroniseras med Azure AD. Du måste lägga till och ta bort domäner från synkroniseringsmotorkonfigurationen när du gör ändringar i din lokala infrastruktur när du har installerat Azure AD Connect-synkronisering.
- **Filtrering av organisationsenhet (OU)** – använder organisationsenheter för att rikta in sig på specifika objekt i Active Directory-domäner för etablering till Azure AD. OU-filtrering är den andra rekommenderade filtreringsmekanismen, eftersom den använder enkla LDAP-scopefrågor för att importera en mindre delmängd av objekt från Active Directory.
- **Attributfiltrering per objekt** - använder attributvärdena på objekt för att avgöra om specifika objekt i Active Directory etableras i Azure AD. Attributfiltrering är perfekt för att finjustera filtren när domän- och organisationsenhetsfiltrering inte uppfyller de specifika filtreringskraven. Attributfiltrering minskar inte importtiden men kan minska synkroniserings- och exporttiderna.
- **Gruppbaserad filtrering** – använder gruppmedlemskap för att avgöra om objekt ska etableras i Azure AD. Gruppbaserad filtrering är endast lämplig för testsituationer och rekommenderas inte för produktion, på grund av de extra omkostnader som krävs för att kontrollera gruppmedlemskap under synkroniseringscykeln.

Många [beständiga frånkopplingsobjekt](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) i Active Directory CS kan orsaka längre synkroniseringstider, eftersom etableringsmotorn måste omvärdera varje frånkopplare objekt för eventuell anslutning i synkroniseringscykeln. Överväg följande rekommendationer för att lösa problemet:



- Placera frånkopplarens objekt utanför omfånget för import med hjälp av domän- eller organisationsenhetsfiltrering.
- Project/anslut objekten till MV och ange [attributet cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) lika med Sant, för att förhindra etablering av dessa objekt i Azure AD CS.

> [!NOTE]
> Användare kan bli förvirrade eller problem med programbehörigheter kan uppstå när för många objekt filtreras. I en hybridimplementering av Exchange ser användare med lokala postlådor till exempel fler användare i sin globala adresslista än användare med postlådor i Exchange online. I andra fall kanske en användare vill bevilja åtkomst i en molnapp till en annan användare som inte ingår i omfattningen av den filtrerade uppsättningen objekt.

### <a name="attribute-flows"></a>Attributflöden

Attributflöden är processen för att kopiera eller omvandla attributvärden för objekt från en ansluten katalog till en annan ansluten katalog. De definieras som en del av synkroniseringsreglerna. När telefonnumret för en användare till exempel ändras i Active Directory uppdateras telefonnumret i Azure AD. Organisationer kan [ändra attributflödena](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-change-the-configuration) till olika krav. Vi rekommenderar att du kopierar de befintliga attributflödena innan du ändrar dem.

Enkla omdirigeringar, som att flöda ett attributvärde till ett annat attribut, har inte materialprestandapåverkan. Ett exempel på en omdirigering flödar ett mobilnummer i Active Directory till kontorets telefonnummer i Azure AD.

Om du omvandlar attributvärden kan ha en prestandapåverkan på synkroniseringsprocessen. Transformering av attributvärden omfattar ändring, omformatering, sammanfogande eller subtrahera värden för attribut.

Organisationer kan förhindra att vissa attribut flödar till Azure AD, men det påverkar inte etableringsmotorns prestanda.

> [!NOTE]
> Ta inte bort oönskade attributflöden i synkroniseringsreglerna. Vi rekommenderar att du hellre inaktiverar dem, eftersom borttagna regler återskapas under Azure AD Connect-uppgraderingar.

## <a name="azure-ad-connect-dependency-factors"></a>Beroendefaktorer för Azure AD Connect

Prestanda för Azure AD Connect är beroende av prestanda för de anslutna kataloger som importeras och exporteras till. Storleken på Active Directory som den behöver importera eller nätverksfördröjningen till Azure AD-tjänsten. Den SQL-databas som etableringsmotorn använder påverkar också synkroniseringscykelns övergripande prestanda.

### <a name="active-directory-factors"></a>Active Directory-faktorer

Som tidigare nämnts påverkar antalet objekt som ska importeras prestandan avsevärt. [Maskinvaran och förutsättningarna för Azure AD Connect](how-to-connect-install-prerequisites.md) beskriver specifika maskinvarunivåer baserat på storleken på distributionen. Azure AD Connect stöder endast specifika topologier som beskrivs i [Topologies för Azure AD Connect](plan-connect-topologies.md). Det finns inga prestandaoptimeringar och rekommendationer för topologier som inte stöds.

Kontrollera att din Azure AD Connect-server uppfyller maskinvarukraven baserat på din Active Directory-storlek som du vill importera. Dålig eller långsam nätverksanslutning mellan Azure AD Connect-servern och Active Directory-domänkontrollanterna kan göra importen långsammare.

### <a name="azure-ad-factors"></a>Azure AD-faktorer

Azure AD använder begränsning för att skydda molntjänsten från DoS-attacker (Denial-of-Service). Azure AD har för närvarande en begränsningsgräns på 7 000 skrivningar per 5 minuter (84 000 per timme). Följande åtgärder kan till exempel begränsas:



- Azure AD Connect-export till Azure AD.
- PowerShell-skript eller program som uppdaterar Azure AD direkt även i bakgrunden, till exempel dynamiska gruppmedlemskap.
- Användare som uppdaterar sina egna identitetsposter som registrering för MFA eller SSPR (återställning av lösenord för självbetjäning).
- Åtgärder inom det grafiska användargränssnittet.

Planera för distributions- och underhållsuppgifter för att se till att synkroniseringscykeln för Azure AD Connect inte påverkas av begränsningsgränser. Om du till exempel har en stor anställningsvåg där du skapar tusentals användaridentiteter kan det orsaka uppdateringar av dynamiska gruppmedlemskap, licensieringstilldelningar och registreringar för återställning av lösenord för självbetjäning. Det är bättre att sprida dessa skriver över flera timmar eller några dagar.

### <a name="sql-database-factors"></a>Sql-databasfaktorer

Storleken på din källa Active Directory-topologi påverkar sql-databasprestanda. Följ [maskinvarukraven](how-to-connect-install-prerequisites.md) för SQL-serverdatabasen och beakta följande rekommendationer:



- Organisationer med mer än 100 000 användare kan minska nätverksdynningar genom att samlokalisera SQL-databasen och etableringsmotorn på samma server.
- På grund av kraven för hög diskingång och utdata (I/O) i synkroniseringsprocessen använder du SSD (Solid State Drives) för SQL-databasen för etableringsmotorn för optimala resultat, om det inte är möjligt, överväga RAID 0- eller RAID 1-konfigurationer.
- Gör inte en fullständig synkronisering förebyggande; det orsakar onödig omsättning och långsammare svarstider.

## <a name="conclusion"></a>Slutsats

Om du vill optimera prestanda för din Azure AD Connect-implementering bör du tänka på följande rekommendationer:



- Använd den [rekommenderade maskinvarukonfigurationen](how-to-connect-install-prerequisites.md) baserat på din implementeringsstorlek för Azure AD Connect-servern.
- När du uppgraderar Azure AD Connect i storskaliga distributioner bör du överväga att använda [swingmigreringsmetod](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration)för att se till att du har minst driftstopp och bästa tillförlitlighet. 
- Använd SSD för SQL-databasen för bästa skrivprestanda.
- Filtrera Active Directory-scopet så att det bara omfattar objekt som behöver etableras i Azure AD med domän-, organisationsenhets- eller attributfiltrering.
- Om du behöver ändra standardregler för attributflöde kopierar du först regeln och ändrar sedan kopian och inaktiverar den ursprungliga regeln. Kom ihåg att köra en fullständig synkronisering igen.
- Planera tillräckligt med tid för den första fullständiga synkroniseringskörningsprofilen.
- Sträva efter att slutföra delta sync cykeln i 30 minuter. Om deltasynkroniseringsprofilen inte slutförs inom 30 minuter ändrar du standardsynkroniseringsfrekvensen så att den innehåller en fullständig deltasynkroniseringscykel.
- Övervaka [synkroniseringshälsan](how-to-connect-health-agent-install.md) för Azure AD Connect i Azure AD.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
