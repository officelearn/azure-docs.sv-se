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
ms.openlocfilehash: e9bc5c876da6bd2be1b22b389b819e51330b2e50
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595470"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Menings koppling och justering i parallella dokument

Under utbildningen är meningar som finns i parallella dokument kopplade eller justerade. Anpassad översättare rapporterar antalet meningar som de kunde para ihop som de justerade meningarna i varje data uppsättning.

## <a name="pairing-and-alignment-process"></a>Kopplings-och justerings process

Anpassad översättare lär sig översättningar av meningar en mening i taget. Den läser en mening från källan och sedan översättningen av den här meningen från målet. Sedan justeras ord och fraser i de här två meningarna till varandra. Den här processen gör det möjligt för IT att skapa en karta över ord och fraser i en mening till motsvarande ord och fraser i översättningen av denna mening. Justeringen försöker se till att system tågen på meningar som är översättningar av varandra.

## <a name="pre-aligned-documents"></a>Dokument som är justerade

Om du vet att du har parallella dokument kan du åsidosätta menings justeringen genom att ange textfiler som är justerade för text. Du kan extrahera alla meningar från båda dokumenten till text filen, ordnade en mening per rad och ladda `.align` upp med ett tillägg. `.align` Tillägget signalerar den anpassade översättare som det ska hoppa över menings justeringen.

För bästa resultat bör du försöka se till att du har en mening per rad i dina filer. Det finns inte några tecken för ny rad i en mening eftersom detta ger dåliga justeringar.

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>Föreslaget minsta antal extraherade och justerade meningar

För att en utbildning ska lyckas visar tabellen nedan det minsta antalet extraherade meningar och justerade meningar som krävs i varje data uppsättning. Det föreslagna minsta antalet extraherade meningar är mycket högre än det föreslagna minsta antalet justerade meningar för att ta hänsyn till att menings justeringen kanske inte kan justera alla extraherade meningar korrekt.

| Data uppsättning   | Föreslaget minsta antal extraherade meningar | Föreslaget minsta justerade menings antal | Maximalt antal justerade meningar |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| Utbildning   | 10 000                                     | 2,000                                    | Ingen övre gräns                 |
| Anpassa     | 2,000                                      | 500                                      | 2,500                          |
| Testning    | 2,000                                      | 500                                      | 2,500                          |
| Ordlista | 0                                          | 0                                        | Ingen övre gräns                 |

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder en [ord lista](what-is-dictionary.md) i en anpassad översättare.
