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
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 2fa0912d120760ddba3b3d21f718e214e9d7c26b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238932"
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
