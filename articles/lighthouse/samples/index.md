---
title: Exempel och mallar för Azure Lighthouse
description: Dessa exempel och Azure Resource Manager-mallar visar hur du registrerar kunder för Azure-delegerad resurshantering och ger stöd för Azure Lighthouse-scenarier.
ms.topic: sample
ms.date: 10/17/2019
ms.openlocfilehash: 20c1fc62c83202f97ed9cff68fd285280d94a321
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132160"
---
# <a name="azure-lighthouse-samples"></a>Azure Lighthouse-exempel

Följande tabell innehåller länkar till viktiga Azure Resource Manager-mallar för Azure Lighthouse. Dessa filer och mycket annat finns även på [lagringsplatsen för Azure Lighthouse-exempel](https://github.com/Azure/Azure-Lighthouse-samples/).

## <a name="onboarding-customers-to-azure-delegated-resource-management"></a>Föra över kunder till Azure-delegerad resurshantering

Våra olika mallar hjälper dig att hantera vissa integreringsscenarier. Välj det alternativ som passar dig bäst och glöm inte att ändra parameterfilen så att den återspeglar din miljö. Mer information om hur du använder de här filerna i distributionen finns i [Föra över en kund till Azure-delegerad resurshantering](../how-to/onboard-customer.md).

| **Mall** | **Beskrivning** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Registrera en kunds prenumeration för Azure-delegerad resurshantering. En separat distribution krävs för varje prenumeration. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Registrera en eller flera av en kunds resursgrupper för Azure-delegerad resurshantering. Använd **rgDelegatedResourceManagement** för en enskild resursgrupp eller **multipleRgDelegatedResourceManagement**  om du behöver publicera flera resursgrupper i samma prenumeration. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Om du har [publicerat ett erbjudande för hanterade tjänster till Azure Marketplace ](../how-to/publish-managed-services-offers.md) kan du välja att använda den här mallen för att publicera resurser för kunder som har accepterat erbjudandet. Marketplace-värdena i parameterfilen måste matcha de värden som du använde när du publicerade erbjudandet. |

Normalt krävs en separat distribution för varje prenumeration som registreras, men du kan även distribuera mallar över flera prenumerationer.

| **Mall** | **Beskrivning** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Distribuera Azure Resource Manager-mallar i flera prenumerationer. |

## <a name="azure-policy"></a>Azure Policy

De här exemplen visar hur du använder Azure Policy med prenumerationer som har registrerats för Azure-delegerad resurshantering.

| **Mall** | **Beskrivning** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | Tilldelar en princip som lägger till eller tar bort en tagg (med modify-effekten) till en delegerad prenumeration. Mer information finns i [Distribuera en princip som kan åtgärdas i en delegerad prenumeration](../how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | Tilldelar en granskningsprincip för delegeringstilldelningar. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | Tilldelar en princip som aktiverar diagnostik för Azure Key Vault-resurser i en delegerad prenumeration (med deployIfNotExists-effekten). Mer information finns i [Distribuera en princip som kan åtgärdas i en delegerad prenumeration](../how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | Tilldelar flera principer för att aktivera diagnostik för en delegerad prenumeration och ansluter alla virtuella Windows- och Linux-datorer till Log Analytics-arbetsytan som skapats av principen. Mer information finns i [Distribuera en princip som kan åtgärdas i en delegerad prenumeration](../how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | Tillämpar ett initiativ (flera relaterade principdefinitioner) till en delegerad prenumeration. |

## <a name="azure-monitor"></a>Azure Monitor

De här exemplen visar hur du använder Azure Monitor för att skapa aviseringar för prenumerationer som har registrerats för Azure-delegerad resurshantering.

| **Mall** | **Beskrivning** |
|---------|---------|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Den här mallen skapar en Azure-avisering och ansluter till en befintlig åtgärdsgrupp.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Skapar flera loggaviseringar baserat på Kusto-frågor.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Distribuerar en avisering i en klientorganisation när en användare delegerar en prenumeration till en hanteringsklient.|

## <a name="additional-cross-tenant-scenarios"></a>Ytterligare scenarier mellan klienter

De här exemplen illustrerar olika uppgifter som kan utföras i hanteringsscenarier med flera klientorganisationer.

| **Mall** | **Beskrivning** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Distribuera lagringskonton i två olika resursgrupper.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Skapar Azure-hanteringstjänster, kopplar dem till varandra och distribuerar ytterligare lösningar. För en distribution från slutpunkt till slutpunkt använder du mallen **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Aktiverar och konfigurerar Azure Security Center i Azure-målprenumerationen. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | Distribuerar och aktiverar Azure Sentinel på en befintlig Log Analytics-arbetsyta i en delegerad prenumeration. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | Med dessa mallar kan du distribuera VM-tillägg för Log Analytics till virtuella Windows- och Linux-datorer och ansluta dem till den angivna Log Analytics-arbetsytan |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure-delegerad resurshantering](../concepts/azure-delegated-resource-management.md).
- Utforska [lagringsplatsen med Azure Lighthouse-exempel](https://github.com/Azure/Azure-Lighthouse-samples/).
