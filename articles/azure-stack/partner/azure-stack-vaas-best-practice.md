---
title: Azure Stack Metodtips för verifiering. | Microsoft Docs
description: Den här artikeln innehåller metodtips för verifiering som en tjänst.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: b61c4a22d0c5343d41e35f2bbd66829b2b9e8d48
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336980"
---
# <a name="best-practices-for-validation-as-a-service"></a>Metodtips för verifiering som en tjänst

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Den här artikeln beskriver Metodtips för hantering av resurser i validering som en tjänst (VaaS). En översikt över VaaS resurser, se [verifiering som en tjänst viktiga begrepp](azure-stack-vaas-key-concepts.md).

## <a name="solution-management"></a>Lösningshantering

### <a name="naming-convention-for-vaas-solutions"></a>Namngivningskonvention för VaaS lösningar

Använda en konsekvent namngivningskonvention för alla lösningar som är registrerade i VaaS. Till exempel kan Lösningsnamnet konstrueras från maskinvaruegenskaper nedan på följande sätt:

|Produktnamn | Unikt maskinvara elementet 1 | Unikt maskinvara Element 2 | Lösningsnamn
|---|---|---|---|
Min lösning XYZ |  Alla Flash | Min växel X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>När du ska skapa en ny VaaS-lösning

Använd samma VaaS lösning när du kör arbetsflöden mot samma maskinvara SKU. En ny VaaS lösning ska skapas endast om det finns en ändring av maskinvara SKU.

## <a name="workflow-management"></a>Arbetsflödeshantering

### <a name="naming-convention-for-vaas-workflows"></a>Namngivningskonvention för VaaS arbetsflöden

Använda en konsekvent namngivningskonvention för alla VaaS arbetsflödeskörningar. Till exempel att skapa ett arbetsflöde namn från build egenskaperna nedan på följande sätt:

|Build-nummer (större) | Date | Storlek på lösning | Namn på arbetsflöde
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [verifiering som en tjänst viktiga begrepp](azure-stack-vaas-key-concepts.md)
