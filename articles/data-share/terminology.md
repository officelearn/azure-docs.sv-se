---
title: Azure Data Share Preview-terminologi
description: Azure Data Share Preview-terminologi
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: b78a1b250f46c2ddd2b36e19590c4a94ca3ab3fb
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844700"
---
# <a name="azure-data-share-preview-concepts"></a>Förhands gransknings begrepp för Azure Data Share 

I för hands versionen av Azure data sharing introduceras en del ny terminologi för data delning. I den här artikeln beskrivs några vanliga villkor som kan användas i hela tjänsten. 

## <a name="data-provider"></a>Dataleverantör

En data leverantör är den organisation som delar data med sina konsumenter. Data leverantören kan vanligt vis vara en ägare eller en curator av data. Data leverantörer vill dela data av olika typer. Några exempel på data som en data leverantör kan vilja dela innehåller rå data, till exempel data punkter för försäljning eller tids serier. En DataProvider kan också vilja dela förbehandlade, granskade data som redan innehåller analyser och insikter. 

## <a name="data-consumer"></a>Datakonsument 

En data konsument är den organisation som tar emot data från en data leverantör. Data konsumenten kanske vill ansluta till delade data med sina egna data för att kunna härleda insikter. I vissa fall kan data konsumenten ta emot data som redan har bearbetats. 

## <a name="data-share"></a>Data resurs

En data resurs är en grupp data uppsättningar som delas som en enda enhet. Data uppsättningar kan vara från ett antal Azure-datakällor som stöds av Azure Data Share. För närvarande stöder Azure Data Share Azure Blob Storage och Azure Data Lake Store. 

## <a name="share-subscription"></a>Dela prenumeration 

En resurs prenumeration skapas när en data konsument accepterar en data delnings inbjudan från en data leverantör. Data leverantörer kan visa aktiva resurs prenumerationer genom att navigera till **skickade resurser** i sitt Azure Data Share-konto och välja **dela prenumerationer**.

En data konsument kan kontrol lera om de har en aktiv resurs prenumeration genom att navigera till **mottagna resurser** och visa statusen för de mottagna resurserna. 

## <a name="snapshot"></a>Ögonblicksbild

En ögonblicks bild kan skapas av en data konsument när de accepterar en inbjudan om data delning. När de accepterar en inbjudan kan de utlösa en fullständig ögonblicks bild av de data som delas med dem. Ögonblicks bilden är en kopia av data vid den tidpunkt då data konsumenten skapade ögonblicks bilden. 

Det finns två typer av ögonblicks bilder – fullständiga och stegvisa. En fullständig ögonblicks bild innehåller alla data i data resursen. En stegvis ögonblicks bild innehåller alla data som har uppdaterats/lagts till sedan den senaste ögonblicks bilden utlöstes. 

## <a name="snapshot-settings-in-azure-data-share"></a>Inställningar för ögonblicks bilder i Azure Data Share
 
En DataProvider kan aktivera en inställning för ögonblicks bilder för en data resurs. Med den här inställningen kan data konsumenter ta emot stegvisa uppdateringar när de inträffar. Den här inställningen ska vara aktive rad om dataprovidern skulle gilla att deras data konsumenter tar emot uppdateringar av data som har delats. 

Om en DataProvider aktiverar den här inställningen kan du välja ett upprepnings intervall. Upprepnings intervallet kan vara varje timme eller varje dag. 

En data konsument har möjlighet att delta i ögonblicks bild scheman för att ta emot stegvisa uppdateringar som innehåller alla data som har ändrats sedan de först genererade en ny ögonblicks bild. 

## <a name="invitation"></a>Inbjudan

En DataProvider kan bjuda in flera mottagare till sina data resurser. De kan göra det genom att lägga till mottagare i data resursen. Inbjudningar kan också läggas till efter att en data resurs har skapats. 

En DataProvider kan ta bort en inbjudan när den har skickats om den inte har accepterats. Om dataprovidern tar bort en inbjudan och den ännu inte har accepterats kommer data konsumenten inte att kunna acceptera den. 

Inbjudningar kan skickas upp till 5 gånger per dag. 

## <a name="recipient"></a>Mottagare

En mottagare är någon som tar emot en inbjudan till en data resurs. Normalt lägger en DataProvider till mottagare till den data resurs som de skapar. När mottagaren av en inbjudan accepterar inbjudan blir han eller hon en data konsument.  

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) .

