---
title: Azures säkerhets kontroll – inventering och till gångs hantering
description: Säkerhets kontroll inventering och till gångs hantering
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 2f24012a69a7673426644a8ded6162cd1021938e
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930065"
---
# <a name="security-control-inventory-and-asset-management"></a>Säkerhets kontroll: inventering och till gångs hantering

Rekommendationer för inventering och till gångs hantering fokuserar på att åtgärda problem som rör aktivt hantering (inventering, spårning och korrigering) av alla Azure-resurser så att endast auktoriserade resurser får åtkomst och obehöriga och ohanterade resurser är identifieras och tas bort.

## <a name="61-use-azure-asset-discovery"></a>6,1: Använd Azure Asset Discovery

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 6.1 | 1,1, 1,2, 1,3, 1,4, 9,1, 12,1 | Kund |

Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.) i din prenumeration (er).  Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

Så här skapar du frågor med Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Så här visar du dina Azure-prenumerationer:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Förstå Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 6.2 | 1.5 | Kund |

Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 6.3 | 1.6 | Kund |

Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att organisera och spåra till gångar. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Så här skapar du ytterligare Azure-prenumerationer:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du Hanteringsgrupper:

https://docs.microsoft.com/azure/governance/management-groups/create

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: underhåll en inventering av godkända Azure-resurser och program varu titlar

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 6.4 | 2.1 | Kund |

Definiera godkända Azure-resurser och godkänd program vara för beräknings resurser.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 6.5 | 2,3, 2,4 | Kund |

Använd Azure Policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer.

Använd Azure Resource Graph för att fråga/identifiera resurser i sina prenumerationer. &nbsp;se till att alla Azure-resurser som finns i miljön är godkända.

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du frågor med Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 6.6 | 2.3/2.4 | Kund |

Använd inventering av virtuella Azure-datorer för att automatisera insamling av information om all program vara på Virtual Machines. Program varu namn, version, utgivare och uppdaterings tid är tillgängliga från Azure Portal. Du kan få åtkomst till installations datum och annan information genom att aktivera diagnostik på gästnivå och ta Windows-händelseloggen till en Log Analytics-arbetsyta.

Så här aktiverar du inventering av virtuella Azure-datorer:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 6.7 | 2.5 | Kund |

Använd Azure Security Center fil integritets övervakning (Ändringsspårning) och inventering av virtuella datorer för att identifiera all program vara som är installerad på Virtual Machines. Du kan implementera en egen process för att ta bort otillåten program vara. Du kan också använda en lösning från tredje part för att identifiera program som inte godkänts.

Så här använder du övervakning av fil integritet:

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

Förstå Azure Ändringsspårning:

https://docs.microsoft.com/azure/automation/change-tracking

Så här aktiverar du inventering av virtuella Azure-datorer:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 6.8 | 2,6 | Kund |

Använd Azure Security Center adaptiva program kontroller för att säkerställa att endast auktoriserade program körs och all obehörig program vara blockeras från att köras på Azure Virtual Machines.

Använda Azure Security Center adaptiva program kontroller:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 6.9 | 2,6 | Kund |

Använd Azure Policy för att begränsa vilka tjänster du kan etablera i din miljö.

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här nekar du en speciell resurs typ med Azure Policy:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6,10: implementera lista över godkända program

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 6.1 | 2.7 | Kund |

Använd Azure Security Center adaptiva program kontroller för att ange vilka filtyper en regel kan eller kanske inte tillämpas på.

Implementera en lösning från tredje part om detta inte uppfyller kravet.

Använda Azure Security Center adaptiva program kontroller:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager via skript

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 6,11 | 2.8 | Kund |

Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera &quot;blockera åtkomst&quot; för appen &quot;Microsoft Azure Management&quot;.

Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 6,12 | 2.8 | Kund |

Använd operativ systemets speciella konfigurationer eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure Compute-resurser.

Till exempel hur du styr körning av PowerShell-skript i Windows-miljöer:

https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 6,13 | 2.9 | Kund |

Program vara som krävs för affärs åtgärder, men som kan innebära högre risk för organisationen, bör isoleras inom den egna virtuella datorn och/eller det virtuella nätverket och tillräckligt säkert med antingen en Azure-brandvägg eller en nätverks säkerhets grupp.

Så här skapar du ett virtuellt nätverk:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Så här skapar du en NSG med en säkerhets konfiguration:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>Nästa steg

Se nästa säkerhets kontroll: [säker konfiguration](security-control-secure-configuration.md)
