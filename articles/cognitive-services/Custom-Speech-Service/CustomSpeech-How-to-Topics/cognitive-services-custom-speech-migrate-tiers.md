---
title: Migrera prisnivåer slutpunkter från anpassade tal tjänsten i Azure | Microsoft Docs
description: Lär dig hur du migrerar distributioner från nivåerna S0 och S1 till S2 av anpassade tal slutpunkter i kognitiva Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/05/2017
ms.author: panosper
ms.openlocfilehash: 6d92459deb3464cd97c215cbf9a8320628b6da80
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352428"
---
# <a name="migrate-deployments-to-the-new-pricing-model"></a>Migrera distributioner till nya priserna
Från och med juli 2017 anpassad tal tjänst erbjuder en [nya Prismodell](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/). Den nya modellen är *lättare att förstå*, *enklare att beräkna kostnader*, och *mer flexibel* vad gäller skalning. För att skala, har Microsoft introducerade konceptet för en skalningsenhet. Varje skalningsenhet kan hantera fem samtidiga begäranden. Skalning för samtidiga begäranden i gamla modellen har ställts in på 5 samtidiga förfrågningar för nivån S0 och den har angetts till 12 samtidiga förfrågningar för nivån S1. Vi har öppnat gränserna för att erbjuda du större flexibilitet med användningsfall krav.

Om du kör en gammal S0 eller S1-nivå, rekommenderar vi du migrera dina befintliga distributioner till ny S2 nivå. Ny S2 nivå omfattar både S0 och S1 nivåer. Du kan se de tillgängliga alternativen i följande bild:

![Sidan ”Välj din prisnivå”](../../../media/cognitive-services/custom-speech-service/custom-speech-pricing-tier.png)

Microsoft hanterar migreringen delvis automatiskt. Först måste utlösa du migreringen genom att välja den nya prisnivån. Vi migrera din distribution automatiskt.

Mappning från de gamla nivåerna att skala enheter visas i följande tabell:

| Nivå | Samtidiga begäranden (gamla model) | Migrering | Samtidiga begäranden |
|----- | ----- | ---- | ---- |
| S0 |  5   |   => **S2** med 1 skalningsenhet |   5 |
| S1 |  12  |   => **S2** med 3 skalningsenheter |  15 |

Om du vill migrera till det nya lagret, gör du följande:

## <a name="step-1-check-your-existing-deployment"></a>Steg 1: Kontrollera den befintliga distributionen
Gå till den [anpassade tal tjänstportalen](http://cris.ai), och kontrollera dina befintliga distributioner. I vårt exempel finns två distributioner. En distribution som körs på en S0 nivå och den andra distributionen körs på en S1-nivå. Distributioner som visas i den **distributionsalternativ** kolumnen i tabellen nedan:

![Sidan distributioner](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments.png)

## <a name="step-2-select-your-new-pricing-tier-in-the-azure-portal"></a>Steg 2: Välj ny prisnivå i Azure-portalen
1. Öppna en ny webbläsarflik och logga in på den [Azure-portalen](http://ms.portal.azure.com/). 

2. I den **kognitiva Services** rutan i den **prenumerationer** väljer du prenumerationen anpassade tal. 

3. Välj i rutan för din prenumeration **prisnivå**.

    ![Länken ”prisnivå”](../../../media/cognitive-services/custom-speech-service/custom-speech-update-tier.png)

4. På den **Välj din prisnivå** väljer **S2 Standard**. Den här prisnivån är den nya, förenkla och mer flexibel prisnivån.

5. Välj den **Välj** knappen.

    ![Sidan ”Välj din prisnivå”](../../../media/cognitive-services/custom-speech-service/custom-speech-update-pricing.png)

## <a name="step-3-check-the-migration-status-in-the-custom-speech-service-portal"></a>Steg 3: Kontrollera status för migrering i anpassade tal Service portal
Tillbaka till anpassad tal tjänstportalen och kontrollera dina distributioner. (Om webbläsaren fortfarande är öppen, uppdatera det.) 

Statusen för relaterade distributionen bör har växlats till *bearbetning*. Du kan också verifiera migreringen genom att kontrollera den **distributionsalternativ** kolumn. Du hittar det nu information om skalningsenheter och loggning. Vilka skalningsenheter bör avspegla tidigare prisnivå. Loggning bör också vara aktiverat, som visas i tabellen:

![Kolumnen distributionsalternativ](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments-new.png)


> [!NOTE]
> Kontakta oss om det uppstår problem under migreringen.
>

## <a name="next-steps"></a>Nästa steg
Flera självstudier finns:
* [Skapa en anpassad ljud modell](cognitive-services-custom-speech-create-acoustic-model.md)
* [Skapa en anpassad språkmodell](cognitive-services-custom-speech-create-language-model.md)
* [Skapa en anpassad till text till tal-slutpunkt](cognitive-services-custom-speech-create-endpoint.md)
