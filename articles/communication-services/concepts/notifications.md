---
title: Meddelanden i Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Skicka meddelanden till användare av appar som bygger på Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: abc2367c309f46ee1b29a51145c67e8d71919774
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665403"
---
# <a name="communication-services-notifications"></a>Meddelanden om kommunikations tjänster

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure Communication Services chatt och anropar klient bibliotek skapar en meddelande kanal i real tid som gör det möjligt att skicka meddelanden till anslutna klienter på ett effektivt och tillförlitligt sätt. På så sätt kan du bygga avancerade funktioner i real tid i dina program utan att behöva implementera logik för komplicerad HTTP-avsökning. Men i mobila program förblir den här signal kanalen bara ansluten när ditt program är aktivt i förgrunden. Om du vill att användarna ska kunna ta emot inkommande samtal eller chatta när ditt program är i bakgrunden, bör du använda push-meddelanden.

Med push-meddelanden kan du skicka information från ditt program till användarnas mobila enheter. Du kan använda push-meddelanden för att visa en dialog ruta, spela upp ett ljud eller visa inkommande samtals gränssnitt. Azure Communication Services ger integration med [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) och [Azure-Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) som gör att du kan lägga till push-meddelanden till dina appar.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Utlös push-meddelanden via Azure Event Grid

Azure Communication Services integreras med [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) för att leverera real tids händelse meddelanden på ett tillförlitligt, skalbart och säkert sätt. Du kan utnyttja den här integrationen för att skapa en meddelande tjänst som levererar mobila push-meddelanden till användarna genom att skapa en Event Grid-prenumeration som utlöser en [Azure Function](https://docs.microsoft.com/azure/azure-functions/functions-overview) -eller webhook.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Diagram över hur kommunikations tjänster integreras med Event Grid.":::

Lär dig mer om [händelse hantering i Azure Communication Services](./event-handling.md).

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Leverera push-meddelanden via Azure Notification Hubs

Du kan ansluta en Azure Notification Hub till kommunikations tjänst resursen för att automatiskt skicka push-meddelanden till en användares mobila enhet när de får ett inkommande samtal. Du bör använda dessa push-meddelanden för att väcka ditt program från bakgrunden och visnings gränssnittet som gör det möjligt för användaren att godkänna eller avböja anropet. 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Diagram över hur kommunikations tjänster integreras med Event Grid.":::

Kommunikations tjänster använder Azure Notification Hub som en direkt tjänst för att kommunicera med de olika plattformsspecifika Push Notification-tjänsterna med hjälp av [direkt sändnings](https://docs.microsoft.com/rest/api/notificationhubs/direct-send) -API: et. På så sätt kan du återanvända dina befintliga Azure Notification Hub-resurser och-konfigurationer för att leverera låg latens, tillförlitliga anrops aviseringar till dina program.

### <a name="notification-hub-provisioning"></a>Etablering av Notification Hub 

Om du vill skicka push-meddelanden till klient enheter med hjälp av Notification Hubs [skapar du en Notification Hub](https://docs.microsoft.com/azure/notification-hubs/create-notification-hub-portal) i samma prenumeration som kommunikations tjänst resursen. Azure Notification Hubs måste konfigureras för den plattforms meddelande tjänst som du vill använda. Information om hur du hämtar push-meddelanden i din klient app från Notification Hubs finns i [komma igång med Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/ios-sdk-get-started) och välj mål klient plattform i list rutan längst upp på sidan.

När Notification Hub har kon figurer ATS kan du koppla den till din kommunikations tjänst resurs genom att tillhandahålla en anslutnings sträng för hubben med hjälp av Azure Resource Manager klienten eller via Azure Portal. Anslutnings strängen ska innehålla behörigheter för "Send". Vi rekommenderar att du skapar en annan åtkomst princip med endast behörigheterna "Skicka" specifikt för hubben. Läs mer om [Notification Hubs säkerhets-och åtkomst principer](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-security)

> Obs: om du vill aktivera Apple Push Notification Service VOIP-meddelanden måste du ange namnet på din Notification Hub som programpaket-ID med `.voip` suffixet. Se [använda APN VoIP via Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/voip-apns).

#### <a name="using-the-azure-resource-manager-client-to-configure-the-notification-hub"></a>Konfigurera Notification Hub med hjälp av Azure Resource Manager-klienten

Logga in på Azure Resource Manager genom att köra följande och logga in med dina autentiseringsuppgifter.

```console
armclient login
```

 När du har loggat in kör du följande för att etablera Notification Hub:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-configure-the-notification-hub"></a>Konfigurera Notification Hub med hjälp av Azure Portal

I portalen navigerar du till din Azure Communication Services-resurs. I resursen kommunikations tjänster väljer du push-meddelanden på den vänstra menyn på sidan kommunikations tjänster och ansluter den meddelande hubb som du etablerade tidigare. Du måste ange anslutnings strängen och resurs-ID: t här:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Diagram över hur kommunikations tjänster integreras med Event Grid.":::

#### <a name="device-registration"></a>Enhetsregistrering 

Se [röst samtals snabb start](../quickstarts/voice-video-calling/getting-started-with-calling.md) för att lära dig hur du registrerar din enhets hantering med kommunikations tjänster.

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är event Grid?](https://docs.microsoft.com/azure/event-grid/overview)
* Mer information om Azure Notification Hub-koncepten finns i [azure Notification Hubs-dokumentationen](https://docs.microsoft.com/azure/notification-hubs/)
