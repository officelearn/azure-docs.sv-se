---
title: Azure Security benchmark v2 – till gångs hantering
description: Azure Security benchmark v2 till gångs hantering
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: c553652d4d8abd16b4e5fd4ff896e42bdba103ad
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408982"
---
# <a name="security-control-v2-asset-management"></a>Säkerhets kontroll v2: till gångs hantering

Till gångs hantering omfattar kontroller för att säkerställa ökad säkerhet och styrning över Azure-resurser. Detta inkluderar rekommendationer om behörigheter för säkerhets personal, säkerhets åtkomst till till gångs inventering och hantering av godkännanden för tjänster och resurser (inventering, spårning och rätt).

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: se till att säkerhets teamet har insyn i risker för till gångar

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| AM-1 | 1,1, 1,2 | CM – 8, PM-5 |

Se till att säkerhets teamen beviljas säkerhets läsar behörigheter i din Azure-klient och prenumerationer så att de kan övervaka säkerhets risker med hjälp av Azure Security Center. 

Beroende på hur säkerhets teamets ansvars områden är strukturerade kan övervakning av säkerhets risker vara ansvaret för en central säkerhets grupp eller ett lokalt team. Detta innebär att säkerhets insikter och risker alltid måste aggregeras centralt inom en organisation. 

Behörigheter för säkerhets läsare kan tillämpas i stort sett till en hel klient organisation (rot hanterings grupp) eller omfattas av hanterings grupper eller vissa prenumerationer. 

Obs: ytterligare behörigheter kan krävas för att få insyn i arbets belastningar och tjänster. 

- [Översikt över rollen säkerhets läsare](../../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure Hanteringsgrupper](../../governance/management-groups/overview.md)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: se till att säkerhets teamet har åtkomst till till gångs inventering och metadata

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| AM-2 | 1,1, 1,2, 1,4, 1,5, 9,1, 12,1 | CM – 8, PM-5 |

Se till att säkerhets teamen har åtkomst till en kontinuerligt uppdaterad inventering av till gångar på Azure. Säkerhets teamen behöver ofta den här inventeringen för att utvärdera organisationens potentiella exponering för nya risker och som informerar om löpande säkerhets förbättringar. 

Azure Security Center Inventory-funktionen och Azure Resource Graph kan fråga efter och identifiera alla resurser i dina prenumerationer, inklusive Azure-tjänster, program och nätverks resurser.  

Organisera till gångar logiskt enligt organisationens taxonomi med hjälp av taggar och andra metadata i Azure (namn, beskrivning och kategori).  

- [Så här skapar du frågor med Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

- [Azure Security Center till gångs lager hantering](../../security-center/asset-inventory.md)

- [Mer information om att tagga till gångar finns i besluts guiden för resurs namn givning och taggning](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%252fazure%252fazure-resource-manager%252fmanagement%252ftoc.json)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd endast godkända Azure-tjänster

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| AM-3 | 2,3, 2,4 | CM – 7, CM-8 |

Använd Azure Policy för att granska och begränsa vilka tjänster som användare kan etablera i din miljö. Använd Azure Resource Graph för att fråga efter och identifiera resurser i prenumerationerna.  Du kan också använda Azure Monitor för att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../../governance/policy/samples/index.md)

- [Så här skapar du frågor med Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: se till att till gångens livs cykel hantering är säker

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| AM-4 | 2,3, 2,4, 2,5 | CM – 7, CM-8, CM-10, CM-11 |

Upprätta eller uppdatera säkerhets principer som kan användas för att hantera livs cykel hanterings processer för potentiellt stora konsekvenser. Dessa ändringar innehåller ändringar av: identitets leverantörer och åtkomst, data känslighet, nätverks konfiguration och tilldelning av administrativa privilegier.

Ta bort Azure-resurser när de inte längre behövs.

- [Ta bort resurs grupp och resurs för Azure](../../azure-resource-manager/management/delete-resource-group.md)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| AM-5 | 2.9 | AC-3 |

Använd villkorlig åtkomst för Azure AD för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../../role-based-access-control/conditional-access-azure-management.md)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: Använd endast godkända program i beräknings resurser

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| AM-6 | 2,6, 2,7 | AC-3, CM-7, CM-8, CM-10, CM-11 |

Se till att endast auktoriserad program vara körs och att all obehörig program vara blockeras från att köras på Azure Virtual Machines.

Använd Azure Security Center (ASC) adaptiva program kontroller för att identifiera och generera en lista över tillåtna program. Du kan också använda ASC-anpassade program kontroller för att säkerställa att endast auktoriserade program körs och all obehörig program vara blockeras från att köras på Azure Virtual Machines.

Använd Azure Automation Ändringsspårning och inventering för att automatisera insamlingen av inventerings information från dina virtuella Windows-och Linux-datorer. Program varu namn, version, utgivare och uppdaterings tid är tillgängliga från Azure Portal. Om du vill hämta programmets installations datum och annan information aktiverar du diagnostik på gästnivå och dirigerar Windows-händelseloggen till Log Analytics-arbetsyta.

Beroende på typen av skript kan du använda konfigurationer för operativ system eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure Compute-resurser. 

Du kan också använda en lösning från tredje part för att identifiera och identifiera ej godkänd program vara.

- [Använda Azure Security Center adaptiva program kontroller](../../security-center/security-center-adaptive-application.md)

- [Förstå Azure Automation Ändringsspårning och inventering](../../automation/change-tracking/overview.md)

- [Så här styr du körning av PowerShell-skript i Windows-miljöer](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)