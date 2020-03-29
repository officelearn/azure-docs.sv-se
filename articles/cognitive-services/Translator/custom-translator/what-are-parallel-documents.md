---
title: Vad är parallella dokument? - Anpassad översättare
titleSuffix: Azure Cognitive Services
description: Parallella dokument är par av dokument där det ena är översättningen av den andra. Ett dokument i paret innehåller meningar på källspråket och det andra dokumentet innehåller dessa meningar översatta till målspråket.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d7c38a44e3111a319e4146b3c9b71a22b0d31bfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "72675472"
---
# <a name="what-are-parallel-documents"></a>Vad är parallella dokument?

Parallella dokument är par av dokument där det ena är översättningen av den andra. Ett dokument i paret innehåller meningar på källspråket och det andra dokumentet innehåller dessa meningar översatta till målspråket.
Det spelar ingen roll vilket språk som är markerat som "källa" och vilket språk som markeras som "mål" – ett parallellt dokument kan användas för att träna ett översättningssystem i båda riktningarna.

## <a name="requirements"></a>Krav

Du behöver minst 10 000 unika parallella meningar för att träna ett system. Denna begränsning är ett skyddsnät för att säkerställa att dina parallella meningar innehåller tillräckligt med unikt ordförråd för att framgångsrikt träna en översättningsmodell. Bästa praxis bör du kontinuerligt lägga till mer parallellt innehåll och träna om för att förbättra kvaliteten på översättningssystemet. Se [meningsjustering](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment).

Microsoft kräver att dokument som laddas upp till den anpassade översättaren inte bryter mot en tredje parts upphovsrätt eller intellektuella egenskaper. För mer information, se [användarvillkoren](https://azure.microsoft.com/support/legal/cognitive-services-terms/).
Om du laddar upp ett dokument med hjälp av portalen ändras inte äganderätten till den immateriella egendomen i själva dokumentet.

## <a name="use-of-parallel-documents"></a>Användning av parallella dokument

Parallella dokument används av systemet:

1.  Om du vill veta hur ord, fraser och meningar ofta mappas mellan de två språken.

2.  Om du vill veta hur du bearbetar rätt sammanhang beroende på de omgivande fraserna. Ett ord kanske inte alltid översätts till exakt samma ord på det andra språket.

Se till att det finns en 1:1-meningskorrespondens mellan käll- och målspråksversionerna av dokumenten.

Om projektet är domänspecifikt (kategori) bör dina dokument vara konsekventa i terminologin inom den kategorin. Kvaliteten på det resulterande översättningssystemet beror på antalet meningar i dokumentuppsättningen och kvaliteten på meningarna. Ju fler exempel dina dokument innehåller med olika användningar för ett ord som är specifikt för din kategori, desto bättre jobb kan systemet göra under översättningen.

Dokument som laddas upp är privata för varje arbetsyta och kan användas i så många projekt eller utbildningar som du vill. Meningar som extraheras från dina dokument lagras separat i databasen som oformaterade Unicode-textfiler och är tillgängliga för dig. Använd inte den anpassade översättaren som en dokumentdatabas, du kommer inte att kunna ladda ned de dokument som du har laddat upp i det format du laddade upp dem.



## <a name="next-steps"></a>Nästa steg

- Läs om hur du använder en [ordlista](what-is-dictionary.md) i Anpassad översättare.
