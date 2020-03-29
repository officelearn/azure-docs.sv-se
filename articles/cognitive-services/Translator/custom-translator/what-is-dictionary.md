---
title: Vad är en ordlista? - Anpassad översättare
titleSuffix: Azure Cognitive Services
description: En ordlista är ett justerat dokument som anger en lista med fraser eller meningar (och deras översättningar) som du alltid vill att Microsoft Translator ska översätta på samma sätt. Ordlistor kallas ibland också ordlistor eller termbaser.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 36b449c4c4ca30eb658c9519ce8e870a4f1fab32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "71970730"
---
# <a name="what-is-a-dictionary"></a>Vad är en ordlista?

En ordlista är ett justerat par dokument som anger en lista med fraser eller meningar och motsvarande översättningar. Använd en ordlista i din utbildning när du vill att Microsoft Translator alltid ska översätta alla förekomster av källfrasen eller meningen med hjälp av översättningen som du har angett i ordlistan. Ordlistor kallas ibland ordlistor eller termbaser. Du kan tänka på ordlistan som en brute force "kopiera och ersätta" för alla termer du lista. Dessutom bygger Microsoft Custom Translator-tjänsten upp och använder sig av sina egna ordlistor för allmänna ändamål för att förbättra kvaliteten på översättningen. En kund som visas i ordlistan tar dock prejudikat och genomsöks först för att söka efter ord eller meningar.

Ordlistor fungerar bara för projekt i språkpar som har en fullständigt stödd Microsoft-modell för neurala nätverk bakom sig. [Visa hela listan med språk](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Frasordlista
När du inkluderar en frasordlista i utbildning av din modell, alla ord eller fraser som anges översätts på det sätt du angav. Resten av meningen översätts som vanligt. Du kan använda en frasordlista för att ange fraser som inte ska översättas genom att ange samma oöversatta fras i käll- och målfilen i ordlistan.

## <a name="sentence-dictionary"></a>Ordbok för mening
I meningsordlistan kan du ange en exakt målöversättning för en källdom. För att en meningsordlista ska kunna matcha måste hela den skickade meningen matcha källordlistposten.  Om bara en del av meningen matchar matchar inte posten.  När en matchning identifieras returneras målposten i meningsordlistan.

## <a name="dictionary-only-trainings"></a>Utbildning endast för ordbok
Du kan träna en modell med endast ordlistedata. Det gör du genom att bara markera ordlistedokumentet (eller flera ordlistedokument) som du vill ta med och tryck på Skapa modell. Eftersom detta är en utbildning endast för ordboken krävs inget minsta antal utbildningsstraff. Din modell slutför vanligtvis träningen mycket snabbare än en vanlig utbildning.  De resulterande modellerna använder Microsofts baslinjemodeller för översättning med tillägg av de ordlistor som du har lagt till.  Du får ingen testrapport.

>[!Note]
>Custom Translator dömer inte justera ordlistefiler, så det är viktigt att det finns lika många käll- och målfraser/meningar i ordlistedokumenten och att de är exakt justerade.

## <a name="recommendations"></a>Rekommendationer

- Ordlistor ersätter inte utbildning av en modell med hjälp av träningsdata. Vi rekommenderar att du undviker dem och låter systemet lära av dina träningsdata. Men när meningar eller sammansatta substantiv måste återges som de är, använd en ordlista.
- Frasordlistan ska användas sparsamt. Så var medveten om att när en fras i en mening ersätts, är sammanhanget i den meningen förlorat eller begränsat för att översätta resten av meningen. Resultatet är att medan frasen eller ordet i meningen kommer att översätta enligt den medföljande ordboken, kommer den övergripande översättningskvaliteten på meningen ofta lida.
- Frasordlistan fungerar bra för sammansatta substantiv som produktnamn ("Microsoft SQL Server"), riktiga namn ("Hamburgs stad" eller produktens funktioner ("pivottabell"). Det fungerar inte lika bra för verb eller adjektiv eftersom dessa vanligtvis är mycket böjda i källan eller i målspråket. Bästa praxis är att undvika fras ordliste poster för allt annat än sammansatta substantiv.
- När du använder en frasordlista är versaler och interpunktion viktiga. Ordlisteposter matchar bara ord och fraser i indata meningen som använder exakt samma versaler och interpunktion som anges i källordlistefilen. Även översättningarna kommer att återspegla versaler och interpunktion som finns i målordlistefilen. Om du till exempel har tränat ett engelskt till spanskt system som använder en frasordlista som anger "USA" i källfilen och "EE.For example, if you trained an English to Spanish system that uses a phrase dictionary that specifies "US" in the source file, and "EE. UU." i målfilen. När du begär översättning av en mening som innehåller ordet "oss" (inte aktiverat), skulle detta INTE matcha ordlistan. Men om du begär översättning av en mening som innehåller ordet "USA" (kapitaliserade) då det skulle matcha ordlistan och översättningen skulle innehålla "EE. UU." Observera att versaler och interpunktion i översättningen kan skilja sig från vad som anges i målfilen för ordlistan och kan skilja sig från versaler och interpunktion i källan. Det följer reglerna för målspråket.
- Om ett ord visas mer än en gång i en ordlistefil används alltid den senaste posten. Därför bör din ordlista inte innehålla flera översättningar av samma ord.

## <a name="next-steps"></a>Nästa steg

- Läs om [riktlinjer för dokumentformat](document-formats-naming-convention.md).
