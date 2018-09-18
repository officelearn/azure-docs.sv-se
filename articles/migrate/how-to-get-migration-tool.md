---
title: Migrera datorer efter utvärdering med Azure Migrate | Microsoft Docs
description: Beskriver hur du får rekommendationer för att migrera datorer när du har kört en utvärdering med Azure Migrate-tjänsten.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 09/17/2018
ms.author: raynew
ms.openlocfilehash: 0b02ae4b75426b379ad7c124f5ddeb053c142ce6
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730302"
---
# <a name="migrate-machines-after-assessment"></a>Migrera datorer efter utvärdering


[Azure Migrate](migrate-overview.md) utvärderar lokala datorer för att kontrollera om de är lämplig för migrering till Azure och ger uppskattningar för storlek och kostnad för att köra datorn i Azure. För närvarande kan utvärderar Azure Migrate enbart datorer för migrering. Själva migreringen utförs för närvarande med andra Azure-tjänster.

Den här artikeln beskriver hur du får förslag på Migreringsverktyg när du har kört en migreringsutvärdering.

## <a name="migration-tool-suggestion"></a>Migrering verktyget förslag

Du behöver göra en djupgående identifieringen av den lokala miljön för att få förslag om Migreringsverktyg. Djupgående identifieringen görs genom att installera agenter på lokala datorer.  

1. Skapa ett Azure Migrate-projekt, identifiera lokala datorer och skapa en migreringsutvärdering. [Läs mer](tutorial-assessment-vmware.md).
2. Ladda ned och installera Azure Migrate-agenter på varje lokal dator som du vill se en rekommenderad migreringsmetod. [Följ den här proceduren](how-to-create-group-machine-dependencies.md#prepare-for-dependency-visualization) att installera agenter.
2. Identifiera dina lokala datorer som är lämplig för lift and shift-migrering. Det här är de virtuella datorer som inte kräver ändringar i appar som körs på dem och kan migreras skick.
3. Vi rekommenderar för lift and shift-migrering med Azure Site Recovery. [Läs mer](../site-recovery/tutorial-migrate-on-premises-to-azure.md). Alternativt kan använda du verktyg från tredje part som har stöd för migrering till Azure.
4. Om du har lokala datorer som inte är lämplig för lift and shift-migrering, om du vill migrera specifik app i stället för en hel virtuell dator, kan du använda andra Migreringsverktyg. Exempelvis kan vi föreslår den [Azure Database Migration service](https://azure.microsoft.com/campaigns/database-migration/) om du vill migrera lokala databaser sådana en SQL Server, MySQL eller Oracle till Azure.


## <a name="review-suggested-migration-methods"></a>Granska föreslagna metoder

1. Innan du kan hämta en föreslagna migreringsmetod, måste du skapa ett Azure Migrate-projekt, identifiera lokala datorer och kör en migreringsutvärdering. [Läs mer](tutorial-assessment-vmware.md).
2. När utvärderingen har skapats kan du visa den i projektet > **översikt** > **instrumentpanelen**. Klicka på **Utvärderingsberedskap**

    ![Utvärderingsberedskap](./media/tutorial-assessment-vmware/assessment-report.png)  

3. I **föreslås verktyget**, granska förslag för verktyg som du kan använda för migrering.

    ![Föreslaget verktyg](./media/tutorial-assessment-vmware/assessment-suitability.png)




## <a name="next-steps"></a>Nästa steg

[Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
