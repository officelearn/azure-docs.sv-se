---
title: Migrera prisnivåer slutpunkter från Custom Speech Service på Azure | Microsoft Docs
description: Lär dig hur du migrerar distributioner från nivåer S0 och S1 till S2 för Custom Speech Service slutpunkter i Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/05/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 44411e0c92f4e24e274761821dd99efbe60c5f5d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964981"
---
# <a name="migrate-deployments-to-the-new-pricing-model"></a>Migrera distributioner till den nya prismodellen
Från och med juli 2017 Custom Speech Service erbjuder en [nya prismodellen](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/). Den nya modellen är *lättare att förstå*, *enklare att beräkna kostnaderna*, och *mer flexibel* när det gäller skalning. Microsoft har infört konceptet med en skalningsenhet för skalning. Varje skalningsenhet kan hantera fem samtidiga begäranden. Skalning för samtidiga förfrågningar enligt den gamla modellen har ställts in på 5 samtidiga begäranden för S0-nivån och den har angetts till 12 samtidiga begäranden för nivån S1. Vi har öppnat gränserna för att erbjuda dig större flexibilitet till dina krav för användningsfall.

Om du kör en gammal S0 eller S1-nivå, rekommenderar vi att du migrerar din befintliga distributioner till den nya S2-nivån. Den nya S2-nivån omfattar varken S0 eller S1-nivån. Du kan se de tillgängliga alternativen i följande bild:

![Sidan ”Välj din prisnivå”](../../../media/cognitive-services/custom-speech-service/custom-speech-pricing-tier.png)

Microsoft hanterar migreringen i en delvis automatiserad metod. Först måste aktivera du migreringen genom att välja den nya prisnivån. Sedan kan vi att migrera din distribution automatiskt.

Mappningen från de gamla nivåerna för att skala enheter visas i följande tabell:

| Nivå | Samtidiga begäranden (gamla modellen) | Migrering | Samtidiga begäranden |
|----- | ----- | ---- | ---- |
| S0 |  5   |   => **S2** med 1 skalningsenhet |   5 |
| S1 |  12  |   => **S2** med 3 skalningsenheter |  15 |

Om du vill migrera till den nya nivån, gör du följande:

## <a name="step-1-check-your-existing-deployment"></a>Steg 1: Kontrollera din befintliga distribution
Gå till den [Custom Speech Service portal](http://cris.ai), och kontrollera dina befintliga distributioner. I vårt exempel finns två distributioner. En distribution som körs på en S0-nivån och den andra distributionen körs på en S1-nivån. Distributionerna visas i den **distributionsalternativ** kolumnen i tabellen nedan:

![Sidan distributioner](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments.png)

## <a name="step-2-select-your-new-pricing-tier-in-the-azure-portal"></a>Steg 2: Välj den nya prisnivån i Azure portal
1. Öppna en ny webbläsarflik och logga in på den [Azure-portalen](http://ms.portal.azure.com/). 

2. I den **Cognitive Services** fönstret i den **prenumerationer** väljer du din prenumeration för anpassat tal. 

3. I fönstret för din prenumeration, Välj **prisnivå**.

    ![Länken ”prisnivå”](../../../media/cognitive-services/custom-speech-service/custom-speech-update-tier.png)

4. På den **Välj din prisnivå** väljer **S2 Standard**. Den här prisnivån är ny, förenklad och mer flexibel prisnivån.

5. Välj den **Välj** knappen.

    ![Sidan ”Välj din prisnivå”](../../../media/cognitive-services/custom-speech-service/custom-speech-update-pricing.png)

## <a name="step-3-check-the-migration-status-in-the-custom-speech-service-portal"></a>Steg 3: Kontrollera Migreringsstatus för i Custom Speech Service-portalen
Gå tillbaka till Custom Speech Service-portalen och kontrollera dina distributioner. (Om ditt webbläsarfönster fortfarande är öppen, uppdatera det.) 

Status för den relaterade distributionen bör har växlats till *bearbetning av*. Du kan också bekräfta migreringen genom att markera den **distributionsalternativ** kolumn. Det kan du nu hitta information om skalningsenheter och loggning. Skalningsenheter bör återspegla din tidigare prisnivå. Loggning ska vara aktiverat på, som visas i tabellen:

![Kolumnen distributionsalternativ](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments-new.png)


> [!NOTE]
> Kontakta oss om det uppstår problem under migreringen.
>

## <a name="next-steps"></a>Nästa steg
Fler självstudier finns i:
* [Skapa en anpassad akustisk modell](cognitive-services-custom-speech-create-acoustic-model.md)
* [Skapa en anpassad språkmodell](cognitive-services-custom-speech-create-language-model.md)
* [Skapa en anpassad tal till text-slutpunkt](cognitive-services-custom-speech-create-endpoint.md)
