---
title: Azure Security Control - Lager- och kapitalförvaltning
description: Lager- och kapitalförvaltning för säkerhetskontroll
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 2f24012a69a7673426644a8ded6162cd1021938e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75930065"
---
# <a name="security-control-inventory-and-asset-management"></a>Säkerhetskontroll: Lager- och kapitalförvaltning

Rekommendationer för lager- och tillgångshantering fokuserar på att ta itu med problem som rör aktiv hantering (inventering, spåra och korrigera) alla Azure-resurser så att endast auktoriserade resurser får åtkomst och obehöriga och ohanterade resurser identifieras och tas bort.

## <a name="61-use-azure-asset-discovery"></a>6.1: Använd Identifiering av Azure Asset

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | Kund |

Använd Azure Resource Graph för att fråga/identifiera alla resurser (till exempel beräkning, lagring, nätverk, portar och protokoll osv.) i dina prenumerationer.  Kontrollera lämpliga (läs) behörigheter i din klientorganisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via Resource Graph, rekommenderas det starkt att skapa och använda Azure Resource Manager-resurser framöver.

Så här skapar du frågor med Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Så här visar du dina Azure-prenumerationer:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Förstå Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6.2: Underhåll metadata för tillgångar

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 6.2 | 1.5 | Kund |

Använd taggar på Azure-resurser som ger metadata för att logiskt ordna dem till en taxonomi.

Så här skapar och använder du Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 6.3 | 1.6 | Kund |

Använd taggning, hanteringsgrupper och separata prenumerationer, där så är lämpligt, för att organisera och spåra tillgångar. Stämma av lager regelbundet och se till att obehöriga resurser tas bort från prenumerationen i tid.

Så här skapar du ytterligare Azure-prenumerationer:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du hanteringsgrupper:

https://docs.microsoft.com/azure/governance/management-groups/create

Så här skapar och använder du Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Underhåll en inventering av godkända Azure-resurser och programvarutitlar

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 6.4 | 2.1 | Kund |

Definiera godkända Azure-resurser och godkänd programvara för beräkningsresurser.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för icke godkända Azure-resurser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 6.5 | 2.3, 2.4 | Kund |

Använd Azure Policy för att sätta begränsningar för vilken typ av resurser som kan skapas i dina prenumerationer.

Använd Azure Resource Graph för att fråga/identifiera resurser i sina prenumerationer. &nbsp;Se till att alla Azure-resurser som finns i miljön är godkända.

Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du frågor med Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Övervaka icke godkända program i beräkningsresurser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 6.6 | 2.3/2.4 | Kund |

Använd Azure virtual machine Inventory för att automatisera insamlingen av information om all programvara på virtuella datorer. Programnamn, version, utgivare och uppdateringstid är tillgängliga från Azure-portalen. Om du vill få åtkomst till installationsdatum och annan information aktiverar du diagnostik på gästnivå och för in Windows-händelseloggarna i en Logganalysarbetsyta.

Aktivera Azure-inventering för virtuella datorer:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort icke godkända Azure-resurser och program

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 6.7 | 2.5 | Kund |

Använd Azure Security Centers filintegritetsövervakning (ändringsspårning) och inventering av virtuella datorer för att identifiera all programvara som är installerad på virtuella datorer. Du kan implementera din egen process för att ta bort obehörig programvara. Du kan också använda en tredjepartslösning för att identifiera icke godkänd programvara.

Så här använder du filintegritetsövervakning:

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

Förstå Azure-ändringsspårning:

https://docs.microsoft.com/azure/automation/change-tracking

Aktivera inventering av virtuella Azure-datorer:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6.8: Använd endast godkända ansökningar

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 6.8 | 2,6 | Kund |

Använd Azure Security Center Adaptive Application Controls för att säkerställa att endast auktoriserad programvara körs och all obehörig programvara blockeras från att köras på virtuella Azure-datorer.

Så här använder du Azure Security Center Adaptive Application Controls:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 6.9 | 2,6 | Kund |

Använd Azure Policy för att begränsa vilka tjänster du kan etablera i din miljö.

Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här nekar du en viss resurstyp med Azure Policy:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6.10: Genomföra godkänd ansökningslista

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 6.1 | 2.7 | Kund |

Använd Azure Security Center Adaptive Application Controls för att ange vilka filtyper en regel kan gälla eller inte kan gälla för.

Implementera tredjepartslösning om detta inte uppfyller kravet.

Så här använder du Azure Security Center Adaptive Application Controls:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager via skript

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 6.11 | 2.8 | Kund |

Använd Azure Villkorlig åtkomst för att begränsa användarnas möjlighet &quot;att&quot; interagera &quot;med Azure&quot; Resource Manager genom att konfigurera blockera åtkomst för Microsoft Azure Management App.

Konfigurera villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Begränsa användarnas möjlighet att köra skript i beräkningsresurser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 6.12 | 2.8 | Kund |

Använd operativsystemspecifika konfigurationer eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure-beräkningsresurser.

Så här styr du exempelvis PowerShell-skriptkörning i Windows-miljöer:

https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt segregera högriskapplikationer

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 6.13 | 2.9 | Kund |

Programvara som krävs för affärsåtgärder, men som kan medföra högre risk för organisationen, bör isoleras inom sin egen virtuella dator och/eller virtuella nätverk och vara tillräckligt säker med antingen en Azure-brandvägg eller nätverkssäkerhetsgrupp.

Så här skapar du ett virtuellt nätverk:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Hur man skapar en NSG med en säkerhet config:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>Nästa steg

Se nästa säkerhetskontroll: [Säker konfiguration](security-control-secure-configuration.md)
