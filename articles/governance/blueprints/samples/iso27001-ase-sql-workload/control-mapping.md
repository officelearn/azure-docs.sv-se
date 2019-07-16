---
title: Exempel - ISO 27001 ASE/SQL arbetsbelastning skissen - kontroll mappning
description: Kontrollen mappning av ISO 27001 App Service Environment/SQL-databas arbetsbelastning skissen exemplet till Azure Policy och RBAC.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 59e47c448f58235114c8fb3147637b77dd5fcf23
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226055"
---
# <a name="control-mapping-of-the-iso-27001-asesql-workload-blueprint-sample"></a>Mappning av kontroll av ISO 27001 ASE/SQL arbetsbelastning skissen exemplet

I följande artikel beskriver hur Azure skisser ISO 27001 ASE/SQL-arbetsbelastningen skissen exemplet mappas till ISO 27001-kontroller. Läs mer om kontrollerna, [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Följande mappningar avser den **ISO 27001: 2013** kontroller. Använd navigeringen till höger för att gå direkt till en viss kontroll-mappning. Många av de mappade kontrollerna implementeras med en [Azure Policy](../../../policy/overview.md) initiativ. Om du vill granska fullständig initiativet öppna **princip** i Azure-portalen och välj den **definitioner** sidan. Hitta och välj sedan den  **\[förhandsversion\] gransknings-och ISO 27001: 2013 styr och distribuera specifika VM-tillägg för att stödja granskningskrav** inbyggd princip initiativ.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 ansvarsfördelning

Med bara en Azure-prenumerationsägare kan inte för administrativa redundans. Däremot kan har för många Azure-prenumerationsägare öka risken för ett intrång via en komprometterad ägare-konto. Den här skissen hjälper dig att bibehålla ett lämpligt antal Azure-prenumerationsägare genom att tilldela två [Azure Policy](../../../policy/overview.md) definitioner som granska antalet ägare för Azure-prenumerationer. Hantera prenumeration ägarbehörighet kan hjälpa dig att implementera lämpliga uppdelning av uppgifter.

- \[Förhandsversion av\]: Granska minsta antalet ägare för prenumeration
- \[Förhandsversion av\]: Granska högsta antalet ägare för en prenumeration

## <a name="a821-classification-of-information"></a>A.8.2.1 klassificering av information

Azures [Sårbarhetsbedömning för SQL-tjänsten](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) kan hjälpa dig att identifiera känsliga data som lagras i dina databaser och innehåller rekommendationer för att klassificera data. Den här skissen tilldelar en [Azure Policy](../../../policy/overview.md) definitionen för att granska att sårbarheter som identifieras under genomsökningen för Sårbarhetsbedömning för SQL har åtgärdats.

- \[Förhandsversion av\]: Övervaka SQL sårbarhetsbedömning resulterar i Azure Security Center

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 åtkomst till nätverk och nätverkstjänster

Azure implementerar [rollbaserad åtkomstkontroll](../../../../role-based-access-control/overview.md) (RBAC) för att hantera vem som har åtkomst till Azure-resurser. Den här skissen hjälper dig att styra åtkomsten till Azure-resurser genom att tilldela sju [Azure Policy](../../../policy/overview.md) definitioner. Dessa principer granska användning av resurstyper och konfigurationer som kan tillåta mer Tillåtande åtkomst till resurser.
Förstå resurser som bryter mot dessa principer kan du vidta åtgärder för att kontrollera åtkomst till Azure-resurser hjälp är begränsad till behöriga användare.

- \[Förhandsversion av\]: Distribuera VM-tillägget för att granska Linux VM-konton med inga lösenord
- \[Förhandsversion av\]: Distribuera VM-tillägget för att granska Linux VM att tillåta fjärranslutningar från konton med inga lösenord
- \[Förhandsversion av\]: Gransknings-och Linux VM-konton med inga lösenord
- \[Förhandsversion av\]: Gransknings-och Linux VM att tillåta fjärranslutningar från konton med inga lösenord
- Granska användning av klassiska lagringskonton
- Granska användning av klassiska virtuella datorer
- Granska virtuella datorer som inte använder hanterade diskar

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 hanteringen av privilegierad åtkomst

Den här skissen hjälper dig att begränsa och kontrollera rättigheter för privilegierad åtkomst genom att tilldela fyra [Azure Policy](../../../policy/overview.md) definitioner för att granska externa konton med ägare och/eller skriva behörigheter och konton med ägare och/eller skrivbehörighet som inte har aktiverat multifaktorautentisering. Azure implementerar rollbaserad åtkomstkontroll (RBAC) för att hantera vem som har åtkomst till Azure-resurser. Den här skissen tilldelar också tre Azure Policy-definitioner för att granska användning av Azure Active Directory-autentisering för SQL-servrar och Service Fabric. Autentisering möjliggör med Azure Active Directory förenklad behörighetshantering och centraliserad Identitetshantering för databasanvändare och andra Microsoft-tjänster. Den här skissen tilldelar också en Azure Policy definition granska användningen av anpassade RBAC-regler. Förstå där anpassade RBAC-regler är implementera kan hjälpa dig att kontrollera behöver och rätt implementering som anpassade RBAC-regler är felbenägna.

- \[Förhandsversion av\]: Granska konton med ägarbehörigheter som inte är MFA är aktiverat på en prenumeration
- \[Förhandsversion av\]: Granska konton med skrivbehörigheter som inte är MFA är aktiverat på en prenumeration
- \[Förhandsversion av\]: Granska externa konton med ägarbehörigheter för en prenumeration
- \[Förhandsversion av\]: Granska externa konton med skrivbehörigheter för en prenumeration
- Granska etablering av en Azure Active Directory-administratör för SQLServer
- Granska användning av Azure Active Directory för klientautentisering i Service Fabric
- Granska användning av anpassade RBAC-regler

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 hantering av hemliga autentiseringsinformation för användare

Den här skissen tilldelas tre [Azure Policy](../../../policy/overview.md) definitioner för att granska konton som inte har aktiverat multifaktorautentisering. Multifaktorautentisering hjälper till att skydda konton även om en del av autentiseringsinformationen komprometteras. Genom att övervaka konton utan multifaktorautentisering aktiverat kan identifiera du konton som kan vara mer troligt systemproblem. Den här skissen tilldelar också två Azure principdefinitioner som gransknings-och Linux VM lösenord filbehörigheter att varna om de har angetts felaktigt. Den här konfigurationen kan du vidta åtgärder för att se till autentiserare inte komprometteras.

- \[Förhandsversion av\]: Granska konton med ägarbehörigheter som inte är MFA är aktiverat på en prenumeration
- \[Förhandsversion av\]: Granska konton med läsbehörigheter som inte är MFA är aktiverat på en prenumeration
- \[Förhandsversion av\]: Granska konton med skrivbehörigheter som inte är MFA är aktiverat på en prenumeration
- \[Förhandsversion av\]: Distribuera VM-tillägget för att granska Linux VM passwd filbehörigheter
- \[Förhandsversion av\]: Granska Linux VM/etc/passwd filbehörigheter är inställda på 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 granskning av användarbehörigheter

Azure implementerar [rollbaserad åtkomstkontroll](../../../../role-based-access-control/overview.md) (RBAC) för att hjälpa dig att hantera vem som har åtkomst till resurser i Azure. Med Azure-portalen kan granska du vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelas fyra [Azure Policy](../../../policy/overview.md) definitioner för att granska konton som ska prioriteras för granskning, inklusive avskriven konton och externa konton med förhöjd behörighet.

- \[Förhandsversion av\]: Granska inaktuella konton på en prenumeration
- \[Förhandsversion av\]: Granska inaktuella konton med ägarbehörigheter för en prenumeration
- \[Förhandsversion av\]: Granska externa konton med ägarbehörigheter för en prenumeration
- \[Förhandsversion av\]: Granska externa konton med skrivbehörigheter för en prenumeration

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 borttagning eller inställning av behörighet

Azure implementerar [rollbaserad åtkomstkontroll](../../../../role-based-access-control/overview.md) (RBAC) för att hjälpa dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) och RBAC, kan du uppdatera användarroller organisatoriska förändringar. När de behövs kan konton blockeras från att logga in (eller tas bort), som direkt tar bort åtkomsträttigheter till Azure-resurser. Den här skissen tilldelas två [Azure Policy](../../../policy/overview.md) definitioner för att granska avskriven konto som du bör överväga att tas bort.

- \[Förhandsversion av\]: Granska inaktuella konton på en prenumeration
- \[Förhandsversion av\]: Granska inaktuella konton med ägarbehörigheter för en prenumeration

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 säker inloggning procedurer

Den här skissen tilldelas tre principdefinitioner i Azure att granska konton som inte har aktiverat multifaktorautentisering. Azure Multi-Factor Authentication ger ökad säkerhet genom att kräva ett andra formen av autentisering och du får stark autentisering. Genom att övervaka konton utan multifaktorautentisering aktiverat kan identifiera du konton som kan vara mer troligt systemproblem.

- \[Förhandsversion av\]: Granska konton med ägarbehörigheter som inte är MFA är aktiverat på en prenumeration
- \[Förhandsversion av\]: Granska konton med läsbehörigheter som inte är MFA är aktiverat på en prenumeration
- \[Förhandsversion av\]: Granska konton med skrivbehörigheter som inte är MFA är aktiverat på en prenumeration

## <a name="a943-password-management-system"></a>A.9.4.3 hanteringssystem för klientlösenord

Den här skissen hjälper dig att genomdriva starka lösenord genom att tilldela 10 [Azure Policy](../../../policy/overview.md) definitioner som Granska Windows virtuella datorer som inte framtvingar minsta styrka och andra lösenordskrav. Feltolerans för virtuella datorer i överträdelse av styrka lösenordsprincip kan du vidta åtgärder för att säkerställa att lösenord för användarkonton för alla virtuella datorer är kompatibla med principen.

- \[Förhandsversion av\]: Distribuera VM-tillägget för att granska Windows VM tillämpar kraven på lösenordskomplexitet
- \[Förhandsversion av\]: Distribuera VM-tillägget för att granska Windows VM högsta ålder för lösenord 70 dagar
- \[Förhandsversion av\]: Distribuera VM-tillägget för att granska Windows VM lägsta ålder för lösenord 1 dag
- \[Förhandsversion av\]: Distribuera VM-tillägget för gransknings-och Windows-VM-lösenord måste vara minst 14 tecken
- \[Förhandsversion av\]: Distribuera VM-tillägget för att granska tidigare 24 lösenord ska inte tillåta att Windows-VM
- \[Förhandsversion av\]: Granska Windows VM tillämpar kraven på lösenordskomplexitet
- \[Förhandsversion av\]: Granska Windows VM högsta ålder för lösenord 70 dagar
- \[Förhandsversion av\]: Granska Windows VM lägsta ålder för lösenord 1 dag
- \[Förhandsversion av\]: Granska Windows VM lösenord måste innehålla minst 14 tecken
- \[Förhandsversion av\]: Granska Windows VM bör inte tillåta tidigare 24 lösenord

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 principen på användningen av kryptografiska kontroller

Den här skissen kan du tillämpa principen på användningen av cryptograph kontroller genom att tilldela 13 [Azure Policy](../../../policy/overview.md) definitioner som tillämpa specifika cryptograph kontroller och granska användning av svaga kryptografiska inställningar.
Förstå där dina Azure-resurser kan ha icke-optimala kryptografiska konfigurationer kan du vidta åtgärder för att se till att resurser som är konfigurerade i enlighet med din säkerhetsprincip för information. Mer specifikt Kräv de principer som tilldelats av den här skissen kryptering för blob storage-konton och data lake storage-konton; Kräv transparent datakryptering på SQL-databaser Granska saknas kryptering på storage-konton, SQL-databaser, virtuella diskar och variabler för automation-konto. Granska osäkert anslutningar till storage-konton, Funktionsappar, Web App, API Apps och Redis Cache Granska kryptering av svaga VM lösenord; och granska dekrypterade Service Fabric-kommunikation.

- \[Förhandsversion av\]: Granska endast HTTPS-åtkomst för en Funktionsapp
- \[Förhandsversion av\]: Granska endast HTTPS-åtkomst för ett webbprogram
- \[Förhandsversion av\]: Granska endast HTTPS-åtkomst för en API-App
- \[Förhandsversion av\]: Granska blobbkryptering som saknas för lagringskonton
- \[Förhandsversion av\]: Distribuera VM-tillägget för att granska Windows VM ska inte lagra lösenord med omvändbar kryptering
- \[Förhandsversion av\]: Granska Windows VM ska inte lagra lösenord med omvändbar kryptering
- \[Förhandsversion av\]: Övervaka okrypterade SQL-databas i Azure Security Center
- \[Förhandsversion av\]: Övervaka okrypterade Virtuella Datordiskar i Azure Security Center
- Granska aktivering av kryptering av variabler för Automation-konto
- Granska aktiverar endast säkra anslutningar till din Redis Cache
- Granska säker överföring till storage-konton
- Granska inställning av ClusterProtectionLevel egenskap EncryptAndSign i Service Fabric
- Granska status för transparent datakryptering

## <a name="a1241-event-logging"></a>A.12.4.1 händelseloggning

Den här skissen hjälper dig att säkerställa händelser loggas genom att tilldela sju [Azure Policy](../../../policy/overview.md) definitioner som gransknings-och inställningar på Azure-resurser.
Diagnostikloggar ger information om åtgärder som utförts i Azure-resurser.

- \[Förhandsversion av\]: Granska beroende Agentdistribution - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Granska beroende Agentdistribution i VMSS - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Granska Agentdistribution för Log Analytics - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Granska Log Analytics-Agentdistribution i VMSS - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Övervaka ogranskad SQL-databas i Azure Security Center
- Granska diagnostikinställning
- Granska nivå granskningsinställningarna för SQL server

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 administratör och operatorn loggar

Den här skissen hjälper dig att säkerställa händelser loggas genom att tilldela sju Azure principdefinitioner som gransknings-och inställningar på Azure-resurser. Diagnostikloggar ger information om åtgärder som utförts i Azure-resurser.

- \[Förhandsversion av\]: Granska beroende Agentdistribution - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Granska beroende Agentdistribution i VMSS - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Granska Agentdistribution för Log Analytics - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Granska Log Analytics-Agentdistribution i VMSS - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Övervaka ogranskad SQL-databas i Azure Security Center
- Granska diagnostikinställning
- Granska nivå granskningsinställningarna för SQL server

## <a name="a1244-clock-synchronization"></a>Synkronisering av datorklocka A.12.4.4

Den här skissen hjälper dig att säkerställa händelser loggas genom att tilldela sju Azure principdefinitioner som gransknings-och inställningar på Azure-resurser. Azure loggar är beroende av synkroniserade interna klockor för att skapa en tid korrelerad post av händelser över resurser.

- \[Förhandsversion av\]: Granska beroende Agentdistribution - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Granska beroende Agentdistribution i VMSS - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Granska Agentdistribution för Log Analytics - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Granska Log Analytics-Agentdistribution i VMSS - VM Image (OS) inte finns i listan
- \[Förhandsversion av\]: Övervaka ogranskad SQL-databas i Azure Security Center
- Granska diagnostikinställning
- Granska nivå granskningsinställningarna för SQL server

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 programvara installeras på operationssystem

Anpassningsbar programkontroll är lösning från Azure Security Center som hjälper dig att styra vilka program kan köras på din virtuella dator i Azure. Den här skissen tilldelar en Azure Policy definition som övervakar ändringar i en uppsättning tillåtna program. Den här funktionen hjälper dig att styra installationen av programvara och program på Azure Virtual Machines.

- \[Förhandsversion av\]: Övervaka möjliga tillåtna appar i Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>Hantering av A.12.6.1 för tekniska problem

Den här skissen hjälper dig att hantera information system sårbarheter genom att tilldela fem [Azure Policy](../../../policy/overview.md) definitioner som övervakar saknade systemuppdateringar, säkerhetsproblem med operativsystem, SQL-säkerhetsrisker och virtuell dator Säkerhetsproblem i Azure Security Center. Azure Security Center rapporteringsfunktioner som hjälper dig att ha i realtid insyn i säkerhetsläget för distribuerade Azure-resurser.

- \[Förhandsversion av\]: Övervaka saknad Endpoint Protection i Azure Security Center
- \[Förhandsversion av\]: Övervaka systemuppdateringar som saknas i Azure Security Center
- \[Förhandsversion av\]: Övervaka OS-säkerhetsproblem i Azure Security Center
- \[Förhandsversion av\]: Övervaka SQL sårbarhetsbedömning resulterar i Azure Security Center
- \[Förhandsversion av\]: Övervaka säkerhetsrisker i virtuell dator i Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 begränsningar för Programvaruinstallation

Anpassningsbar programkontroll är lösning från Azure Security Center som hjälper dig att styra vilka program kan köras på din virtuella dator i Azure. Den här skissen tilldelar en Azure Policy definition som övervakar ändringar i en uppsättning tillåtna program. Begränsningar för Programvaruinstallation kan hjälpa dig att minska sannolikheten för införande av säkerhetsproblem för programvaran.

- \[Förhandsversion av\]: Övervaka möjliga tillåtna appar i Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 nätverkskontroller

Den här skissen hjälper dig att hantera och styra nätverk genom att tilldela en [Azure Policy](../../../policy/overview.md) definition som övervakar nätverkssäkerhetsgrupper med Tillåtande regler. Regler som är alltför Tillåtande kan tillåta oönskade nätverksåtkomst och bör granskas. Den här skissen tilldelar också tre Azure principdefinitioner som övervakar oskyddade slutpunkter, program och lagringskonton. Slutpunkter och program som inte skyddas av en brandvägg och storage-konton med obegränsad åtkomst kan oavsiktlig tillgång till informationen i systemet.

- \[Förhandsversion av\]: Övervaka tillåten åtkomst till nätverk i Azure Security Center
- \[Förhandsversion av\]: Övervaka oskyddade nätverksslutpunkter i Azure Security Center
- \[Förhandsversion av\]: Övervaka oskyddat webbprogram i Azure Security Center
- Granska obegränsad nätverksåtkomst till storage-konton

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Information överföring principer och procedurer

Skissen hjälper dig att säkerställa informationsöverföring med Azure-tjänster är säker genom att tilldela två [Azure Policy](../../../policy/overview.md) definitioner för att granska osäkert anslutningar till storage-konton och Redis Cache.

- Granska aktiverar endast säkra anslutningar till din Redis Cache
- Granska säker överföring till storage-konton

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontroll mappningen av ISO 27001 App Service Environment/SQL-databas arbetsbelastning skissen exemplet, finns i följande artiklar för att lära dig om arkitekturen och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL-databas arbetsbelastning skissen - översikt](./index.md)
> [ISO 27001 App Service Environment/SQL-databas-arbetsbelastning skiss – distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Lär dig mer om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
