---
title: "Anpassa Azure migrera inställningar för utvärdering | Microsoft Docs"
description: "Beskriver hur du ställer in och kör en bedömning för migrera VMwares virtuella datorer till Azure med Azure migrering Planner"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 02/26/2018
ms.author: raynew
ms.openlocfilehash: efb4ad59d25a0c1209e4f0f6cd406c2f0d48159c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="customize-an-assessment"></a>Anpassa en utvärdering

[Azure migrera](migrate-overview.md) skapar bedömningar med standardegenskaper. Du kan ändra standardegenskaperna med hjälp av anvisningarna i den här artikeln när du har skapat en utvärdering.


## <a name="edit-assessment-properties"></a>Redigera egenskaper för utvärdering

1. I den **bedömningar** i migreringen projektet, Välj bedömning och klickar på **redigera egenskaper för**.
2. Ändra egenskaper i enlighet med följande tabell:

    **Inställning** | **Detaljer** | **Standard**
    --- | --- | ---
    **Målplats** | Azure-platsen du vill migrera till.<br/><br/> Azure migrera stöder för närvarande 30 regioner inbegripet Östra Australien, sydost, södra, Kanada Central, Kanada, Öst, centrala Indien, centrala USA, Kina Öst, Kina Nord, Östasien, östra USA, Tyskland Central, Tyskland nordöst, östra USA 2, Japan Öst, västra Japan, Korea Central, Korea söder, norra centrala USA, Norra Europa, södra centrala USA, Sydostasien, södra Indien, Storbritannien, Syd, Storbritannien, Väst, västra centrala USA, västra Europa, västra Indien, västra USA och västra US2. |  USA, västra 2 är standardplatsen.
    **Lagringsredundans** | Typ av lagring redundans som virtuella Azure-datorer ska använda efter migreringen. | [Lokalt redundant lagring (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) är standardvärdet. Azure migrera har bara stöd för hanterade diskar-baserade bedömningar och hanterade diskar har endast stöd för LRS, därför egenskapen har för närvarande endast alternativet LRS. 
    **Storlek för kriterium** | Kriteriet som ska användas av Azure Migrate för att ställa in rätt storlek på virtuella datorer för Azure. Du kan göra antingen gör *prestandabaserad* storlek eller ändra storlek på de virtuella datorerna *som lokala*, utan att överväga historik för minnesprestanda. | Prestandabaserad storlek är standardalternativet.
    **Prestandahistorik** | Varaktighet att tänka på för att du utvärderar prestanda för de virtuella datorerna. Den här egenskapen kan bara användas när sizing kriteriet är *prestandabaserad storlek*. | Standardvärdet är en dag.
    **Percentil användning** | Percentilvärdet i prestanda exempel tilldelas beaktas för rätt storlek. Den här egenskapen kan bara användas när sizing kriteriet är *prestandabaserad storlek*.  | Standardvärdet är 95: e percentilen.
    **prisnivå** | Du kan ange [prisnivå (Basic/Standard)](../virtual-machines/windows/sizes-general.md) för Azure-måldatorerna. Om du exempelvis planerar att migrera en produktionsmiljö bör du överväga standardnivån, som tillhandahåller virtuella datorer med låg svarstid men kan kosta mer. Om du å andra sidan har en miljö för utveckling och testning kanske du vill överväga Basic-nivån som har virtuella datorer med högre svarstider och lägre kostnader. | Som standard används [standardnivån](../virtual-machines/windows/sizes-general.md).
    **Komfortfaktor** | Azure Migrate överväger en buffert (komfortfaktor) under utvärderingen. Bufferten tillämpas utöver datorns användningsdata för virtuella datorer (CPU, minne, disk och nätverk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor. | Standardinställningen är 1.3 x.
    **Erbjudande** | [Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/) som du är registrerad på. | [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) är standard.
    **Valuta** | Faktureringsvaluta. | Standardvärdet är USD.
    **Rabatt (%)** | Prenumerationen-specifika rabatter felmeddelandet ovanpå Azure-erbjudande. | Standardinställningen är 0%.
    **Hybridrapportering i Azure-förmån** | Ange om du har software assurance och är tillgängliga för [Azure Hybrid förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om anges till Ja, Windows Azure-priser anses för virtuella Windows-datorer. | Standardvärdet är Ja.

3. Klicka på **spara** att uppdatera bedömningen.


## <a name="next-steps"></a>Nästa steg

[Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
