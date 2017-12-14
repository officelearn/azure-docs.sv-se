---
title: "Anpassa Azure migrera inställningar för utvärdering | Microsoft Docs"
description: "Beskriver hur du ställer in och kör en bedömning för migrera VMwares virtuella datorer till Azure med Azure migrering Planner"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: ce47790f6214864afdba33eb5cbe3a9e49b81cd5
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="customize-an-assessment"></a>Anpassa en utvärdering

[Azure migrera](migrate-overview.md) skapar bedömningar med standardinställningar. Du kan ändra dessa standardinställningar med hjälp av anvisningarna i den här artikeln när du har skapat en utvärdering.


## <a name="edit-assessment-values"></a>Redigera assessment värden

1. I Azure migrera projektet **bedömningar** , Välj bedömning och klicka på **redigera egenskaper för**.
2. Ändra inställningarna enligt tabellen nedan.

    **Inställning** | **Detaljer** | **Standard**
    --- | --- | ---
    **Målplats** | Azure-platsen du vill migrera till. |  USA, västra 2 är standardplatsen.
    **Lagringsredundans** | Den typ av lagring som de virtuella Azure-datorerna kommer att använda efter migreringen. | Endast [lokalt redundant lagring (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) replikering stöds för närvarande.
    **Komfortfaktor** | Bekvämlighet faktorn är en buffert som används under utvärdering. Används för att konto för till exempel när användning, kort prestandahistorik, sannolikt ökar i framtida användning. | Standardinställningen är 1.3 x.
    **Prestanda långsammare historik** | Tid som används vid utvärdering av prestandahistorik. | Standardvärdet är en månad.
    **Percentil användning** | Percentilvärdet väga in för prestanda långsammare historik. | Standardvärdet är 95%.
    **prisnivå** | Du kan ange den [prisnivån](https://azure.microsoft.com/blog/basic-tier-virtual-machines-2/) för en virtuell dator.  | Som standard den [Standard](../virtual-machines/windows/sizes-general.md) nivå används.
    **Erbjudande** | [Azure erbjuder](https://azure.microsoft.com/support/legal/offer-details/) som gäller. | [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) är standard.
    **Valuta** | Faktureringsvaluta. | Standardvärdet är USD.
    **Rabatt (%)** | Prenumerationen-specifika rabatter felmeddelandet ovanpå erbjudande. | Standardinställningen är 0%.
    **Hybridrapportering i Azure används förmån** | Anger om du är registrerad i den [Azure Hybrid Använd förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om anges till Ja priser för Windows Azure är väga in för virtuella Windows-datorer. | Standardvärdet är Ja.

3. Klicka på **spara** att uppdatera bedömningen.


## <a name="next-steps"></a>Nästa steg

[Lär dig mer](concepts-assessment-calculation.md) om hur bedömningar beräknas.
