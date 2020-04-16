---
title: Förbättra syntesen med custom voice - taltjänst
titleSuffix: Azure Cognitive Services
description: Custom Voice är en uppsättning online-verktyg som gör att du kan skapa en igenkännlig, unik röst för ditt varumärke. Allt som krävs för att komma igång är en handfull ljudfiler och tillhörande transkriptioner. Följ länkarna nedan för att börja skapa en anpassad tal-till-text-upplevelse.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 2aacf8881a57f0677177cd341a0ddc63224b26b6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402058"
---
# <a name="get-started-with-custom-voice"></a>Komma igång med Custom Voice

[Custom Voice](https://aka.ms/customvoice) är en uppsättning online-verktyg som gör att du kan skapa en igenkännlig, unik röst för ditt varumärke. Allt som krävs för att komma igång är en handfull ljudfiler och tillhörande transkriptioner. Följ länkarna nedan för att börja skapa en anpassad text-till-tal-upplevelse.

## <a name="whats-in-custom-voice"></a>Vad finns i Custom Voice?

Innan du börjar med Custom Voice behöver du ett Azure-konto och en taltjänstprenumeration. När du har skapat ett konto kan du förbereda dina data, träna och testa dina modeller, utvärdera röstkvaliteten och i slutändan distribuera din anpassade röstmodell.

Diagrammet nedan markerar stegen för att skapa en anpassad röstmodell med hjälp av [Custom Voice-portalen](https://aka.ms/customvoice). Använd länkarna för att läsa mer.

![Anpassat arkitekturdiagram för röst](media/custom-voice/custom-voice-diagram.png)

1. [Prenumerera och skapa ett projekt](#set-up-your-azure-account) - Skapa ett Azure-konto och skapa en taltjänstprenumeration. Den här enhetliga prenumerationen ger dig tillgång till tal-till-text, text-till-tal, talöversättning och Custom Voice-portalen. Skapa sedan ditt första Custom Voice-projekt med hjälp av prenumerationen på Taltjänsten.

2. [Ladda upp data](how-to-custom-voice-create-voice.md#upload-your-datasets) - Ladda upp data (ljud och text) med hjälp av Custom Voice portal eller Custom Voice API. Från portalen kan du undersöka och utvärdera uttalspoäng och signal-brus-förhållanden. Mer information finns i [Så här förbereder du data för anpassad röst](how-to-custom-voice-prepare-data.md).

3. [Träna din modell](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – Använd dina data för att skapa en anpassad text-till-tal-röstmodell. Du kan träna en modell på olika språk. Efter träningen testar du din modell och om du är nöjd med resultatet kan du distribuera modellen.

4. [Distribuera din modell](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) – Skapa en anpassad slutpunkt för din röstmodell för text till tal och använd den för talsyntes i dina produkter, verktyg och program.

## <a name="custom-neural-voices"></a>Anpassade neurala röster

Funktionen för anpassning av neural röst är för närvarande i offentlig förhandsversion, begränsad till utvalda kunder. Fyll i det här [ansökningsformuläret](https://go.microsoft.com/fwlink/?linkid=2108737) för att komma igång.

> [!NOTE]
> Som en del av Microsofts åtagande att utforma ansvarsfull AI är vår avsikt att skydda individens och samhällets rättigheter och främja transparenta interaktioner mellan människa och dator. Av denna anledning, Custom Neural Voice är inte allmänt tillgänglig för alla kunder. Du kan få tillgång till tekniken först efter att dina program har granskats och du har åtagit dig att använda den i enlighet med våra etiska principer. Läs mer om vår [ansökningsprocess](https://aka.ms/custom-neural-gating-overview).

## <a name="set-up-your-azure-account"></a>Konfigurera ditt Azure-konto

En taltjänstprenumeration krävs innan du kan använda portalen Anpassat tal för att skapa en anpassad modell. Följ dessa instruktioner för att skapa en taltjänstprenumeration i Azure. Om du inte har ett Azure-konto kan du registrera dig för ett nytt.  

När du har skapat ett Azure-konto och en taltjänstprenumeration måste du logga in på Custom Voice-portalen och ansluta din prenumeration.

1. Hämta prenumerationsnyckeln för Taltjänsten från Azure-portalen.
2. Logga in på [custom voice-portalen](https://aka.ms/custom-voice).
3. Välj din prenumeration och skapa ett talprojekt.
4. Om du vill byta till en annan Talprenumeration använder du kuggikonen som finns i den övre navigeringen.

> [!NOTE]
> Tjänsten Custom Voice stöder INTE den kostnadsfria provnyckeln på 30 dagar. Du måste ha en F0- eller en S0-nyckel skapad i Azure innan du kan använda tjänsten.

## <a name="how-to-create-a-project"></a>Så här skapar du ett projekt

Innehåll som data, modeller, tester och slutpunkter är ordnat i **Projekt** i portalen Anpassad röst. Varje projekt är specifikt för ett land/språk och könet på den röst du vill skapa. Du kan till exempel skapa ett projekt för en kvinnlig röst för callcentrets chattrobotar som använder engelska i USA (en-US).

Om du vill skapa ditt första projekt markerar du fliken **Text-till-tal/Anpassad röst** och klickar sedan på **Nytt projekt**. Skapa projektet genom att följa instruktionerna i guiden. När du har skapat ett projekt visas fyra flikar: **Data,** **Utbildning,** **Testning**och **Distribution**. Använd länkarna i [nästa steg](#next-steps) för att lära dig hur du använder varje flik.

> [!IMPORTANT]
> [Custom Voice-portalen](https://aka.ms/custom-voice) har nyligen uppdaterats! Om du har skapat tidigare data, modeller, tester och publicerade slutpunkter i CRIS.ai portalen eller med API:er måste du skapa ett nytt projekt i den nya portalen för att ansluta till dessa gamla entiteter.

## <a name="next-steps"></a>Nästa steg

- [Förbereda anpassade röstdata](how-to-custom-voice-prepare-data.md)
- [Skapa en anpassad röst](how-to-custom-voice-create-voice.md)
- [Guide: Spela in dina röstexempel](record-custom-voice-samples.md)
