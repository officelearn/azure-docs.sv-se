---
title: Terminologi för Azure Data Share
description: Lär dig mer om vanliga termer som används för att beskriva resurser som används i Azure Data Share (dataprovider, datakonsument, datadelning, aktieprenumeration, ögonblicksbild, inbjudan, mottagare.)
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 33532380d8f98df44029eeea998130d1da5fdafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73468550"
---
# <a name="azure-data-share-concepts"></a>Azure Data Share-koncept 

Azure Data Share introducerar en del ny terminologi som är relaterad till datadelning. I den här artikeln beskrivs några vanliga termer som kan visas i hela tjänsten. 

## <a name="data-provider"></a>Uppgiftslämnare

En dataleverantör är den organisation som delar data med sina konsumenter. Vanligtvis kan dataleverantören vara ägare eller intendent för data. Uppgiftslämnare vill dela data av olika typer. Några exempel på data som en dataprovider kanske vill dela är rådata, till exempel försäljnings- eller tidsseriedata. En dataleverantör kanske också vill dela förbehandlade, kurerade data som redan innehåller analyser och insikter. 

## <a name="data-consumer"></a>Data konsument 

En datakonsument är den organisation som tar emot data från en dataleverantör. Datakonsumenten kanske vill ansluta sig till delade data med sina egna data för att härleda insikter. I vissa fall kan datakonsumenten ta emot uppgifter som redan har behandlats. 

## <a name="data-share"></a>Data Share

En dataresurs är en grupp datauppsättningar som delas som en enda entitet. Datauppsättningar kan vara från ett antal Azure-datakällor som stöds av Azure Data Share. För närvarande stöder Azure Data Share Azure Blob Storage och Azure Data Lake Store. 

## <a name="share-subscription"></a>Dela teckning 

En aktieprenumeration skapas när en datakonsument accepterar en datadelningsinbjudan från en dataleverantör. Dataleverantörer kan visa aktiva delningsprenumerationer genom att navigera till **Skickade resurser** i sitt Azure Data Share-konto och välja **Dela prenumerationer**.

En datakonsument kan kontrollera om de har en aktiv aktieteckning genom att navigera till **mottagna aktier** och visa status för sina mottagna aktier. 

## <a name="snapshot"></a>Ögonblicksbild

En ögonblicksbild kan skapas av en datakonsument när de accepterar en inbjudan till datadelning. När de accepterar en inbjudan kan de utlösa en fullständig ögonblicksbild av de data som delas med dem. Ögonblicksbilden är en kopia av data vid den tidpunkt då datakonsumenten genererade ögonblicksbilden. 

Det finns två typer av ögonblicksbilder - fullständig och inkrementell. En fullständig ögonblicksbild innehåller alla data i dataresursen. En inkrementell ögonblicksbild innehåller alla data som har uppdaterats/lagts till sedan den senaste ögonblicksbilden utlöstes. 

## <a name="snapshot-settings-in-azure-data-share"></a>Ögonblicksbildinställningar i Azure Data Share
 
En dataprovider kan aktivera en ögonblicksbildinställning för en dataresurs. Med den här inställningen kan datakonsumenter ta emot inkrementella uppdateringar när de inträffar. Den här inställningen bör aktiveras om dataleverantören vill att deras datakonsumenter ska ta emot uppdateringar av data som har delats. 

Om en dataprovider aktiverar den här inställningen kan ett upprepningsintervall väljas. Upprepningsintervallet kan vara per timme eller dagligen. 

En datakonsument har möjlighet att välja det här ögonblicksbildschemat för att ta emot inkrementella uppdateringar, vilket inkluderar alla data som har ändrats sedan de först skapade en ny ögonblicksbild. 

## <a name="invitation"></a>Inbjudan

En dataleverantör kan bjuda in flera mottagare till sin dataresurs. De kan göra det genom att lägga till mottagare i dataresursen. Inbjudningar kan också läggas till när en dataresurs har skapats. 

En dataleverantör kan ta bort en inbjudan när den har skickats om den inte har accepterats. Om uppgiftslämnaren tar bort en inbjudan och den ännu inte har accepterats, kommer datakonsumenten inte att kunna acceptera den. 

Inbjudningar kan skickas upp till fem gånger om dagen. 

## <a name="recipient"></a>Mottagare

En mottagare är någon som får en inbjudan till en dataresurs. Vanligtvis lägger en dataleverantör till mottagare i dataresurs som de skapar. När mottagaren av en inbjudan accepterar inbjudan blir de en datakonsument.  

## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du börjar dela data fortsätter du till [att dela din datahandledning.](share-your-data.md)
