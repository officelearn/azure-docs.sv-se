---
title: Storbritannien OFFICIELLA & BRITTISKA NHS plan prov kontroller
description: Kontroll kartläggning av storbritannien officiella och brittiska NHS skiss prover. Varje kontroll mappas till en eller flera Azure-principer som hjälper till med utvärdering.
ms.date: 12/04/2019
ms.topic: sample
ms.openlocfilehash: 5bef590013a9ef06b791e58dc6c82e74dffe1a17
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74851374"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Kontroll kartläggning av Storbritannien OFFICIELLA och brittiska NHS plan prover

Följande artikel beskriver hur den brittiska officiella och brittiska NHS skiss prover karta till Storbritannien OFFICIELLA och brittiska NHS kontroller. Mer information om kontrollerna finns i [STORBRITANNIEN.](https://www.gov.uk/government/publications/government-security-classifications)

Följande kartläggningar är till **Storbritanniens officiella** och **brittiska NHS** kontroller. Använd navigeringen till höger för att hoppa direkt till en specifik kontrollmappning. Många av de mappade kontrollerna implementeras med ett [Azure Policy-initiativ.](../../../policy/overview.md) Om du vill granska hela initiativet öppnar du **Princip** i Azure-portalen och väljer sidan **Definitioner.** Leta sedan reda på och välj ** \[förhandsgranskning\] uk OFFICIELLA och brittiska NHS kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav** inbyggda politiska initiativ.

> [!IMPORTANT]
> Varje kontroll nedan är associerad med en eller flera [Azure-principdefinitioner.](../../../policy/overview.md) Dessa policyer kan hjälpa dig att [bedöma efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta inte en 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan refererar **kompatibel** i Azure-princip endast till principerna själva. Detta säkerställer inte att du är helt kompatibel med alla krav på en kontroll. Dessutom innehåller efterlevnadsstandarden kontroller som inte åtgärdas av några Azure-principdefinitioner just nu. Därför är efterlevnad i Azure Policy bara en partiell bild av din övergripande efterlevnadsstatus. Associationerna mellan kontroller och Azure-principdefinitioner för det härmplet för efterlevnadsritning kan ändras med tiden. Om du vill visa ändringshistoriken läser du [GitHub Commit-historiken](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 Data i genomreseskydd

Skissen hjälper dig att se till att informationsöverföring med Azure-tjänster är säker genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar osäkra anslutningar till lagringskonton och Redis-cache.

- Endast säkra anslutningar till Redis-cachen ska aktiveras
- Säker överföring till lagringskonton ska vara aktiverat
- Visa granskningsresultat från Windows webbservrar som inte använder säkra kommunikationsprotokoll
- Distribuera förutsättningar för granskning av Windows-webbservrar som inte använder säkra kommunikationsprotokoll
- Senaste TLS-versionen ska användas i din API-app
- Senaste TLS-versionen ska användas i din Webbapp
- Senaste TLS-versionen ska användas i din funktionsapp

## <a name="23-data-at-rest-protection"></a>2.3 Data på vilaskydd

Den här skissen hjälper dig att tillämpa din princip om användningen av kryptografkontroller genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som framtvingar specifika kryptografkontroller och granskningsanvändning av svaga kryptografiska inställningar.
Om du förstår var dina Azure-resurser kan ha icke-optimala kryptografiska konfigurationer kan du vidta korrigerande åtgärder för att säkerställa att resurser konfigureras i enlighet med din informationssäkerhetsprincip. De principer som tilldelas av den här skissen kräver kryptering för lagringskonton för datasjöar. kräver transparent datakryptering i SQL-databaser. Granska kryptering som saknas på lagringskonton, SQL-databaser, virtuella datordiskar och automatiseringskontovariabler. granska osäkra anslutningar till lagringskonton och Redis-cache; granska svag kryptering av lösenord för virtuella datorer. och granska okrypterad service fabric-kommunikation.

- Diskkryptering bör tillämpas på virtuella datorer
- Variabler för automationskonto ska krypteras
- Service Fabric-kluster bör ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign
- Transparent datakryptering i SQL-databaser bör aktiveras
- Distribuera SQL DB-transparent datakryptering
- Kräv kryptering på DataSjölagringskonton
- Tillåtna platser (har varit hårt kodade till "UK SOUTH" och "UK WEST")
- Tillåtna platser för resursgrupper (har varit hårt kodade till "UK SOUTH" och "UK WEST")

## <a name="52-vulnerability-management"></a>5.2 Sårbarhetshantering

Den här skissen hjälper dig att hantera sårbarheter i informationssystem genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som övervakar saknade slutpunktsskydd, systemuppdateringar som saknas, sårbarheter i operativsystemet, SQL-sårbarheter och sårbarheter för virtuella datorer. Dessa insikter ger information i realtid om säkerhetstillståndet för dina distribuerade resurser och kan hjälpa dig att prioritera reparationsåtgärder.

- Övervaka saknade slutpunktsskydd i Azure Security Center
- Systemuppdateringar ska ha installerats på dina datorer
- Systemuppdateringar på skalningsuppsättningar för virtuella datorer bör installeras
- Sårbarheter i säkerhetskonfigurationen på dina datorer bör åtgärdas
- Säkerhetsproblem i DINA SQL-databaser bör åtgärdas
- Sårbarheter bör åtgärdas med en sårbarhetsbedömningslösning
- Sårbarhetsbedömning bör aktiveras på dina SQL-servrar
- Sårbarhetsbedömning bör aktiveras på dina SQL-hanterade instanser
- Säkerhetsproblem i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas
- Avancerad datasäkerhet bör aktiveras på dina SQL-hanterade instanser
- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar

## <a name="53-protective-monitoring"></a>5.3 Skyddsövervakning

Den här skissen hjälper dig att skydda informationssystemresurser genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som ger skyddande övervakning på obegränsad åtkomst, tillåta listaktivitet och hot.

- Granska obegränsad nätverksåtkomst till lagringskonton
- Adaptiva programkontroller bör aktiveras på virtuella datorer
- Granska virtuella datorer utan haveriberedskap konfigurerad
- DDoS Protection Standard bör aktiveras
- Avancerade typer av skydd mot hot bör ställas in på alla i SQL-hanterade instans avancerade datasäkerhetsinställningar
- Avancerade typer av skydd mot hot bör ställas in på alla i avancerade datasäkerhetsinställningar för SQL-servern
- Distribuera hotidentifiering på SQL-servrar
- Distribuera standardtillägget Microsoft IaaSAntimalware för Windows Server

## <a name="9-secure-user-management"></a>9 Säker användarhantering 

Azure implementerar rollbaserad åtkomstkontroll (RBAC) för att hjälpa dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure-portalen kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen hjälper dig att begränsa och kontrollera åtkomsträttigheter genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) för att granska externa konton med ägare och/eller läs-/skrivbehörighet och konton med ägare, läsa och/eller skrivbehörighet som inte har multifaktorautentisering aktiverat.

- MFA ska aktiveras på konton med ägarbehörighet för din prenumeration
- MFA ska vara aktiverade konton med skrivbehörighet för din prenumeration
- MFA ska aktiveras på konton med läsbehörighet för din prenumeration
- Externa konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med skrivbehörighet ska tas bort från din prenumeration
- Externa konton med läsbehörighet bör tas bort från din prenumeration

## <a name="10-identity-and-authentication"></a>10 Identitet och autentisering

Den här skissen hjälper dig att begränsa och kontrollera åtkomsträttigheter genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) för att granska externa konton med ägare och/eller läs-/skrivbehörighet och konton med ägare, läsa och/eller skrivbehörighet som inte har multifaktorautentisering aktiverat.

- MFA ska aktiveras på konton med ägarbehörighet för din prenumeration
- MFA ska vara aktiverade konton med skrivbehörighet för din prenumeration
- MFA ska aktiveras på konton med läsbehörighet för din prenumeration
- Externa konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med skrivbehörighet ska tas bort från din prenumeration
- Externa konton med läsbehörighet bör tas bort från din prenumeration

Den här skissen tilldelar Azure-principdefinitioner för granskning av Azure Active Directory-autentisering för SQL-servrar och Service Fabric. Med Hjälp av Azure Active Directory-autentisering möjliggör förenklad behörighetshantering och centraliserad identitetshantering för databasanvändare och andra Microsoft-tjänster.

- En Azure Active Directory-administratör bör etableras för SQL-servrar
- Service Fabric-kluster bör endast använda Azure Active Directory för klientautentisering

Den här skissen tilldelar också Azure Policy-definitioner till granskningskonton som ska prioriteras för granskning, inklusive avskrivna konton och externa konton. Vid behov kan konton blockeras från att logga in (eller tas bort), vilket omedelbart tar bort åtkomsträttigheter till Azure-resurser. Den här skissen tilldelar två Azure-principdefinitioner för granskning av det avskrivna kontot som bör beaktas för borttagning.

- Inaktuella konton ska tas bort från din prenumeration
- Inaktuella konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med skrivbehörighet ska tas bort från din prenumeration

Den här skissen tilldelar också en Azure Policy definition som granskar Linux VM lösenord filbehörigheter för att varna om de är felaktigt inställda. Med den här designen kan du vidta korrigerande åtgärder för att säkerställa att autentiserare inte äventyras.

- \[Preview\]: Visa granskningsresultat från virtuella Linux-datorer som inte har passwd-filbehörigheterna inställda på 0644

Den här skissen hjälper dig att framtvinga starka lösenord genom att tilldela Azure Policy-definitioner som granskar virtuella datorer i Windows som inte tillämpar minsta styrka och andra lösenordskrav. Medvetenhet om virtuella datorer i strid med principen om lösenordsstyrka hjälper dig att vidta korrigerande åtgärder för att säkerställa att lösenord för alla vm-användarkonton är kompatibla med principen.

- \[Förhandsversion\]: Distribuera förutsättningar för granskning av virtuella Windows-datorer som inte har inställningen för lösenordskomplexitet aktiverad
- \[Förhandsversion\]: Distribuera förutsättningar för granskning av virtuella Windows-datorer som inte har en maximal lösenordsålder på 70 dagar
- \[Förhandsversion\]: Distribuera förutsättningar för granskning av virtuella Windows-datorer som inte har en lägsta lösenordsålder på 1 dag
- \[Förhandsversion\]: Distribuera förutsättningar för granskning av virtuella Windows-datorer som inte begränsar den minsta lösenordslängden till 14 tecken
- \[Förhandsversion\]: Distribuera förutsättningar för granskning av virtuella Windows-datorer som tillåter återanvändning av de tidigare 24 lösenorden
- \[Förhandsgranskning\]: Visa granskningsresultat från virtuella windows-datorer som inte har inställningen för lösenordskomplexitet aktiverad
- \[Preview\]: Visa granskningsresultat från virtuella Windows-datorer som inte har en maximal lösenordsålder på 70 dagar
- \[Preview\]: Visa granskningsresultat från virtuella Windows-datorer som inte har en lägsta lösenordsålder på 1 dag
- \[Förhandsgranskning\]: Visa granskningsresultat från virtuella windows-datorer som inte begränsar den minsta lösenordslängden till 14 tecken
- \[Preview\]: Visa granskningsresultat från virtuella Windows-datorer som tillåter återanvändning av de tidigare 24 lösenorden

Den här skissen hjälper dig också att kontrollera åtkomsten till Azure-resurser genom att tilldela Azure Policy-definitioner. Dessa principer granskar användningen av resurstyper och konfigurationer som kan ge mer tillåtande åtkomst till resurser. Att förstå resurser som bryter mot dessa principer kan hjälpa dig att vidta korrigerande åtgärder för att säkerställa att åtkomsten till Azure-resurser är begränsad till behöriga användare.

- \[Preview\]: Distribuera krav för att granska virtuella Linux-datorer som har konton utan lösenord
- \[Preview\]: Distribuera krav för att granska virtuella Linux-datorer som tillåter fjärranslutningar från konton utan lösenord
- \[Preview:\]Granska virtuella Linux-datorer som har konton utan lösenord
- \[Preview\]: Granska virtuella Linux-datorer som tillåter fjärranslutningar från konton utan lösenord
- Lagringskonton ska migreras till nya Azure Resource Manager-resurser
- Virtuella datorer ska migreras till nya Azure Resource Manager-resurser
- Granska virtuella datorer som inte använder hanterade diskar

## <a name="11-external-interface-protection"></a>11 Skydd för externt gränssnitt

Förutom att använda fler än 25 principer för lämplig säker användarhantering hjälper den här skissen dig att skydda tjänstgränssnitt från obehörig åtkomst genom att tilldela en [Azure Policy-definition](../../../policy/overview.md) som övervakar obegränsade lagringskonton. Lagringskonton med obegränsad åtkomst kan ge oavsiktlig åtkomst till information som finns i informationssystemet. Den här skissen tilldelar också en princip som möjliggör anpassningsbara programkontroller på virtuella datorer.

- Granska obegränsad nätverksåtkomst till lagringskonton
- Adaptiva programkontroller bör aktiveras på virtuella datorer
- NSGs-reglerna för webbapplikationer på IaaS bör härdas
- Åtkomst via internetvänd slutpunkt bör begränsas
- Regler för nätverkssäkerhetsgrupp för internetvända virtuella datorer bör skärpas
- Slutpunktsskyddslösningen bör installeras på skalningsuppsättningar för virtuella datorer
- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer
- Granska obegränsad nätverksåtkomst till lagringskonton
- Fjärrfelsökning bör inaktiveras för Funktionsapp
- Fjärrfelsökning bör inaktiveras för webbprogram
- Fjärrfelsökning bör inaktiveras för API-app
- Webbprogrammet ska endast vara tillgängligt via HTTPS
- Funktionsappen ska endast vara tillgänglig via HTTPS
- API-app bör endast vara tillgänglig via HTTPS

## <a name="12-secure-service-administration"></a>12 Säker serviceadministration

Azure implementerar rollbaserad åtkomstkontroll (RBAC) för att hjälpa dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure-portalen kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen hjälper dig att begränsa och kontrollera privilegierade åtkomsträttigheter genom att tilldela fem [Azure-principdefinitioner](../../../policy/overview.md) för att granska externa konton med ägare och/eller skrivbehörighet och konton med ägare och/eller skrivbehörighet som inte har multifaktorautentisering aktiverat.

System som används för administration av en molntjänst har mycket privilegierad åtkomst till den tjänsten. Deras kompromiss skulle ha betydande inverkan, inklusive medel för att kringgå säkerhetskontroller och stjäla eller manipulera stora mängder data. De metoder som tjänsteleverantörens administratörer kan använda för att hantera drifttjänsten bör utformas för att minska risken för utnyttjande som skulle kunna undergräva tjänstens säkerhet. Om den här principen inte implementeras kan en angripare ha möjlighet att kringgå säkerhetskontroller och stjäla eller manipulera stora mängder data.

- MFA ska aktiveras på konton med ägarbehörighet för din prenumeration
- MFA ska vara aktiverade konton med skrivbehörighet för din prenumeration
- Externa konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med skrivbehörighet ska tas bort från din prenumeration

Den här skissen tilldelar Azure-principdefinitioner för granskning av Azure Active Directory-autentisering för SQL-servrar och Service Fabric. Med Hjälp av Azure Active Directory-autentisering möjliggör förenklad behörighetshantering och centraliserad identitetshantering för databasanvändare och andra Microsoft-tjänster.

- En Azure Active Directory-administratör bör etableras för SQL-servrar
- Service Fabric-kluster bör endast använda Azure Active Directory för klientautentisering

Den här skissen tilldelar också Azure Policy-definitioner till granskningskonton som ska prioriteras för granskning, inklusive avskrivna konton och externa konton med förhöjda behörigheter. Vid behov kan konton blockeras från att logga in (eller tas bort), vilket omedelbart tar bort åtkomsträttigheter till Azure-resurser. Den här skissen tilldelar två Azure-principdefinitioner för granskning av det avskrivna kontot som bör beaktas för borttagning.

- Inaktuella konton ska tas bort från din prenumeration
- Inaktuella konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med skrivbehörighet ska tas bort från din prenumeration

Den här skissen tilldelar också en Azure Policy definition som granskar Linux VM lösenord filbehörigheter för att varna om de är felaktigt inställda. Med den här designen kan du vidta korrigerande åtgärder för att säkerställa att autentiserare inte äventyras.

- \[Preview:\]Audit Linux VM /etc/passwd-filbehörigheter är inställda på 0644

## <a name="13-audit-information-for-users"></a>13 Granskningsinformation för användare

Den här skissen hjälper dig att se till att systemhändelser loggas genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar logginställningar på Azure-resurser. En tilldelad princip granskar också om virtuella datorer inte skickar loggar till en angiven logganalysarbetsyta.

- Granskning bör aktiveras vid avancerade datasäkerhetsinställningar på SQL Server
- Granska diagnostikinställning
- \[Preview\]: Distribuera LoggAnalys agent för Linux virtuella datorer
- \[Förhandsgranskning:\]Distribuera logganalysagent för virtuella Windows-datorer
- Distribuera nätverksbevakare när virtuella nätverk skapas

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontrollkartläggningen av de brittiska officiella och brittiska NHS-ritningarna kan du besöka följande artiklar om hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [Brittiska officiella och brittiska NHS ritningar - Översikt](./index.md)
> [Storbritannien officiella och brittiska NHS ritningar - Distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Läs mer om [skisslivscykeln](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
