---
title: Vikten av push-meddelanden i dina mobilappar med Visual Studio App Center och Azure Notification Hubs
description: Lär dig mer om tjänster som Visual Studio App Center som du kan använda för att kommunicera med dina mobil program användare.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/05/2020
ms.author: masoucou
ms.openlocfilehash: 515583a19c576d25bd9573cbf80974d72c6df1ec
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2020
ms.locfileid: "84482648"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Engagera dig med dina program användare genom att skicka push-meddelanden

Oavsett om du skapar konsument-eller företags program, vill att användarna ska kunna använda programmet aktivt. Det finns ofta tillfällen när du vill dela med dig av de senaste nyheterna, spel uppdateringar, spännande erbjudanden, produkt uppdateringar eller annan relevant information med dina användare. Du behöver ett sätt att kommunicera med dina användare i real tid för att öka användarnas engagemang och få dem att återgå till ditt program.

Push-meddelanden ger ett snabbt och effektivt sätt att kommunicera med dina program användare. Du kan kontakta dina användare vid rätt tidpunkt och meddela dem om önskad information, vanligt vis i ett popup-objekt eller en dialog ruta på en mobil enhet, oavsett vad de gör.

## <a name="value-of-push-notifications"></a>Värde för push-meddelanden
Push-meddelanden ger användare och företag värde.

Företag kan:
- Kommunicera direkt med användarna genom att skicka meddelanden på plattformar som stöds vid rätt tidpunkt.
- Förbättra användarnas engagemang genom att skicka real tids uppdateringar och påminnelser till användare, så att de kan samar beta med programmet med jämna mellanrum.
- Behåll användare och engagera med inaktiva användare som laddade ned programmet men Använd det inte för att bli aktivt igen.
- Öka konverterings takten genom att analysera användar beteende, segmentera användare och använda kampanjer baserade på mobila push-meddelanden.
- Marknadsför produkter och tjänster genom att skicka push-meddelanden och uppdatera dem vid ett senare tillfälle.
- Rikta användare genom att skicka personliga push-meddelanden.

Push-meddelanden för program användare:
- Ange värde och information i real tid.
- Påminn användarna om att använda programmet.

Använd följande tjänster för att aktivera push-meddelanden i dina mobila appar.

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
[Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) tillhandahåller en lättanvänd och skalad push-motor. Du kan använda den för att skicka meddelanden till valfri plattform och från valfri Server del i molnet eller lokalt.

**Huvudfunktioner**
- Skicka push-meddelanden till valfri plattform från valfri Server del, i molnet eller lokalt.
- Snabb sändnings-push till miljon tals mobila enheter med ett enda API-anrop.
- Skräddarsy push-meddelanden efter kund, språk och plats.
- Definiera och meddela kund segment dynamiskt.
- Skala direkt till miljon tals mobila enheter.
- Få plattforms stöd för iOS, Android, Windows, Kindle och Baidu.
        
**Referenser**
- [Azure Portal](https://portal.azure.com) 
- [Kom igång med Azure Notification Hubs](/azure/notification-hubs/)
- [Snabbstarter](/azure/notification-hubs/create-notification-hub-portal)
- [Exempel](/azure/notification-hubs/samples)
