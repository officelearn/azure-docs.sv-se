---
title: Hantera ett videoindexererkonto
titleSuffix: Azure Media Services
description: Läs om hur du hanterar ett videoindexerkonto som är anslutet till Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2019
ms.author: juliako
ms.openlocfilehash: 5b9ec1c04dd90af9d9380a1f4d30386c8f9cc3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499661"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Hantera ett videoindexerkonto som är anslutet till Azure

Den här artikeln visar hur du hanterar ett Video Indexer-konto som är anslutet till din Azure-prenumeration och ett Azure Media Services-konto.

> [!NOTE]
> Du måste vara ägare till videoindexererkontot för att göra justeringar av kontokonfigurationen som beskrivs i det här avsnittet.

## <a name="prerequisites"></a>Krav

Anslut ditt Video Indexer-konto till Azure enligt beskrivningen i [Ansluten till Azure](connect-to-azure.md).

Se till att följa [Förutsättningar](connect-to-azure.md#prerequisites) och granska [Överväganden](connect-to-azure.md#considerations) i artikeln.

## <a name="examine-account-settings"></a>Granska kontoinställningar

I det här avsnittet undersöks inställningarna för ditt Video Indexer-konto.

Så här visar du inställningar:

1. Klicka på användarikonen i det övre högra hörnet och välj **Inställningar**.

    ![Inställningar i Video Indexer](./media/manage-account-connected-to-azure/select-settings.png)

2. Välj fliken **Konto** på sidan **Inställningar.**

Om ditt Videos Indexer-konto är anslutet till Azure visas följande:

* Namnet på det underliggande Azure Media Services-kontot.
* Antalet indexeringsjobb som körs och köade.
* Antalet och typen av tilldelade reserverade enheter.

Om ditt konto behöver vissa justeringar visas relevanta fel och varningar om kontokonfigurationen på sidan **Inställningar.** Meddelandena innehåller länkar till exakta platser i Azure-portalen där du behöver göra ändringar. Mer information finns i avsnittet [fel och varningar](#errors-and-warnings) som följer.

## <a name="repair-the-connection-to-azure"></a>Reparera anslutningen till Azure

I dialogrutan **Uppdatera anslutning till Azure Media Services** på sidan Video [Indexer](https://www.videoindexer.ai/) uppmanas du att ange värden för följande inställningar:

|Inställning|Beskrivning|
|---|---|
|Azure-prenumerations-ID|Prenumerations-ID kan hämtas från Azure-portalen. Klicka på **Alla tjänster** i den vänstra panelen och sök efter "prenumerationer". Välj **Prenumerationer** och välj önskat ID i listan över dina prenumerationer.|
|Namn på Azure Media Services-resursgrupp|Namnet på resursgruppen där du skapade Media Services-kontot.|
|Program-ID:t|Azure AD-program-ID (med behörigheter för det angivna Media Services-kontot) som du skapade för det här videoindexerkontot. <br/><br/>Om du vill hämta app-ID:et navigerar du till Azure-portalen. Under medietjänstkontot väljer du ditt konto och går till **API Access**. Välj **Anslut till API för mediatjänster med tjänstens huvudnamn** -> Azure AD**App**. Kopiera relevanta parametrar.|
|Programnyckel|Azure AD-programnyckeln som är associerad med ditt Media Services-konto som du angav ovan. <br/><br/>Om du vill hämta appnyckeln navigerar du till Azure-portalen. Under medietjänstkontot väljer du ditt konto och går till **API Access**. Välj **Anslut till Api för mediatjänster med tjänsthuvudnamn** -> Hantera**programcertifikat** -> **& hemligheter**. Kopiera relevanta parametrar.|

## <a name="autoscale-reserved-units"></a>Reserverade enheter för automatisk skalning

På sidan **Inställningar** kan du ställa in automatisk skalning av mediereserverade enheter (RU). Om alternativet är **På**kan du allokera det maximala antalet ru:er och se till att videoindexeraren stoppar/startar ru:er automatiskt. Med det här alternativet betalar du inte extra pengar för inaktiv tid, men väntar inte heller på att indexeringsjobb ska slutföras under lång tid när indexeringsbelastningen är hög.

Automatisk skalning skalas inte under 1 RU eller över standardgränsen för Media Services-kontot. Skapa en servicebegäran om du vill öka gränsen. Information om kvoter och begränsningar och hur du öppnar en supportbiljett finns i [Kvoter och begränsningar](../../media-services/previous/media-services-quotas-and-limitations.md).

![Automatisk skalning reserverade enheter Video Indexer](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Fel och varningar

Om ditt konto behöver vissa justeringar visas relevanta fel och varningar om kontokonfigurationen på sidan **Inställningar.** Meddelandena innehåller länkar till exakta platser i Azure-portalen där du behöver göra ändringar. I det här avsnittet finns mer information om felmeddelanden och varningsmeddelanden.

* EventGrid (På ett sätt som är fallet)

    Du måste registrera EventGrid-resursleverantören med hjälp av Azure-portalen. Gå till **Prenumerationer** > [prenumeration] > **ResourceProviders** > **Microsoft.EventGrid**i [Azure-portalen](https://portal.azure.com/). Om inte i **registrerat** tillstånd väljer du **Registrera**. Det tar ett par minuter att registrera sig.

* Slutpunkt för direktuppspelning

    Kontrollera att det underliggande Media Services-kontot har **standardslutpunkten för direktuppspelning** i ett startat tillstånd. Annars kan du inte titta på videoklipp från det här Media Services-kontot eller i Video Indexer.

* Reserverade enheter för media

    Du måste allokera mediereserverade enheter på mediatjänstens resurs för att kunna indexera videor. För optimal indexeringsprestanda rekommenderas att minst 10 S3-reserverade enheter allokeras. Prisinformation finns i avsnittet Vanliga frågor och svar på [prissidan för Media Services.](https://azure.microsoft.com/pricing/details/media-services/)

## <a name="next-steps"></a>Nästa steg

Du kan interagera med utvärderingskontot eller Video Indexer-konton som är anslutna till Azure programmässigt genom att följa instruktionerna i: [Använd API:er](video-indexer-use-apis.md).

Använd samma Azure AD-användare som du använde när du ansluter till Azure.
