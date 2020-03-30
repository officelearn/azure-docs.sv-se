---
title: ta med fil
description: ta med fil
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/13/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: d0ede337f2ae17f28cd02df7ed35919bd9955e7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048575"
---
Den här artikeln innehåller säkerhetsrekommendationer för virtuella Azure-datorer. Följ dessa rekommendationer för att uppfylla de säkerhetsskyldigheter som beskrivs i vår modell för delat ansvar. Rekommendationerna hjälper dig också att förbättra den övergripande säkerheten för webbapplösningarna. Mer information om vad Microsoft gör för att uppfylla ansvaret för tjänsteleverantörer finns i [Delat ansvar för molnbaserad databehandling](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Vissa av den här artikelns rekommendationer kan automatiskt åtgärdas av Azure Security Center. Azure Security Center är den första försvarslinjen för dina resurser i Azure. Den analyserar regelbundet säkerhetstillståndet för dina Azure-resurser för att identifiera potentiella säkerhetsproblem. Den rekommenderar sedan hur du åtgärdar sårbarheter. Mer information finns [i Säkerhetsrekommendationer i Azure Security Center](../articles/security-center/security-center-recommendations.md).

Allmän information om Azure Security Center finns i [Vad är Azure Security Center?](../articles/security-center/security-center-intro.md)

## <a name="general"></a>Allmänt

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| När du skapar anpassade VM-avbildningar använder du de senaste uppdateringarna. | Innan du skapar avbildningar installerar du de senaste uppdateringarna för operativsystemet och för alla program som kommer att vara en del av avbildningen.  | - |
| Håll dina virtuella datorer aktuella. | Du kan använda [update management-lösningen](../articles/automation/automation-update-management.md) i Azure Automation för att hantera operativsystemuppdateringar för dina Windows- och Linux-datorer i Azure. | [Ja](../articles/security-center/security-center-apply-system-updates.md) |
| Säkerhetskopiera dina virtuella datorer. | [Azure Backup](../articles/backup/backup-overview.md) hjälper till att skydda dina programdata och har minimala driftskostnader. Programfel kan skada dina data och mänskliga fel kan införa buggar i dina program. Azure Backup skyddar dina virtuella datorer som kör Windows och Linux. | - |
| Använd flera virtuella datorer för större återhämtning och tillgänglighet. | Om den virtuella datorn kör program som måste vara mycket tillgängliga använder du flera virtuella datorer eller [tillgänglighetsuppsättningar](../articles/virtual-machines/windows/manage-availability.md). | - |
| Anta en strategi för kontinuitet och katastrofåterställning (BCDR). | Med Azure Site Recovery kan du välja mellan olika alternativ som utformats för att stödja affärskontinuitet. Den stöder olika replikerings- och redundansscenarier. Mer information finns i [Om webbplatsåterställning](../articles/site-recovery/site-recovery-overview.md). | - |

## <a name="data-security"></a>Datasäkerhet

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Kryptera operativsystemdiskar. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) hjälper dig att kryptera dina Windows- och Linux IaaS VM-diskar. Utan nödvändiga nycklar är innehållet i krypterade diskar oläsligt. Diskkryptering skyddar lagrade data från obehörig åtkomst som annars skulle vara möjlig om disken kopierades.| [Ja](../articles/security-center/security-center-apply-disk-encryption.md) |
| Kryptera datadiskar. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) hjälper dig att kryptera dina Windows- och Linux IaaS VM-diskar. Utan nödvändiga nycklar är innehållet i krypterade diskar oläsligt. Diskkryptering skyddar lagrade data från obehörig åtkomst som annars skulle vara möjlig om disken kopierades.| -  |
| Begränsa installerad programvara. | Begränsa installerad programvara till vad som krävs för att kunna använda din lösning. Den här riktlinjen hjälper till att minska lösningens angreppsyta. | - |
| Använd antivirus eller antimalware. | I Azure kan du använda programvara mot skadlig kod från säkerhetsleverantörer som Microsoft, Symantec, Trend Micro och Kaspersky. Den här programvaran hjälper till att skydda dina virtuella datorer från skadliga filer, adware och andra hot. Du kan distribuera Microsoft Antimalware baserat på dina programarbetsbelastningar. Använd antingen grundläggande säker standard eller avancerad anpassad konfiguration. Mer information finns i [Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../articles/security/azure-security-antimalware.md). | - |
| Lagra nycklar och hemligheter på ett säkert sätt. | Förenkla hanteringen av dina hemligheter och nycklar genom att ge dina programägare ett säkert, centralt hanterat alternativ. Denna hantering minskar risken för oavsiktliga kompromisser eller läckage. Azure Key Vault kan säkert lagra dina nycklar i maskinvarusäkerhetsmoduler (HSM) som är certifierade enligt FIPS 140-2 Nivå 2. Om du behöver använda FIPs 140.2 Nivå 3 för att lagra nycklar och hemligheter kan du använda [Azure Dedicated HSM](../articles/dedicated-hsm/overview.md). | - |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering 

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Centralisera vm-autentisering. | Du kan centralisera autentiseringen av dina virtuella Windows- och Linux-datorer med hjälp av [Azure Active Directory-autentisering](../articles/active-directory/develop/authentication-scenarios.md). | - |

## <a name="monitoring"></a>Övervakning

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Övervaka dina virtuella datorer. | Du kan använda [Azure Monitor för virtuella datorer för](../articles/azure-monitor/insights/vminsights-overview.md) att övervaka tillståndet för dina virtuella Azure-datorer och skaluppsättningar för virtuella datorer. Prestandaproblem med en virtuell dator kan leda till avbrott i tjänsten, vilket bryter mot säkerhetsprincipen för tillgänglighet. | - |

## <a name="networking"></a>Nätverk

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Begränsa åtkomsten till hanteringsportar. | Angripare skannar offentliga moln-IP-intervall för öppna hanteringsportar och försöker "enkla" attacker som vanliga lösenord och kända opatrullerade sårbarheter. Du kan använda [just-in-time (JIT) VM-åtkomst](../articles/security-center/security-center-just-in-time.md) för att låsa inkommande trafik till dina virtuella Azure-datorer, vilket minskar exponeringen för attacker samtidigt som du enkelt ansluter till virtuella datorer när de behövs. | - |
| Begränsa nätverksåtkomsten. | Med nätverkssäkerhetsgrupper kan du begränsa nätverksåtkomsten och kontrollera antalet exponerade slutpunkter. Mer information finns i [Skapa, ändra eller ta bort en nätverkssäkerhetsgrupp](../articles/virtual-network/manage-network-security-group.md). | - |

## <a name="next-steps"></a>Nästa steg

Kontakta programleverantören om du vill veta mer om ytterligare säkerhetskrav. Mer information om hur du utvecklar säkra program finns i [Dokumentation om säker utveckling](../articles/security/fundamentals/abstract-develop-secure-apps.md).
