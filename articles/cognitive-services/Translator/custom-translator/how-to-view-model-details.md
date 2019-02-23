---
title: Visa information om modellen - anpassad Translator
titleSuffix: Azure Cognitive Services
description: Modeller fliken under alla projekt Visar information om varje modell, till exempel namn, status för modellen, BLEU poäng, utbildning, justering, testa mening antal.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 8a828bcb7817c30631930da953bc9eef6250c398
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736445"
---
# <a name="view-model-details"></a>Visa modellinformation

Fliken modeller under projektet visas alla modeller i projektet. Alla modeller tränas för som projektet visas i den här fliken.

För varje modell i projektet visas följande modellinformation.

1.  Modellnamn: Visar modellnamnet på en viss modell.

2.  Status: Visar status för en viss modell. Ny utbildning har statusen Skickad tills accepterar. Statusen ändras till databearbetning medan tjänsten utvärderar innehållet i dokumenten. När utvärderingen av dina dokument är klar ändras statusen till körs och du kan se antalet meningar som ingår i utbildning, inklusive den justering och testning anger som skapas automatiskt åt dig. Nedan visas en lista över modellen status som beskriver tillståndet för modellerna.

    -  Skickad: Anger att serverdelen bearbetar dokument för den modellen.

    -  TrainingQueued: Anger att utbildningen har placerats i kö till MT för den modellen.

    -  Körs: Anger att utbildningen körs i MT system för den modellen.

    -  Lyckades: Anger att utbildningen har skapats i MT-system och en modell är tillgänglig. I den här statusen visas en BLEU poäng för den modellen.

    -  Distribuerad: Anger att den lyckade tränade modellen har skickats till MT-systemet för distribution.

    -  Den kan avinstalleras: Anger att distribuerad modell den kan avinstalleras.

    -  Avdistribuerats: Anger att processen undeployment för en modell har slutförts.

    -  Det gick inte att utbildning: Anger att utbildningen har misslyckats. Om ett utbildnings-fel uppstår, försöker du utbildningsjobbet. Om felet kvarstår, kontakta oss. Ta inte bort den misslyckade modellen.

    - DataProcessingFailed: Anger att databearbetning misslyckades för en eller flera dokument som hör till modellen.

    - DeploymentFailed: Anger att modellen distributionen misslyckades.

    - MigratedDraft: Anger att modellen inte är utkast efter migreringen från hubben till anpassad Translator.

4.  BLEU poäng: Visar BLEU (tvåspråkig utvärdering Understudy) poängen för modellen, som anger kvaliteten på översättningssystemet. Det här resultatet visar hur nära översättningar som görs av den översättningssystemet som härrör från denna utbildning matchar referens meningar i test-datauppsättningen. BLEU poäng visas om utbildning är slutförd. Om utbildning inte fullständig / misslyckade, visas inte några BLEU poäng.

5.  Utbildning mening antal: Visar totalt antal meningar som används som träningsmängden.

6.  Justering mening antal: Visar totalt antal meningar som används som justering set.

7.  Utbildning mening antal: Visar totalt antal meningar som används som testar set.

8.  Mono mening antal: Visar totalt antal meningar som används som mono.

9.  Distribuera &: För en har tränad modell, den visar knappen ”distribuera” om inte distribuerats. Om en modell har distribuerats visas en ”Undeploy”-knapp.

10. Ta bort: Du kan använda den här knappen om du vill ta bort modellen. Om du tar bort en modell bort inte någon av de dokument som används för att skapa den modellen.

    ![Visa modellinformation](media/how-to/how-to-view-model-details.png)

>[!Note]
>Om du vill jämföra på varandra följande kurser för samma system, är det viktigt att hålla den justering ställa in och testa ange konstant.

## <a name="view-model-training-details"></a>Visa modellen utbildningsinformation

När utbildning är klar kan granska du information om utbildning från sidan. Välj ett projekt, leta upp och välj fliken modeller och välj en modell.

Sidan modellen har två flikar: Information om utbildning och testning.

1.  **Information om utbildning:** Den här fliken innehåller en lista över dokument som används i utbildningen:

    -  Namn på dokument: Det här fältet visar namnet på dokumentet

    -  Typ av standarddokument: Det här fältet visar om det här dokumentet är parallell / mono.

    -  Mening antal i källspråk: Det här fältet visas antalet meningar är det som en del av källspråk.

    -  Mening antal i målspråk: Det här fältet visas antalet meningar är det som en del av målspråk.

    -  Justerade meningar: Det här fältet visas antalet meningar har har justerats med anpassade Translator under justera processen.

    -  Används meningar: Det här fältet visas antalet meningar har använts av anpassade Translator under den här kursen.

    ![Modelldetaljer för utbildning](media/how-to/how-to-model-training-details.png)

2.  **Test:** Den här fliken visar test-information för en lyckad utbildning.

## <a name="next-steps"></a>Nästa steg

- Granska [testresultat](how-to-view-system-test-results.md) och analysera resultaten för utbildning.
