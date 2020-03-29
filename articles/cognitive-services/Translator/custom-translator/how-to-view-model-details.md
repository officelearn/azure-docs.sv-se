---
title: Visa modelldetaljer - Anpassad översättare
titleSuffix: Azure Cognitive Services
description: Fliken Modeller under ett projekt visar information om varje modell, till exempel modellnamn, modellstatus, BLEU-poäng, utbildning, justering, testning av meningsräkning.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 64f446c3b331c1aa6ddaae9081b7f61943f74ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595557"
---
# <a name="view-model-details"></a>Visa modellinformation

Fliken Modeller under projektet visar alla modeller i det projektet. Alla modeller som utbildats för det projektet visas på den här fliken.

För varje modell i projektet visas dessa uppgifter.

1.  Modellnamn: Visar modellnamnet för en viss modell.

2.  Status: Visar status för en viss modell. Din nya utbildning kommer att ha status som inskickat tills den accepteras. Statusen ändras till Databehandling medan tjänsten utvärderar innehållet i dina dokument. När utvärderingen av dina dokument är klar ändras statusen till Kör och du kommer att kunna se antalet meningar som ingår i utbildningen, inklusive justerings- och testuppsättningar som skapas automatiskt. Nedan finns en lista över modellstatus som beskriver modellernas tillstånd.

    -  Inlämnad: Anger att backend bearbetar dokumenten för den modellen.

    -  TrainingQueued: Anger att utbildningen köas till MT-systemet för den modellen.

    -  Kör: Anger att utbildningen körs i MT-systemet för den modellen.

    -  Lyckades: Anger att utbildningen lyckades i MT-systemet och en modell är tillgänglig. I den här statusen visas en BLEU-poäng för den modellen.

    -  Distribuerad: Anger att den lyckades tränade modellen skickas till MT-system för distribution.

    -  Avdistribution: Anger att den distribuerade modellen inte distribueras.

    -  Ej distribuerad: Anger att avdistributionsprocessen för en modell har slutförts.

    -  Träningen misslyckades: Anger att utbildningen misslyckades. Om ett utbildningsfel inträffar försöker du igen utbildningsjobbet. Om felet kvarstår kontaktar du oss. Ta inte bort den misslyckade modellen.

    - DataProcessingFailed: Anger att databearbetningen har misslyckats för ett eller flera dokument som tillhör modellen.

    - DistributionMissilerad: Anger att modelldistributionen har misslyckats.

    - MigratedDraft: Anger att modellen är i utkastläge efter migreringen från Hub till Custom Translator.

4.  BLEU Score: visar BLEU (Tvåspråkig utvärdering Inhoppare) poäng av modellen, som anger kvaliteten på ditt översättningssystem. Den här poängen visar hur nära översättningarna som görs av översättningssystemet till följd av den här träningen matchar referensmeningarna i testdatauppsättningen. BLEU-poängen visas om utbildningen har slutförts. Om träningen inte är klar/ misslyckad ser du ingen BLEU-poäng.

5.  Antal träningsansent: Visar totalt antal meningar som används som träningsuppsättning.

6.  Antal justeringsmejningar: Visar totalt antal meningar som används som justeringsuppsättning.

7.  Antal träningsansent: Visar totalt antal meningar som används som testuppsättning.

8.  Antal monomeningar: Visar totalt antal meningar som används som monouppsättning.

9.  Knappen Distribuera åtgärd: För en modell som har tränats visas knappen "Distribuera" om den inte har distribuerats. Om en modell distribueras visas en "Odeploy"-knapp.

10. Ta bort: Du kan använda den här knappen om du vill ta bort modellen. Om du tar bort en modell tas inget av de dokument som används för att skapa modellen bort.

    ![Visa modellinformation](media/how-to/how-to-view-model-details.png)

>[!Note]
>För att jämföra på varandra följande utbildningar för samma system är det viktigt att hålla justeringsuppsättningen och testningsuppsättningen konstant.

## <a name="view-model-training-details"></a>Visa information om modellutbildning

När din träning är klar kan du granska information om utbildningen från informationssidan. Välj ett projekt, leta reda på och välj fliken Modeller och välj en modell.

Modellsidan har två flikar: Träningsdetaljer och Test.

1.  **Information om utbildning:** På den här fliken visas listan över dokument som används i utbildningen:

    -  Dokumentnamn: I det här fältet visas namnet på dokumentet

    -  Dokumenttyp: I det här fältet visas om dokumentet är parallellt/mono.

    -  Antal meninger på källspråk: Det här fältet visar antalet meningar som finns där som en del av källspråket.

    -  Antal meninger i målspråket: Det här fältet visar hur många meningar som finns där som en del av målspråket.

    -  Justerade meningar: I det här fältet visas antalet meningar som har justerats av Anpassad översättare under justeringsprocessen.

    -  Använda meningar: I det här fältet visas antalet meningar som har använts av Custom Translator under den här utbildningen.

    ![Detaljer för modellutbildning](media/how-to/how-to-model-training-details.png)

2.  **Test:** På den här fliken visas testinformationen för en lyckad utbildning.

## <a name="next-steps"></a>Nästa steg

- Granska [testresultat](how-to-view-system-test-results.md) och analysera träningsresultat.
