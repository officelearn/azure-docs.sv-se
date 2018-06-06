---
title: Anpassa Azure migrera inställningar för utvärdering | Microsoft Docs
description: Beskriver hur du ställer in och kör en bedömning för migrera VMwares virtuella datorer till Azure med Azure migrering Planner
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/31/2018
ms.author: raynew
ms.openlocfilehash: 73dab9c7eca53ecce44d43a9607fcc7426f9de8d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715514"
---
# <a name="customize-an-assessment"></a>Anpassa en utvärdering

[Azure migrera](migrate-overview.md) skapar bedömningar med standardegenskaper. Du kan ändra standardegenskaperna med hjälp av anvisningarna i den här artikeln när du har skapat en utvärdering.


## <a name="edit-assessment-properties"></a>Redigera egenskaper för utvärdering

1. På sidan **Utvärderingar** i migreringsprojektet väljer du utvärderingen och klickar på **Redigera egenskaper**.
2. Ändra egenskaper i enlighet med följande tabell:

    **Inställning** | **Detaljer** | **Standard**
    --- | --- | ---
    **Målplats** | Azure-platsen du vill migrera till.<br/><br/> För närvarande stöder Azure Migrate 30 regioner, bland andra: Australien – östra, Australien – sydöstra, Brasilien – södra, Kanada – centrala, Kanada – östra, Indien – centrala, USA – centrala, Kina – östra, Kina – norra, Asien – östra (Asien och stillahavsområdet), USA – östra, Tyskland – centrala, Tyskland – nordöstra, USA – östra 2, Japan – östra, Japan – västra, Korea – centrala, Korea – södra, USA – norra centrala, Europa – norra, USA – södra centrala, Asien – sydost, Indien – södra, Storbritannien – södra, Storbritannien – västra, USA – US Gov Arizona – US Gov Texas – US Gov Virginia – västra centrala, USA – västra centrala, Europa – västra, Indien – västra, USA – västra och USA – västra 2. |  USA, västra 2 är standardplatsen.
    **Lagringstyp** | Du kan ange vilken typ av diskar som du vill allokera i Azure. Den här egenskapen gäller när sizing kriteriet är lokalt storlek. Du kan ange disk måltypen antingen som Premium hanterade diskar eller Standard hanterade diskar. Disk-rekommendationen görs automatiskt baserat på prestandadata för de virtuella datorerna för prestandabaserad storlek. Observera att Azure migrera stöder bara hanterade diskar för utvärdering av migreringen. | Standardvärdet är Premium hanterade diskar (med storlek kriterium som *som lokalt storlek*).
    **Ändra storlek på kriterium** | Kriteriet som ska användas av Azure Migrate för att ställa in rätt storlek på virtuella datorer för Azure. Du kan antingen göra en *prestandabaserad* storleksändring eller ändra storlek på de virtuella datorerna *som lokalt*, utan att överväga prestandahistorik. | Standardalternativet är prestandabaserad storleksändring.
    **Prestandahistorik** | Tidsperioden att beakta när du utvärderar prestanda för de virtuella datorerna. Den här egenskapen gäller bara när storleksändringskriteriet är *prestandabaserad storleksändring*. | Standardvärdet är en dag.
    **Percentilutnyttjande** | Percentilvärdet för prestandaexempeluppsättningen som beaktas för rätt storleksändring. Den här egenskapen gäller bara när storleksändringskriteriet är *prestandabaserad storleksändring*.  | Standardvärdet är 95: e percentilen.
    **VM-serie** | Du kan ange den VM-serie som du vill överväga vid storleksberäkningen. Om du till exempel har en produktionsmiljö som du inte planerar att migrera till A-serien av virtuella datorer i Azure, kan du utesluta A-serien från listan eller serien, så fastställs storleken endast baserat på de valda serierna. | Som standard markeras alla VM-serien.
    **prisnivå** | Du kan ange [prisnivå (Basic/Standard)](../virtual-machines/windows/sizes-general.md) för Azure-måldatorerna. Om du exempelvis planerar att migrera en produktionsmiljö bör du överväga standardnivån, som tillhandahåller virtuella datorer med låg svarstid men kan kosta mer. Om du å andra sidan har en miljö för utveckling och testning kanske du vill överväga Basic-nivån som har virtuella datorer med högre svarstider och lägre kostnader. | Som standard används [standardnivån](../virtual-machines/windows/sizes-general.md).
    **Komfortfaktor** | Azure Migrate överväger en buffert (komfortfaktor) under utvärderingen. Bufferten tillämpas utöver datorns användningsdata för virtuella datorer (CPU, minne, disk och nätverk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor. | Standardinställningen är 1.3x.
    **Erbjudande** | [Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/) som du är registrerad för. | [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) är standard.
    **Valuta** | Faktureringsvalutan. | Standardvärdet är USD.
    **Rabatt (%)** | Prenumerationsspecifika rabatter som du får utöver Azure-erbjudandet. | Standardinställningen är 0%.
    **Azure Hybrid-förmån** | Ange om du har Software Assurance och är berättigad [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om inställningen är Ja beaktas andra priser än Windows Azure-priser för virtuella Windows-datorer. | Standardvärdet är Ja.

3. Klicka på **spara** att uppdatera bedömningen.


## <a name="next-steps"></a>Nästa steg

[Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
