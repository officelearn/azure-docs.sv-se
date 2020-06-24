---
title: Fel söknings fel vid redigering av ett anpassat kommando program (för hands version)
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig hur du felsöker fel när du redigerar program för anpassade kommandon.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: b0a26b4d3f0f59f8e05c5990bbc64ee55f12f124
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307924"
---
# <a name="debug-errors-when-authoring-a-custom-commands-application"></a>Fel söknings fel vid redigering av ett program för anpassade kommandon

I den här artikeln beskrivs fel sökning när du ser fel meddelanden när du skapar anpassade kommandon. 

## <a name="errors-when-creating-an-application"></a>Fel när du skapar ett program
Anpassade kommandon skapar också ett program i [Luis](https://www.luis.ai/) när du skapar ett program för anpassade kommandon. 

[Luis begränsar 500 program per redigering av resurs](https://docs.microsoft.com/azure/cognitive-services/luis/luis-limits). Det gick inte att skapa LUIS-programmet om du använder en redigerings resurs som redan har 500-program. 

Se till att den valda LUIS Authoring-resursen har mindre än 500. Om inte, kan du skapa en ny LUIS som skapar resurs, byter till en annan eller försöker rensa dina LUIS-program.  

## <a name="errors-when-deleting-an-application"></a>Fel vid borttagning av ett program
### <a name="cant-delete-luis-application"></a>Det går inte att ta bort LUIS-programmet
När du tar bort ett anpassat kommando program kan anpassade kommandon också försöka ta bort det LUIS-program som är associerat med programmet för anpassade kommandon.

Om det inte gick att ta bort LUIS-programmet går du till ditt [Luis](https://www.luis.ai/) -konto för att ta bort dem manuellt.

### <a name="toomanyrequests"></a>TooManyRequests
När du försöker ta bort stora mängder program samtidigt, ser du förmodligen "TooManyRequests"-felen. Det innebär att dina borttagnings förfrågningar begränsas av Azure. 

Uppdatera sidan och försök ta bort färre program.

## <a name="errors-when-modifying-an-application"></a>Fel vid ändring av ett program

### <a name="cant-delete-a-parameter-or-a-web-endpoint"></a>Det går inte att ta bort en parameter eller en webb slut punkt
Du får inte ta bort en parameter när den används. Ta bort alla referenser till parametern i alla tal svar, exempel på meningar, villkor, åtgärder och försök igen.

### <a name="cant-delete-a-web-endpoint"></a>Det går inte att ta bort en webb slut punkt
Du får inte ta bort en webb slut punkt när den används. Ta bort alla **anrops webb slut punkts** åtgärder som använder den här webb slut punkten innan du tar bort en webb slut punkt.

## <a name="errors-when-training-an-application"></a>Fel vid träning av ett program
### <a name="build-in-intents"></a>Build i avsikter
LUIS har skapat Ja/Nej-avsikter. Om du har exempel på meningar med endast "Ja", skulle ingen utbildning underkännas. 

| Följt | Variationer | 
| ------- | --------- | 
| Yes | Säker, OK |
| No | Nej, inte | 

### <a name="common-sample-sentences"></a>Vanliga exempel meningar
Anpassade kommandon tillåter inte vanliga exempel meningar som delas mellan olika kommandon. Träningen av ett program kan sluta fungera om några exempel meningar i ett kommando redan har definierats i ett annat kommando. 

Kontrol lera att du inte har några vanliga exempel meningar som delas mellan olika kommandon. 

För bästa praxis för att balansera exempel meningar över olika kommandon, se [Luis Best Practice](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices).

### <a name="empty-sample-sentences"></a>Tomma exempel meningar
Du måste ha minst en exempels-sats för varje kommando.

### <a name="undefined-parameter-in-sample-sentences"></a>Odefinierad parameter i exempel meningar
En eller flera parametrar används i exempel meningar men inte definierade.

### <a name="training-takes-too-long"></a>Inlärningen tar för lång tid
LUIS-utbildningen är avsedd att lära sig snabbt med färre exempel. Lägg inte till för många exempel meningar. 

Om du har många exempel meningar som liknar varandra definierar du en parameter, sammanfattar dem i ett mönster och lägger till den i exempel meningar.

Du kan till exempel definiera en parameter {vehikel} för exempel meningarna nedan och bara lägga till "bok a {vehikel}" till exempel meningar.

| Exempel på meningar | Mönster | 
| ------- | ------- | 
| Boka en bil | Boka ett {vehikel} | 
| Boka en flygning | Boka ett {vehikel} |
| Boka en taxi | Boka ett {vehikel} |

För bästa praxis för LUIS-utbildning, se [bästa praxis för Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices).

## <a name="cant-update-luis-key"></a>Det går inte att uppdatera LUIS-nyckeln
### <a name="reassign-to-e0-authoring-resource"></a>Tilldela om till E0 Authoring Resource
LUIS stöder inte omtilldelning av LUIS-program till E0 Authoring-resurs.

Om du behöver ändra din redigerings resurs från F0 till E0, eller ändra till en annan E0-resurs, måste du skapa programmet på nytt.

### <a name="save-button-is-disabled"></a>Knappen Spara är inaktive rad
Om du aldrig tilldelar en LUIS förutsägelse resurs till programmet inaktive ras knappen Spara när du försöker ändra din redigerings resurs utan att lägga till en förutsägelse resurs.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Se exempel på GitHub](https://aka.ms/speech/cc-samples)
