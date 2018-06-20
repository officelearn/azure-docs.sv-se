---
title: Gruppera datorer för bedömning med Azure migrera | Microsoft Docs
description: Beskriver hur du grupperar datorer innan du kör en bedömning med tjänsten Azure migrera.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: ccab88c0195a7ca459c8579b7870d121dfd0fe1d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231414"
---
# <a name="group-machines-for-assessment"></a>Grupp datorer för utvärdering

Den här artikeln beskriver hur du skapar en grupp datorer för bedömning av [Azure migrera](migrate-overview.md). Azure migrera utvärderar datorer i gruppen för att kontrollera om de är lämplig för migrering till Azure och ger storlek och kostnad beräkningar för att köra datorn i Azure.


## <a name="create-a-group"></a>Skapa en grupp

1. I den **översikt** Azure migrera projektet, under hantera, klickar du på **grupper** > **+ grupp**, och ange ett gruppnamn.
2. Lägg till en eller flera datorer i gruppen och klicka på **skapa**. 
3. Du kan också välja för att köra en ny utvärdering för gruppen. 

    ![Skapa en grupp](./media/how-to-create-a-group/create-group.png)

När gruppen har skapats kan du ändra den genom att välja gruppen på den **grupper** sidan och lägga till eller ta bort datorer.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder [datorn beroende mappning](how-to-create-group-machine-dependencies.md) skapa hög exakthet grupper.
- [Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
