---
title: Skapa en anpassad tjänst för nyckelords tal
titleSuffix: Azure Cognitive Services
description: Enheten lyssnar alltid efter ett nyckelord (eller en fras). När användaren säger nyckelordet skickar enheten allt efterföljande ljud till molnet tills användaren slutar att tala. Att anpassa ditt nyckelord är ett effektivt sätt att särskilja din enhet och förbättra ditt varumärke.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: erhopf
ms.openlocfilehash: 42bcc336bfeb325a08c3d65438d66690c0b35100
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896426"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>Skapa ett anpassat nyckelord med hjälp av tal tjänsten

Enheten lyssnar alltid efter ett nyckelord (eller en fras). Till exempel är "Hej Cortana" ett nyckelord för Cortana-assistenten. När användaren säger nyckelordet skickar enheten allt efterföljande ljud till molnet tills användaren slutar att tala. Att anpassa ditt nyckelord är ett effektivt sätt att särskilja din enhet och förbättra ditt varumärke.

I den här artikeln får du lära dig hur du skapar ett anpassat nyckelord för din enhet.

## <a name="choose-an-effective-keyword"></a>Välj ett effektivt nyckelord

Överväg följande rikt linjer när du väljer ett nyckelord:

* Ditt nyckelord bör vara ett engelskt ord eller en fras. Det bör ta högst två sekunder för att säga.

* Ord av 4-7 stavelser fungerar bäst. Till exempel "Hej, dator" är ett utmärkt nyckelord. Bara ”Hey” är dålig.

* Nyckelord bör följa vanliga intals regler för engelska uttal.

* Ett unikt eller ett sydda ord som följer den vanliga engelska uttal reglerna kan minska falska positiva identifieringar. Till exempel kan "computerama" vara ett utmärkt nyckelord.

* Välj inte ett vanligt ord. Till exempel ”äta” och ”gå” är ord som personer säga ofta i vanlig konversationen. De kan vara falskt utlösare för din enhet.

* Undvik att använda nyckelord som kan ha alternativa uttal. Användare behöver veta ”höger” uttal att få sin enhet för att svara. Till exempel ”509” förstärkas ”fem noll nio”, ”fem ojsan nio”, eller ”fem hundra och nio”. ”R.E.I.” förstärkas ”r-e-i” eller ”ray”. ”Live” förstärkas ”/līv/” eller ”/liv/”.

* Använd inte specialtecken, symboler och siffror. Till exempel "go #" och "20 + katter" skulle inte vara lämpliga nyckelord. Dock ”gå sharp” eller ”tjugo plus katt” fungerar. Du kan fortfarande använda symbolerna i din företagsanpassning och använda marknadsföring och dokumentation för att säkerställa rätt uttal.

> [!NOTE]
> Om du väljer ett tilldelat ord som nyckelord måste du vara säker på att du äger detta varumärke eller att du har behörighet från varumärkes ägaren för att använda ordet. Microsoft ansvarar inte för några juridiska problem som kan uppstå från ditt val av nyckelord.

## <a name="create-your-keyword"></a>Skapa ditt nyckelord

Innan du kan använda ett anpassat nyckelord måste du skapa ett nyckelord med hjälp av den [anpassade nyckelords](https://aka.ms/sdsdk-wakewordportal) sidan i [tal Studio](https://aka.ms/sdsdk-speechportal). När du har angett ett nyckelord genererar det en fil som du distribuerar till din enhet.

1. Gå till [tal Studio](https://aka.ms/sdsdk-speechportal) och **Logga** in, eller Välj [**skapa en prenumeration**](https://go.microsoft.com/fwlink/?linkid=2086754)om du inte har en tal prenumeration än.

1. Skapa ett **nytt projekt**på sidan [anpassat nyckelord](https://aka.ms/sdsdk-wakewordportal) . 

1. Ange ett **namn**, en valfri **Beskrivning**och välj språket. Du behöver ett projekt per språk och stöd är för närvarande begränsat till ett-US-språk.

    ![Beskriv ditt nyckelords projekt](media/custom-keyword/custom-kws-portal-new-project.png)

1. Välj ditt projekt i listan. 

    ![Välj ditt nyckelords projekt](media/custom-keyword/custom-kws-portal-project-list.png)

1. Starta en ny nyckelords modell genom att klicka på **träna modell**.

1. Ange ett **namn** för nyckelords modellen och valfri **Beskrivning** och Skriv valfritt alternativ och **Klicka på** **Nästa**. Vi har några [rikt linjer](#choose-an-effective-keyword) som hjälper dig att välja ett effektivt nyckelord.

    ![Ange ditt nyckelord](media/custom-keyword/custom-kws-portal-new-model.png) 

1. Portalen kommer nu att skapa kandidat uttal för ditt nyckelord. Lyssna på varje kandidat genom att klicka på uppspelnings knapparna och ta bort kontrollerna bredvid eventuella uttal som är felaktiga. När endast väluttal är markerat klickar du på **träna** för att börja generera nyckelordet. 

    ![Granska ditt nyckelord](media/custom-keyword/custom-kws-portal-choose-prons.png) 

1. Det kan ta upp till tio minuter innan modellen genereras. Nyckelords listan ändras från **bearbetning** till **lyckades** när modellen har slutförts. Sedan kan du hämta filen.

    ![Granska ditt nyckelord](media/custom-keyword/custom-kws-portal-download-model.png) 

1. Spara ZIP-filen till datorn. Du kommer att behöva den här filen för att distribuera ditt anpassade nyckelord till din enhet.

## <a name="next-steps"></a>Nästa steg

Testa ditt anpassade nyckelord med [tal enheter SDK snabb start](https://aka.ms/sdsdk-quickstart).
