---
title: ISO 27001 Exempelkontroller för delade tjänster
description: Kontrollmappning av skissexemplet ISO 27001 Shared Services. Varje kontroll mappas till en eller flera Azure-principer som hjälper till med utvärdering.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 6c03da7d5d4caada9ef47a828163a79a003bea93
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75922525"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>Kontrollmappning av skissexemplet ISO 27001 Shared Services

I följande artikel beskrivs hur azure blueprints ISO 27001 Shared Services-skissen mappas till ISO 27001-kontrollerna. Mer information om kontrollerna finns i [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Följande mappningar är till **ISO 27001:2013-kontrollerna.** Använd navigeringen till höger för att hoppa direkt till en specifik kontrollmappning. Många av de mappade kontrollerna implementeras med ett [Azure Policy-initiativ.](../../../policy/overview.md) Om du vill granska hela initiativet öppnar du **Princip** i Azure-portalen och väljer sidan **Definitioner.** Leta sedan reda på och välj ** \[kontrollerna\] Förhandsgranska granskning ISO 27001:2013 och distribuera specifika vm-tillägg för att stödja inbyggda** principinitiativ för granskningskrav.

> [!IMPORTANT]
> Varje kontroll nedan är associerad med en eller flera [Azure-principdefinitioner.](../../../policy/overview.md) Dessa policyer kan hjälpa dig att [bedöma efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta inte en 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan refererar **kompatibel** i Azure-princip endast till principerna själva. Detta säkerställer inte att du är helt kompatibel med alla krav på en kontroll. Dessutom innehåller efterlevnadsstandarden kontroller som inte åtgärdas av några Azure-principdefinitioner just nu. Därför är efterlevnad i Azure Policy bara en partiell bild av din övergripande efterlevnadsstatus. Associationerna mellan kontroller och Azure-principdefinitioner för det härmplet för efterlevnadsritning kan ändras med tiden. Om du vill visa ändringshistoriken läser du [GitHub Commit-historiken](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-shared/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A.6.1.2 Ansvarsfördelning

Att bara ha en Azure-prenumerationsägare tillåter inte administrativ redundans. Omvänt kan för många Azure-prenumerationsägare öka risken för ett intrång via ett komprometterat ägarkonto. Den här skissen hjälper dig att underhålla ett lämpligt antal Azure-prenumerationsägare genom att tilldela två [Azure-principdefinitioner](../../../policy/overview.md) som granskar antalet ägare för Azure-prenumerationer. Genom att hantera prenumerationsägare kan du implementera lämplig åtskillnad mellan uppgifter.

- \[Förhandsversion\]: Granska minsta antal ägare för en prenumeration
- \[Förhandsversion\]: Granska maximalt antal ägare för en prenumeration

## <a name="a821-classification-of-information"></a>A.8.2.1 Klassificering av information

Azures [SQL Vulnerability Assessment-tjänst](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) kan hjälpa dig att identifiera känsliga data som lagras i dina databaser och innehåller rekommendationer för att klassificera dessa data. Den här skissen tilldelar en [Azure-principdefinition](../../../policy/overview.md) för granskning att säkerhetsproblem som identifierats vid SQL Vulnerability Assessment-genomsökningen åtgärdas.

- \[Förhandsversion\]: Övervaka SQL-sårbarhetsbedömningsresultat i Azure Security Center

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 Tillgång till nät och nätverkstjänster

Azure implementerar [rollbaserad åtkomstkontroll](../../../../role-based-access-control/overview.md) (RBAC) för att hantera vem som har åtkomst till Azure-resurser. Den här skissen hjälper dig att kontrollera åtkomsten till Azure-resurser genom att tilldela sju [Azure-principdefinitioner.](../../../policy/overview.md) Dessa principer granskar användningen av resurstyper och konfigurationer som kan ge mer tillåtande åtkomst till resurser.
Att förstå resurser som bryter mot dessa principer kan hjälpa dig att vidta korrigerande åtgärder för att säkerställa att åtkomsten till Azure-resurser är begränsad till behöriga användare.

- \[Preview:\]Distribuera VM-tillägg för granskning av Linux VM-konton utan lösenord
- \[Preview\]: Distribuera VM-tillägg för att granska Linux VM så att fjärranslutningar från konton utan lösenord
- \[Preview:\]Granska Linux VM-konton utan lösenord
- \[Preview\]: Audit Linux VM tillåter fjärranslutningar från konton utan lösenord
- Granskningsanvändning av klassiska lagringskonton
- Granskningsanvändning av klassiska virtuella datorer
- Granska virtuella datorer som inte använder hanterade diskar

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 Hantering av rättigheter för privilegierad tillgång

Den här skissen hjälper dig att begränsa och kontrollera privilegierade åtkomsträttigheter genom att tilldela fyra [Azure-principdefinitioner](../../../policy/overview.md) för att granska externa konton med ägare och/eller skrivbehörighet och konton med ägare och/eller skrivbehörighet som inte har multifaktorautentisering aktiverat. Azure implementerar rollbaserad åtkomstkontroll (RBAC) för att hantera vem som har åtkomst till Azure-resurser. Den här skissen tilldelar också tre Azure-principdefinitioner för granskning av Azure Active Directory-autentisering för SQL-servrar och tjänstinfrastruktur. Med Hjälp av Azure Active Directory-autentisering möjliggör förenklad behörighetshantering och centraliserad identitetshantering för databasanvändare och andra Microsoft-tjänster. Den här skissen tilldelar också en Azure-principdefinition för att granska användningen av anpassade RBAC-regler. Att förstå var anpassade RBAC-regler implementeras kan hjälpa dig att verifiera behov och korrekt implementering, eftersom anpassade RBAC-regler är felbenägna.

- \[Förhandsgranska\]: Granskningskonton med ägarbehörigheter som inte är MFA-aktiverade på en prenumeration
- \[Förhandsgranska\]: Granskningskonton med skrivbehörigheter som inte är MFA-aktiverade på en prenumeration
- \[Förhandsgranska\]: Granska externa konton med ägarbehörigheter för en prenumeration
- \[Förhandsgranska\]: Granska externa konton med skrivbehörigheter för en prenumeration
- Granskningsetablering av en Azure Active Directory-administratör för SQL-server
- Granska användning av Azure Active Directory för klientautentisering i Service Fabric
- Granskningsanvändning av anpassade RBAC-regler

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 Hantering av hemlig autentiseringsinformation för användare

Den här skissen tilldelar tre [Azure-principdefinitioner](../../../policy/overview.md) till granskningskonton som inte har multifaktorautentisering aktiverat. Multifaktorautentisering hjälper till att skydda konton även om en del av autentiseringsinformationen äventyras. Genom att övervaka konton utan multifaktorautentisering aktiverat kan du identifiera konton som kan vara mer benägna att komprometteras. Den här skissen tilldelar också två Azure Policy-definitioner som granskar lösenordsfiler för Linux VM för att avisera om de är felaktigt inställda. Med den här inställningen kan du vidta korrigerande åtgärder för att säkerställa att autentiserare inte äventyras.

- \[Förhandsgranska\]: Granskningskonton med ägarbehörigheter som inte är MFA-aktiverade på en prenumeration
- \[Förhandsgranska\]: Granskningskonton med läsbehörigheter som inte är MFA-aktiverade på en prenumeration
- \[Förhandsgranska\]: Granskningskonton med skrivbehörigheter som inte är MFA-aktiverade på en prenumeration
- \[Förhandsversion:\]Distribuera VM-tillägg för granskning av Linux VM-passwd-filbehörigheter
- \[Preview:\]Audit Linux VM /etc/passwd-filbehörigheter är inställda på 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 Granskning av användaråtkomsträttigheter

Azure implementerar [rollbaserad åtkomstkontroll](../../../../role-based-access-control/overview.md) (RBAC) som hjälper dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure-portalen kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelar fyra [Azure-principdefinitioner](../../../policy/overview.md) till granskningskonton som ska prioriteras för granskning, inklusive avskrivna konton och externa konton med förhöjda behörigheter.

- \[Förhandsgranskning\]: Granska inaktuella konton för en prenumeration
- \[Förhandsgranskning\]: Granska inaktuella konton med ägarbehörigheter för en prenumeration
- \[Förhandsgranska\]: Granska externa konton med ägarbehörigheter för en prenumeration
- \[Förhandsgranska\]: Granska externa konton med skrivbehörigheter för en prenumeration

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 Borttagning eller justering av nyttjanderätter

Azure implementerar [rollbaserad åtkomstkontroll](../../../../role-based-access-control/overview.md) (RBAC) som hjälper dig att hantera vem som har åtkomst till resurser i Azure. Med Hjälp av [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) och RBAC kan du uppdatera användarroller för att återspegla organisationsändringar. Vid behov kan konton blockeras från att logga in (eller tas bort), vilket omedelbart tar bort åtkomsträttigheter till Azure-resurser. Den här skissen tilldelar två [Azure-principdefinitioner](../../../policy/overview.md) för granskning av det avskrivna kontot som bör beaktas för borttagning.

- \[Förhandsgranskning\]: Granska inaktuella konton för en prenumeration
- \[Förhandsgranskning\]: Granska inaktuella konton med ägarbehörigheter för en prenumeration

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 Säkra inloggningsprocedurer

Den här skissen tilldelar tre Azure-principdefinitioner till granskningskonton som inte har multifaktorautentisering aktiverat. Azure Multi-Factor Authentication ger ytterligare säkerhet genom att kräva en andra form av autentisering och ger stark autentisering. Genom att övervaka konton utan multifaktorautentisering aktiverat kan du identifiera konton som kan vara mer benägna att komprometteras.

- \[Förhandsgranska\]: Granskningskonton med ägarbehörigheter som inte är MFA-aktiverade på en prenumeration
- \[Förhandsgranska\]: Granskningskonton med läsbehörigheter som inte är MFA-aktiverade på en prenumeration
- \[Förhandsgranska\]: Granskningskonton med skrivbehörigheter som inte är MFA-aktiverade på en prenumeration

## <a name="a943-password-management-system"></a>A.9.4.3 System för lösenordshantering

Den här skissen hjälper dig att framtvinga starka lösenord genom att tilldela 10 [Azure-principdefinitioner](../../../policy/overview.md) som granskar virtuella datorer i Windows som inte tillämpar minsta styrka och andra lösenordskrav. Medvetenhet om virtuella datorer i strid med principen om lösenordsstyrka hjälper dig att vidta korrigerande åtgärder för att säkerställa att lösenord för alla vm-användarkonton är kompatibla med principen.

- \[Förhandsversion:\]Distribuera VM-tillägg för granskning av Windows VM framtvingar krav på lösenordskomplexitet
- \[Förhandsversion:\]Distribuera VM-tillägg för granskning av högsta lösenordsålder för Windows VM 70 dagar
- \[Förhandsversion:\]Distribuera VM-tillägg för granskning av windows VM-minimilösenordålder 1 dag
- \[Förhandsversion:\]Distribuera VM-tillägg för granskning av lösenord för Windows VM måste vara minst 14 tecken
- \[Förhandsversion:\]Distribuera VM-tillägg för granskning Windows VM bör inte tillåta tidigare 24 lösenord
- \[Förhandsversion:\]Granska Windows VM tillämpar krav på lösenordskomplexitet
- \[Preview:\]Granska Windows VM högsta lösenord ålder 70 dagar
- \[Preview:\]Granska Windows VM lägsta lösenord ålder 1 dag
- \[Förhandsgranskning:\]Audit Windows VM-lösenord måste vara minst 14 tecken
- \[Preview\]: Granska Windows VM bör inte tillåta tidigare 24 lösenord

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 Policy för användning av kryptografiska kontroller

Den här skissen hjälper dig att tillämpa din princip om användningen av kryptografkontroller genom att tilldela 13 [Azure-principdefinitioner](../../../policy/overview.md) som framtvingar specifika kryptografkontroller och granskningsanvändning av svaga kryptografiska inställningar.
Om du förstår var dina Azure-resurser kan ha icke-optimala kryptografiska konfigurationer kan du vidta korrigerande åtgärder för att säkerställa att resurser konfigureras i enlighet med din informationssäkerhetsprincip. De principer som tilldelas av den här skissen kräver kryptering för blob-lagringskonton och lagringskonton för datasjöar. kräver transparent datakryptering i SQL-databaser. Granska kryptering som saknas på lagringskonton, SQL-databaser, virtuella datordiskar och automatiseringskontovariabler. granska osäkra anslutningar till lagringskonton, funktionsappar, webbappar, API-appar och Redis-cache. granska svag kryptering av lösenord för virtuella datorer. och granska okrypterad service fabric-kommunikation.

- \[Förhandsversion:\]Granska HTTPS endast åtkomst för en funktionsapp
- \[Förhandsgranska:\]Granska HTTPS endast åtkomst för ett webbprogram
- \[Förhandsversion:\]Granska HTTPS endast åtkomst för en API-app
- \[Förhandsversion\]: Granskning saknas blob-kryptering för lagringskonton
- \[Preview\]: Distribuera VM-tillägg för granskning Windows VM bör inte lagra lösenord med reversibel kryptering
- \[Preview\]: Granska Windows VM bör inte lagra lösenord med reversibla kryptering
- \[Förhandsversion:\]Övervaka okrypterade VM-diskar i Azure Security Center
- Granskningsaktivering av kryptering av automationskontovariabler
- Granskning som aktiverar endast säkra anslutningar till Redis-cachen
- Granska säker överföring till lagringskonton
- Granska inställningen för egenskapen ClusterProtectionLevel för att kryptera OchSign i service fabric
- Granska status för transparent datakryptering
- Transparent datakryptering i SQL-databaser bör aktiveras

## <a name="a1241-event-logging"></a>A.12.4.1 Loggning av evenemang

Den här skissen hjälper dig att se till att systemhändelser loggas genom att tilldela sju [Azure-principdefinitioner](../../../policy/overview.md) som granskar logginställningar på Azure-resurser.
Diagnostikloggar ger insikt i åtgärder som utfördes inom Azure-resurser.

- \[Förhandsversion\]: Distribution av granskningsberoendeagent – OS-avbildning (VM)
- \[Förhandsversion:\]Distribution av granskningsberoendeagent i VMSS - VM-avbildning (OS) olistad
- \[Förhandsversion:\]Distribution av granskningslogganalysagent – OS-avbildning (VM)
- \[Förhandsversion:\]Distribution av granskningslogganalysagent i VMSS - VM-avbildning (OS) olistad
- Granska diagnostikinställning
- Granska granskningsinställningar på SQL-servernivå
- Granskning bör aktiveras vid avancerade datasäkerhetsinställningar på SQL Server

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 Administratörs- och operatörsloggar

Den här skissen hjälper dig att se till att systemhändelser loggas genom att tilldela sju Azure-principdefinitioner som granskar logginställningar på Azure-resurser. Diagnostikloggar ger insikt i åtgärder som utfördes inom Azure-resurser.

- \[Förhandsversion\]: Distribution av granskningsberoendeagent – OS-avbildning (VM)
- \[Förhandsversion:\]Distribution av granskningsberoendeagent i VMSS - VM-avbildning (OS) olistad
- \[Förhandsversion:\]Distribution av granskningslogganalysagent – OS-avbildning (VM)
- \[Förhandsversion:\]Distribution av granskningslogganalysagent i VMSS - VM-avbildning (OS) olistad
- Granska diagnostikinställning
- Granska granskningsinställningar på SQL-servernivå
- Granskning bör aktiveras vid avancerade datasäkerhetsinställningar på SQL Server

## <a name="a1244-clock-synchronization"></a>A.12.4.4 Klocksynkronisering

Den här skissen hjälper dig att se till att systemhändelser loggas genom att tilldela sju Azure-principdefinitioner som granskar logginställningar på Azure-resurser. Azure-loggar förlitar sig på synkroniserade interna klockor för att skapa en tidsrelerad post av händelser över resurser.

- \[Förhandsversion\]: Distribution av granskningsberoendeagent – OS-avbildning (VM)
- \[Förhandsversion:\]Distribution av granskningsberoendeagent i VMSS - VM-avbildning (OS) olistad
- \[Förhandsversion:\]Distribution av granskningslogganalysagent – OS-avbildning (VM)
- \[Förhandsversion:\]Distribution av granskningslogganalysagent i VMSS - VM-avbildning (OS) olistad
- Granska diagnostikinställning
- Granska granskningsinställningar på SQL-servernivå
- Granskning bör aktiveras vid avancerade datasäkerhetsinställningar på SQL Server

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 Installation av programvara på driftsystem

Adaptiv programkontroll är en lösning från Azure Security Center som hjälper dig att styra vilka program som kan köras på dina virtuella datorer i Azure. Den här skissen tilldelar en Azure-principdefinition som övervakar ändringar i uppsättningen tillåtna program. Den här funktionen hjälper dig att styra installation av programvara och program på virtuella Azure-datorer.

- \[Förhandsversion\]: Övervaka möjlig app vitlistning i Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 Hantering av tekniska sårbarheter

Den här skissen hjälper dig att hantera sårbarheter i informationssystem genom att tilldela fem [Azure-principdefinitioner](../../../policy/overview.md) som övervakar systemuppdateringar som saknas, sårbarheter i operativsystemet, SQL-sårbarheter och sårbarheter för virtuella datorer i Azure Security Center. Azure Security Center tillhandahåller rapporteringsfunktioner som gör att du kan ha insikt i realtid om säkerhetstillståndet för distribuerade Azure-resurser.

- \[Förhandsversion:\]Övervakare som saknas slutpunktsskydd i Azure Security Center
- \[Förhandsversion:\]Övervaka saknade systemuppdateringar i Azure Security Center
- \[Förhandsversion:\]Övervaka os-sårbarheter i Azure Security Center
- \[Förhandsversion\]: Övervaka SQL-sårbarhetsbedömningsresultat i Azure Security Center
- \[Förhandsversion:\]Övervaka vm-sårbarheter i Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 Begränsningar för installation av programvara

Adaptiv programkontroll är en lösning från Azure Security Center som hjälper dig att styra vilka program som kan köras på dina virtuella datorer i Azure. Den här skissen tilldelar en Azure-principdefinition som övervakar ändringar i uppsättningen tillåtna program. Begränsningar för programvaruinstallation kan hjälpa dig att minska sannolikheten för att programvaran ska införas.

- \[Förhandsversion\]: Övervaka möjlig app vitlistning i Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 Nätverkskontroller

Den här skissen hjälper dig att hantera och kontrollera nätverk genom att tilldela en [Azure Policy-definition](../../../policy/overview.md) som övervakar nätverkssäkerhetsgrupper med tillåtande regler. Regler som är alltför tillåtande kan tillåta oavsiktlig nätverksåtkomst och bör ses över. Den här skissen tilldelar också tre Azure-principdefinitioner som övervakar oskyddade slutpunkter, program och lagringskonton. Slutpunkter och program som inte skyddas av en brandvägg och lagringskonton med obegränsad åtkomst kan ge oavsiktlig åtkomst till information som finns i informationssystemet.

- \[Förhandsversion\]: Övervaka tillåtande nätverksåtkomst i Azure Security Center
- \[Förhandsversion\]: Övervaka oskyddade nätverksslutpunkter i Azure Security Center
- \[Förhandsversion\]: Övervaka oskyddat webbprogram i Azure Security Center
- Granska obegränsad nätverksåtkomst till lagringskonton

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Riktlinjer och förfaranden för informationsöverföring

Skissen hjälper dig att se till att informationsöverföring med Azure-tjänster är säker genom att tilldela två [Azure-principdefinitioner](../../../policy/overview.md) för att granska osäkra anslutningar till lagringskonton och Redis-cache.

- Granskning som aktiverar endast säkra anslutningar till Redis-cachen
- Granska säker överföring till lagringskonton

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontrollmappningen av skissen för ISO 27001 Shared Services kan du läsa följande artiklar om arkitekturen och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [Skiss för delade tjänster i ISO 27001 – översikten](./index.md)
> [för skissen för delade tjänster i ISO 27001 – driftsättningssteg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Läs mer om [skisslivscykeln](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).