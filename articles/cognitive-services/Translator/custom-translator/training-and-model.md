---
title: Vad är utbildning och modellen? – Anpassade Translator
titleSuffix: Azure Cognitive Services
description: En modell är i systemet, vilket ger översättning för ett visst språk-par. Resultatet av en lyckad utbildning är en modell. När träna en modell, krävs tre ömsesidigt uteslutande datauppsättningar utbildning datauppsättning, justering datauppsättning och testningen datauppsättning.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-rada
ms.openlocfilehash: adc78b176afb0a43a7e1e3a7bb882282a914761d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57783082"
---
# <a name="what-are-trainings-and-models"></a>Vad är utbildningar och modeller?

En modell är i systemet, vilket ger översättning för ett visst språk-par.
Resultatet av en lyckad utbildning är en modell. När träna en modell, tre ömsesidigt uteslutande datauppsättningar krävs: datauppsättning för träning, justering datauppsättning och test datauppsättning. Ordlista data kan också anges.

Om det bara träningsdata tillhandahålls när queuing en utbildning, Assemblera anpassad Translator automatiskt justera och testningsdatauppsättningar. Installationsprogrammet utesluta dina utbildningsdata 5 000 meningar och använder 2 500 varje sätta ihop en justering och testning uppsättningar.

## <a name="training-dataset-for-custom-translator"></a>Datauppsättning för träning för anpassad Translator

Dokument som ingår i träningsmängden används av anpassade Translator som grund för att skapa din modell. Under körningen utbildning är meningar som finns i dessa dokument justerad (eller länkas). Du kan dra individens i Skapa en uppsättning Utbildningsdokument. Du kan inkludera dokument som du tror är tangerar relevanta i en modell. Undanta dem igen i en annan för att se hur i [BLEU (tvåspråkig utvärdering Understudy) poäng](what-is-bleu-score.md). Passa på att experimentera med sammansättning av träningsmängden så länge som du behåller justering set och test set konstant. Den här metoden är ett effektivt sätt att ändra kvaliteten på översättningssystemet.

Du kan köra flera utbildningar i ett projekt och jämför den [BLEU poäng](what-is-bleu-score.md) över alla träningskörningar. När du kör flera utbildningar för jämförelse, se till att samma justering / testdata anges varje gång. Se också till att även granska resultaten manuellt i den [”testning”](how-to-view-system-test-results.md) fliken.

## <a name="tuning-dataset-for-custom-translator"></a>Justera datauppsättning för anpassad Translator

Parallell dokument som ingår i den här uppsättningen som används av anpassade översättaren för att finjustera översättningssystemet för bästa möjliga resultat.

Justering uppsättningen används vid träning för att justera alla parametrar och vikter för översättningssystemet till optimala värden. Välj din justering Ställ in noggrant: justering uppsättningen ska vara representativ för innehållet i dokument som du planerar att översätta i framtiden. Justering uppsättningen har en större inverkan på kvaliteten på översättningarna genereras. Justering kan translation systemet för att ange översättningar som ligger närmast de exempel som du anger i justering datauppsättningen. Du behöver inte mer än 2 500 meningar som justering set. För optimala översättningen rekommenderar vi att du väljer justering uppsättningen manuellt genom att välja det mest representativa urvalet av meningar.

När du skapar din justering uppsättning, väljer du meningar som är en meningsfull och representativ längd på framtida meningar som du förväntar dig att översätta. Du bör även välja meningar som har ord och fraser som du avser att översätta i den ungefärliga distribution som du förväntar dig av dina framtida översättningar. I praktiken skapas en Meningslängd på 8-18 ord bästa resultat bör eftersom dessa meningar innehåller tillräckligt med kontext för att visa angripits och ange en fras längd som är betydande, utan att vara alltför komplex.

En bra beskrivning av typ av meningar som ska användas i justering uppsättningen är prose: faktiska fluent meningar. Inte tabellcellerna, inte dikter, inte en lista över saker, inte bara punkter eller siffror i en mening - vanliga språk.

Om du väljer manuellt justering datauppsättningen, bör det inte innehåller något av samma meningar som dina data med utbildning och testning. Justering uppsättningen har en betydande inverkan på kvaliteten på översättningarna – Välj meningarna noggrant.

Om du inte är säker på vad du ska välja för din justering, bara välja träningsmängden och låt Custom Translator välja din justering uppsättning åt dig. När du ger anpassad översättaren väljer justering uppsättningen automatiskt den använder besvara vissa av meningar från dina Utbildningsdokument tvåspråkig och utesluta utbildningsmaterial själva dessa meningar.

## <a name="testing-dataset-for-custom-translator"></a>Testa datauppsättning för anpassad Translator

Parallell dokument som ingår i testmängden används för att beräkna poäng BLEU (tvåspråkig utvärdering Understudy). Det här resultatet anger kvaliteten på översättningssystemet. Det här resultatet faktiskt berättas hur nära översättningar som görs av den översättningssystemet som härrör från denna utbildning matchar referens meningar i test-datauppsättningen.

BLEU poängen är ett mått på delta mellan automatisk översättning och översättningen referens. Dess värdeintervall mellan 0 och 100. Ett resultat på 0 anger att inte ett enstaka ord referensens visas i översättningen. Ett resultat på 100 indikerar att automatisk översättning exakt matchar referensen: samma ord är på exakt samma plats. Du får poängen är BLEU poäng genomsnittet för alla meningar av testmängden.

Test-uppsättningen bör innehålla parallella dokument där målspråk meningarna är de lämpligaste översättningarna av motsvarande källspråk meningarna i paret. Du kanske vill använda samma villkor som du använde för att skapa uppsättningen justering. Dock påverkas testmängden inte över kvaliteten på översättningen-system. Den används uteslutande för att generera BLEU poängen för dig och inget annat.

Du behöver inte mer än 2 500 meningar som testmängden. När du låter systemet väljer testmängden automatiskt den använder besvara vissa av meningar från dina Utbildningsdokument tvåspråkig, och utesluta utbildningsmaterial själva dessa meningar.

Du kan visa anpassade översättningar av testmängden och jämföra dem med översättningar som tillhandahålls i din testning, genom att gå till fliken test inom en modell.
