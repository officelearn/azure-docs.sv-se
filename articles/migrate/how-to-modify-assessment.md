---
title: Anpassa utvärderingar för Utvärdering av Azure Migrate Server | Microsoft-dokument
description: Beskriver hur du anpassar utvärderingar som skapats med Azure Migrate Server Assessment
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68234276"
---
# <a name="customize-an-assessment"></a>Anpassa en utvärdering

I den här artikeln beskrivs hur du anpassar utvärderingar som skapats av Azure Migrate Server Assessment.

[Azure Migrate](migrate-services-overview.md) är en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbetsbelastningar och privata/offentliga virtuella datorer i molnet till Azure. Hubben tillhandahåller Azure Migrate-verktyg för utvärdering och migrering samt ISV-erbjudanden (Independent Software Vendor) från tredje part.

Du kan använda verktyget Azure Migrate Server Assessment för att skapa utvärderingar för lokala virtuella datorer med VMware och virtuella virtuella hyper-virtuella datorer, som förberedelse för migrering till Azure.

## <a name="about-assessments"></a>Om bedömningar

Det finns två typer av utvärderingar som du kan köra med Azure Migrate Server Assessment.

**Utvärdering** | **Detaljer** | **Data**
--- | --- | ---
**Prestationsbaserad** | Bedömningar baserade på insamlade resultatdata | **Rekommenderad VM-storlek:** Baserat på CPU- och minnesanvändningsdata.<br/><br/> **Rekommenderad disktyp (standard eller premiumhanterad disk)**: Baserat på IOPS och dataflöde för de lokala diskarna.
**Som lokalt** | Bedömningar baserade på lokal storlek. | **Rekommenderad VM-storlek:** Baserat på den lokala vm-storleken<br/><br> **Rekommenderad disktyp**: Baserat på den lagringstyp som du väljer för utvärderingen.


## <a name="how-is-an-assessment-done"></a>Hur görs en bedömning?

En utvärdering som görs i Azure Migrate Server Assessment har tre steg. Bedömningen inleds med en lämplighetsanalys, följt av storlek, och slutligen en månatlig kostnadsuppskattning. En maskin flyttas bara vidare till ett senare stadium om den passerar den föregående. Om en dator till exempel misslyckas med Azure-lämplighetskontrollen markeras den som olämplig för Azure och storlek och kostnadsredovisning görs inte. [Läs mer.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>Vad ingår i en utvärdering?

**Egenskap** | **Detaljer**
--- | ---
**Målplats** | Azure-platsen du vill migrera till.<br/> Server Assessment stöder för närvarande dessa målregioner: Östra Australien, Sydöstra Australien, Södra Brasilien, Centrala Kanada, Östra Kanada, Centrala Indien, Centrala USA, Östra Kina, Kina Nord-, Östasien, Östra USA, Östra USA2, Tyskland Central, Tyskland Nordost, Japan East, Japan West, Korea Central, Korea South, North Central US, North Europe, South Central US, Southeast Asia, South India, UK South, UK West, US Gov Arizona, US Gov Texas, US Gov Virginia, West Central US, West Europe, West India, West US och West US2.
**Lagringstyp** | Du kan använda den här egenskapen för att ange vilken typ av diskar du vill flytta till i Azure.<br/><br/> För lokalstorlek kan du ange mållagringstypen antingen som Premium-hanterade diskar, Standard SSD-hanterade diskar eller vanliga hårddiskar. För prestandabaserad storlek kan du ange måldisktypen antingen som Automatiska, Premium-hanterade diskar, Vanliga hårddiskar som hanteras av hdd eller Standard SSD-hanterade diskar.<br/><br/> När du anger lagringstypen som automatisk görs diskrekommendationen baserat på diskarnas prestandadata (IOPS och dataflöde). Om du anger lagringstypen som premium/standard rekommenderar bedömningen en disk SKU inom den valda lagringstypen. Om du vill uppnå ett vm-serviceavtal med en instans på 99,9 %, kanske du vill ange lagringstypen som Premium-hanterade diskar. Detta säkerställer att alla diskar i utvärderingen rekommenderas som Premium-hanterade diskar. Azure
**Reserverade instanser (RI)** | Den här egenskapen hjälper dig att ange om du har [reserverade instanser](https://azure.microsoft.com/pricing/reserved-vm-instances/) i Azure, kostnadsuppskattningar i utvärderingen görs sedan med RI-rabatter. Reserverade instanser stöds för närvarande endast för pay-as-you-go-erbjudande i Azure Migrate.
**Ändra storlek på kriterium** | Det villkor som ska användas för att rätt storlek virtuella datorer för Azure. Du kan antingen göra *prestandabaserad* storlek eller ändra storlek på de virtuella datorerna *som lokala datorer*, utan att ta hänsyn till prestandahistoriken.
**Prestandahistorik** | Varaktigheten att tänka på för att utvärdera prestandadata för datorer. Den här egenskapen är endast tillämplig när storleksvillkor är *prestationsbaserat*.
**Percentilutnyttjande** | Percentilvärdet för prestandaexempeluppsättningen som beaktas för rätt storleksändring. Den här egenskapen är endast tillämplig när storleksändringen är *prestandabaserad*.
**VM-serie** |     Du kan ange den VM-serie som du vill överväga vid storleksberäkningen. Om du till exempel har en produktionsmiljö som du inte planerar att migrera till virtuella datorer i A-serien i Azure, kan du utesluta A-serier från listan eller serien och högerstorleken görs endast i den valda serien.
**Komfortfaktor** | Azure Migrate Server Assessment tar hänsyn till en buffert (komfortfaktor) under utvärderingen. Bufferten tillämpas utöver datorns användningsdata för virtuella datorer (CPU, minne, disk och nätverk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor.
**Erbjuder** | Det [Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/) du har registrerat dig för. Azure Migrate beräknar kostnaden enligt detta.
**Valuta** | Faktureringsvalutan.
**Rabatt (%)** | Prenumerationsspecifika rabatter som du får utöver Azure-erbjudandet.<br/> Standardinställningen är 0%.
**VM-drifttid** | Om dina virtuella datorer inte kommer att köras 24x7 i Azure kan du ange varaktigheten (antal dagar per månad och antal timmar per dag) som de skulle köras för och kostnadsuppskattningarna skulle göras i enlighet med detta.<br/> Standardvärdet är 31 dagar per månad och 24 timmar per dag.
**Azure Hybrid-förmån** | Ange om du har programvarusäkring och är berättigad till [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om inställningen är Ja beaktas andra priser än Windows Azure-priser för virtuella Windows-datorer. Standardvärdet är Ja.


## <a name="edit-assessment-properties"></a>Redigera bedömningsegenskaper

Så här redigerar du bedömningsegenskaper när du har skapat en utvärdering:

1. Klicka på **Servrar**i Azure Migrate-projektet .
2. I **Azure Migrate: ServerUtvärdering**klickar du på antalet utvärderingar.
3. Klicka på relevanta > Redigera **egenskaper**i **Bedömning.**
5. Anpassa bedömningsegenskaperna i enlighet med tabellen ovan.
6. Klicka på **Spara** om du vill uppdatera utvärderingen.


Du kan också redigera bedömningsegenskaper när du skapar en utvärdering.


## <a name="next-steps"></a>Nästa steg

[Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
