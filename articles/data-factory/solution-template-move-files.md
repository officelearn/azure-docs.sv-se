---
title: Flytta filer mellan filbaserad lagring genom att använda Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder en lösnings mall för att flytta filer mellan filbaserad lagring med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/12/2019
ms.openlocfilehash: 9eb82a23aac5a98a521976118c1e859d0be253d0
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881248"
---
# <a name="move-files-with-azure-data-factory"></a>Flytta filer med Azure Data Factory

Den här artikeln beskriver en lösnings mall som du kan använda för att flytta filer från en mapp till en annan mellan filbaserade arkiv. Ett av de vanligaste scenarierna med den här mallen: Filerna släpps kontinuerligt till en landnings katalog i käll arkivet. Genom att skapa en schema utlösare kan ADF-pipeline regelbundet flytta filerna från källan till mål lagret.  Det sätt på vilket ADF-pipelinen når "flytta filer" hämtar filerna från landnings mappen, kopierar var och en av dem till en annan mapp på mål lagret och sedan tar bort samma filer från landnings-mappen i käll arkivet.

> [!NOTE]
> Tänk på att den här mallen är utformad för att flytta filer i stället för att flytta mappar.  Om du vill flytta mappen genom att ändra data uppsättningen så att den bara innehåller en mappsökväg, och sedan använda aktiviteten Kopiera aktivitet och ta bort för att referera till samma data uppsättning som representerar en mapp, måste du vara mycket försiktig. Det beror på att du måste se till att det inte finns nya filer som kommer in i mappen mellan kopierings åtgärden och borttagnings åtgärden. Om det finns nya filer som kommer till mappen när kopierings aktiviteten precis har slutfört kopieringen men borttagnings aktiviteten inte har varit avslutad, är det möjligt att borttagnings aktiviteten tar bort den här nya inkommande filen som inte har kopierats till destinati ännu genom att ta bort hela mappen.

## <a name="about-this-solution-template"></a>Om den här lösnings mal len

Den här mallen hämtar filerna från det källfilbaserade arkivet. Sedan flyttas var och en av dem till mål lagret.

Mallen innehåller fem aktiviteter:
- **GetMetaData** hämtar listan över objekt, inklusive filer och undermappar från din mapp i käll arkivet. Objektet kommer inte att hämta rekursivt objekt. 
- **Filtrera** filter objekt listan från **getMetaData** -aktivitet för att välja enbart filerna. 
- Med **början får fil** listan från **filter** aktiviteten och itererar sedan över listan och överför varje fil till aktiviteten Kopiera och ta bort.
- **Kopiera** kopierar en fil från källan till mål butiken.
- **Ta bort** tar bort samma fil från käll arkivet.

Mallen definierar två parametrar:
- *FolderPath_SourceStore* är mappsökvägen till käll arkivet där du vill flytta filer från. 
- *FolderPath_DestinationStore* är sökvägen till den mål lagrings plats där du vill flytta filer till. 

## <a name="how-to-use-this-solution-template"></a>Så här använder du den här lösnings mal len

1. Gå till mallen **Flytta filer** . Välj en befintlig anslutning eller skapa en **ny** anslutning till käll fil arkivet dit du vill flytta filer. Tänk på att **DataSource_Folder** och **DataSource_File** refererar till samma anslutning till käll fil arkivet.

    ![Skapa en ny anslutning till källan](media/solution-template-move-files/move-files1.png)

2. Välj befintlig anslutning eller skapa en **ny** anslutning till mål fil arkivet dit du vill flytta filer till.

    ![Skapa en ny anslutning till målet](media/solution-template-move-files/move-files2.png)

3. Välj **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-move-files/move-files3.png)
    
4. Du ser pipelinen, som i följande exempel:

    ![Visa pipelinen](media/solution-template-move-files/move-files4.png)

5. Välj **Felsök**, ange **parametrarna**och välj sedan **Slutför**.   Parametrarna är sökvägen till mappen där du vill flytta filer från och den mappsökväg dit du vill flytta filer. 

    ![Köra en pipeline](media/solution-template-move-files/move-files5.png)

6. Granska resultatet.

    ![Granska resultatet](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>Nästa steg

- [Kopiera nya och ändrade filer genom LastModifiedDate med Azure Data Factory](solution-template-copy-new-files-lastmodifieddate.md)

- [Kopiera filer från flera behållare med Azure Data Factory](solution-template-copy-files-multiple-containers.md)