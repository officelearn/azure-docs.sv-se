---
title: Sentence pairing and alignment - Custom Translator
titleSuffix: Azure Cognitive Services
description: Under körningen utbildning ihop eller justerad meningar som finns i parallella dokument. Anpassade Translator lär sig översättningar en mening i taget, genom att läsa en mening översättningen av den här meningen. Sedan justerar ord och fraser i dessa två meningarna till varandra.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 8d2933f6a3bbab792acb708f2a59cad4eb2cabf7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777166"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Mening parkoppling och justering i parallella dokument

Vid träning, meningar som finns i parallella dokument tillsammans eller justerad. Anpassade Translator rapporterar antalet meningar kunde att para ihop som justerad meningarna i var och en av datauppsättningarna.

## <a name="pairing-and-alignment-process"></a>Länkning och justering

Anpassade Translator lär sig översättningar av meningar en mening i taget. Den avläsningar en mening från källa och översättningen av den här meningen från målet. Sedan justerar ord och fraser i dessa två meningarna till varandra. Den här processen gör det möjligt att skapa en karta med ord och fraser i en mening till motsvarande ord och fraser i översättningen av den här meningen. Justering försöker se till att systemet träna på meningar som är översättningar från varandra.

## <a name="pre-aligned-documents"></a>Före justerade dokument

Om du vet att du har parallella dokument, kan du åsidosätta mening justering genom att tillhandahålla förväg justerade textfiler. Du kan extrahera alla meningar från båda dokumenten i textfil, ordnad en mening per rad och ladda upp med en `.align` tillägget. Den `.align` tillägget signalerar anpassad Translator att det ska hoppa över en mening justering.

För bästa resultat bör du försöka se till att du har en mening per rad i dina filer. Har inte tecken för ny rad i en mening som detta innebär att dålig justering.

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>Föreslagna minsta antal extraherade och justerade meningen

Tabellen nedan visar det minsta antalet extraherade meningar och justerade meningar som krävs i varje datauppsättning till en utbildning ska lyckas. Minsta antal föreslagna extraherade meningar är mycket högre än det föreslagna minsta antalet justerade meningar ta hänsyn till det faktum att mening justering inte kanske kan justera alla extraherade meningar har.

| Datauppsättning   | Föreslås minsta extraherade mening antal | Föreslås minsta justerade mening antal | Maximal justerade mening antal |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| Utbildning   | 10 000                                     | 2,000                                    | Ingen övre gräns                 |
| Justering     | 2,000                                      | 500                                      | 2,500                          |
| Testning    | 2,000                                      | 500                                      | 2,500                          |
| Ordlista | 0                                          | 0                                        | Ingen övre gräns                 |

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder en [ordlista](what-is-dictionary.md) i anpassade Translator.
