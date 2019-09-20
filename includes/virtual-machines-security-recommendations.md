---
title: ta med fil
description: ta med fil
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/19/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: 60d4957f65c124de683c6d156561de3b52aaec94
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71163948"
---
Den här artikeln innehåller säkerhets rekommendationer för Azure Virtual Machines. Genom att implementera dessa rekommendationer kan du uppfylla dina säkerhets krav enligt beskrivningen i vår delade ansvars modell och förbättrar den övergripande säkerheten för dina webb programs lösningar. Mer information om vad Microsoft gör för att uppfylla ansvaret för service leverantörer finns i [delade ansvars områden för molnbaserad data behandling](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

Några av rekommendationerna som ingår i den här artikeln kan övervakas automatiskt av Azure Security Center. Azure Security Center är den första försvars linjen i att skydda dina resurser i Azure. Den analyserar regelbundet säkerhets läget för dina Azure-resurser för att identifiera potentiella säkerhets risker. Sedan får du rekommendationer om hur du kan åtgärda dem.

- Mer information om Azure Security Center rekommendationer finns [i säkerhets rekommendationer i Azure Security Center](../articles/security-center/security-center-recommendations.md).
- Mer information om Azure Security Center finns i [Azure Security Center?](../articles/security-center/security-center-intro.md)

## <a name="recommendations"></a>Rekommendationer

| Category | Rekommendation | Kommentar | Security Center |
|-|-|----|--|
| Allmänt | Använd de senaste uppdateringarna när du skapar anpassade VM-avbildningar | Innan du skapar avbildningar ser du till att alla de senaste uppdateringarna har installerats för operativ systemet och alla program som ska ingå i din avbildning.  | - |
| Allmänt | Håll dina virtuella datorer aktuella | Du kan använda [uppdateringshantering](../articles/automation/automation-update-management.md) lösning i Azure Automation för att hantera operativ system uppdateringar för dina Windows-och Linux-datorer i Azure | [Ja](../articles/security-center/security-center-apply-system-updates.md) |
| Allmänt | Säkerhetskopiera dina virtuella datorer | [Azure Backup](../articles/backup/backup-overview.md) hjälper till att skydda dina program data med minimala drifts kostnader. Programfel kan skada dina data och den mänskliga faktorn kan införa buggar i dina program. Med Azure Backup skyddas dina virtuella datorer som kör Windows och Linux. | - |
| Allmänt | Använd flera virtuella datorer för större återhämtning och tillgänglighet | Om din virtuella dator kör program som behöver hög tillgänglighet bör du ha flera virtuella datorer eller [tillgänglighets uppsättningar](../articles/virtual-machines/windows/manage-availability.md) | - |
| Allmänt | Anta en strategi för affärs kontinuitet och haveri beredskap (BCDR) | Med Azure Site Recovery kan du välja mellan olika alternativ som har utformats för att stödja företagets kontinuitets behov. Den stöder olika replikering och växlar över scenarier. Mer information finns i [About Site Recovery](../articles/site-recovery/site-recovery-overview.md) | - |
| Identitets- och åtkomsthantering | Centralisera VM-autentisering | Du kan centralisera autentiseringen av dina virtuella Windows-och Linux-datorer med [Azure AD-autentisering](../articles/active-directory/develop/authentication-scenarios.md). | - |
| Datasäkerhet | Kryptera operativ system diskar | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) hjälper dig att kryptera dina virtuella Windows-och Linux IaaS-datorer. Kryptering av diskar gör innehållet oläsligt utan nödvändiga nycklar. Disk kryptering skyddar lagrade data från obehörig åtkomst som annars skulle vara möjliga om disken kopierades| [Ja](../articles/security-center/security-center-apply-disk-encryption.md) |
| Datasäkerhet | Kryptera data diskar | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) hjälper dig att kryptera dina virtuella Windows-och Linux IaaS-datorer. Kryptering av diskar gör innehållet oläsligt utan nödvändiga nycklar. Disk kryptering skyddar lagrade data från obehörig åtkomst som annars skulle vara möjliga om disken kopierades| -  |
| Datasäkerhet | Begränsa installerad program vara | Begränsa installerad program vara till vad som krävs för att implementera lösningen hjälper till att minska din lösnings attack yta | - |
| Datasäkerhet | Använda antivirus program/program mot skadlig kod | I Azure kan du använda program mot skadlig kod från säkerhets leverantörer som Microsoft, Symantec, Trend Micro och Kasper Sky. Den här program varan hjälper till att skydda dina virtuella datorer från skadliga filer, annons program och andra hot. Du kan distribuera Microsofts skydd mot skadlig kod baserat på dina program arbets belastningar med antingen grundläggande eller avancerad anpassad konfiguration. Mer information om Microsoft Antimalware för Azure finns i [Microsoft Antimalware för azure Cloud Services och Virtual Machines](../articles/security/azure-security-antimalware.md) | - |
| Datasäkerhet | Lagra nycklar och hemligheter på ett säkert sätt | Förenkla hanteringen av dina hemligheter och nycklar genom att tillhandahålla dina program ägare med ett säkert, centralt hanterat alternativ, vilket gör att du kan minska risken för oavsiktliga kompromisser eller läckor. Azure Key Vault kan lagra dina nycklar säkert i HSM: er (Hardware Security modules) som är certifierade till FIPS 140-2 nivå 2. Om du behöver lagra dina nycklar och hemligheter med FIPs 140,2 nivå 3 kan du använda [Azure Dedicated HSM](../articles/dedicated-hsm/overview.md) | - |
| Nätverk | Begränsa åtkomsten till hanterings portar | Angripare genomsöker offentliga moln-IP-intervall för öppna hanterings portar och försöker med "Easy" attacker som vanliga lösen ord och kända säkerhets problem. [Just-in-Time (JIT)-åtkomst till virtuell dator (VM)](../articles/security-center/security-center-just-in-time.md) kan användas för att låsa inkommande trafik till dina virtuella Azure-datorer, vilket minskar exponeringen för attacker och ger enkel åtkomst till att ansluta till virtuella datorer när det behövs. | - |
| Nätverk | Begränsa nätverks åtkomst | Med nätverks säkerhets grupper kan du begränsa nätverks åtkomst och kontrol lera antalet exponerade slut punkter. Mer information finns i [skapa, ändra eller ta bort en nätverks säkerhets grupp](../articles/virtual-network/manage-network-security-group.md) | - |
| Övervakning | Övervaka dina virtuella datorer | Du kan använda [Azure Monitor för virtuella datorer](../articles/azure-monitor/insights/vminsights-overview.md) för att övervaka statusen för dina virtuella Azure-datorer och skalnings uppsättningar för virtuella datorer. Prestanda problem med en virtuell dator kan leda till avbrott i tjänsten, vilket strider mot tillgänglighetens säkerhets princip. | - |

## <a name="next-steps"></a>Nästa steg

Kontakta din programprovider för att se om det finns ytterligare säkerhets krav. Mer information om hur du utvecklar säkra program finns i [dokumentationen om säker utveckling](../articles/security/fundamentals/abstract-develop-secure-apps.md).