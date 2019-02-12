---
title: Hantera en Video Indexer-konto
titlesuffix: Azure Media Services
description: Den här artikeln visar hur du hanterar en Video Indexer-konto som är ansluten till Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 1515a026ae297a960f220a97449d2258c0b75e58
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004554"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Hantera en Video Indexer-konto som är ansluten till Azure

Den här artikeln visar hur du hanterar en Video Indexer-konto som är ansluten till din Azure-prenumeration och ett Azure Media Services-konto.

> [!NOTE]
> Du måste vara Kontoägare om Video Indexer och göra kontot konfigurationsjusteringar som beskrivs i det här avsnittet.

## <a name="prerequisites"></a>Förutsättningar

Anslut ditt Video Indexer-konto till Azure, enligt beskrivningen i [är ansluten till Azure](connect-to-azure.md). 

Se till att följa [krav](connect-to-azure.md#prerequisites) och granska [överväganden](connect-to-azure.md#considerations) i artikeln.

## <a name="examine-account-settings"></a>Granska inställningarna för kontot

Det här avsnittet går igenom inställningarna för ditt Video Indexer-konto.

Visa inställningar:

1. Klicka på användarikonen i det övre högra hörnet och välj **inställningar**.

    ![Inställningar](./media/manage-account-connected-to-azure/select-settings.png)

2. På den **inställningar** väljer den **konto** fliken.

Om ditt video Indexer-konto är ansluten till Azure kan se du följande:

* Namnet på det underliggande Azure Media Services-kontot.
* Antal indexerade jobb som körs och väntar i kö.
* Antalet och typen av allokerade-reserverade enheter.

Om ditt konto måste vissa justeringar, visas relevanta fel och varningar om din kontokonfiguration på den **inställningar** sidan. Meddelanden innehåller länkar till exakt platser i Azure-portalen där du behöver göra ändringar. Mer information finns i den [fel och varningar](#errors-and-warnings) avsnitt.

## <a name="auto-scale-reserved-units"></a>Autoskala reserverade enheter

Den **inställningar** sidan kan du ställa in automatisk skalning av Media reserverade enheter (RU). Om alternativet är **på**, du kan allokera det maximala antalet enheter för programbegäran och se till att Video Indexer stoppas/startar ru: er automatiskt. Med det här alternativet kan du betalar inte extra pengar för inaktivitetstid men också vänta inte indexeringsjobb för att slutföra en lång tid när indexering belastningen är hög.

Automatisk skalning skalar inte under 1 RU eller över Standardgränsen för Media Services-kontot. För att öka gränsen, skapar du en tjänstbegäran. Läs om hur kvoter och begränsningar och hur du öppnar ett supportärende [kvoter och begränsningar](../../media-services/previous/media-services-quotas-and-limitations.md).

![Registrera dig](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Fel och varningar

Om ditt konto behöver vissa justeringar kan du se relevanta fel och varningar om din kontokonfiguration på den **inställningar** sidan. Meddelanden innehåller länkar till exakt platser i Azure-portalen där du behöver göra ändringar. Det här avsnittet ger mer information om felet och varningsmeddelanden.

* Event Grid

    Du måste registrera resursprovidern EventGrid med Azure portal. I den [Azure-portalen](https://portal.azure.com/)går du till **prenumerationer** > [prenumeration] > **ResourceProviders** > **Microsoft.EventGrid**. Om inte i den **registrerad** tillstånd, klickar du på **registrera**. Det tar några minuter att registrera. 

* Slutpunkt för direktuppspelning

    Kontrollera att det underliggande Media Services-kontot har standardvärdet **Strömningsslutpunkt** tillståndet startad. I annat fall kommer du inte att kunna titta på videor från Media Services-konto eller i Video Indexer.

* Mediereserverade enheter 

    Mediereserverade enheter måste du allokera på Media Service resursen ska index videor. För optimala prestanda för indexering rekommenderar vi för att allokera minst 10 S3 Mediereserverade enheter. Information om priser finns i avsnittet vanliga frågor och svar i den [prissättning för Media Services](https://azure.microsoft.com/pricing/details/media-services/) sidan.   

## <a name="next-steps"></a>Nästa steg

Du kan interagera med ditt utvärderingskonto och/eller med din Video Indexer-användarkonton som är anslutna till azure genom att följa anvisningarna i: [Använd API: er](video-indexer-use-apis.md).

Du bör använda samma Azure AD-användare som du använde när du ansluter till Azure.
