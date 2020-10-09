---
title: ta med fil
description: ta med fil
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 09/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: a9c9b5081232988cb4ab80bfcbb9f2d92d1d025f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336588"
---
De här exemplen visar hur du använder Azure Policy med prenumerationer som har registrerats i Azure Lighthouse.

| **Mall** | **Beskrivning** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Tilldelar en princip som lägger till eller tar bort en tagg (med modify-effekten) till en delegerad prenumeration. Mer information finns i [Distribuera en princip som kan åtgärdas i en delegerad prenumeration](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Tilldelar en granskningsprincip för delegeringstilldelningar. |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | Tilldelar en princip för att bekräfta att prenumerationer i en hanteringsgrupp har delegerats till en hanteringsklient. Om så inte är fallet skapar den tilldelningen.
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Tilldelar en princip som aktiverar diagnostik för Azure Key Vault-resurser i en delegerad prenumeration (med deployIfNotExists-effekten). Mer information finns i [Distribuera en princip som kan åtgärdas i en delegerad prenumeration](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Tilldelar flera principer för att aktivera diagnostik för en delegerad prenumeration och ansluter alla virtuella Windows- och Linux-datorer till Log Analytics-arbetsytan som skapats av principen. Mer information finns i [Distribuera en princip som kan åtgärdas i en delegerad prenumeration](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Tillämpar ett initiativ (flera relaterade principdefinitioner) till en delegerad prenumeration. |

