---
title: Anpassa utvärderingar för Azure Migrate Server-utvärdering | Microsoft Docs
description: Beskriver hur du anpassar utvärderingar som skapats med Azure Migrate Server-utvärdering
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 8b200ce3d6e73a575b1b89d82a9323d58f435a48
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807914"
---
# <a name="customize-an-assessment"></a>Anpassa en utvärdering

Den här artikeln beskriver hur du anpassar utvärderingar som skapats av Azure Migrate Server-utvärdering.

[Azure Migrate](migrate-services-overview.md) ger en central knutpunkt för att spåra identifiering, bedömning och migrering av dina lokala appar och arbetsbelastningar och privata/offentliga virtuella datorer till Azure. Hubben innehåller Azure Migrate verktyg för bedömning och migrering, samt tredjepartsprogram oberoende programvaruleverantörer (ISV)-erbjudanden.

Du kan använda verktyget Azure Migrate Server-utvärdering för att skapa utvärderingar för lokala virtuella VMware-datorer och Hyper-V-datorer som förberedelse för migrering till Azure. 

## <a name="about-assessments"></a>Om utvärderingar

Det finns två typer av utvärderingar kan du köra med Azure Migrate Server-utvärdering.

**Utvärdering** | **Detaljer** | **Data**
--- | --- | ---
**Prestandabaserad** | Utvärderingar utifrån insamlade prestandadata | **Rekommenderad storlek**: Baserat på användningsdata för CPU och minne.<br/><br/> **Rekommenderade disktyp (standard eller premium managed disk)** : Baserat på IOPS och dataflöden för lokala diskar.
**Som lokalt** | Utvärderingar baserat på den lokala storlek. | **Rekommenderad storlek**: Baserat på den lokala VM-storlek<br/><br> **Rekommenderade disktyp**: Baserat på storage-typ du väljer för utvärderingen.


## <a name="how-is-an-assessment-done"></a>Hur görs en utvärdering?

Ett Azure Migrate-utvärdering har tre steg. Utvärderingen börjar med en lämplighet analys, följt av storlek, och slutligen en månatlig kostnadsuppskattning. En dator flyttar endast till senare om den godkänns föregående. Till exempel om en dator misslyckas kontrollen Azure-lämplighet, har den markerats som olämpliga för Azure, och ange storlek och kostnad inte göras.

## <a name="whats-in-an-assessment"></a>Vad ingår i en utvärdering?

**Egenskap** | **Detaljer**
--- | ---
**Målplats** | Azure-platsen du vill migrera till.<br/> Azure Migrate stöder för närvarande dessa målregioner: Östra Australien, sydöstra Australien, södra Brasilien, Kanada-centrala, Kanada, östra, centrala Indien, centrala USA, Kina, östra, Kina, norra, Asien, östra USA, östra usa2, centrala Tyskland, nordöstra Tyskland, Japan östra, Japan, västra centrala Korea, södra Nord Centrala USA, Nordeuropa, södra centrala USA, Sydostasien, södra Indien, Storbritannien, södra, Storbritannien, västra, Virginia (USA-förvaltad region Arizona USA Gov Texas, USA-förvaltad region), USA, västra centrala, Västeuropa, västra Indien, västra USA och USA, västra 2.<br/> Målregionen är som standard angiven som USA, västra 2.
**Lagringstyp** | Premium/standard HHD diskar/Standard SSD-diskar.<br/> När du anger lagringstypen som automatisk i en utvärdering, baseras disk-rekommendationen på prestandadata för diskar (IOPS och dataflöde).<br/> Om du anger lagringstypen som Premium/Standard valt utvärderingen rekommenderar en disk SKU i lagringstyp.<br/> Om du vill få en enda instans VM SLA på 99,9%, kan du ange lagringstypen som Premium-hanterade diskar. Alla diskar i utvärderingen kommer sedan att rekommenderas som Premium-hanterade diskar. <br/> Azure Migrate stöder endast hanterade diskar för migreringsutvärdering.<br/> 
**Reserverade instanser (RI)** | Ange den här egenskapen om du har reserverade instanser i Azure. Kostnad få uppskattningar i utvärderingen ska beakta RI rabatter. Reserverade instanser är för närvarande stöds endast för användningsbaserade erbjudanden i Azure Migrate.
**Ändra storlek på kriterium** | Används för att storleksanpassa virtuella datorer. Storlek kan vara prestandabaserad, eller **som lokalt**, utan att överväga prestandahistorik.
**Prestandahistorik** | Tidsperioden att beakta för att utvärdera prestanda för virtuella datorer. Den här egenskapen gäller endast när storlek är prestandabaserad.
**Percentilutnyttjande** | Percentilvärdet för prestanda-exemplet som används för rätt storlek för virtuella datorer. Den här egenskapen gäller endast när storlek är prestandabaserad.
**VM-serie** | Den virtuell dator-serie som används för storleksuppskattningar. Om du till exempel har en produktionsmiljö som du inte planerar att migrera till A-seriens virtuella datorer i Azure kan du utesluta A-serien från listan eller serien. Storleken baseras bara på den valda serien.
**Komfortfaktor** | Azure Migrate Server-utvärdering överväger en buffert (komfortfaktor) under utvärderingen. Bufferten tillämpas utöver datorns användningsdata för virtuella datorer (CPU, minne, disk och nätverk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor.
**Erbjudande** | Det [Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/) du har registrerat dig för. Azure Migrate beräknar kostnaden enligt detta.
**Valuta** | Faktureringsvalutan. 
**Rabatt (%)** | Prenumerationsspecifika rabatter som du får utöver Azure-erbjudandet.<br/> Standardinställningen är 0%.
**VM-drifttid** | Om dina virtuella datorer inte ska som körs 24 x 7 i Azure måste du ange varaktighet (antal dagar per månad) och antalet timmar per dag för som de skulle köra och kostnadsuppskattningar utförs i enlighet med detta.<br/> Standardvärdet är 31 dagar per månad och 24 timmar per dag.
**Azure Hybrid-förmån** | Anger om du har software assurance och att du är berättigad till [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om inställningen är Ja beaktas andra priser än Windows Azure-priser för virtuella Windows-datorer. | Standardvärdet är Ja.


## <a name="edit-assessment-properties"></a>Redigera egenskaperna för utvärdering

Om du vill redigera egenskaperna för utvärdering när du har skapat en utvärdering, gör du följande:

1. I Azure Migrate-projektet klickar du på **servrar**.
2. I **Azure Migrate: Server-utvärdering**, klicka på antalet utvärderingar.
3. I **utvärdering**, klickar du på den relevanta utvärderingen > **redigera egenskaper för**.
5. Anpassa egenskaperna för utvärdering i enlighet med tabellen ovan.
6. Klicka på **spara** att uppdatera utvärderingen.


Du kan också redigera egenskaperna för utvärdering när du skapar en utvärdering.


## <a name="next-steps"></a>Nästa steg

[Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
