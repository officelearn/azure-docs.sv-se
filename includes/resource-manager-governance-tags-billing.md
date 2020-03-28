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
ms.openlocfilehash: 226079b1970508004c55c54cd3de6d2e9475aa08
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75970133"
---
När du har applicerat taggar på resurser kan du visa kostnaderna för resurser med de taggarna. Det tar en stund innan kostnadsanalysen visar den senaste användningen, och därför ser du kanske inte kostnaderna ännu. När kostnaderna är tillgängliga kan du visa kostnaderna för resurser över olika resursgrupper i prenumerationen. Användarna måste ha [åtkomst på prenumerationsnivå till faktureringsinformation](../articles/cost-management-billing/manage/manage-billing-access.md) för att se kostnaderna.

Om du vill visa kostnader efter taggar i portalen väljer du din prenumeration och sedan **Kostnadsanalys**.

![Kostnadsanalys](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Filtrera sedan efter taggvärdet och välj **Applicera**.

![Visa kostnad efter tagg](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

Du kan även använda [API:er för Azure-fakturering](../articles/cost-management-billing/manage/usage-rate-card-overview.md) för att programmässigt visa kostnaderna.
