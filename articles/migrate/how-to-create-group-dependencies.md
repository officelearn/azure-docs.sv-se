---
title: "Förfina en bedömning grupp med Gruppmappning av beroende i Azure migrera | Microsoft Docs"
description: "Beskriver hur du förfina bedömning med mappning av beroende i tjänsten Azure migrera."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0527e34e-a078-405e-aeb9-c91a5808112a
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: b4d6861f147fbb6e65a9d529f17f78b54075eb90
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2017
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Förfina grupp med mappning av beroende

Den här artikeln beskriver hur du ställer in beroende mappning för [Azure migrera](migrate-overview.md) assessment. Du använder vanligtvis den här metoden när du vill begränsa inställningar för en befintlig grupp genom att dubbelkontrollera grupp beroenden, innan du kör en utvärdering. Grupper som du vill köra mappning av beroende får inte innehålla fler än 10-datorer.  

## <a name="modify-a-group"></a>Ändra en grupp

1. I Azure migrera projektet, under **hantera**, klickar du på **grupper**, och välj gruppen.
2. På sidan grupp **Visa beroenden**, för att öppna grupp beroende kartan. 

     ![Visa grupp](./media/how-to-create-group-dependencies/create-group.png)

3. Klicka på tidsintervall om du vill ändra den om du vill visa mer detaljerad beroenden. Som standard är intervallet en timme. Du kan ändra tidsintervallet eller ange start- och slutdatum och varaktighet.
4. Använd Ctrl + klicka om du vill välja datorer på kartan. Lägg till eller ta bort datorer från mappningen.
    - Du kan bara lägga till datorer som har identifierats.
    - Lägga till och ta bort datorer från en grupp upphäver tidigare bedömningar för den.
    - Du kan också skapa en ny utvärdering när du ändrar gruppen.
5. Klicka på **OK** att spara gruppen.

    ![Lägga till och ta bort](./media/how-to-create-group-dependencies/add-remove.png)

Om du vill kontrollera beroenden för en specifik dator som visas i gruppen beroende kartan [Ställ in datorn beroende mappning](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>Nästa steg

[Lär dig mer](concepts-assessment-calculation.md) om hur bedömningar beräknas.
