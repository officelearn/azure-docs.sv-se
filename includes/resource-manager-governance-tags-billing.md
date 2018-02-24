---
title: ta med fil
description: ta med fil
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 7843410043b726526380b2a916d96f414a2decda
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
Du kan visa kostnaderna för resurser med taggarna när taggar till resurser. Det tar ett tag för kostnadsanalys att visa den senaste användningen, så du inte kan visa kostnaderna ännu. När kostnader, kan du visa kostnader för resurser över resursgrupper i din prenumeration. Användarna måste ha [prenumeration åtkomst till faktureringsinformationen](../articles/billing/billing-manage-access.md) att visa kostnaderna.

Om du vill visa kostnaderna efter tagg i portal, väljer din prenumeration och välj **kostnaden Analysis**.

![Kostnadsanalys](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Filtrera efter Taggvärdet sedan och välj **tillämpa**.

![Visa kostnad per tagg](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

Du kan också använda den [Azure Billing API: erna](../articles/billing/billing-usage-rate-card-overview.md) programmässigt visa kostnaderna.
