---
title: Dataflöde för Azure Data Factory mappning inspektera och förhandsgranskning
description: Azure Data Factory mappning dataflöde har ett fönster som visar data flow metadata (granska) och förhandsgranskning i felsökningsläge (förhandsgranskning)
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: e7d03ecd8ea4aecf1e0cfdd02d3b9bc5300abe6b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213532"
---
# <a name="azure-data-factory-mapping-data-flow-transformation-inspect-tab"></a>Flöde för Azure Data Factory mappning Dataomvandling inspektera fliken

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Granska fönstret](media/data-flow/agg3.png "inspektera fönstret")

Fönstret inspektera ger en överblick över metadata för dataströmmen som du omvandlar. Du kommer att kunna se kolumnen antal, kolumner ändras, kolumner som lagts till, datatyper, kolumnordning och kolumnreferenser. ”Granska” är en skrivskyddad vy av dina metadata. Du behöver inte ha felsökningsläget aktiverat för att kunna se metadate i fönstret granska.

När du ändrar formen på dina data via omvandlingar, visas metadata som flödar via fönstret granska ändringar. Om det inte ett definierat schema i käll-transformering, sedan kan metadata inte visas i fönstret granska. Brist på metadata är vanligt i scenarier med schemat Drift.

![Dataförhandsgranskning](media/data-flow/datapreview.png "förhandsgranskning")

Förhandsgranskningen är ett fönster som tillhandahåller en skrivskyddad vy över dina data när den förändras. Du kan visa utdata från din omvandling och uttryck för att verifiera ditt dataflöde. Du måste ha felsökningsläget tända att se förhandsgranskningar av data. När du klickar på kolumner i rutnät för förhandsgranskning av data, visas en efterföljande panelen till höger. Panelen expanderade visar profilen statistik om var och en av de kolumner som du väljer.

![Förhandsgranskning av diagrammet](media/data-flow/chart.png "förhandsgranskning av diagram")

