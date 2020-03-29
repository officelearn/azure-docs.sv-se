---
title: Meningskoppling och justering - Custom Translator
titleSuffix: Azure Cognitive Services
description: Under utbildningskörningen paras eller justeras meningar som finns i parallella dokument. Custom Translator lär sig översättningar en mening i taget, genom att läsa en mening, översättningen av denna mening. Sedan anpassar den ord och fraser i dessa två meningar till varandra.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cf5b2b84142c9104ea5b3afa3ad179fd0ec07449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80370144"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Meningskoppling och justering i parallella dokument

Under utbildningen paras eller justeras meningar som finns i parallella dokument. Custom Translator rapporterar antalet meningar som den kunde para ihop som justerade meningar i var och en av datauppsättningarna.

## <a name="pairing-and-alignment-process"></a>Parkopplings- och justeringsprocess

Custom Translator lär sig översättningar av meningar en mening i taget. Den läser en mening från källan, och sedan översättningen av denna mening från målet. Sedan anpassar den ord och fraser i dessa två meningar till varandra. Denna process gör det möjligt att skapa en karta över ord och fraser i en mening till motsvarande ord och fraser i översättningen av denna mening. Alignment försöker se till att systemet tränar på meningar som är översättningar av varandra.

## <a name="pre-aligned-documents"></a>Förjusterade dokument

Om du vet att du har parallella dokument kan du åsidosätta meningsjusteringen genom att tillhandahålla förjusterade textfiler. Du kan extrahera alla meningar från båda dokumenten till textfilen, ordna en mening per rad och ladda upp med ett `.align` tillägg. Tillägget `.align` signalerar Custom Translator att det ska hoppa över meningsjustering.

Bäst resultat får du om du vill vara säker på att du har en mening per rad i filerna.Har du inga nya radtecken i en mening eftersom det orsakar dåliga justeringar.

## <a name="suggested-minimum-number-of-sentences"></a>Föreslaget minsta antal meningar

För att en utbildning ska lyckas visar tabellen nedan det minsta antalet meningar som krävs i varje dokumenttyp.Denna begränsning är ett skyddsnät för att säkerställa att dina parallella meningar innehåller tillräckligt med unikt ordförråd för att framgångsrikt träna en översättningsmodell. Den allmänna riktlinjen är att ha mer in-domain parallella meningar av mänsklig översättning kvalitet bör producera modeller av högre kvalitet.

| Dokumenttyp   | Föreslagen minsta antal straff | Antal maximistraff |
|------------|--------------------------------------------|--------------------------------|
| Utbildning   | 10 000                                     | Ingen övre gräns                 |
| Tuning     | 500                                      | 2 500       |
| Testning    | 500                                      | 2 500  |
| Ordlista | 0                                          | Ingen övre gräns                 |

> [!NOTE]
> - Träningen startar inte och misslyckas om det lägsta antalet straff för utbildning inte uppfylls. 
> - Trimning och testning är valfria. Om du inte tillhandahåller dem tar systemet bort en lämplig procentsats från Utbildning som ska användas för validering och testning. 
> - Du kan träna en modell med endast ordlistedata. Se [Vad är ordlista](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary).

## <a name="next-steps"></a>Nästa steg

- Läs om hur du använder en [ordlista](what-is-dictionary.md) i Anpassad översättare.
