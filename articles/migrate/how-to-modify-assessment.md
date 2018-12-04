---
title: Anpassa inställningar för Azure Migrate utvärdering | Microsoft Docs
description: Beskriver hur du konfigurerar och kör en utvärdering för att migrera virtuella VMware-datorer till Azure med Azure-Migreringshanteraren
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/30/2018
ms.author: raynew
ms.openlocfilehash: 2423c4fde177ab50552af580a60c7a15550e5586
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840443"
---
# <a name="customize-an-assessment"></a>Anpassa en utvärdering

[Azure Migrate](migrate-overview.md) skapar utvärderingar med standardegenskaper. Du kan ändra standardegenskaperna med hjälp av anvisningarna i den här artikeln när du har skapat en utvärdering.


## <a name="edit-assessment-properties"></a>Redigera egenskaperna för utvärdering

1. På sidan **Utvärderingar** i migreringsprojektet väljer du utvärderingen och klickar på **Redigera egenskaper**.
2. Anpassa egenskaperna för utvärdering baserat på informationen nedan:

    **Inställning** | **Detaljer** | **Standard**
    --- | --- | ---
    **Målplats** | Azure-platsen du vill migrera till.<br/><br/> För närvarande stöder Azure Migrate 30 regioner, bland andra: Australien, östra; Australien, sydöstra; Brasilien, södra; Kanada, centrala; Kanada, östra; Indien, centrala; USA, centrala; Kina, östra; Kina, norra; Asien, östra; USA, östra; Tyskland, centrala; Tyskland, nordöstra; USA, östra 2; Japan, östra; Japan, västra; Sydkorea, centrala; Sydkorea, södra; USA, norra centrala; Europa, norra; USA, södra centrala; Asien, sydöstra; Indien, södra; Storbritannien, södra; Storbritannien, västra; US Gov, Arizona; US Gov, Texas; US Gov, Virginia; USA, västra centrala; Europa, västra; Indien, västra; USA, västra och USA, västra 2. |  USA, västra 2 är standardplatsen.
    **Lagringstyp** | Du kan använda den här egenskapen för att ange vilken typ av diskar som du vill tilldela i Azure. För som-on-premises som ändrar storlek, du vill kan du ange måldisktyp som Premium-hanterade diskar eller Standard-hanterade diskar. För prestandabaserade storleksändringar behöver ange du disk måltypen som automatisk, Premium-hanterade diskar eller Standard-hanterade diskar. När du anger lagringstypen som automatisk görs disk rekommendationen baserat på prestandadata för diskar (IOPS och dataflöde). Exempel: Om du vill uppnå en [instans VM SLA på 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), kan du ange lagringstypen som Premium-hanterade diskar som säkerställer att alla diskar i utvärderingen rekommenderas som Premium-hanterade diskar. Observera att Azure Migrate endast stöder hanterade diskar för migreringsutvärdering. | Standardvärdet är Premium-hanterade diskar (med storlekskriteriet som *som lokalt storlek*).
    **Reserverade instanser** |  Du kan också ange om du har [reserverade instanser](https://azure.microsoft.com/pricing/reserved-vm-instances/) i Azure så beräknar Azure Migrate kostnaden därefter. Reserverade instanser kan inte användas för landsbaserade regioner (Azure Government, Tyskland och Kina) och de är gäller endast för betala per användning-erbjudandet i Azure Migrate. | Standardvärdet för den här egenskapen är 3 års reserverade instanser.
    **Ändra storlek på kriterium** | Kriteriet som ska användas av Azure Migrate för att ställa in rätt storlek på virtuella datorer för Azure. Du kan antingen göra en *prestandabaserad* storleksändring eller ändra storlek på de virtuella datorerna *som lokalt*, utan att överväga prestandahistorik. | Standardalternativet är prestandabaserad storleksändring.
    **Prestandahistorik** | Tidsperioden att beakta när du utvärderar prestanda för de virtuella datorerna. Den här egenskapen gäller bara när storleksändringskriteriet är *prestandabaserad storleksändring*. | Standardvärdet är en dag.
    **Percentilutnyttjande** | Percentilvärdet för prestandaexempeluppsättningen som beaktas för rätt storleksändring. Den här egenskapen gäller bara när storleksändringskriteriet är *prestandabaserad storleksändring*.  | Standardvärdet är 95: e percentilen.
    **VM-serie** | Du kan ange den VM-serie som du vill överväga vid storleksberäkningen. Om du till exempel har en produktionsmiljö som du inte planerar att migrera till A-serien av virtuella datorer i Azure, kan du utesluta A-serien från listan eller serien, så fastställs storleken endast baserat på de valda serierna. | Som standard markeras alla VM-serier.
    **Komfortfaktor** | Azure Migrate överväger en buffert (komfortfaktor) under utvärderingen. Bufferten tillämpas utöver datorns användningsdata för virtuella datorer (CPU, minne, disk och nätverk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor. | Standardinställningen är 1.3x.
    **Erbjudande** | [Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/) som du är registrerad för. | [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) är standard.
    **Valuta** | Faktureringsvalutan. | Standardvärdet är USD.
    **Rabatt (%)** | Prenumerationsspecifika rabatter som du får utöver Azure-erbjudandet. | Standardinställningen är 0%.
    **VM-drifttid** | Om dina virtuella datorer inte ska som körs 24 x 7 i Azure måste du ange varaktighet (antal dagar per månad) och antalet timmar per dag för som de skulle köra och kostnadsuppskattningar kommer att ske i enlighet med detta. | Standardvärdet är 31 dagar per månad och 24 timmar per dag.
    **Azure Hybrid-förmån** | Ange om du har Software Assurance och är berättigad [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om inställningen är Ja beaktas andra priser än Windows Azure-priser för virtuella Windows-datorer. | Standardvärdet är Ja.

3. Klicka på **spara** att uppdatera utvärderingen.


## <a name="next-steps"></a>Nästa steg

[Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
