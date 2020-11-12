---
title: Hantera slut punkter och vägar (portal)
titleSuffix: Azure Digital Twins
description: Se hur du konfigurerar och hanterar slut punkter och händelse vägar för Azure Digitals dubbla data med hjälp av Azure Portal.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 083d868f2d2652be9480227c29dfb289564056d6
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533794"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Hantera slut punkter och vägar i Azure Digitals dubbla (portal)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

I Azure Digitals dubbla, kan du dirigera [händelse meddelanden](how-to-interpret-event-data.md) till underordnade tjänster eller anslutna beräknings resurser. Detta görs genom att först konfigurera **slut punkter** som kan ta emot händelserna. Du kan sedan skapa [**händelse vägar**](concepts-route-events.md) som anger vilka händelser som genereras av digitala Digital-meddelanden i Azure som levereras till vilka slut punkter.

Den här artikeln vägleder dig genom processen att skapa slut punkter och flöden med hjälp av [Azure Portal](https://portal.azure.com).

Du kan också hantera slut punkter och vägar med [händelse vägar API: er](/rest/api/digital-twins/dataplane/eventroutes), [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis): er eller [Azure Digitals flätade CLI](how-to-use-cli.md). En version av den här artikeln som använder dessa mekanismer i stället för portalen finns i [*så här gör du: hantera slut punkter och vägar (API: er och CLI)*](how-to-manage-routes-apis-cli.md).

## <a name="prerequisites"></a>Förutsättningar

* Du behöver ett **Azure-konto** (du kan ställa in ett kostnads fritt [här](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* Du behöver en **Azure Digitals-instans** i din Azure-prenumeration. Om du inte redan har en instans kan du skapa en med hjälp av anvisningarna i [*instruktion: Konfigurera en instans och autentisering*](how-to-set-up-instance-portal.md). Ha följande värden från installations programmet som är praktiskt att använda senare i den här artikeln:
    - Instansnamn
    - Resursgrupp

Du hittar den här informationen i [Azure Portal](https://portal.azure.com) efter att du har konfigurerat instansen. Logga in på portalen och Sök efter namnet på din instans i portalens Sök fält.
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Skärm bild av Azure Portal Sök fältet.":::

Välj din instans från resultaten om du vill visa informations sidan för din instans:

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Skärm bild av ADT instance-information." border="false":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Skapa en slut punkt för Azure Digitals dubbla

Detta är de typer av slut punkter som stöds som du kan skapa för din instans:
* [Event Grid](../event-grid/overview.md) 
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Mer information om olika typer av slut punkter finns i [*Välj mellan Azure Messaging Services*](../event-grid/compare-messaging-services.md).

Om du vill länka en slut punkt till Azure Digitals-band måste Event Grid-ämnet, händelsehubben eller Service Bus som du använder för slut punkten redan finnas. 

### <a name="create-an-event-grid-endpoint"></a>Skapa en Event Grid-slutpunkt

**Förutsättning** : skapa ett event Grid-ämne genom att följa stegen i [avsnittet *skapa ett anpassat ämne*](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) i snabb starten för Event Grid *anpassade händelser* .

När du har skapat ämnet kan du länka det till Azure Digitals-sidor från sidan med Azure Digitals dubbla instansen i [Azure Portal](https://portal.azure.com) (du kan hitta instansen genom att ange dess namn i portalens Sök fält).

Från menyn instans väljer du _slut punkter_. Välj sedan *+ skapa en slut punkt* från sidan *slut punkter* som följer. 

På sidan *skapa en slut punkt* som öppnas, kan du skapa en slut punkt av typen _Event Grid_ genom att välja motsvarande alternativ knapp. Slutför den andra informationen: Ange ett namn för din slut punkt i fältet _namn_ , välj din _prenumeration_ i list rutan och välj det  _Event Grid avsnittet_ som skapats i den tredje List rutan.

Skapa sedan slut punkten genom att trycka på _Spara_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Skärm bild som visar hur du skapar en slut punkt av typen Event Grid.":::

Du kan kontrol lera att slut punkten har skapats genom att kontrol lera meddelande ikonen i översta Azure Portals fältet: 

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="Skärm bild av meddelande för att verifiera att slut punkten skapas." border="false":::

Du kan också Visa slut punkten som skapades på sidan *slut punkter* för din Azure Digital-instansen.

Om det inte går att skapa en slut punkt, Observera fel meddelandet och försök igen om några minuter.

Nu är event Grid-avsnittet tillgängligt som en slut punkt inuti Azure Digital-dubbla, under det namn som anges i fältet _namn_ . Du använder vanligt vis det namnet som mål för en **händelse väg** , som du kommer att skapa [senare i den här artikeln](#create-an-event-route).

### <a name="create-an-event-hubs-endpoint"></a>Skapa en Event Hubs-slutpunkt

**Krav** : 
* Du behöver ett _Event Hubs-namnområde_ och en _Event Hub_. Skapa båda dessa genom att följa stegen i Event Hubs skapa en snabb start för [*Event Hub*](../event-hubs/event-hubs-create.md) .
* Du behöver en _auktoriseringsregel_. Information om hur du skapar detta finns i artikeln Event Hubs [*auktorisering av åtkomst till Event Hubs resurser med hjälp av signaturer för delad åtkomst*](../event-hubs/authorize-access-shared-access-signature.md) .

Gå till informations sidan för din Azure Digital-instansen i [Azure Portal](https://portal.azure.com) (du kan hitta den genom att ange dess namn i portalens Sök fält).

Från menyn instans väljer du _slut punkter_. Välj sedan *+ skapa en slut punkt* från sidan *slut punkter* som följer. 

På sidan *skapa en slut punkt* som öppnas, kan du skapa en slut punkt av typen _Event Hub_ genom att välja motsvarande alternativ knapp. Ange ett namn för din slut punkt i fältet _namn_ . Välj din _prenumeration_ och ditt förskapade _Event Hub-namnområde_ , _händelsehubben_ och _auktoriseringsregel_ från respektive listruta.

Skapa sedan slut punkten genom att trycka på _Spara_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub.png" alt-text="Skärm bild som visar hur du skapar en slut punkt av typen Event Hubs.":::

Du kan kontrol lera att slut punkten har skapats genom att kontrol lera meddelande ikonen i det översta Azure Portal fältet. 

Om det inte går att skapa en slut punkt, Observera fel meddelandet och försök igen om några minuter.

Nu är Händelsehubben tillgänglig som en slut punkt inuti Azure Digital-dubbla, under det namn som anges i fältet _namn_ . Du använder vanligt vis det namnet som mål för en **händelse väg** , som du kommer att skapa [senare i den här artikeln](#create-an-event-route).

### <a name="create-a-service-bus-endpoint"></a>Skapa en Service Bus-slutpunkt

**Krav** : 
* Du behöver ett _Service Bus-namnområde_ och ett _Service Bus ämne_. Skapa båda dessa genom att följa stegen i snabb starten för Service Bus [*skapa ämnen och prenumerationer*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) . Du behöver inte slutföra avsnittet [*skapa prenumerationer på avsnittet*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md#create-subscriptions-to-the-topic) .
* Du behöver en _auktoriseringsregel_. Information om hur du skapar detta finns i artikeln Service Bus [*autentisering och auktorisering*](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) .

Gå till informations sidan för din Azure Digital-instansen i [Azure Portal](https://portal.azure.com) (du kan hitta den genom att ange dess namn i portalens Sök fält).

Från menyn instans väljer du _slut punkter_. Välj sedan *+ skapa en slut punkt* från sidan *slut punkter* som följer. 

På sidan *skapa en slut punkt* som öppnas, kan du skapa en slut punkt av typen _Service Bus_ genom att välja motsvarande alternativ knapp. Ange ett namn för din slut punkt i fältet _namn_ . Välj din _prenumeration_ och den _Service Bus namn rymden_ som du har skapat, _Service Bus ämnet_ och _auktoriseringsregeln_ från respektive listruta.

Skapa sedan slut punkten genom att trycka på _Spara_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-service-bus.png" alt-text="Skärm bild som visar hur du skapar en slut punkt av typen Service Bus.":::

Du kan kontrol lera att slut punkten har skapats genom att kontrol lera meddelande ikonen i det översta Azure Portal fältet. 

Om det inte går att skapa en slut punkt, Observera fel meddelandet och försök igen om några minuter.

Nu är Service Bus avsnittet tillgängligt som en slut punkt inuti Azure Digital-dubbla, under det namn som anges i fältet _namn_ . Du använder vanligt vis det namnet som mål för en **händelse väg** , som du kommer att skapa [senare i den här artikeln](#create-an-event-route).

### <a name="create-an-endpoint-with-dead-lettering"></a>Skapa en slut punkt med obeställbara meddelanden

När en slut punkt inte kan leverera en händelse inom en viss tids period eller när händelsen försöker leverera händelsen ett visst antal gånger, kan den skicka den ej levererade händelsen till ett lagrings konto. Den här processen kallas för **obeställbara meddelanden**.

Du måste använda [arm-API: erna](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) för att skapa en slut punkt, i stället för Azure Portal, för att kunna skapa en slut punkt där obeställbara meddelanden har Aktiver ATS.

Instruktioner för hur du gör detta med API: erna finns i [*API: erna och CLI*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering) -versionen av den här artikeln.

## <a name="create-an-event-route"></a>Skapa en händelse väg

Om du faktiskt vill skicka data från digitala Azure-sändningar till en slut punkt måste du definiera en **händelse väg**. Med de här vägarna kan utvecklare skapa händelse flöden i systemet och till underordnade tjänster. Läs mer om händelse vägar i [*begrepp: routing Azure Digitals, dubbla händelser*](concepts-route-events.md).

**Förutsättning** : du måste skapa slut punkter enligt beskrivningen ovan i den här artikeln innan du kan gå vidare till skapa en väg. Du kan fortsätta att skapa en händelse väg när slut punkterna har koner ATS.

>[!NOTE]
>Om du nyligen har distribuerat dina slut punkter kontrollerar du att de är klara med distributionen **innan** du försöker använda dem för en ny händelse väg. Om du inte kan konfigurera vägen eftersom slut punkterna inte är klara, väntar du några minuter och försöker igen.

### <a name="creation-steps-with-the-azure-portal"></a>Skapa steg med Azure Portal

En händelse flödes definition innehåller följande element:
* Det väg namn som du vill använda
* Namnet på den slut punkt som du vill använda
* Ett filter som definierar vilka händelser som skickas till slut punkten
    - Om du vill inaktivera vägen så att inga händelser skickas, använder du ett filter värde för `false`
    - Om du vill aktivera en väg som inte har någon speciell filtrering använder du ett filter värde för `true`
    - Mer information om någon annan typ av filter finns i avsnittet [*Filtrera händelser*](#filter-events) nedan.

En enda väg kan tillåta att flera meddelanden och händelse typer väljs.

Om du vill skapa en händelse väg går du till informations sidan för din Azure Digital-instansen i [Azure Portal](https://portal.azure.com) (du kan hitta instansen genom att ange dess namn i portalens Sök fält).

Från menyn instans väljer du _händelse vägar_. Sedan väljer du *+ skapa en händelse väg* från sidan *händelse vägar* som följer. 

På sidan *skapa en händelse väg* som öppnas väljer du minst:
* Ett namn för din väg i fältet _namn_
* _Slut punkten_ som du vill använda för att skapa vägen 

För att vägen ska aktive ras måste du också **lägga till ett händelse flödes filter** på minst `true` . (Om du lämnar standardvärdet för `false` skapas vägen, men inga händelser skickas till den.) Det gör du genom att växla växeln för _avancerad redigerare_ för att aktivera den och skriva `true` i *filter* rutan.

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="Skärm bild som visar hur du skapar en händelse väg för din instans." lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

När du är färdig trycker du på knappen _Spara_ för att skapa din händelse väg.

## <a name="filter-events"></a>Filtrera händelser

Som beskrivs ovan har vägar ett **filter** fält. Om filtervärdet i din väg är `false` , skickas inga händelser till din slut punkt. 

När du har aktiverat det minimala filtret för `true` får slut punkter flera olika händelser från Azures digitala dubbla:
* Telemetri som har utlösts av [digitala sammanflätade](concepts-twins-graph.md) tjänster med Azure Digitals dubbla tjänst-API
* Dubbla egenskaps ändrings meddelanden, utlöses vid egenskaps ändringar för alla dubbla i Azure Digitals-instansen
* Livs cykel händelser, utlöses när de är uppdelade eller relationer skapade eller raderas

Du kan begränsa vilka typer av händelser som skickas genom att definiera ett mer Specific-filter.

Om du vill lägga till ett händelse filter när du skapar en händelse väg använder du avsnittet _Lägg till ett händelse flödes filter_ på sidan *skapa en händelse väg* . 

Du kan antingen välja bland några grundläggande vanliga filter alternativ eller använda avancerade filter alternativ för att skriva egna anpassade filter.

#### <a name="use-the-basic-filters"></a>Använd de grundläggande filtren

Om du vill använda de grundläggande filtren expanderar du alternativet _händelse typer_ och markerar kryss rutorna som motsvarar de händelser som du vill skicka till din slut punkt. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="Skärm bild som visar hur du skapar en händelse väg med ett grundläggande filter. Markera kryss rutorna för händelserna.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Då fylls text rutan filter i automatiskt med texten i det filter du har valt:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="Skärm bild som visar hur du skapar en händelse väg med ett grundläggande filter. Visar den automatiskt ifyllda filter texten när du har valt händelser.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>Använd de avancerade filtren

Alternativt kan du använda alternativet Avancerat filter för att skriva egna anpassade filter.

Om du vill skapa en händelse väg med avancerade filter alternativ växlar du över växeln för _avancerad redigerare_ för att aktivera den. Du kan sedan skriva egna händelse filter i rutan *filter* :

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="Skärm bild som visar hur du skapar en händelse väg med ett avancerat filter.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Här följer de väg filter som stöds. Informationen i kolumnen *filtrera text schema* är den text som kan anges i filter rutan.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Nästa steg

Läs om de olika typerna av händelse meddelanden som du kan ta emot:
* [*Anvisningar: tolka händelse data*](how-to-interpret-event-data.md)
