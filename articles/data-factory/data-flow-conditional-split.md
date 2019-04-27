---
title: Azure Data Factory mappning Dataomvandling Flow villkorlig dela
description: Azure Data Factory Data Flow villkorlig Split-transformering
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: a4ea79e05165dfae4f79aa6473a07151ba7c00fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627646"
---
# <a name="azure-data-factory-mapping-data-flow-conditional-split-transformation"></a>Azure Data Factory mappning Dataomvandling Flow villkorlig dela

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Villkorlig dela transformeringen kan vidarebefordra rader med data till olika dataströmmar beroende på innehållet i data. Implementeringen av villkorlig dela transformeringen liknar en CASE beslut struktur i ett programmeringsspråk. Transformeringen utvärderar uttryck, och baserat på resultatet, leder datarad till den angivna dataströmmen. Den här omvandlingen tillhandahåller även en standardutdata, så att om en rad matchar inget uttryck dirigeras den till standardutdata.

![villkorlig dela](media/data-flow/cd1.png "villkorlig dela")

Välj ”Lägg till Stream” längst ned i fönstret konfiguration för att lägga till ytterligare villkor, och klicka i textrutan Uttrycksverktyget för att skapa uttrycket.
