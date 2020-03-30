---
title: Gruppera datorer för utvärdering med Azure Migrate | Microsoft-dokument
description: Beskriver hur du grupperar datorer innan du kör en utvärdering med Azure Migrate-tjänsten.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 13c640d25265b2663520ef7ab203b0b0a33829e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68301665"
---
# <a name="create-a-group-for-assessment"></a>Skapa en grupp för bedömning

I den här artikeln beskrivs hur du skapar grupper av datorer för utvärdering med Azure Migrate: Server Assessment.

[Azure Migrate](migrate-services-overview.md) hjälper dig att migrera till Azure. Azure Migrate tillhandahåller en centraliserad hubb för att spåra identifiering, utvärdering och migrering av lokal infrastruktur, program och data till Azure. Hubben tillhandahåller Azure-verktyg för utvärdering och migrering samt ISV-erbjudanden (Independent Software Vendor) från tredje part. 

## <a name="grouping-machines"></a>Grupperingsmaskiner

Du samlar datorer i grupper för att bedöma om de är lämpliga för migrering till Azure och för att få Azure-storlek och kostnadsuppskattningar för dem. Det finns ett par sätt att skapa grupper:

- Om du vet att datorerna som behöver migreras tillsammans kan du skapa gruppen manuellt i Azure Migrate.
- Om du inte är säker på vilka datorer som behöver grupperas tillsammans kan du använda funktionen beroendevisualisering i Azure Migrate för att skapa grupper. 

> [!NOTE]
> Funktionen för beroendevisualisering är inte tillgänglig i Azure Government.

## <a name="create-a-group-manually"></a>Skapa en grupp manuellt

Du kan skapa en grupp samtidigt som du [skapar en utvärdering](how-to-create-assessment.md).

Om du vill skapa en grupp manuellt utanför en utvärdering gör du följande:

1. Klicka på **Utvärdera och migrera servrar**i Azure Migrate-projektet > **översikt**. I **Azure Migrate: Serverutvärdering**klickar du på **Grupper**
    - Om du ännu inte har lagt till verktyget Azure Migrate: Server Assessment klickar du för att lägga till det. [Läs mer](how-to-assess.md).
    - Om du ännu inte har skapat ett Azure Migrate-projekt [kan du läsa mer](how-to-add-tool-first-time.md).

    ![Välj grupper](./media/how-to-create-a-group/select-groups.png)

2. Klicka **Group** på gruppikonen.
3. I **Gruppen Skapa**anger du ett gruppnamn och väljer den Azure Migrate-apparat som du använder för datoridentifiering i Gruppen Skapa. **Appliance name**
1. Välj de datorer som du vill lägga till i gruppen > **Skapa**i maskinlistan .

    ![Skapa grupp](./media/how-to-create-a-group/create-group.png)

Du kan nu använda den här gruppen när du [skapar en utvärdering](how-to-create-assessment.md).

## <a name="refine-a-group-with-dependency-mapping"></a>Förfina en grupp med beroendemappning

Beroendemappning hjälper dig att visualisera beroenden mellan datorer. Du använder vanligtvis beroendemappning när du vill bedöma datorgrupper med högre konfidensnivåer.
- Det hjälper dig att dubbelkolla datorberoenden innan du kör en utvärdering. 
- Det hjälper också till att effektivt planera din migrering till Azure, genom att se till att ingenting lämnas kvar och på så sätt undvika överraskande avbrott under migreringen.
- Du kan identifiera beroende system som är beroende av varandra och identifiera om ett system som körs fortfarande betjänar användare eller är en kandidat för att inaktivera i stället för migrering.

Om du redan har [ställt in beroendemappning](how-to-create-group-machine-dependencies.md)och vill förfina en befintlig grupp gör du följande:

1. Klicka på **Grupper**i panelen **Serverutvärdering** på fliken **Servrar.**
2. Klicka på den grupp som du vill förfina.
    - Om du ännu inte har ställt in beroendemappning visas en **installationsstatus för** beroenden i kolumnen **Beroenden.** För varje virtuell dator som du vill visualisera beroenden för klickar du på **Kräver installation**. Installera ett par agenter på varje virtuell dator, innan du kan mappa datorberoenden. [Läs mer](how-to-create-group-machine-dependencies.md).

        ![Lägga till beroendemappning](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Om du redan har ställt in beroendemappning klickar du på **Visa beroenden** på gruppsidan för att öppna gruppberoendekartan.

3. När du har klickat på **Visa beroenden**visas följande på gruppberoendekartan:

    - Inkommande (klienter) och utgående (servrar) TCP-anslutningar till och från alla datorer i gruppen som har beroendeagenterna installerade.
    - Beroende datorer som inte har beroendeagenterna installerade grupperas efter portnummer.
    - Beroende datorer med installerade beroendeagenter visas som separata rutor.
    - Processer som körs inuti maskinen. Expandera varje maskinruta för att visa processerna.
    - Maskinegenskaper (inklusive FQDN, operativsystem, MAC-adress). Klicka på varje maskinruta för att visa detaljerna.

4. Om du vill visa beroenden i ett tidsintervall som du väljer ändrar du tidsintervallet (en timme som standard) genom att ange start- och slutdatum eller varaktigheten.

    > [!NOTE]
    > Tidsintervallet kan vara upp till en timme. Om du behöver ett längre intervall använder du Azure Monitor för [att fråga beroende data](how-to-create-group-machine-dependencies.md) under en längre period.

5. När du har identifierat de beroenden som du vill lägga till eller ta bort från gruppen kan du ändra gruppen. Använd Ctrl+Klicka för att lägga till eller ta bort datorer från gruppen.

    - Du kan bara lägga till datorer som har upptäckts.
    - Om du lägger till och tar bort datorer ogiltigförklaras tidigare utvärderingar för en grupp.
    - Du kan också skapa en ny bedömning när du ändrar gruppen.


## <a name="next-steps"></a>Nästa steg

Lär dig hur du ställer in och använder [beroendemappning](how-to-create-group-machine-dependencies.md) för att skapa grupper med högt förtroende.

