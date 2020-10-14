---
title: ta med fil
description: ta med fil
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 10/12/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 20d80fe1a09d388552b6289f8a9b23c878672f94
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974301"
---
De här exemplen visar hur du använder Azure Policy med prenumerationer som har registrerats i Azure Lighthouse.

| **Mall** | **Beskrivning** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Tilldelar en princip som lägger till eller tar bort en tagg (med modify-effekten) till en delegerad prenumeration. Mer information finns i [Distribuera en princip som kan åtgärdas i en delegerad prenumeration](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-allow-certain-managing-tenants](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-allow-certain-managing-tenants) | Tilldelar en princip som begränsar Azure Lighthouse-delegeringar till specifika hanterande klientorganisationer. |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Tilldelar en granskningsprincip för delegeringstilldelningar. |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | Tilldelar en princip för att bekräfta att prenumerationer i en hanteringsgrupp har delegerats till en hanteringsklient. Om så inte är fallet skapar den tilldelningen.
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Tilldelar en princip som aktiverar diagnostik för Azure Key Vault-resurser i en delegerad prenumeration (med deployIfNotExists-effekten). Mer information finns i [Distribuera en princip som kan åtgärdas i en delegerad prenumeration](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Tilldelar flera principer för att aktivera diagnostik för en delegerad prenumeration och ansluter alla virtuella Windows- och Linux-datorer till Log Analytics-arbetsytan som skapats av principen. Mer information finns i [Distribuera en princip som kan åtgärdas i en delegerad prenumeration](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Tillämpar ett initiativ (flera relaterade principdefinitioner) till en delegerad prenumeration. |

