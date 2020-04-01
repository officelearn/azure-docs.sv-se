---
title: PCI-DSS v3.2.1 skissprovkontroller
description: Kontrollera mappningen av skissexemplet Payment Card Industry Data Security Standard v3.2.1 till Azure Policy och RBAC.
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: db21ac9d628e777b6ff2cc86516cfb1497f5a62f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76905646"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Kontrollkartläggning av pci-DSS v3.2.1-skissprovet

I följande artikel beskrivs hur azure blueprints PCI-DSS v3.2.1-skissen mappar till PCI-DSS v3.2.1-kontrollerna. Mer information om kontrollerna finns i [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Följande mappningar är till **PCI-DSS v3.2.1:2018-kontrollerna.** Använd navigeringen till höger för att hoppa direkt till en specifik kontrollmappning. Många av de mappade kontrollerna implementeras med ett [Azure Policy-initiativ.](../../../policy/overview.md) Om du vill granska hela initiativet öppnar du **Princip** i Azure-portalen och väljer sidan **Definitioner.** Leta sedan reda på och välj ** \[kontrollerna Förhandsgranska\] gransknings-PCI v3.2.1:2018 och distribuera specifika VM-tillägg för att stödja inbyggda** principinitiativ för granskningskrav.

> [!IMPORTANT]
> Varje kontroll nedan är associerad med en eller flera [Azure-principdefinitioner.](../../../policy/overview.md) Dessa policyer kan hjälpa dig att [bedöma efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta inte en 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan refererar **kompatibel** i Azure-princip endast till principerna själva. Detta säkerställer inte att du är helt kompatibel med alla krav på en kontroll. Dessutom innehåller efterlevnadsstandarden kontroller som inte åtgärdas av några Azure-principdefinitioner just nu. Därför är efterlevnad i Azure Policy bara en partiell bild av din övergripande efterlevnadsstatus. Associationerna mellan kontroller och Azure-principdefinitioner för det härmplet för efterlevnadsritning kan ändras med tiden. Om du vill visa ändringshistoriken läser du [GitHub Commit-historiken](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md).

## <a name="132-and-134-boundary-protection"></a>1.3.2 och 1.3.4 Gränsskydd

Den här skissen hjälper dig att hantera och kontrollera nätverk genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som övervakar nätverkssäkerhetsgrupper med tillåtande regler. Regler som är alltför tillåtande kan tillåta oavsiktlig nätverksåtkomst och bör ses över. Den här skissen tilldelar en Azure-principdefinitioner som övervakar oskyddade slutpunkter, program och lagringskonton. Slutpunkter och program som inte skyddas av en brandvägg och lagringskonton med obegränsad åtkomst kan ge oavsiktlig åtkomst till information som finns i informationssystemet.

- Granska obegränsad nätverksåtkomst till lagringskonton
- Åtkomst via internetvänd slutpunkt bör begränsas

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1, 4.1.g, 4.1.h och 6.5.3 Kryptografiskt skydd

Den här skissen hjälper dig att tillämpa din princip med hjälp av kryptografkontroller genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som tillämpar specifika kryptografkontroller och granskningsanvändning av svaga kryptografiska inställningar. Om du förstår var dina Azure-resurser kan ha icke-optimala kryptografiska konfigurationer kan du vidta korrigerande åtgärder för att säkerställa att resurser konfigureras i enlighet med din informationssäkerhetsprincip. De principer som tilldelas av den här skissen kräver transparent datakryptering i SQL-databaser. granska kryptering som saknas på lagringskonton och automatiseringskontovariabler. Det finns också principer som behandlar granskning av osäkra anslutningar till lagringskonton, funktionsappar, WebApp, API-appar och Redis-cache och granskar okrypterad serviceinfrastrukturkommunikation.

- Funktionsappen ska endast vara tillgänglig via HTTPS
- Webbprogrammet ska endast vara tillgängligt via HTTPS
- API-app bör endast vara tillgänglig via HTTPS
- Transparent datakryptering i SQL-databaser bör aktiveras
- Diskkryptering bör tillämpas på virtuella datorer
- Variabler för automationskonto ska krypteras
- Endast säkra anslutningar till Redis-cachen ska aktiveras
- Säker överföring till lagringskonton ska vara aktiverat
- Service Fabric-kluster bör ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign
- Transparent datakryptering i SQL-databaser bör aktiveras
- Distribuera SQL DB-transparent datakryptering

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 och 11.2.1 Sårbarhetsskanning och systemuppdateringar

Den här skissen hjälper dig att hantera sårbarheter i informationssystem genom att tilldela [Azure-principdefinitioner](../../../policy/overview.md) som övervakar systemuppdateringar som saknas, sårbarheter i operativsystemet, SQL-sårbarheter och sårbarheter för virtuella datorer i Azure Security Center. Azure Security Center tillhandahåller rapporteringsfunktioner som gör att du kan ha insikt i realtid om säkerhetstillståndet för distribuerade Azure-resurser.

- Övervaka saknade slutpunktsskydd i Azure Security Center
- Distribuera standardtillägget Microsoft IaaSAntimalware för Windows Server
- Distribuera hotidentifiering på SQL-servrar
- Systemuppdateringar ska ha installerats på dina datorer
- Sårbarheter i säkerhetskonfigurationen på dina datorer bör åtgärdas
- Säkerhetsproblem i DINA SQL-databaser bör åtgärdas
- Sårbarheter bör åtgärdas med en sårbarhetsbedömningslösning

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 och 7.1.3 Tulldelning

Att bara ha en Azure-prenumerationsägare tillåter inte administrativ redundans. Omvänt kan för många Azure-prenumerationsägare öka risken för ett intrång via ett komprometterat ägarkonto. Den här skissen hjälper dig att underhålla ett lämpligt antal Azure-prenumerationsägare genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar antalet ägare för Azure-prenumerationer. Genom att hantera prenumerationsägare kan du implementera lämplig åtskillnad mellan uppgifter.

- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Högst 3 ägare bör utses för din prenumeration 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1.a och 8.3.1.b Hantering av rättigheter för privilegierad tillgång

Den här skissen hjälper dig att begränsa och kontrollera privilegierade åtkomsträttigheter genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) för att granska externa konton med ägare, skriv- och/eller läsbehörigheter och medarbetarkonton med ägare och/eller skrivbehörighet som inte har multifaktorautentisering aktiverat. Azure implementerar rollbaserad åtkomstkontroll (RBAC) för att hantera vem som har åtkomst till Azure-resurser. Att förstå var anpassade RBAC-regler implementeras kan hjälpa dig att verifiera behov och korrekt implementering, eftersom anpassade RBAC-regler är felbenägna. Den här skissen tilldelar också [Azure-principdefinitioner](../../../policy/overview.md) för granskning av Azure Active Directory-autentisering för SQL-servrar. Med Hjälp av Azure Active Directory-autentisering förenklas behörighetshantering och centraliserar identitetshantering för databasanvändare och andra Microsoft  
tjänster.
 
- Externa konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med skrivbehörighet ska tas bort från din prenumeration
- Externa konton med läsbehörighet bör tas bort från din prenumeration
- MFA ska aktiveras på konton med ägarbehörighet för din prenumeration
- MFA ska vara aktiverade konton med skrivbehörighet för din prenumeration
- MFA ska aktiveras på konton med läsbehörighet för din prenumeration
- En Azure Active Directory-administratör bör etableras för SQL-servrar
- Granskningsanvändning av anpassade RBAC-regler

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 och 8.1.5 Minst privilegium och granskning av rättigheter för användaråtkomst

Azure implementerar rollbaserad åtkomstkontroll (RBAC) för att hjälpa dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure-portalen kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) till granskningskonton som ska prioriteras för granskning, inklusive avskrivna konton och externa konton med förhöjda behörigheter.

- Inaktuella konton ska tas bort från din prenumeration
- Inaktuella konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med ägarbehörigheter ska tas bort från din prenumeration
- Externa konton med skrivbehörighet ska tas bort från din prenumeration
- Externa konton med läsbehörighet bör tas bort från din prenumeration

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 Borttagning eller justering av nyttjanderätter

Azure implementerar rollbaserad åtkomstkontroll (RBAC) som hjälper dig att hantera vem som har åtkomst till resurser i Azure. Med Hjälp av Azure Active Directory och RBAC kan du uppdatera användarroller för att återspegla organisationsändringar. Vid behov kan konton blockeras från att logga in (eller tas bort), vilket omedelbart tar bort åtkomsträttigheter till Azure-resurser. Den här skissen tilldelar [Azure Policy-definitioner](../../../policy/overview.md) för granskning av avskrivna konto som bör beaktas för borttagning.

- Inaktuella konton ska tas bort från din prenumeration
- Inaktuella konton med ägarbehörigheter ska tas bort från din prenumeration

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b och 8.2.5 Lösenordsbaserad autentisering

Den här skissen hjälper dig att framtvinga starka lösenord genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar virtuella datorer i Windows som inte tillämpar minsta styrka och andra lösenordskrav. Medvetenhet om virtuella datorer i strid med principen om lösenordsstyrka hjälper dig att vidta korrigerande åtgärder för att säkerställa att lösenord för alla vm-användarkonton är kompatibla med principen.

- \[Preview\]: Granska virtuella windows-datorer som inte har en maximal lösenordsålder på 70 dagar
- \[Förhandsversion\]: Distribuera krav för granskning av virtuella Windows-datorer som inte har en maximal lösenordsålder på 70 dagar
- \[Preview\]: Granska virtuella windows-datorer som inte begränsar den minsta lösenordslängden till 14 tecken
- \[Förhandsgranskning\]: Distribuera krav för granskning av virtuella Windows-datorer som inte begränsar den minsta lösenordslängden till 14 tecken
- \[Preview\]: Granska virtuella windows-datorer som tillåter återanvändning av de tidigare 24 lösenorden
- \[Förhandsversion\]: Distribuera krav för granskning av virtuella Windows-datorer som tillåter återanvändning av de tidigare 24 lösenorden

## <a name="103-and-1054-audit-generation"></a>10.3 och 10.5.4 Revisionsgenerering

Den här skissen hjälper dig att se till att systemhändelser loggas genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar logginställningar på Azure-resurser.
Diagnostikloggar ger insikt i åtgärder som utfördes inom Azure-resurser. Azure-loggar förlitar sig på synkroniserade interna klockor för att skapa en tidsrelerad post av händelser över resurser.

- Granskning bör aktiveras vid avancerade datasäkerhetsinställningar på SQL Server
- Granska diagnostikinställning
- Granska granskningsinställningar på SQL-servernivå
- Distribuera granskning på SQL-servrar
- Lagringskonton ska migreras till nya Azure Resource Manager-resurser
- Virtuella datorer ska migreras till nya Azure Resource Manager-resurser

## <a name="1236-and-1237-information-security"></a>12.3.6 och 12.3.7 Informationssäkerhet

Den här skissen hjälper dig att hantera och styra nätverket genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som granskar de godtagbara nätverksplatserna och de godkända företagsprodukter som tillåts för miljön. Dessa är anpassningsbara av varje företag genom principparametrarna inom var och en av dessa principer.

- Tillåtna platser
- Tillåtna platser för resursgrupper

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontrollmappningen av PCI-DSS v3.2.1-skissen kan du besöka följande artiklar om översikten och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1-ritning - Översikt](./index.md)
> [PCI-DSS v3.2.1-ritning - Distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Läs mer om [skisslivscykeln](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).