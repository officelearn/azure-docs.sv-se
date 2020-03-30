---
title: Skapa ett dedikerat kluster för händelsehubbar med Azure-portalen
description: I den här snabbstarten får du lära dig hur du skapar ett Azure Event Hubs-kluster med Azure-portalen.
services: event-hubs
documentationcenter: ''
author: femila
manager: ''
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/20/2019
ms.author: femila
ms.openlocfilehash: 5b1574eaac8771043e09500225b65e4835c8e627
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77157490"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Snabbstart: Skapa ett dedikerat eventhubbar-kluster med Azure-portal 
Event Hubs-kluster erbjuder distributioner med en klientorganisation för kunder med de mest krävande streamingbehoven. Detta erbjudande har ett garanterat 99,99% serviceavtal och är endast tillgängligt på vår dedikerade prisnivå. Ett [eventhubbar-kluster](event-hubs-dedicated-overview.md) kan gå in i miljontals händelser per sekund med garanterad kapacitet och underordnad svarstid. Namnområden och händelsehubbar som skapats i ett kluster innehåller alla funktioner i standarderbjudandet med mera, men utan några begränsningar för ingående. Det dedikerade erbjudandet innehåller också den populära funktionen [Event Hubs Capture](event-hubs-capture-overview.md) utan extra kostnad, så att du automatiskt kan batch- och loggdataströmmar till [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) eller Azure Data Lake Storage Gen [1](../data-lake-store/data-lake-store-overview.md).

Dedikerade kluster etableras och faktureras av **kapacitetsenheter ,** en förallokerad mängd processor- och minnesresurser. Du kan köpa 1, 2, 4, 8, 12, 16 eller 20 CUs för varje kluster. I den här snabbstarten kommer vi att gå igenom att skapa ett 1 CU Event Hubs-kluster via Azure-portalen.

> [!NOTE]
> Den här självbetjäningsupplevelsen är för närvarande tillgänglig i förhandsversionen på [Azure Portal](https://aka.ms/eventhubsclusterquickstart). Om du har några frågor om erbjudandet dedikerad, vänligen kontakta [Event Hubs-teamet](mailto:askeventhubs@microsoft.com).


## <a name="prerequisites"></a>Krav
För att slutföra den här snabbstarten behöver du följande:

- Ett Azure-konto. Om du inte har ett, [köpa ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) innan du börjar. Den här funktionen stöds inte med ett kostnadsfritt Azure-konto. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 Uppdatering 3 (version 15.3, 26730.01) eller senare.
- [SDK för .NET Standard](https://dotnet.microsoft.com/download) version 2.0 eller senare.
- [Skapade en resursgrupp](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Skapa ett dedikerat kluster för händelsehubbar
Ett eventhubbar-kluster tillhandahåller en unik omfångsbehållare där du kan skapa ett eller flera namnområden. I den här förhandsgranskningsfasen av portalens självbetjäningsupplevelse kan du skapa 1 CU-kluster i vissa regioner. Om du behöver ett kluster som är större än 1 CU kan du skicka en Azure-supportbegäran för att skala upp klustret när det har skapats.

Så här skapar du ett kluster i resursgruppen med Azure-portalen:

1. Följ [den här länken](https://aka.ms/eventhubsclusterquickstart) för att skapa ett kluster på Azure Portal. Omvänt väljer du **Alla tjänster** i det vänstra navigeringsfönstret och skriver sedan i "Event Hubs Clusters" i sökfältet och väljer "Event Hubs Clusters" i resultatlistan.
2. Konfigurera följande på sidan **Skapa kluster:**
    1. Ange ett **namn på klustret**. Systemet kontrollerar omedelbart om namnet är tillgängligt.
    2. Välj den **prenumeration** där du vill skapa klustret.
    3. Markera den **resursgrupp** där du vill skapa klustret.
    4. Välj en **plats** för klustret. Om önskad region är nedtonad är den tillfälligt ur kapacitet och du kan skicka en [supportbegäran](#submit-a-support-request) till Event Hubs-teamet.
    5. Välj knappen **Nästa: Taggar** längst ned på sidan. Du kan behöva vänta några minuter på att systemet blir klar med att etablera resurserna.

        ![Skapa kluster för händelsehubbar – sidan Grunderna](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. Konfigurera följande på sidan **Taggar:**
    1. Ange ett **namn** och ett **värde** för den tagg som du vill lägga till. Det här steget är **valfritt**.  
    2. Välj knappen **Granska + Skapa.**

        ![Sidan Skapa klustersida för händelsehubbar – sidan Taggar](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. På sidan **Granska + Skapa** granskar du informationen och väljer **Skapa**. 

    ![Sidan Skapa klustersida för händelsehubbar – sidan Granska + Skapa](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Skapa ett namnområde och en händelsehubb i ett kluster

1. Om du vill skapa ett namnområde i ett kluster väljer du **+Namnområde** på den övre menyn på sidan Kluster med **händelsehubbar för klustret.**

    ![Sidan Klusterhantering - knappen Lägg till namnområde](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Gör följande på sidan Skapa ett namnområde:
    1. Ange ett **namn för namnrymden**.  Systemet kontrollerar om namnet är tillgängligt.
    2. Namnområdet ärver följande egenskaper:
        1. Prenumerations-ID:t
        2. Resursgrupp
        3. Location
        4. Klusternamn
    3. Välj **Skapa** om du vill skapa namnområdet. Nu kan du hantera ditt kluster.  

        ![Skapa namnområde på klustersidan](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. När namnområdet har skapats kan du [skapa en händelsehubb](event-hubs-create.md#create-an-event-hub) som vanligt skapa en inom ett namnområde. 


## <a name="submit-a-support-request"></a>Skicka en supportbegäran

Om du vill ändra storleken på klustret när du har skapat det eller om den region som föredras inte är tillgänglig skickar du en supportbegäran genom att följa dessa steg:

1. I [Azure Portal](https://portal.azure.com)väljer du Stöd + **stöd** från den vänstra menyn.
2. Välj **+ Ny supportbegäran** på supportmenyn.
3. Gör så här på supportsidan:
    1. För **Ärendetyp**väljer du **Teknisk** i listrutan.
    2. I fältet **Prenumeration** väljer du din prenumeration.
    3. För **Tjänst**väljer du **Mina tjänster**och väljer sedan Event **Hubs**.
    4. För **Resurs**väljer du klustret om det redan finns, annars väljer du **Allmän fråga/resurs inte tillgänglig**.
    5. Välj **Kvot**för **typen Problem**.
    6. För **undertyp problem**väljer du ett av följande värden i listrutan:
        1. Välj **Begär dedikerad SKU** om du vill begära att funktionen ska stödjas i din region.
        2. Välj **Begäran om att skala upp eller skala ned dedikerat kluster** om du vill skala upp eller skala ned ditt dedikerade kluster. 
    7. Beskriv problemet **för Ämne.**

        ![Sida för supportbiljett](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Ta bort ett dedikerat kluster
 
1. Om du vill ta bort klustret väljer du **Ta bort** på den övre menyn. Observera att klustret debiteras för minst 4 timmars användning efter att ha skapats. 
2. Ett meddelande visas som bekräftar din önskan att ta bort klustret.
3. Skriv **namnet på klustret** och välj **Ta bort** om du vill ta bort klustret.

    ![Ta bort klustersida](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat ett eventhubs-kluster. Stegvisa instruktioner för att skicka och ta emot händelser från en händelsehubb och samla in händelser till ett Azure-lagringsutrymme eller Azure Data Lake Store finns i följande självstudier:

- Skicka och ta emot händelser 
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
- [Använda Azure-portal för att aktivera hämtning av händelsehubbar](event-hubs-capture-enable-through-portal.md)
- [Använda Azure Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
