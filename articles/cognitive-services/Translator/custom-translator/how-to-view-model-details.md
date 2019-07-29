---
title: Visa modell information – anpassad översättare
titleSuffix: Azure Cognitive Services
description: Fliken modeller under ett projekt visar information om varje modell, till exempel modell namn, modell status, BLEU poäng, utbildning, justering, antal test meningar.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 64f446c3b331c1aa6ddaae9081b7f61943f74ab2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595557"
---
# <a name="view-model-details"></a>Visa modellinformation

På fliken modeller under projektet visas alla modeller i projektet. Alla modeller som har tränats för projektet visas på den här fliken.

För varje modell i projektet visas dessa uppgifter.

1.  Modell namn: Visar modell namnet för en specifik modell.

2.  Status: Visar status för en specifik modell. Din nya utbildning kommer att ha statusen skickad tills den accepteras. Statusen ändras till data bearbetning medan tjänsten utvärderar dokumentets innehåll. När utvärderingen av dokumenten har slutförts ändras statusen till körs och du kan se antalet meningar som är en del av utbildningen, inklusive de justerings-och test uppsättningar som skapas automatiskt. Nedan visas en lista med modell status som beskriver modellens tillstånd.

    -  Läggs Anger att Server delen bearbetar dokumenten för den modellen.

    -  TrainingQueued: Anger att utbildningen har placerats i kö till MT-system för den modellen.

    -  Körs: Anger att träningen körs i MT-system för den modellen.

    -  Brutit Anger att träningen lyckades i MT-systemet och att en modell är tillgänglig. I den här statusen visas en BLEU Poäng för den modellen.

    -  Distribueras Anger att lyckad tränad modell skickas till MT system för distribution.

    -  Avdistribuerar: Anger att den distribuerade modellen är avdistribuerad.

    -  Odistribuerade Anger att processen för att distribuera en modell har slutförts korrekt.

    -  Inlärningen misslyckades: Anger att träningen misslyckades. Om ett inlärnings fel inträffar försöker du igen med utbildnings jobbet. Om felet kvarstår kan du kontakta oss. Ta inte bort den misslyckade modellen.

    - DataProcessingFailed: Anger att data bearbetningen har misslyckats för ett eller flera dokument som tillhör modellen.

    - DeploymentFailed Anger att modell distributionen har misslyckats.

    - MigratedDraft: Anger att modellen är i utkast tillstånd efter migrering från hubb till anpassad översättare.

4.  BLEU Poäng: visar BLEU (tvåspråkig utvärdering understudie) av modellen, vilket anger kvaliteten på översättnings systemet. Det här resultatet visar hur nära översättnings systemet som är resultatet av den här utbildningen som matchar referens meningarna i test data uppsättningen. BLEU-poängen visas om träningen har slutförts. Om inlärningen inte är slutförd/misslyckad visas inga BLEU poäng.

5.  Antal utbildnings meningar: Visar det totala antalet meningar som används som inlärnings uppsättning.

6.  Justera menings antal: Visar det totala antalet meningar som används som justerings uppsättning.

7.  Antal utbildnings meningar: Visar det totala antalet meningar som används som testnings uppsättning.

8.  Antal svartvita meningar: Visar det totala antalet meningar som används som mono uppsättning.

9.  Knappen distribuera åtgärd: För en lyckad tränad modell visas knappen distribuera om den inte har distribuerats. Om en modell distribueras visas en "avdistribuera"-knapp.

10. Ta bort Du kan använda den här knappen om du vill ta bort modellen. Om du tar bort en modell raderas inte alla dokument som används för att skapa modellen.

    ![Visa modellinformation](media/how-to/how-to-view-model-details.png)

>[!Note]
>För att jämföra efterföljande utbildningar för samma system är det viktigt att du behåller justerings uppsättningen och testnings uppsättnings konstanten.

## <a name="view-model-training-details"></a>Visa information om modell utbildning

När din utbildning är klar kan du granska information om utbildningen från informations sidan. Välj ett projekt, leta upp och välj fliken modeller och välj en modell.

Modell sidan har två flikar: Tränings information och test.

1.  **Tränings information:** På den här fliken visas en lista över dokument som används i utbildningen:

    -  Dokument namn: Det här fältet visar namnet på dokumentet

    -  Dokument typ: Det här fältet visar om det här dokumentet är parallellt/mono.

    -  Antal meningar i käll språk: Det här fältet visar antalet meningar som finns som en del av käll språket.

    -  Antal meningar på mål språket: Det här fältet visar antalet meningar som finns som en del av mål språket.

    -  Justerade meningar: Det här fältet visar antalet meningar som har justerats efter anpassad översättare under justerings processen.

    -  Använda meningar: Det här fältet visar antalet meningar som har använts av en anpassad översättare under den här utbildningen.

    ![Information om modell utbildning](media/how-to/how-to-model-training-details.png)

2.  **Koncentration** På den här fliken visas test informationen för en lyckad utbildning.

## <a name="next-steps"></a>Nästa steg

- Granska [test resultat](how-to-view-system-test-results.md) och analysera utbildnings resultatet.
