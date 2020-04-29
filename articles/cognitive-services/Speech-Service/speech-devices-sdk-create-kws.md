---
title: Skapa anpassade nyckelord – tal tjänsten
titleSuffix: Azure Cognitive Services
description: Enheten lyssnar alltid efter ett nyckelord (eller en fras). När användaren säger nyckelordet skickar enheten allt efterföljande ljud till molnet tills användaren slutar att tala. Att anpassa ditt nyckelord är ett effektivt sätt att särskilja din enhet och förbättra ditt varumärke.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: trbye
ms.openlocfilehash: 8e67d624c77eb838f7646731bbdedd8f97f81b96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400065"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>Skapa ett anpassat nyckelord med tal Studio

Enheten lyssnar alltid efter ett nyckelord (eller en fras). Till exempel är "Hej Cortana" ett nyckelord för Cortana-assistenten. När användaren säger nyckelordet skickar enheten allt efterföljande ljud till molnet tills användaren slutar att tala. Att anpassa ditt nyckelord är ett effektivt sätt att särskilja din enhet och förbättra ditt varumärke.

I den här artikeln får du lära dig hur du skapar ett anpassat nyckelord för din enhet.

## <a name="create-your-keyword"></a>Skapa ditt nyckelord

Innan du kan använda ett anpassat nyckelord måste du skapa ett nyckelord med hjälp av den [anpassade nyckelords](https://aka.ms/sdsdk-wakewordportal) sidan i [tal Studio](https://aka.ms/sdsdk-speechportal). När du har angett ett nyckelord genererar det en fil som du distribuerar till din enhet.

1. Gå till [tal Studio](https://aka.ms/sdsdk-speechportal) och **Logga** in, eller Välj [**skapa en prenumeration**](https://go.microsoft.com/fwlink/?linkid=2086754)om du inte har en tal prenumeration än.

1. Skapa ett **nytt projekt**på sidan [anpassat nyckelord](https://aka.ms/sdsdk-wakewordportal) . 

1. Ange ett **namn**, en valfri **Beskrivning**och välj språket. Du behöver ett projekt per språk och stöd är för närvarande begränsat till ett-US-språk.

    ![Beskriv ditt nyckelords projekt](media/custom-keyword/custom-kws-portal-new-project.png)

1. Välj ditt projekt i listan. 

    ![Välj ditt nyckelords projekt](media/custom-keyword/custom-kws-portal-project-list.png)

1. Starta en ny nyckelords modell genom att klicka på **träna modell**.

1. Ange ett **namn** för nyckelords modellen och valfri **Beskrivning** och Skriv valfritt alternativ och **Klicka på** **Nästa**. Vi har några [rikt linjer](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) som hjälper dig att välja ett effektivt nyckelord.

    ![Ange ditt nyckelord](media/custom-keyword/custom-kws-portal-new-model.png)

1. Portalen kommer nu att skapa kandidat uttal för ditt nyckelord. Lyssna på varje kandidat genom att klicka på uppspelnings knapparna och ta bort kontrollerna bredvid eventuella uttal som är felaktiga. När endast väluttal är markerat klickar du på **träna** för att börja generera nyckelordet. 

    ![Granska ditt nyckelord](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Det kan ta upp till trettio minuter innan modellen genereras. Nyckelords listan ändras från **bearbetning** till **lyckades** när modellen har slutförts. Sedan kan du hämta filen.

    ![Granska ditt nyckelord](media/custom-keyword/custom-kws-portal-download-model.png)

1. Spara zip-filen på din dator. Du kommer att behöva den här filen för att distribuera ditt anpassade nyckelord till din enhet.

## <a name="next-steps"></a>Nästa steg

Testa ditt anpassade nyckelord med [tal enheter SDK snabb start](https://aka.ms/sdsdk-quickstart).
