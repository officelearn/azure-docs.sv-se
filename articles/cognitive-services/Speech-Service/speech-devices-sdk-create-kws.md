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
ms.openlocfilehash: 9a5b5de71ee290b39603968cf4309171689e22e4
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184834"
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

1. På sidan [anpassat nyckelord](https://aka.ms/sdsdk-wakewordportal) skriver du in nyckelordet som du vill välja och klickar på **Lägg till nyckelord**. Vi har några [rikt linjer](#choose-an-effective-keyword) som hjälper dig att välja ett effektivt nyckelord. Support är för närvarande begränsat till ett-US-språk.

    ![Ange ditt nyckelord](media/speech-devices-sdk/custom-kws-portal-enter-keyword.png)

1. Portalen kommer nu att skapa kandidat uttal för ditt nyckelord. Lyssna på varje kandidat genom att klicka på uppspelnings knapparna och ta bort kontrollerna bredvid eventuella uttal som är felaktiga. När endast väluttal har marker ATS väljer du **Skicka** för att börja generera nyckelordet. Om du vill ändra nyckelordet måste du först ta bort det befintliga genom att klicka på knappen Ta bort som visas på höger sida av raden och hovra över det.

    ![Granska ditt nyckelord](media/speech-devices-sdk/custom-kws-portal-review-keyword.png)

1. Det kan ta upp till en minut innan modellen genereras. Du uppmanas sedan att ladda ned filen.

    ![Hämta ditt nyckelord](media/speech-devices-sdk/custom-kws-portal-download-keyword.png)

1. Spara ZIP-filen till datorn. Du kommer att behöva den här filen för att distribuera ditt anpassade nyckelord till din enhet.

## <a name="next-steps"></a>Nästa steg

Testa ditt anpassade nyckelord med [tal enheter SDK snabb start](https://aka.ms/sdsdk-quickstart).
