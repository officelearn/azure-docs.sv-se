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
ms.openlocfilehash: e7fcb72cecbfad2de80b844ed5281267d5e4c0c3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66164560"
---
När du har applicerat taggar på resurser kan du visa kostnaderna för resurser med de taggarna. Det tar en stund innan kostnadsanalysen visar den senaste användningen, och därför ser du kanske inte kostnaderna ännu. När kostnaderna är tillgängliga kan du visa kostnaderna för resurser över olika resursgrupper i prenumerationen. Användarna måste ha [åtkomst på prenumerationsnivå till faktureringsinformation](../articles/billing/billing-manage-access.md) för att se kostnaderna.

Om du vill visa kostnader efter taggar i portalen väljer du din prenumeration och sedan **Kostnadsanalys**.

![Kostnadsanalys](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Filtrera sedan efter taggvärdet och välj **Applicera**.

![Visa kostnad efter tagg](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

Du kan även använda [API:er för Azure-fakturering](../articles/billing/billing-usage-rate-card-overview.md) för att programmässigt visa kostnaderna.
