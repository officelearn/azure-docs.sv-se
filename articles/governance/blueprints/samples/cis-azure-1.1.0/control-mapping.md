---
title: Exempel på CIS Microsoft Azure grunderna – rekommendations mappning
description: Rekommendations mappning av CIS-exemplet Microsoft Azure grunderna för att Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/09/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: 3814b5169a687a854cb977eee90fbf6c3fd45afe
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980981"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Rekommendations mappning av CIS-exemplet för benchmark-Microsoft Azure grunderna

I följande artikel beskrivs hur Azure-ritningar CIS-Microsoft Azure Base-exempel kartor mappar till CIS-Microsoft Azure grunderna för benchmark-rekommendationer. Mer information om rekommendationerna finns i [CIS Microsoft Azure stiftelser benchmark](https://www.cisecurity.org/benchmark/azure/).

Följande mappningar är till **CIS Microsoft Azure grunderna 1.1.0** -rekommendationer för benchmark. Använd navigeringen till höger om du vill gå direkt till en bestämd rekommendations mappning.
Många av de mappade rekommendationerna implementeras med ett [Azure policy](../../../policy/overview.md) initiativ. Om du vill granska hela initiativet öppnar du **princip** i Azure Portal och väljer sidan **definitioner** . Leta sedan reda på och välj **\[Preview @ no__t-2 audit CIS Microsoft Azure grunderna för benchmark v-1.1.0 och distribuera särskilda VM-tillägg för att stödja gransknings krav** inbyggda princip initiativ.

> [!IMPORTANT]
> Varje kontroll nedan är kopplad till en eller flera [Azure policy](../../../policy/overview.md) -definitioner. Dessa principer kan hjälpa dig att [utvärdera efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta ingen 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan är **kompatibel** i Azure policy endast som avser själva principerna. Detta garanterar inte att du är helt kompatibel med alla krav för en kontroll. Standarden för efterlevnad innehåller dessutom kontroller som inte åtgärdas av några Azure Policy definitioner för tillfället. Därför är regelefterlevnad i Azure Policy bara en partiell vy av din övergripande kompatibilitetsstatus. Kopplingarna mellan kontroller och Azure Policy definitioner för det här skiss exemplet för efterlevnad kan ändras med tiden. Om du vill visa ändrings historiken läser du [inchecknings historiken för GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1,1 se till att Multi-Factor Authentication har Aktiver ATS för alla privilegierade användare

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som överensstämmer med den här CIS-rekommendationen.

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- MFA ska vara aktiverat på konton med Skriv behörighet för din prenumeration

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1,2 se till att Multi-Factor Authentication har Aktiver ATS för alla icke-privilegierade användare

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration

## <a name="13-ensure-that-there-are-no-guest-users"></a>1,3 se till att det inte finns några gäst användare

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som överensstämmer med den här CIS-rekommendationen.

- Externa konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med Läs behörighet bör tas bort från din prenumeration
- Externa konton med skrivbehörigheter bör tas bort från prenumerationen

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2,3 se till att ASC standard princip inställningen "övervaka system uppdateringar" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- System uppdateringar bör installeras på dina datorer

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2,4 kontrol lera att ASC-standardprincip inställningen "övervaka OS-sårbarheter" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2,5 Se till att ASC standard princip inställningen "övervaka Endpoint Protection" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- Övervaka saknade Endpoint Protection i Azure Security Center

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2,6 Se till att ASC standard princip inställningen "övervaka disk kryptering" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- Disk kryptering bör tillämpas på virtuella datorer

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2,8 Se till att ASC-standardprincip inställningen "övervaka brand vägg för webbaserade program" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- NSG: er-reglerna för webb program på IaaS bör vara härdade

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2,10 Se till att ASC standard princip inställningen "övervaka sårbarhets bedömning" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2,12 Se till att ASC standard princip inställningen "övervaka JIT-nätverksanslutning" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- Just-in-Time-kontroll för nätverks åtkomst ska tillämpas på virtuella datorer

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2,15 kontrol lera att ASC-standardprincip inställningen "övervaka SQL-kryptering" inte är inaktive rad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- transparent datakryptering på SQL-databaser ska aktive ras

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3,1 kontrol lera att "säker överföring krävs" är inställt på ' Enabled '

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- Säker överföring till lagrings konton ska vara aktiverat

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3,7 Se till att standard regeln för nätverks åtkomst för lagrings konton är inställd på neka

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- Granska obegränsad nätverks åtkomst till lagrings konton

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4,1 se till att "granskning" är inställt på "on"

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- Granskning ska aktive ras för avancerade data säkerhets inställningar på SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4,2 kontrol lera att AuditActionGroups i gransknings principen för en SQL Server är korrekt inställd

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- Inställningarna för SQL-granskning bör ha åtgärds grupper konfigurerade för att fånga kritiska aktiviteter

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4,3 kontrol lera att kvarhållning av granskning är större än 90 dagar

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- SQL-servrar bör konfigureras med granskning av antalet dagar som är större än 90 dagar.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4,4 se till att "avancerad data säkerhet" på en SQL-Server är inställt på "på"

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- Avancerad datasäkerhet bör aktiveras på dina SQL-servrar

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4,5 Se till att hot identifierings typerna är inställt på all

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som överensstämmer med den här CIS-rekommendationen.

- Avancerade skydds typer måste anges till alla i avancerade data säkerhets inställningar för SQL Server
- Avancerade skydds typer måste anges till alla i avancerade data säkerhets inställningar för SQL-hanterad instans

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4,6 kontrol lera att skicka aviseringar till har angetts

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- Avancerade data säkerhets inställningar för SQL Server ska innehålla en e-postadress för att ta emot säkerhets aviseringar

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4,7 kontrol lera att "e-posttjänst och medadministratörer" är aktiverat

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- Avancerade data säkerhets inställningar för SQL-hanterad instans ska innehålla en e-postadress för att ta emot säkerhets aviseringar

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4,8 kontrol lera att Azure Active Directory admin har kon figurer ATS

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- En Azure Active Directory administratör bör tillhandahållas för SQL-servrar

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4,9 Se till att "data kryptering" är inställt på "on" på en SQL Database

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- transparent datakryptering på SQL-databaser ska aktive ras

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4,10 kontrol lera att SQL Servers TDE-skydd krypteras med BYOK (Använd din egen nyckel)

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som överensstämmer med den här CIS-rekommendationen.

- SQL-serverns TDE-skydd bör krypteras med din egen nyckel
- SQL-hanterad instans TDE-skydd ska vara krypterat med din egen nyckel

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 kontrol lera att loggning för Azure-valv är aktiverat

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- Diagnostikloggar i Key Vault ska vara aktive rad

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7,1 kontrol lera att OS-disken är krypterad

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- Disk kryptering bör tillämpas på virtuella datorer

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7,2 kontrol lera att data diskarna är krypterade

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- Disk kryptering bör tillämpas på virtuella datorer

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7,5 Se till att de senaste OS-korrigeringarna för alla Virtual Machines tillämpas

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- System uppdateringar bör installeras på dina datorer

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7,6 Se till att Endpoint Protection för alla Virtual Machines har installerats

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- Övervaka saknade Endpoint Protection i Azure Security Center

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8,5 aktivera rollbaserad åtkomst kontroll (RBAC) i Azure Kubernetes Services

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- \[Preview @ no__t-1: Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9,2 se till att webbapp omdirigerar all HTTP-trafik till HTTPS i Azure App Service

Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som överensstämmer med den här CIS-rekommendationen.

- Webbprogram bör enbart vara åtkomliga via HTTPS

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontroll mappningen av CIS Microsoft Azure grunderna benchmark-skiss kan du gå till följande artikel om du vill lära dig mer om skissen eller besöka Azure Policy i Azure Portal för att tilldela initiativet:

> [!div class="nextstepaction"]
> [CIS Microsoft Azure grunderna benchmark-skiss – översikt](./index.md)
> [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).