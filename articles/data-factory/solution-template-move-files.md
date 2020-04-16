---
title: Flytta filer mellan filbaserad lagring
description: Lär dig hur du använder en lösningsmall för att flytta filer mellan filbaserad lagring med hjälp av Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/12/2019
ms.openlocfilehash: b36eb2615e98ee8ea7751c836fd43e81a5a0f4e2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414749"
---
# <a name="move-files-with-azure-data-factory"></a>Flytta filer med Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs en lösningsmall som du kan använda för att flytta filer från en mapp till en annan mellan filbaserade butiker. Ett av de vanligaste scenarierna med att använda den här mallen: Filer släpps kontinuerligt till en landningsmapp i källarkivet. Genom att skapa en schemautlösare kan ADF-pipelinen regelbundet flytta dessa filer från källan till målarkivet.  Det sätt som ADF pipeline uppnår "rörliga filer" är att få filerna från landningsmappen, kopiera var och en av dem till en annan mapp på målarkivet och sedan ta bort samma filer från landningsmappen på källarkivet.

> [!NOTE]
> Tänk på att den här mallen är utformad för att flytta filer i stället för att flytta mappar.  Om du vill flytta mappen genom att ändra datauppsättningen så att den bara innehåller en mappsökväg och sedan använder kopieringsaktiviteten och ta bort aktiviteten för att referera till samma datauppsättning som representerar en mapp, måste du vara mycket försiktig. Det beror på att du måste se till att det inte kommer nya filer som kommer in i mappen mellan kopiering och borttagning. Om det finns nya filer som anländer till mappen vid den tidpunkt då kopieringsaktiviteten just slutfört kopieringsjobbet men aktiviteten Ta bort inte har stirrats, är det möjligt att ta bort aktiviteten Ta bort den nya ankommande filen som INTE har kopierats till målet ännu genom att ta bort hela mappen.

## <a name="about-this-solution-template"></a>Om den här lösningsmallen

Den här mallen hämtar filerna från källfilbaserade arkivet. Den flyttar sedan var och en av dem till destinationsarkivet.

Mallen innehåller fem aktiviteter:
- **GetMetadata** hämtar listan över objekt, inklusive filer och undermappar från mappen i källarkivet. Objekten hämtas inte rekursivt. 
- **Filtrera** objektlistan från **GetMetadata-aktivitet** för att välja endast filerna. 
- **ForEach** hämtar fillistan från **filteraktiviteten** och itererar sedan över listan och skickar varje fil till kopieringsaktiviteten och ta bort aktiviteten.
- **Kopiera** kopior av en fil från källan till målarkivet.
- **Ta** bort tar bort samma fil från källarkivet.

Mallen definierar fyra parametrar:
- *SourceStore_Location* är mappsökvägen för källarkivet där du vill flytta filer från. 
- *SourceStore_Directory* är undermappssökvägen i källarkivet där du vill flytta filer från.
- *DestinationStore_Location* är mappsökvägen för målarkivet där du vill flytta filer till. 
- *DestinationStore_Directory* är undermappssökvägen till målarkivet där du vill flytta filer till.

## <a name="how-to-use-this-solution-template"></a>Så här använder du den här lösningsmallen

1. Gå till mallen **Flytta filer.** Välj befintlig anslutning eller skapa en **ny** anslutning till källfilarkivet där du vill flytta filer från. Tänk på att **DataSource_Folder** och **DataSource_File** refererar till samma anslutning till källfilarkivet.

    ![Skapa en ny anslutning till källan](media/solution-template-move-files/move-files1.png)

2. Välj befintlig anslutning eller skapa en **ny** anslutning till målfilarkivet där du vill flytta filer till.

    ![Skapa en ny anslutning till målet](media/solution-template-move-files/move-files2.png)

3. Välj **Använd den här mallfliken.**
    
4. Du ser pipelinen, som i följande exempel:

    ![Visa pipelinen](media/solution-template-move-files/move-files4.png)

5. Välj **Felsökning,** ange **parametrar**och välj sedan **Slutför**.   Parametrarna är mappsökvägen där du vill flytta filer från och mappsökvägen där du vill flytta filer till. 

    ![Köra en pipeline](media/solution-template-move-files/move-files5.png)

6. Granska resultatet.

    ![Granska resultatet](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>Nästa steg

- [Kopiera nya och ändrade filer efter LastModifiedDate med Azure Data Factory](solution-template-copy-new-files-lastmodifieddate.md)

- [Kopiera filer från flera behållare med Azure Data Factory](solution-template-copy-files-multiple-containers.md)