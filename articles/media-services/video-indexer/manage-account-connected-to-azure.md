---
title: Hantera ett Video Indexer konto
titleSuffix: Azure Media Services
description: Den här artikeln visar hur du hanterar ett Video Indexer-konto som är anslutet till Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 4f8491e31747eda9cbe8689ba7db3026df0ff3ad
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892778"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Hantera ett Video Indexer-konto som är anslutet till Azure

Den här artikeln visar hur du hanterar ett Video Indexer-konto som är anslutet till din Azure-prenumeration och ett Azure Media Services-konto.

> [!NOTE]
> Du måste vara ägare till Video Indexer konto för att göra ändringar i konto konfigurationen som beskrivs i det här avsnittet.

## <a name="prerequisites"></a>Krav

Anslut ditt Video Indexer-konto till Azure enligt beskrivningen i [ansluta till Azure](connect-to-azure.md). 

Se till att följa [krav](connect-to-azure.md#prerequisites) och [överväganden](connect-to-azure.md#considerations) vid granskning i artikeln.

## <a name="examine-account-settings"></a>Granska konto inställningar

Det här avsnittet undersöker inställningarna för ditt Video Indexer-konto.

Så här visar du inställningar:

1. Klicka på användar ikonen i det övre högra hörnet och välj **Inställningar**.

    ![Inställningar](./media/manage-account-connected-to-azure/select-settings.png)

2. På sidan **Inställningar** väljer du fliken **konto** .

Om ditt video Indexer-konto är anslutet till Azure ser du följande:

* Namnet på det underliggande Azure Media Services kontot.
* Antalet indexerings jobb som körs och har placerats i kö.
* Antalet och typen av allokerade reserverade enheter.

Om ditt konto behöver vissa justeringar visas relevanta fel och varningar om konto konfigurationen på sidan **Inställningar** . Meddelandena innehåller länkar till exakta platser i Azure Portal där du behöver göra ändringar. Mer information finns i avsnittet [fel och varningar](#errors-and-warnings) nedan.

## <a name="auto-scale-reserved-units"></a>Automatiskt skala reserverade enheter

På sidan **Inställningar** kan du ställa in automatisk skalning av reserverade enheter (ru). Om alternativet är **aktiverat**kan du allokera maximalt antal ru: er och se till att video Indexer stoppar/startar ru: er automatiskt. Med det här alternativet betalar du inte extra pengar för inaktiv tid, men du kan inte heller vänta på att indexerings jobben ska slutföras under en längre tid när indexerings belastningen är hög.

Autoskalning skalar inte under 1 RU eller över standard gränsen för det Media Services kontot. Skapa en tjänstbegäran för att öka gränsen. Information om kvoter och begränsningar och hur du öppnar ett support ärende finns i [kvoter och begränsningar](../../media-services/previous/media-services-quotas-and-limitations.md).

![Registrera dig](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Fel och varningar

Om ditt konto behöver vissa justeringar visas relevanta fel och varningar om konto konfigurationen på sidan **Inställningar** . Meddelandena innehåller länkar till exakta platser i Azure Portal där du behöver göra ändringar. Det här avsnittet innehåller mer information om fel-och varnings meddelanden.

* Event Grid

    Du måste registrera EventGrid-resurs leverantören med hjälp av Azure Portal. I [Azure Portal](https://portal.azure.com/)går du till **prenumerationer** > [prenumeration] > **ResourceProviders** > **Microsoft. EventGrid**. Om inte i det **registrerade** läget klickar du på **Registrera**. Det tar några minuter att registrera sig. 

* Slut punkt för direkt uppspelning

    Kontrol lera att det underliggande Media Servicess kontot har standard **slut punkten för direkt uppspelning** i ett start läge. Annars kan du inte titta på videor från det här Media Servicess kontot eller i Video Indexer.

* Mediereserverade enheter 

    Du måste allokera medie reserverade enheter på din medie tjänst resurs för att kunna indexera videor. För optimal indexerings prestanda rekommenderar vi att du allokerar minst 10 S3-reserverade enheter. Information om priser finns i avsnittet Vanliga frågor och svar på sidan [Media Services prissättning](https://azure.microsoft.com/pricing/details/media-services/) .   

## <a name="next-steps"></a>Nästa steg

Du kan interagera med ditt utvärderings konto och/eller med dina Video Indexer-konton som är anslutna till Azure genom att följa anvisningarna i: [använda API: er](video-indexer-use-apis.md).

Du bör använda samma Azure AD-användare som du använde när du anslöt till Azure.
