---
title: "Gruppera datorer för bedömning med Azure migrera | Microsoft Docs"
description: "Beskriver hur du grupperar datorer innan du kör en bedömning med tjänsten Azure migrera."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5c279804-aa30-4946-a222-6b77c7aac508
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 966e0aebf56ab049e898d1787bfdfbb2ef23635e
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2017
---
# <a name="group-machines-for-assessment"></a>Grupp datorer för utvärdering

Den här artikeln beskriver hur du skapar en grupp datorer för bedömning av [Azure migrera](migrate-overview.md). Azure migrera utvärderar datorer i gruppen för att kontrollera om de är lämplig för migrering till Azure och ger storlek och kostnad beräkningar för att köra datorn i Azure.


## <a name="create-a-group"></a>Skapa en grupp

1. I den **instrumentpanelen** Azure migrera-projektet klickar du på **grupper** > **+ grupp**, och ange ett gruppnamn.
2. Lägg till en eller flera datorer i gruppen och klicka på **skapa**. 
3. Du kan också välja för att köra en ny utvärdering för gruppen. 

    ![Skapa en grupp](./media/how-to-create-a-group/create-group.png)

När gruppen har skapats kan du ändra den genom att välja gruppen på den **grupper** sidan och lägga till eller ta bort datorer.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder [datorn beroende mappning](how-to-create-group-machine-dependencies.md) att skapa mer detaljerade grupper.
- [Lär dig mer](concepts-assessment-calculation.md) om hur bedömningar beräknas.
