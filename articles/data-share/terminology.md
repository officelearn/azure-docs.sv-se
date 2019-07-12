---
title: Terminologi för Azure Data resurs förhandsversion
description: Terminologi för Azure Data resurs förhandsversion
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 4e7db84666b9d3786c3fc25e3653d24d0b95f2e4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789233"
---
# <a name="azure-data-share-preview-concepts"></a>Azure Preview begrepp för resursen 

Azure Data resurs Preview introducerar en ny terminologi som rör delning av data. Den här artikeln beskriver några vanliga termer som kan visas som används i tjänsten. 

## <a name="data-provider"></a>Dataleverantören

En dataleverantör är organisationen som delar data med sina kunder. Dataprovidern kan vanligtvis vara ägare eller en curator av data. Dataleverantörer vill dela olika typer av data. Några exempel på data som en dataleverantör kan dela är rådata, till exempel försäljning eller time series-data. En dataleverantör kanske också vill dela förbearbetade, granskad data som redan innehåller analyser och insikter. 

## <a name="data-consumer"></a>Datakonsument 

En Data-Consumer är den organisation som tar emot data från en dataleverantör. Datakonsumenten kanske vill ansluta till delade data med sina egna data att härleda insikter. I vissa fall kan datakonsumenten få data som redan har bearbetats. 

## <a name="data-share"></a>Data Share

En Dataresurs är en grupp av datauppsättningar som delas som en enda enhet. Datauppsättningar kan vara från ett antal Azure-datakällor som stöds av Azure Data filresurs. Azure Data filresurs stöder för närvarande, Azure Blob Storage och Azure Data Lake Store. 

## <a name="share-subscription"></a>Dela prenumeration 

En dela prenumeration skapas när en datakonsumenten accepterar inbjudan en data-resurs från en dataleverantör. Dataleverantörer kan visa aktiva resursen prenumerationer genom att gå till **skickas resurser** dela konto i Azure Data och välja **dela prenumerationer**.

En datakonsument kan kontrollera om de har en aktiv resurs-prenumeration genom att gå till **emot resurser** och visa status för sina mottagna resurser. 

## <a name="snapshot"></a>Ögonblicksbild

En ögonblicksbild kan skapas av en datakonsument när de accepterar inbjudan en data-resursen. De kan utlösa en fullständig ögonblicksbild av data som delas med dem när de accepterar inbjudan. Ögonblicksbilden är en kopia av data vid den tidpunkt som datakonsumenten genererats ögonblicksbilden. 

Det finns två typer av ögonblicksbilder - fullständiga och inkrementella. En fullständig ögonblicksbild innehåller alla data på data-resursen. En inkrementell ögonblicksbild innehåller alla data som har uppdaterats/lagts till sedan den senaste ögonblicksbilden utlöstes. 

## <a name="snapshot-settings-in-azure-data-share"></a>Ögonblicksbild inställningar i Azure Data filresurs
 
En dataleverantör kan aktivera en inställning för ögonblicksbild av en Dataresurs. Den här inställningen gör det möjligt för datakonsumenterna att ta emot inkrementella uppdateringar när de inträffar. Den här inställningen måste vara aktiverad om dataleverantören skulle vilja ha sina datakonsumenterna att ta emot uppdateringar till data som har delats. 

Om en dataleverantör aktiverar den här inställningen, kan du välja ett intervall. Upprepningsintervallet kan vara per timme eller dag. 

En datakonsumenten har alternativet att delta i det här schemat ska ta emot inkrementella uppdateringar ögonblicksbild som innehåller alla data som har ändrats sedan de först skapas en ny ögonblicksbild. 

## <a name="invitation"></a>Inbjudan

En dataleverantör kan bjuda in flera mottagare att dela sina data. De kan göra det genom att lägga till mottagare till dataresursen. Inbjudningar kan också läggas till när du har skapat en Dataresurs. 

En dataleverantör kan ta bort en inbjudan när det har skickats. Observera att om en dataleverantör tar bort en inbjudan när den har godkänts, datakonsumenten kan fortfarande ha en aktiv resurs-prenumeration. Om dataleverantören tar bort en inbjudan och inte har godkänts ännu, kan datakonsumenten inte ta emot den. 

## <a name="recipient"></a>Mottagaren

En mottagare är någon som tar emot en inbjudan till en Dataresurs. Vanligtvis en dataleverantör lägger till mottagare till data som de skapar. När mottagaren av en inbjudan har accepterat inbjudan, blir de en datakonsument.  

## <a name="next-steps"></a>Nästa steg

Information om hur du kan börja dela data, fortsätter du till den [dela dina data](share-your-data.md) självstudien.

