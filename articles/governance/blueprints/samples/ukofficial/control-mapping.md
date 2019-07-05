---
title: Exempel - Storbritannien officiella och Storbritannien NHS skisser - kontroll mappning
description: Kontrollen mappning av Storbritannien officiella och Storbritannien NHS skissen exempel.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 01a8e104f6d590113784db28e4bfde849d78b15f
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491919"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Kontrollen mappning av Storbritannien officiella och Storbritannien NHS skissen exempel

I följande artikel beskriver hur Storbritannien officiella och Storbritannien NHS skissen exempel mappas till den officiella Storbritannien och Storbritannien NHS kontroller. Läs mer om kontrollerna, [Storbritannien officiella](https://www.gov.uk/government/publications/government-security-classifications).

Följande mappningar avser den **Storbritannien officiella** och **Storbritannien NHS** kontroller. Använd navigeringen till höger för att gå direkt till en viss kontroll-mappning. Många av de mappade kontrollerna implementeras med en [Azure Policy](../../../policy/overview.md) initiativ. Om du vill granska fullständig initiativet öppna **princip** i Azure-portalen och välj den **definitioner** sidan. Hitta och välj sedan den  **[förhandsversion] gransknings-och Storbritannien officiella och Storbritannien NHS styr och distribuera specifika VM-tillägg för att stödja granskningskrav** inbyggd princip initiativ.

## <a name="1-data-in-transit-protection"></a>1 data i rörelse skydd

Skissen hjälper dig att säkerställa informationsöverföring med Azure-tjänster är säker genom att tilldela [Azure Policy](../../../policy/overview.md) definitioner som granska osäkert anslutningar till storage-konton och Redis Cache.

- Endast säkra anslutningar till Redis-cachen ska aktiveras
- Säker överföring till storage-konton måste vara aktiverat

## <a name="23-data-at-rest-protection"></a>2.3-data med rest-skydd

Den här skissen kan du tillämpa principen på användningen av cryptograph kontroller genom att tilldela [Azure Policy](../../../policy/overview.md) definitioner som tillämpa specifika cryptograph kontroller och granska användning av svaga kryptografiska inställningar.
Förstå där dina Azure-resurser kan ha icke-optimala kryptografiska konfigurationer kan du vidta åtgärder för att se till att resurser som är konfigurerade i enlighet med din säkerhetsprincip för information. Mer specifikt Kräv de principer som tilldelats av den här skissen kryptering för data lake storage-konton; Kräv transparent datakryptering på SQL-databaser Granska saknas kryptering på storage-konton, SQL-databaser, virtuella diskar och variabler för automation-konto. Granska osäkert anslutningar till storage-konton och Redis Cache Granska kryptering av svaga VM lösenord; och granska dekrypterade Service Fabric-kommunikation.

- Övervaka okrypterade SQL-databaser i Azure Security Center
- Diskkryptering ska tillämpas på virtuella datorer
- Automation-konto variabler ska krypteras
- Säker överföring till storage-konton måste vara aktiverat
- Service Fabric-kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign
- Transparent datakryptering på SQL-databaser ska aktiveras
- Distribuera transparent datakryptering för SQL DB
- Kräv kryptering på Data Lake Store-konton
- Tillåtna platser (har kodat ”Storbritannien, SÖDRA” och ”Storbritannien, västra”)
- Tillåtna platser för resursgrupper (har kodat ”Storbritannien, SÖDRA” och ”Storbritannien, västra”)

## <a name="52-vulnerability-management"></a>5.2 Sårbarhetshantering

Den här skissen hjälper dig att hantera information system sårbarheter genom att tilldela [Azure Policy](../../../policy/overview.md) definitioner som övervaka saknad endpoint protection, systemuppdateringar, driva system sårbarheter, SQL säkerhetsproblem och säkerhetsrisker i virtuell dator. Dessa insikter i realtid informera om säkerhetsläget för dina distribuerade resurser och kan hjälpa dig att prioritera åtgärder.

- Övervaka saknad Endpoint Protection i Azure Security Center
- Systemuppdateringar bör vara installerad på dina datorer
- Säkerhetsproblem i Säkerhetskonfiguration på dina datorer bör åtgärdas
- Säkerhetsproblem på SQL-databaser bör åtgärdas
- Sårbarheter som bör åtgärdas av en lösning för Sårbarhetsbedömning

## <a name="53-protective-monitoring"></a>5.3 skyddande övervakning

Den här skissen hjälper dig att skydda system informationstillgångar genom att tilldela [Azure Policy](../../../policy/overview.md) definitioner som ger skyddande övervakning på obegränsad åtkomst, godkända aktivitet och hot.

- Granska obegränsad nätverksåtkomst till storage-konton
- Anpassningsbara programkontroller måste vara aktiverad på virtuella datorer
- Distribuera Hotidentifiering på SQL-servrar
- Distribuera Microsoft IaaS Anti-malware-Standardtillägg för Windows Server

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9 säker hantering av användare / 10 enhetsidentitet samt autentisering

Azure implementerar rollbaserad åtkomstkontroll (RBAC) till hjälper dig att hantera vem som har åtkomst till resurser i Azure. Med Azure-portalen kan granska du vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen hjälper dig att begränsa och kontrollera behörighet genom att tilldela [Azure Policy](../../../policy/overview.md) definitioner för att granska externa konton med behörighet för ägare och/eller Läs/Skriv- och konton med ägare kan läsa och/eller skrivbehörighet som gör inte har aktiverat multifaktorautentisering.

- MFA måste vara aktiverad på konton med ägarbehörigheter för din prenumeration
- MFA ska vara aktiverad konton med skrivbehörigheter för din prenumeration
- MFA måste vara aktiverad på konton med läsbehörigheter för din prenumeration
- Externa konton med ägarbehörigheter som ska tas bort från prenumerationen
- Externa konton med skrivbehörigheter bör tas bort från prenumerationen
- Bör ta bort externa konton med läsbehörigheter från prenumerationen

Den här skissen tilldelar Azure Policy-definitioner för att granska användning av Azure Active Directory-autentisering för SQL-servrar och Service Fabric. Autentisering möjliggör med Azure Active Directory förenklad behörighetshantering och centraliserad Identitetshantering för databasanvändare och andra Microsoft-tjänster.

- En Azure Active Directory-administratör ska etableras för SQL-servrar
- Service Fabric-kluster bör endast använda Azure Active Directory för klientautentisering

Den här skissen tilldelar också Azure principdefinitioner granska konton som ska prioriteras för granskning, inklusive avskriven konton och externa konton. När de behövs kan konton blockeras från att logga in (eller tas bort), som direkt tar bort åtkomsträttigheter till Azure-resurser. Denna skiss tilldelar två Azure principdefinitioner audit avskriven-konto som du bör överväga att tas bort.

- Bör ta bort inaktuella konton från prenumerationen
- Bör ta bort inaktuella konton med ägarbehörigheter från prenumerationen
- Externa konton med ägarbehörigheter som ska tas bort från prenumerationen
- Externa konton med skrivbehörigheter bör tas bort från prenumerationen

Den här skissen tilldelar också en Azure Policy definition som granskar Linux VM lösenord filbehörigheter att varna om de har angetts felaktigt. Den här designen kan du vidta åtgärder för att se till autentiserare inte komprometteras.

- [Förhandsversion]: Audit Linux VM /etc/passwd file permissions are set to 0644

Den här skissen hjälper dig att genomdriva starka lösenord genom att tilldela Azure principdefinitioner som granskar Windows virtuella datorer som inte framtvingar minsta styrka och andra lösenordskrav. Feltolerans för virtuella datorer i överträdelse av styrka lösenordsprincip kan du vidta åtgärder för att säkerställa att lösenord för användarkonton för alla virtuella datorer är kompatibla med principen.

- [Förhandsversion]: Deploy requirements to audit Windows VMs that do not have the password complexity setting enabled
- [Förhandsversion]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [Förhandsversion]: Deploy requirements to audit Windows VMs that do not have a minimum password age of 1 day
- [Förhandsversion]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Förhandsversion]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords
- [Förhandsversion]: Audit Windows VMs that do not have the password complexity setting enabled
- [Förhandsversion]: Audit Windows VMs that do not have a maximum password age of 70 days
- [Förhandsversion]: Audit Windows VMs that do not have a minimum password age of 1 day
- [Förhandsversion]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Förhandsversion]: Audit Windows VMs that allow re-use of the previous 24 passwords

Detta skiss hjälper dig att styra åtkomst till Azure-resurser genom att tilldela principdefinitioner i Azure. Dessa principer granska användning av resurstyper och konfigurationer som kan tillåta mer Tillåtande åtkomst till resurser. Förstå resurser som bryter mot dessa principer kan du vidta åtgärder för att kontrollera åtkomst till Azure-resurser hjälp är begränsad till behöriga användare.

- [Förhandsversion]: Deploy requirements to audit Linux VMs that have accounts without passwords
- [Förhandsversion]: Deploy requirements to audit Linux VMs that allow remote connections from accounts without passwords
- [Förhandsversion]: Audit Linux VMs that have accounts without passwords
- [Förhandsversion]: Audit Linux VMs that allow remote connections from accounts without passwords
- Storage-konton som ska migreras till nya Azure Resource Manager-resurser
- Virtuella datorer som ska migreras till nya Azure Resource Manager-resurser
- Granska virtuella datorer som inte använder hanterade diskar

## <a name="11-external-interface-protection"></a>11 externa gränssnittet skydd

Än med hjälp av fler än 25 principer för hantering av lämplig säker användare skissen hjälper dig att skydda tjänstgränssnitt från obehörig åtkomst genom att tilldela en [Azure Policy](../../../policy/overview.md) definition som Övervakare obegränsad Storage-konton. Storage-konton med obegränsad åtkomst kan oavsiktlig tillgång till informationen i systemet. Den här skissen tilldelar också en princip som gör det möjligt för adaptiva programkontroller på virtuella datorer.

- Granska obegränsad nätverksåtkomst till storage-konton
- Anpassningsbara programkontroller måste vara aktiverad på virtuella datorer

## <a name="12-secure-service-administration"></a>12 säker Tjänstadministration

Azure implementerar rollbaserad åtkomstkontroll (RBAC) till hjälper dig att hantera vem som har åtkomst till resurser i Azure. Med Azure-portalen kan granska du vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen hjälper dig att begränsa och kontrollera rättigheter för privilegierad åtkomst genom att tilldela fem [Azure Policy](../../../policy/overview.md) definitioner för att granska externa konton med ägare och/eller skriva behörigheter och konton med ägare och/eller skrivbehörighet som inte har aktiverat multifaktorautentisering.

System som används för administration av en molnbaserad tjänst kommer mycket har privilegierad åtkomst till tjänsten. Deras kompromettering skulle ha stor inverkan, inklusive sätt att kringgå säkerhetskontroller och stjäla eller ändra stora mängder data. De metoder som används av tjänstleverantörens administratörer för att hantera tjänsten operational bör utformas för att minimera risk för utnyttjande som kunde underminera säkerheten i tjänsten. Om denna princip inte är implementerat kan en angripare ha möjlighet att kringgå säkerhetskontroller och stjäla eller ändra stora mängder data.

- MFA måste vara aktiverad på konton med ägarbehörigheter för din prenumeration
- MFA ska vara aktiverad konton med skrivbehörigheter för din prenumeration
- Externa konton med ägarbehörigheter som ska tas bort från prenumerationen
- Externa konton med skrivbehörigheter bör tas bort från prenumerationen

Den här skissen tilldelar Azure Policy-definitioner för att granska användning av Azure Active Directory-autentisering för SQL-servrar och Service Fabric. Autentisering möjliggör med Azure Active Directory förenklad behörighetshantering och centraliserad Identitetshantering för databasanvändare och andra Microsoft-tjänster.

- En Azure Active Directory-administratör ska etableras för SQL-servrar
- Service Fabric-kluster bör endast använda Azure Active Directory för klientautentisering

Den här skissen tilldelar också Azure principdefinitioner granska konton som ska prioriteras för granskning, inklusive avskriven konton och externa konton med förhöjd behörighet. När de behövs kan konton blockeras från att logga in (eller tas bort), som direkt tar bort åtkomsträttigheter till Azure-resurser. Denna skiss tilldelar två Azure principdefinitioner audit avskriven-konto som du bör överväga att tas bort.

- Bör ta bort inaktuella konton från prenumerationen
- Bör ta bort inaktuella konton med ägarbehörigheter från prenumerationen
- Externa konton med ägarbehörigheter som ska tas bort från prenumerationen
- Externa konton med skrivbehörigheter bör tas bort från prenumerationen

Den här skissen tilldelar också en Azure Policy definition som granskar Linux VM lösenord filbehörigheter att varna om de har angetts felaktigt. Den här designen kan du vidta åtgärder för att se till autentiserare inte komprometteras.

- [Förhandsversion]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="13-audit-information-for-users"></a>13 granskningsinformation för användare

Den här skissen hjälper dig att säkerställa händelser loggas genom att tilldela [Azure Policy](../../../policy/overview.md) definitioner som gransknings-och inställningar på Azure-resurser. En tilldelad princip granskar även om virtuella datorer inte skickar loggar till en angiven log analytics-arbetsyta.

- Övervaka Ej granskad SQL-servrar i Azure Security Center
- Granska diagnostikinställning
- Granska nivå granskningsinställningarna för SQL server
- [Förhandsversion]: Deploy Log Analytics Agent for Linux VMs
- [Förhandsversion]: Deploy Log Analytics Agent for Windows VMs
- Distribuera nätverksbevakare när virtuella nätverk skapas

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontroll mappningen av Storbritannien officiella och Storbritannien NHS skisser, finns i följande artiklar för att lära dig om översikten och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [Storbritannien officiella och Storbritannien NHS skisser - översikt](./index.md)
> [Storbritannien officiella och Storbritannien NHS skisser - distributionsstegen](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Lär dig mer om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
