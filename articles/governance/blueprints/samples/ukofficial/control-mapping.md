---
title: Exempel på officiella och UK NHS-ritningar – kontroll mappning
description: Kontroll mappning av NHS i Storbritannien och Storbritannien.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 654ae2da9bcdb6d73de47730aff6870c2e3dfaeb
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001527"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Kontroll mappning av NHS i Storbritannien och Storbritannien i Storbritannien

I följande artikel beskrivs hur NHS i Storbritannien och Storbritannien i Storbritannien mappas till de officiella NHS-kontrollerna i Storbritannien och Storbritannien. Mer information om kontrollerna finns i [Storbritannien officiellt](https://www.gov.uk/government/publications/government-security-classifications).

Följande mappningar är de officiella NHS-kontrollerna i **Storbritannien** och **Storbritannien** . Använd navigeringen till höger om du vill gå direkt till en bestämd kontroll mappning. Många av de mappade kontrollerna implementeras med ett [Azure policy](../../../policy/overview.md) initiativ. Om du vill granska hela initiativet öppnar du **princip** i Azure Portal och väljer sidan **definitioner** . Leta sedan reda på och välj  **\[\] förhands granskningen i Storbritannien NHS kontroller och distribuera särskilda VM-tillägg så att de stöder gransknings krav i** det inbyggda princip initiativet.

> [!IMPORTANT]
> Varje kontroll nedan är kopplad till en eller flera [Azure policy](../../../policy/overview.md) -definitioner. Dessa principer kan hjälpa dig att [utvärdera efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta ingen 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan är **kompatibel** i Azure policy endast som avser själva principerna. Detta garanterar inte att du är helt kompatibel med alla krav för en kontroll. Standarden för efterlevnad innehåller dessutom kontroller som inte åtgärdas av några Azure Policy definitioner för tillfället. Därför är regelefterlevnad i Azure Policy bara en partiell vy av din övergripande kompatibilitetsstatus. Kopplingarna mellan kontroller och Azure Policy definitioner för det här skiss exemplet för efterlevnad kan ändras med tiden. Om du vill visa ändrings historiken läser du [inchecknings historiken för GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 data i överförings skydd

Skissen hjälper dig att se till att informations överföring med Azure-tjänster är säker genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som granskar oskyddade anslutningar till lagrings konton och Redis cache.

- Endast säkra anslutningar till din Redis Cache ska vara aktiverade
- Säker överföring till lagrings konton ska vara aktiverat

## <a name="23-data-at-rest-protection"></a>2,3 data vid rest-skydd

Den här skissen hjälper dig att genomdriva principen om användningen av kryptografiska kontroller genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som upprätthåller vissa kryptografiska kontroller och granska användningen av svaga kryptografiska inställningar.
Att förstå var dina Azure-resurser kan ha icke-optimala kryptografiska konfigurationer kan hjälpa dig att vidta korrigerande åtgärder för att säkerställa att resurserna konfigureras i enlighet med din informations säkerhets princip. Mer specifikt kräver principerna som tilldelats av den här skissen kryptering för data Lake Storage-konton. Kräv transparent data kryptering på SQL-databaser; granska saknad kryptering på lagrings konton, SQL-databaser, virtuella dator diskar och variabler för Automation-konton. granska oskyddade anslutningar till lagrings konton och Redis Cache. granska svag kryptering av lösen ord för virtuella datorer; och granska okrypterad Service Fabric kommunikation.

- transparent datakryptering på SQL-databaser ska aktive ras
- Disk kryptering bör tillämpas på virtuella datorer
- Variabler för Automation-konton ska vara krypterade
- Säker överföring till lagrings konton ska vara aktiverat
- Service Fabric-kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign
- transparent datakryptering på SQL-databaser ska aktive ras
- Distribuera transparent data kryptering för SQL DB
- Kräv kryptering för Data Lake Store-konton
- Tillåtna platser (har hårdkodats till "Storbritannien, södra" och "Storbritannien väst")
- Tillåtna platser för resurs grupper (har hårdkodats till "Storbritannien, södra" och "Storbritannien väst")

## <a name="52-vulnerability-management"></a>5,2 sårbarhets hantering

Den här skissen hjälper dig att hantera sårbarheter i informations systemet genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som övervakar saknade slut punkts skydd, system uppdateringar som saknas, sårbarheter för operativ system, SQL-sårbarheter och virtuella dator sårbarheter. Dessa insikter ger real tids information om säkerhets statusen för dina distribuerade resurser och kan hjälpa dig att prioritera åtgärds åtgärder.

- Övervaka saknade Endpoint Protection i Azure Security Center
- System uppdateringar bör installeras på dina datorer
- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas
- Säkerhets risker i SQL-databaser bör åtgärdas
- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning

## <a name="53-protective-monitoring"></a>5,3 skydds övervakning

Den här skissen hjälper dig att skydda informations systemets till gångar genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som ger skydds övervakning av obegränsad åtkomst, vitlista-aktivitet och hot.

- Granska obegränsad nätverks åtkomst till lagrings konton
- Anpassningsbara program kontroller ska vara aktiverade på virtuella datorer
- Distribuera hot identifiering på SQL-servrar
- Distribuera standard tillägget Microsoft IaaS-program mot skadlig kod för Windows Server

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9 säker användar hantering/10 identitet och autentisering

Azure implementerar rollbaserad åtkomst kontroll (RBAC) för att hjälpa dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure Portal kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen hjälper dig att begränsa och kontrol lera åtkomst behörighet genom att tilldela [Azure policy](../../../policy/overview.md) definitioner för att granska externa konton med ägare och/eller Läs-/skriv behörigheter och konton med ägar-, Läs-och/eller Skriv behörighet som inte har Multi-Factor autentisering aktive rad.

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- MFA ska vara aktiverade konton med Skriv behörighet för din prenumeration
- MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med skrivbehörigheter bör tas bort från prenumerationen
- Externa konton med Läs behörighet bör tas bort från din prenumeration

Den här skissen tilldelar Azure Policy definitioner för att granska användningen av Azure Active Directory autentisering för SQL-servrar och Service Fabric. Med hjälp av Azure Active Directory-autentisering möjliggörs förenklad behörighets hantering och centraliserad identitets hantering för databas användare och andra Microsoft-tjänster.

- En Azure Active Directory administratör bör tillhandahållas för SQL-servrar
- Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering

Den här skissen tilldelar också Azure Policy definitioner till gransknings konton som ska prioriteras för granskning, inklusive avskrivna konton och externa konton. Vid behov kan konton blockeras från att logga in (eller tas bort), vilket omedelbart tar bort åtkomst behörighet till Azure-resurser. Den här skissen tilldelar två Azure Policy definitioner för att granska avskrivet konto som bör tas bort.

- Föråldrade konton bör tas bort från din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med skrivbehörigheter bör tas bort från prenumerationen

Den här skissen tilldelar också en Azure Policy-definition som granskar Linux VM lösen ords fil behörigheter för att varna om de är felaktigt inställda. Med den här designen kan du vidta lämpliga åtgärder för att säkerställa att autentiserare inte komprometteras.

- \[För\]hands version: Granska virtuella Linux-/etc/passwd fil behörigheter har angetts till 0644

Den här skissen hjälper dig att använda starka lösen ord genom att tilldela Azure Policy definitioner som granskar virtuella Windows-datorer som inte uppfyller minimi kraven och andra lösen ords krav. Om de virtuella datorerna strider mot principen för lösen ords säkerhet kan du vidta åtgärder för att säkerställa att lösen ord för alla VM-användarkonton är kompatibla med principen.

- \[För\]hands version: Distribuera krav för att granska virtuella Windows-datorer som inte har inställningen för lösen ords komplexitet aktiverat
- \[För\]hands version: Distribuera krav för att granska virtuella Windows-datorer som inte har en högsta ålder för lösen ord på 70 dagar
- \[För\]hands version: Distribuera krav för att granska virtuella Windows-datorer som inte har en minsta ålder på lösen ord på 1 dag
- \[För\]hands version: Distribuera krav för att granska virtuella Windows-datorer som inte begränsar minsta längd på lösen ord till 14 tecken
- \[För\]hands version: Distribuera krav för att granska virtuella Windows-datorer som tillåter åter användning av de tidigare 24 lösen orden
- \[För\]hands version: Granska virtuella Windows-datorer som inte har inställningen för lösen ords komplexitet aktiverat
- \[För\]hands version: Granska virtuella Windows-datorer som inte har en högsta ålder för lösen ord på 70 dagar
- \[För\]hands version: Granska virtuella Windows-datorer som inte har en minsta ålder på lösen ord på 1 dag
- \[För\]hands version: Granska virtuella Windows-datorer som inte begränsar minsta längd på lösen ord till 14 tecken
- \[För\]hands version: Granska virtuella Windows-datorer som tillåter åter användning av de tidigare 24 lösen orden

Den här skissen hjälper dig också att styra åtkomsten till Azure-resurser genom att tilldela Azure Policy definitioner. Dessa principer granskar användningen av resurs typer och konfigurationer som kan tillåta mer åtkomst till resurser. Att förstå resurser som strider mot dessa principer kan hjälpa dig att vidta lämpliga åtgärder för att säkerställa åtkomst till Azure-resurser som är begränsade till behöriga användare.

- \[För\]hands version: Distribuera krav för att granska virtuella Linux-datorer som har konton utan lösen ord
- \[För\]hands version: Distribuera krav för att granska virtuella Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord
- \[För\]hands version: Granska virtuella Linux-datorer som har konton utan lösen ord
- \[För\]hands version: Granska virtuella Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord
- Lagrings konton ska migreras till nya Azure Resource Manager resurser
- Virtuella datorer ska migreras till nya Azure Resource Manager-resurser
- Granska virtuella datorer som inte använder hanterade diskar

## <a name="11-external-interface-protection"></a>11 externt gränssnitts skydd

Förutom att använda fler än 25 principer för lämplig säker användar hantering hjälper den här skissen dig att skydda tjänst gränssnitt från obehörig åtkomst genom att tilldela en [Azure policy](../../../policy/overview.md) -definition som övervakar obegränsade lagrings konton. Lagrings konton med obegränsad åtkomst kan ge oavsiktlig åtkomst till information som finns i informations systemet. Den här skissen tilldelar också en princip som aktiverar anpassningsbara program kontroller på virtuella datorer.

- Granska obegränsad nätverks åtkomst till lagrings konton
- Anpassningsbara program kontroller ska vara aktiverade på virtuella datorer

## <a name="12-secure-service-administration"></a>12 säker tjänst administration

Azure implementerar rollbaserad åtkomst kontroll (RBAC) för att hjälpa dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure Portal kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen hjälper dig att begränsa och kontrol lera privilegierade åtkomst rättigheter genom att tilldela fem [Azure policy](../../../policy/overview.md) definitioner för att granska externa konton med ägare och/eller Skriv behörigheter och konton med ägare, och/eller Skriv behörigheter som inte har Multi-Factor Authentication har Aktiver ATS.

System som används för administration av en moln tjänst har hög privilegie rad åtkomst till tjänsten. Deras kompromisser skulle ha betydande påverkan, inklusive metoder för att kringgå säkerhets kontroller och stjäla eller manipulera stora mängder data. De metoder som används av tjänst leverantörens administratörer för att hantera drifts tjänsten bör utformas för att minska risken för utnyttjande som kan undergräva säkerheten för tjänsten. Om den här principen inte har implementerats kan en angripare ha möjlighet att kringgå säkerhets kontroller och stjäla eller manipulera stora mängder data.

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- MFA ska vara aktiverade konton med Skriv behörighet för din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med skrivbehörigheter bör tas bort från prenumerationen

Den här skissen tilldelar Azure Policy definitioner för att granska användningen av Azure Active Directory autentisering för SQL-servrar och Service Fabric. Med hjälp av Azure Active Directory-autentisering möjliggörs förenklad behörighets hantering och centraliserad identitets hantering för databas användare och andra Microsoft-tjänster.

- En Azure Active Directory administratör bör tillhandahållas för SQL-servrar
- Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering

Den här skissen tilldelar också Azure Policy definitioner till gransknings konton som ska prioriteras för granskning, inklusive avskrivna konton och externa konton med utökade behörigheter. Vid behov kan konton blockeras från att logga in (eller tas bort), vilket omedelbart tar bort åtkomst behörighet till Azure-resurser. Den här skissen tilldelar två Azure Policy definitioner för att granska avskrivet konto som bör tas bort.

- Föråldrade konton bör tas bort från din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med skrivbehörigheter bör tas bort från prenumerationen

Den här skissen tilldelar också en Azure Policy-definition som granskar Linux VM lösen ords fil behörigheter för att varna om de är felaktigt inställda. Med den här designen kan du vidta lämpliga åtgärder för att säkerställa att autentiserare inte komprometteras.

- \[För\]hands version: Granska virtuella Linux-/etc/passwd fil behörigheter har angetts till 0644

## <a name="13-audit-information-for-users"></a>13 gransknings information för användare

Den här skissen hjälper dig att se till att system händelser loggas genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som granskar logg inställningar på Azure-resurser. En tilldelad princip granskar också om virtuella datorer inte skickar loggar till en angiven Log Analytics-arbetsyta.

- Granskning ska aktive ras för avancerade data säkerhets inställningar på SQL Server
- Granska diagnostikinställning
- Granska gransknings inställningar för SQL Server-nivå
- \[För\]hands version: Distribuera Log Analytics agent för virtuella Linux-datorer
- \[För\]hands version: Distribuera Log Analytics agent för virtuella Windows-datorer
- Distribuera nätverks övervakare när virtuella nätverk skapas

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontroll mappningen av NHS-ritningar från Storbritannien och Storbritannien kan du gå till följande artiklar och läsa mer om översikten och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [Officiella och brittiska NHS-ritningar – översikt](./index.md)
> [Storbritannien och Storbritannien NHS ritningar – distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).