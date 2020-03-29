---
title: Skapa anpassade nyckelord - Taltjänst
titleSuffix: Azure Cognitive Services
description: Enheten lyssnar alltid efter ett nyckelord (eller en fras). När användaren säger nyckelordet skickar enheten allt efterföljande ljud till molnet tills användaren slutar tala. Anpassa ditt sökord är ett effektivt sätt att differentiera din enhet och stärka ditt varumärke.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 46e9f8e660c3fd62807d630481e6b3057d2351a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717017"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>Skapa ett anpassat nyckelord med Speech Studio

Enheten lyssnar alltid efter ett nyckelord (eller en fras). "Hey Cortana" är till exempel ett nyckelord för Cortana-assistenten. När användaren säger nyckelordet skickar enheten allt efterföljande ljud till molnet tills användaren slutar tala. Anpassa ditt sökord är ett effektivt sätt att differentiera din enhet och stärka ditt varumärke.

I den här artikeln får du lära dig hur du skapar ett anpassat nyckelord för enheten.

## <a name="create-your-keyword"></a>Skapa ditt sökord

Innan du kan använda ett anpassat nyckelord måste du skapa ett nyckelord med sidan [Anpassat nyckelord](https://aka.ms/sdsdk-wakewordportal) i [Talstudion](https://aka.ms/sdsdk-speechportal). När du har ange ett nyckelord skapas en fil som du distribuerar till enheten.

1. Gå till [Talstudion](https://aka.ms/sdsdk-speechportal) och **Logga in** eller, om du ännu inte har en talprenumeration, välj Skapa en [**prenumeration**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Skapa ett **nytt projekt**på sidan [Anpassat nyckelord](https://aka.ms/sdsdk-wakewordportal) . 

1. Ange ett **namn**, en valfri **beskrivning**och välj språk. Du behöver ett projekt per språk och stödet är för närvarande begränsat till språket en-US.

    ![Beskriv sökordsprojektet](media/custom-keyword/custom-kws-portal-new-project.png)

1. Välj projektet i listan. 

    ![Välj sökordsprojekt](media/custom-keyword/custom-kws-portal-project-list.png)

1. Om du vill starta en ny sökordsmodell klickar du på **Träna modell**.

1. Ange ett **namn** för sökordsmodellen och valfri **beskrivning** och skriv in det **nyckelord** du väljer och klicka på **Nästa**. Vi har några [riktlinjer](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) för att välja ett effektivt sökord.

    ![Ange ditt sökord](media/custom-keyword/custom-kws-portal-new-model.png)

1. Portalen kommer nu att skapa kandidat uttal för ditt sökord. Lyssna på varje kandidat genom att klicka på uppspelningsknapparna och ta bort checkarna bredvid eventuella uttal som är felaktiga. När endast bra uttal är markerade klickar du på **Tåg** för att börja generera nyckelordet. 

    ![Granska sökordet](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Det kan ta upp till trettio minuter innan modellen genereras. Nyckelordslistan ändras från **Bearbetning** till **lyckades** när modellen är klar. Du kan sedan ladda ner filen.

    ![Granska sökordet](media/custom-keyword/custom-kws-portal-download-model.png)

1. Spara ZIP-filen på datorn. Du behöver den här filen för att distribuera ditt anpassade sökord till din enhet.

## <a name="next-steps"></a>Nästa steg

Testa ditt anpassade sökord med [Snabbstart för Talenheter SDK](https://aka.ms/sdsdk-quickstart).
