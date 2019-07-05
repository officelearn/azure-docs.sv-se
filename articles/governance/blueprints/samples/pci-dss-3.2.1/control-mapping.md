---
title: Exempel - PCI-DSS v3.2.1 skissen - kontroll mappning
description: Kontrollen mappning av Payment Card Industry Data Security Standard v3.2.1 skissen exemplet till Azure Policy och RBAC.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 38b1cc6249da98e11167416c8e18d06de1645679
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540954"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Mappning av kontroll av PCI-DSS v3.2.1 skissen exemplet

I följande artikel beskriver hur Azure skisser PCI-DSS v3.2.1 skissen exemplet mappas till PCI-DSS v3.2.1 kontroller. Läs mer om kontrollerna, [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Följande mappningar avser den **PCI-DSS v3.2.1:2018** kontroller. Använd navigeringen till höger för att gå direkt till en viss kontroll-mappning. Många av de mappade kontrollerna implementeras med en [Azure Policy](../../../policy/overview.md) initiativ. Om du vill granska fullständig initiativet öppna **princip** i Azure-portalen och välj den **definitioner** sidan. Hitta och välj sedan den  **[förhandsversion] granska PCI v3.2.1:2018 styr och distribuera specifika VM-tillägg för att stödja granskningskrav** inbyggd princip initiativ.

## <a name="132-and-134-boundary-protection"></a>1.3.2 och 1.3.4 gräns-skydd

Den här skissen hjälper dig att hantera och styra nätverk genom att tilldela [Azure Policy](../../../policy/overview.md) definitioner som övervakar nätverkssäkerhetsgrupper med Tillåtande regler. Regler som är alltför Tillåtande kan tillåta oönskade nätverksåtkomst och bör granskas. Den här skissen tilldelas någon Azure principdefinitioner som övervakar oskyddade slutpunkter, program och lagringskonton. Slutpunkter och program som inte skyddas av en brandvägg och storage-konton med obegränsad åtkomst kan oavsiktlig tillgång till informationen i systemet.

- Granska obegränsad nätverksåtkomst till storage-konton
- Åtkomst via Internet-riktade slutpunkten ska vara begränsad

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1, 4.1.g, 4.1.h och 6.5.3 kryptering

Den här skissen kan du tillämpa principen med hjälp av cryptograph kontroller genom att tilldela [Azure Policy](../../../policy/overview.md) definitioner som framtvingar specifika cryptograph kontroller och granska användning av svaga kryptografiska inställningar. Förstå där dina Azure-resurser kan ha icke-optimala kryptografiska konfigurationer kan du vidta åtgärder för att se till att resurser som är konfigurerade i enlighet med din säkerhetsprincip för information. Mer specifikt kräver de principer som tilldelats av den här skissen transparent datakryptering på SQL-databaser Granska saknas kryptering på storage-konton och automation-konto variabler. Det finns också principer vilken adress granska osäkert anslutningar till storage-konton, Funktionsappar, WebApp, API Apps och Redis Cache och granska dekrypterade Service Fabric-kommunikation.

- Funktionen App bör enbart vara åtkomliga via HTTPS
- Webbprogram bör enbart vara åtkomliga via HTTPS
- API-App bör enbart vara åtkomliga via HTTPS
- Övervaka okrypterade SQL-databas i Azure Security Center
- Diskkryptering ska tillämpas på virtuella datorer
- Automation-konto variabler ska krypteras
- Endast säkra anslutningar till Redis-cachen ska aktiveras
- Säker överföring till storage-konton måste vara aktiverat
- Service Fabric-kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign
- Transparent datakryptering på SQL-databaser ska aktiveras
- Distribuera transparent datakryptering för SQL DB

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 och 11.2.1 Säkerhetsriskssökning och systemuppdateringar

Den här skissen hjälper dig att hantera information system sårbarheter genom att tilldela [Azure Policy](../../../policy/overview.md) definitioner som övervakar saknade systemuppdateringar, säkerhetsproblem med operativsystem, SQL-säkerhetsrisker och virtuell dator Säkerhetsproblem i Azure Security Center. Azure Security Center rapporteringsfunktioner som hjälper dig att ha i realtid insyn i säkerhetsläget för distribuerade Azure-resurser.

- Övervaka saknad Endpoint Protection i Azure Security Center
- Distribuera Microsofts IaaSAntimalware-Standardtillägg för Windows Server
- Distribuera Hotidentifiering på SQL-servrar
- Systemuppdateringar bör vara installerad på dina datorer
- Säkerhetsproblem i Säkerhetskonfiguration på dina datorer bör åtgärdas
- Säkerhetsproblem på SQL-databaser bör åtgärdas
- Sårbarheter som bör åtgärdas av en lösning för Sårbarhetsbedömning

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 och 7.1.3 uppdelning av uppgifter

Med bara en Azure-prenumerationsägare kan inte för administrativa redundans. Däremot kan har för många Azure-prenumerationsägare öka risken för ett intrång via en komprometterad ägare-konto. Den här skissen hjälper dig att bibehålla ett lämpligt antal Azure-prenumerationsägare genom att tilldela [Azure Policy](../../../policy/overview.md) definitioner som granska antalet ägare för Azure-prenumerationer. Hantera prenumeration ägarbehörighet kan hjälpa dig att implementera lämpliga uppdelning av uppgifter.

- Det bör finnas fler än en ägare till prenumerationen
- Högst 3 ägare bör anges för din prenumeration 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1.a och 8.3.1.b hantering av privilegierad åtkomsträttigheter

Den här skissen hjälper dig att begränsa och kontrollera rättigheter för privilegierad åtkomst genom att tilldela [Azure Policy](../../../policy/overview.md) definitioner för att granska externa konton med ägare, skriva och/eller läsa behörigheter och personalkonton med ägare och/eller Skriv behörigheter som inte har aktiverat multifaktorautentisering. Azure implementerar rollbaserad åtkomstkontroll (RBAC) för att hantera vem som har åtkomst till Azure-resurser. Förstå där anpassade RBAC-regler är implementera kan hjälpa dig att kontrollera behöver och rätt implementering som anpassade RBAC-regler är felbenägna. Detta också skiss tilldelar [Azure Policy](../../../policy/overview.md) definitioner för att granska användning av Azure Active Directory-autentisering för SQL-servrar. Med Azure Active Directory authentication förenklar hanteringen av behörighet och centralisera hanteringen av identiteter för databasanvändare och andra Microsoft-  
Tjänster.
 
- Externa konton med ägarbehörigheter som ska tas bort från prenumerationen
- Externa konton med skrivbehörigheter bör tas bort från prenumerationen
- Bör ta bort externa konton med läsbehörigheter från prenumerationen
- MFA måste vara aktiverad på konton med ägarbehörigheter för din prenumeration
- MFA ska vara aktiverad konton med skrivbehörigheter för din prenumeration
- MFA måste vara aktiverad på konton med läsbehörigheter för din prenumeration
- En Azure Active Directory-administratör ska etableras för SQL-servrar
- Granska användning av anpassade RBAC-regler

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 och 8.1.5 minst behörigheten och granskning av användaren åtkomstbehörigheter

Azure implementerar rollbaserad åtkomstkontroll (RBAC) till hjälper dig att hantera vem som har åtkomst till resurser i Azure. Med Azure-portalen kan granska du vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelar [Azure Policy](../../../policy/overview.md) definitioner för att granska konton som ska prioriteras för granskning, inklusive avskriven konton och externa konton med förhöjd behörighet.

- Bör ta bort inaktuella konton från prenumerationen
- Bör ta bort inaktuella konton med ägarbehörigheter från prenumerationen
- Externa konton med ägarbehörigheter som ska tas bort från prenumerationen
- Externa konton med skrivbehörigheter bör tas bort från prenumerationen
- Bör ta bort externa konton med läsbehörigheter från prenumerationen

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 borttagning eller inställning av behörighet

Azure implementerar rollbaserad åtkomstkontroll (RBAC) för att hjälpa dig att hantera vem som har åtkomst till resurser i Azure. Med Azure Active Directory och RBAC kan uppdatera du användarroller organisatoriska förändringar. När de behövs kan konton blockeras från att logga in (eller tas bort), som direkt tar bort åtkomsträttigheter till Azure-resurser. Den här skissen tilldelar [Azure Policy](../../../policy/overview.md) definitioner för att granska avskriven konto som du bör överväga att tas bort.

- Bör ta bort inaktuella konton från prenumerationen
- Bör ta bort inaktuella konton med ägarbehörigheter från prenumerationen

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b och 8.2.5 lösenordsbaserad autentisering

Den här skissen hjälper dig att genomdriva starka lösenord genom att tilldela [Azure Policy](../../../policy/overview.md) definitioner som Granska Windows virtuella datorer som inte framtvingar minsta styrka och andra lösenordskrav. Feltolerans för virtuella datorer i överträdelse av styrka lösenordsprincip kan du vidta åtgärder för att säkerställa att lösenord för användarkonton för alla virtuella datorer är kompatibla med principen.

- [Förhandsversion]: Audit Windows VMs that do not have a maximum password age of 70 days
- [Förhandsversion]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [Förhandsversion]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Förhandsversion]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Förhandsversion]: Audit Windows VMs that allow re-use of the previous 24 passwords
- [Förhandsversion]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords

## <a name="103-and-1054-audit-generation"></a>10.3 och 10.5.4 granska Generation

Den här skissen hjälper dig att säkerställa händelser loggas genom att tilldela [Azure Policy](../../../policy/overview.md) definitioner som gransknings-och inställningar på Azure-resurser.
Diagnostikloggar ger information om åtgärder som utförts i Azure-resurser. Azure loggar är beroende av synkroniserade interna klockor för att skapa en tid korrelerad post av händelser över resurser.

- Övervaka Ej granskad SQL-servrar i Azure Security Center
- Granska diagnostikinställning
- Granska nivå granskningsinställningarna för SQL server
- Distribuera granskning på SQL-servrar
- Storage-konton som ska migreras till nya Azure Resource Manager-resurser
- Virtuella datorer som ska migreras till nya Azure Resource Manager-resurser

## <a name="1236-and-1237-information-security"></a>12.3.6 och 12.3.7 informationssäkerhet

Den här skissen hjälper dig att hantera och kontroll över nätverket genom att tilldela [Azure Policy](../../../policy/overview.md) definitioner som granska godkända nätverksplatser och godkända företagets produkter som tillåts för miljön. Det här är anpassningsbara av varje företag via principparametrar i var och en av dessa principer.

- Tillåtna platser
- Tillåtna platser för resursgrupper

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontroll mappningen av PCI-DSS v3.2.1 skissen, finns i följande artiklar för att lära dig om översikten och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1 skissen - översikt](./index.md)
> [PCI-DSS v3.2.1 skiss – distribuera steg](./deploy.md)

## <a name="addition-articles-about-blueprints-and-how-to-use-them"></a>Ytterligare artiklar om skisser och hur de används:

- Lär dig mer om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
