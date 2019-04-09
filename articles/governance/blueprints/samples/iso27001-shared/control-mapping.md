---
title: Exempel - ISO 27001 delade tjänster skissen - kontroll mappning
description: Kontrollen mappning av ISO 27001 delade tjänster skissen provet Azure Policy och RBAC.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 93a26311216ca5682c02a319f989b3d342a33ce1
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256489"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>Mappning av kontroll av ISO 27001 delade tjänster skissen exemplet

I följande artikel beskriver hur Azure skisser ISO 27001 delade tjänster skiss exemplet mappas till ISO 27001-kontroller. Läs mer om kontrollerna, [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Följande mappningar avser den **ISO 27001: 2013** kontroller. Använd navigeringen till höger för att gå direkt till en viss kontroll-mappning. Många av de mappade kontrollerna implementeras med en [Azure Policy](../../../policy/overview.md) initiativ. Om du vill granska fullständig initiativet öppna **princip** i Azure-portalen och välj den **definitioner** sidan. Hitta och välj sedan den  **[förhandsversion] gransknings-och ISO 27001: 2013 styr och distribuera specifika VM-tillägg för att stödja granskningskrav** inbyggd princip initiativ.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 ansvarsfördelning

Med bara en Azure-prenumerationsägare kan inte för administrativa redundans. Däremot kan har för många Azure-prenumerationsägare öka risken för ett intrång via en komprometterad ägare-konto. Den här skissen hjälper dig att bibehålla ett lämpligt antal Azure-prenumerationsägare genom att tilldela två [Azure Policy](../../../policy/overview.md) definitioner som granska antalet ägare för Azure-prenumerationer. Hantera prenumeration ägarbehörighet kan hjälpa dig att implementera lämpliga uppdelning av uppgifter.

- [Förhandsversion]: Audit minimum number of owners for subscription
- [Förhandsversion]: Audit maximum number of owners for a subscription

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 åtkomst till nätverk och nätverkstjänster

Azure implementerar [rollbaserad åtkomstkontroll](../../../../role-based-access-control/overview.md) (RBAC) för att hantera vem som har åtkomst till Azure-resurser. Den här skissen hjälper dig att styra åtkomsten till Azure-resurser genom att tilldela sju [Azure Policy](../../../policy/overview.md) definitioner. Dessa principer granska användning av resurstyper och konfigurationer som kan tillåta mer Tillåtande åtkomst till resurser.
Förstå resurser som bryter mot dessa principer kan du vidta åtgärder för att kontrollera åtkomst till Azure-resurser hjälp är begränsad till behöriga användare.

- [Förhandsversion]: Deploy VM extension to audit Linux VM accounts with no passwords
- [Förhandsversion]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  Lösenords
- [Förhandsversion]: Audit Linux VM accounts with no passwords
- [Förhandsversion]: Audit Linux VM allowing remote connections from accounts with no passwords
- Granska användningen av klassiska lagringskonton
- Granska användningen av klassiska virtuella datorer
- Granska virtuella datorer som inte använder hanterade diskar

## <a name="a922-user-access-provisioning"></a>Etableringen av A.9.2.2 användare åtkomst

Azure implementerar [rollbaserad åtkomstkontroll](../../../../role-based-access-control/overview.md) (RBAC) för att hantera vem som har åtkomst till Azure-resurser. Den här skissen tilldelas tre [Azure Policy](../../../policy/overview.md) definitioner för att granska användning av [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) autentisering för SQL-servrar och [Service Fabric](../../../../service-fabric/service-fabric-overview.md). Autentisering möjliggör med Azure Active Directory förenklad behörighetshantering och centraliserad Identitetshantering för databasanvändare och andra Microsoft-tjänster. Den här skissen tilldelar också en Azure Policy definition granska användningen av anpassade RBAC-regler. Förstå där anpassade RBAC-regler är implementera kan hjälpa dig att kontrollera behöver och rätt implementering som anpassade RBAC-regler är felbenägna.

- Granska tillhandahållande av Azure Active Directory-administratör för SQL Server
- Granska användning av Azure Active Directory för klientautentisering i Service Fabric
- Granska användning av anpassade RBAC-regler

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 hanteringen av privilegierad åtkomst

Den här skissen hjälper dig att begränsa och kontrollera rättigheter för privilegierad åtkomst genom att tilldela fyra [Azure Policy](../../../policy/overview.md) definitioner för att granska externa konton med ägare och/eller skriva behörigheter och konton med ägare och/eller skrivbehörighet som inte har aktiverat multifaktorautentisering.

- [Förhandsversion]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Förhandsversion]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Förhandsversion]: Audit external accounts with owner permissions on a subscription
- [Förhandsversion]: Audit external accounts with write permissions on a subscription

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 hantering av hemliga autentiseringsinformation för användare

Den här skissen tilldelas tre [Azure Policy](../../../policy/overview.md) definitioner för att granska konton som inte har aktiverat multifaktorautentisering. Multifaktorautentisering hjälper till att skydda konton även om en del av autentiseringsinformationen komprometteras. Genom att övervaka konton utan multifaktorautentisering aktiverat kan identifiera du konton som kan vara mer troligt systemproblem. Den här skissen tilldelar också två Azure principdefinitioner som gransknings-och Linux VM lösenord filbehörigheter att varna om de har angetts felaktigt. Den här konfigurationen kan du vidta åtgärder för att se till autentiserare inte komprometteras.

- [Förhandsversion]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Förhandsversion]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [Förhandsversion]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Förhandsversion]: Deploy VM extension to audit Linux VM passwd file permissions
- [Förhandsversion]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 granskning av användarbehörigheter

Azure implementerar [rollbaserad åtkomstkontroll](../../../../role-based-access-control/overview.md) (RBAC) för hjälper till att du hanterar vem som har åtkomst till resurser i Azure. Med Azure-portalen kan granska du vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelas fyra [Azure Policy](../../../policy/overview.md) definitioner för att granska konton som ska prioriteras för granskning, inklusive avskriven konton och externa konton med förhöjd behörighet.

- [Förhandsversion]: Audit deprecated accounts on a subscription
- [Förhandsversion]: Audit deprecated accounts with owner permissions on a subscription
- [Förhandsversion]: Audit external accounts with owner permissions on a subscription
- [Förhandsversion]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 borttagning eller inställning av behörighet

Azure implementerar [rollbaserad åtkomstkontroll](../../../../role-based-access-control/overview.md) (RBAC) för hjälper till att du hanterar vem som har åtkomst till resurser i Azure. Med hjälp av [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) och RBAC, kan du uppdatera användarroller organisatoriska förändringar. När de behövs kan konton blockeras från att logga in (eller tas bort), som direkt tar bort åtkomsträttigheter till Azure-resurser. Den här skissen tilldelas två [Azure Policy](../../../policy/overview.md) definitioner för att granska avskriven konto som du bör överväga att tas bort.

- [Förhandsversion]: Audit deprecated accounts on a subscription
- [Förhandsversion]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a943-password-management-system"></a>A.9.4.3 hanteringssystem för klientlösenord

Den här skissen hjälper dig att genomdriva starka lösenord genom att tilldela 10 [Azure Policy](../../../policy/overview.md) definitioner som Granska Windows virtuella datorer som inte framtvingar minsta styrka och andra lösenordskrav. Feltolerans för virtuella datorer i överträdelse av styrka lösenordsprincip kan du vidta åtgärder för att säkerställa att lösenord för användarkonton för alla virtuella datorer är kompatibla med principen.

- [Förhandsversion]: Deploy VM extension to audit Windows VM enforces password complexity requirements
- [Förhandsversion]: Deploy VM extension to audit Windows VM maximum password age 70 days
- [Förhandsversion]: Deploy VM extension to audit Windows VM minimum password age 1 day
- [Förhandsversion]: Deploy VM extension to audit Windows VM passwords must be at least 14 characters
- [Förhandsversion]: Deploy VM extension to audit Windows VM should not allow previous 24 passwords
- [Förhandsversion]: Audit Windows VM enforces password complexity requirements
- [Förhandsversion]: Audit Windows VM maximum password age 70 days
- [Förhandsversion]: Audit Windows VM minimum password age 1 day
- [Förhandsversion]: Audit Windows VM passwords must be at least 14 characters
- [Förhandsversion]: Audit Windows VM should not allow previous 24 passwords

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 principen på användningen av kryptografiska kontroller

Den här skissen kan du tillämpa principen på användningen av cryptograph kontroller genom att tilldela 13 [Azure Policy](../../../policy/overview.md) definitioner som tillämpa specifika cryptograph kontroller och granska användning av svaga kryptografiska inställningar.
Förstå där dina Azure-resurser kan ha icke-optimala kryptografiska konfigurationer kan du vidta åtgärder för att se till att resurser som är konfigurerade i enlighet med din säkerhetsprincip för information. Mer specifikt Kräv de principer som tilldelats av den här skissen kryptering för blob storage-konton och data lake storage-konton; Kräv transparent datakryptering på SQL-databaser Granska saknas kryptering på storage-konton, SQL-databaser, virtuella diskar och variabler för automation-konto. Granska osäkert anslutningar till storage-konton, Funktionsappar, Web App, API Apps och Redis Cache Granska kryptering av svaga VM lösenord; och granska dekrypterade Service Fabric-kommunikation.

- [Förhandsversion]: Audit HTTPS only access for a Function App
- [Förhandsversion]: Audit HTTPS only access for a Web Application
- [Förhandsversion]: Audit HTTPS only access for an API App
- [Förhandsversion]: Audit missing blob encryption for storage accounts
- [Förhandsversion]: Deploy VM extension to audit Windows VM should not store passwords using reversible
  krypteringn
- [Förhandsversion]: Audit Windows VM should not store passwords using reversible encryption
- [Förhandsversion]: Monitor unencrypted SQL database in Azure Security Center
- [Förhandsversion]: Monitor unencrypted VM Disks in Azure Security Center
- Granska aktivering av kryptering av Automation-kontovariabler
- Granska aktivering av enbart säkra anslutningar till Redis Cache
- Granska säker överföring till lagringskonton
- Granska att inställningen för egenskapen ClusterProtectionLevel är inställd på EncryptAndSign i Service Fabric
- Granska status för transparent datakryptering

## <a name="a1241-event-logging"></a>A.12.4.1 händelseloggning

Den här skissen hjälper dig att säkerställa händelser loggas genom att tilldela sju [Azure Policy](../../../policy/overview.md) definitioner som gransknings-och inställningar på Azure-resurser. En tilldelad princip granskar även om virtuella datorer inte skickar loggar till en angiven log analytics-arbetsyta.

- [Förhandsversion av]: Granska beroende Agentdistribution - VM Image (OS) inte finns i listan
- [Förhandsversion av]: Granska beroende Agentdistribution i VMSS - VM Image (OS) inte finns i listan
- [Förhandsversion av]: Granska Agentdistribution för Log Analytics - VM Image (OS) inte finns i listan
- [Förhandsversion av]: Granska Log Analytics-Agentdistribution i VMSS - VM Image (OS) inte finns i listan
- [Förhandsversion]: Monitor unaudited SQL database in Azure Security Center
- Granska diagnostikinställning
- Granska granskningsinställningar på SQL-servernivå

## <a name="a121-management-of-technical-vulnerabilities"></a>Hantering av A.12.1 för tekniska problem

Den här skissen hjälper dig att hantera information system sårbarheter genom att tilldela fem [Azure Policy](../../../policy/overview.md) definitioner som övervakar saknade systemuppdateringar, säkerhetsproblem med operativsystem, SQL-säkerhetsrisker och virtuell dator sårbarheter. Dessa insikter i realtid informera om säkerhetsläget för dina distribuerade resurser och kan hjälpa dig att prioritera åtgärder.

- [Förhandsversion]: Monitor missing Endpoint Protection in Azure Security Center
- [Förhandsversion]: Monitor missing system updates in Azure Security Center
- [Förhandsversion]: Monitor OS vulnerabilities in Azure Security Center
- [Förhandsversion]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Förhandsversion]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 begränsningar för Programvaruinstallation

Anpassningsbar programkontroll är lösning från Azure Security Center som hjälper dig att styra vilka program kan köras på din virtuella dator i Azure. Den här skissen tilldelar en Azure Policy definition som övervakar ändringar i en uppsättning tillåtna program. Begränsningar för Programvaruinstallation kan hjälpa dig att minska sannolikheten för införande av säkerhetsproblem för programvaran.

- [Förhandsversion]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 nätverkskontroller

Den här skissen hjälper dig att hantera och styra nätverk genom att tilldela en [Azure Policy](../../../policy/overview.md) definition som övervakar nätverkssäkerhetsgrupper med Tillåtande regler. Regler som är alltför Tillåtande kan tillåta oönskade nätverksåtkomst och bör granskas.

- [Förhandsversion]: Monitor permissive network access in Azure Security Center

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Information överföring principer och procedurer

Skissen hjälper dig att säkerställa informationsöverföring med Azure-tjänster är säker genom att tilldela två [Azure Policy](../../../policy/overview.md) definitioner för att granska osäkert anslutningar till storage-konton och Redis Cache.

- Granska aktivering av enbart säkra anslutningar till Redis Cache
- Granska säker överföring till lagringskonton

## <a name="a1413-protecting-application-services-transactions"></a>A.14.1.3 Protecting application services transaktioner

Den här skissen hjälper dig att skydda system informationstillgångar genom att tilldela tre [Azure Policy](../../../policy/overview.md) definitioner som övervaka oskyddade slutpunkter, program och lagringskonton. Slutpunkter och program som inte skyddas av en brandvägg och storage-konton med obegränsad åtkomst kan oavsiktlig tillgång till informationen i systemet.

- [Förhandsversion]: Monitor unprotected network endpoints in Azure Security Center
- [Förhandsversion]: Monitor unprotected web application in Azure Security Center
- Granska obegränsad nätverksåtkomst till lagringskonton

## <a name="a1613-reporting-information-security-weaknesses"></a>A.16.1.3 rapportering information security svagheter

Den här skissen hjälper dig att bibehålla medvetenhet om säkerhetsrisker i systemet genom att tilldela fem [Azure Policy](../../../policy/overview.md) definitioner som övervakar säkerhetsrisker, korrigeringsstatus och aviseringar om skadlig kod i Azure Security Center. Azure Security Center rapporteringsfunktioner som hjälper dig att ha i realtid insyn i säkerhetsläget för distribuerade Azure-resurser.

- [Förhandsversion]: Monitor missing Endpoint Protection in Azure Security Center
- [Förhandsversion]: Monitor missing system updates in Azure Security Center
- [Förhandsversion]: Monitor OS vulnerabilities in Azure Security Center
- [Förhandsversion]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Förhandsversion]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontroll mappningen av skissen ISO 27001 delade tjänster, finns i följande artiklar för att lära dig om arkitekturen och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [Delade tjänster för ISO 27001 skissen - översikt](./index.md)
> [ISO 27001 delade tjänster skiss – distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Lär dig mer om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).