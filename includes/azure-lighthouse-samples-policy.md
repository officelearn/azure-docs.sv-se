---
title: ta med fil
description: ta med fil
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 1c0be8ed8c176fe32b8fe7fd420d65727c5288d2
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986624"
---
De här exemplen visar hur du använder Azure Policy med prenumerationer som har registrerats för Azure-delegerad resurshantering.

| **Mall** | **Beskrivning** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Tilldelar en princip som lägger till eller tar bort en tagg (med modify-effekten) till en delegerad prenumeration. Mer information finns i [Distribuera en princip som kan åtgärdas i en delegerad prenumeration](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Tilldelar en granskningsprincip för delegeringstilldelningar. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Tilldelar en princip som aktiverar diagnostik för Azure Key Vault-resurser i en delegerad prenumeration (med deployIfNotExists-effekten). Mer information finns i [Distribuera en princip som kan åtgärdas i en delegerad prenumeration](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Tilldelar flera principer för att aktivera diagnostik för en delegerad prenumeration och ansluter alla virtuella Windows- och Linux-datorer till Log Analytics-arbetsytan som skapats av principen. Mer information finns i [Distribuera en princip som kan åtgärdas i en delegerad prenumeration](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Tillämpar ett initiativ (flera relaterade principdefinitioner) till en delegerad prenumeration. |

