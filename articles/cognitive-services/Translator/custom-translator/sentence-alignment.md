---
title: Menings länkning och justering – anpassad översättare
titleSuffix: Azure Cognitive Services
description: Under övnings körningen är meningar som finns i parallella dokument kopplade eller justerade. Anpassad översättare lär sig översättning av en mening i taget, genom att läsa en mening, översättning av denna mening. Sedan justeras ord och fraser i de här två meningarna till varandra.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: adbc21c3e963a98a8482de0c26bf5e257f43013e
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675460"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Menings koppling och justering i parallella dokument

Under utbildningen är meningar som finns i parallella dokument kopplade eller justerade. Anpassad översättare rapporterar antalet meningar som de kunde para ihop som de justerade meningarna i varje data uppsättning.

## <a name="pairing-and-alignment-process"></a>Kopplings-och justerings process

Anpassad översättare lär sig översättningar av meningar en mening i taget. Den läser en mening från källan och sedan översättningen av den här meningen från målet. Sedan justeras ord och fraser i de här två meningarna till varandra. Den här processen gör det möjligt för IT att skapa en karta över ord och fraser i en mening till motsvarande ord och fraser i översättningen av denna mening. Justeringen försöker se till att system tågen på meningar som är översättningar av varandra.

## <a name="pre-aligned-documents"></a>Dokument som är justerade

Om du vet att du har parallella dokument kan du åsidosätta menings justeringen genom att ange textfiler som är justerade för text. Du kan extrahera alla meningar från båda dokumenten till textfil, ordnade en mening per rad och överföra med ett `.align`-tillägg. @No__t_0-tillägget signalerar anpassad översättare om att det ska hoppa över menings justeringen.

För bästa resultat bör du försöka se till att du har en mening per rad i dina filer. Det finns inte några tecken för ny rad i en mening eftersom detta ger dåliga justeringar.

## <a name="suggested-minimum-number-of-sentences"></a>Föreslaget minsta antal meningar

För att en utbildning ska lyckas visar tabellen nedan det minsta antalet meningar som krävs för varje dokument typ. Den här begränsningen är ett säkerhets nät för att se till att dina parallella meningar innehåller tillräckligt med unik vokabulär för att kunna träna en översättnings modell. Den allmänna rikt linjen har flera parallella meningar av mänsklig översättning för mänsklig översättnings kvalitet bör ge bättre kvalitets modeller.

| Dokumenttyp   | Föreslaget minsta antal meningar | Maximalt antal meningar |
|------------|--------------------------------------------|--------------------------------|
| Utbildning   | 10 000                                     | Ingen övre gräns                 |
| Anpassa     | 5 000                                      | 2 500                          |
| Testning    | 5 000                                      | 2 500                          |
| Ordlista | 0                                          | Ingen övre gräns                 |

> [!NOTE]
> - Träningen kommer inte att starta och kommer att Miss klaras om 10 000 det lägsta antalet meningar för träning inte är uppfyllt. 
> - Justering och testning är valfria. Om du inte anger dem kommer systemet att ta bort en lämplig procent andel av träningen som används för validering och testning. 
> - Du kan träna en modell med endast data från en ord lista. Se [ord listan](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder en [ord lista](what-is-dictionary.md) i en anpassad översättare.
