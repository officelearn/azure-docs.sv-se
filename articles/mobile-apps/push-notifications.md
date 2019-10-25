---
title: Vikten av push-meddelanden i dina mobilappar med Visual Studio App Center och Azure Notification Hubs
description: Lär dig mer om tjänsterna, till exempel App Center som gör att du kan kommunicera med dina mobil program användare.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 05a9bc6ccd26aaecd3e0bf615880e0543b8604f7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795918"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Engagera dig med dina program användare genom att skicka push-meddelanden 

Oavsett om du skapar konsument-eller företags program, vill att användarna ska kunna använda programmet aktivt. Det finns ofta tillfällen när du vill dela med dig av de senaste nyheterna, spel uppdateringar, spännande erbjudanden, produkt uppdatering eller relevant information med dina användare. För att öka användarnas engagemang och fortsätta att få tillbaka dem till ditt program, behöver du ett sätt för att kommunicera med dina användare i real tid.

Push-meddelanden ger ett snabbt och effektivt sätt att kommunicera med dina program användare. Du kan kontakta dina användare vid rätt tidpunkt och kan meddela dem om önskad information, vanligt vis i ett popup-fönster eller en dialog ruta på en mobil enhet, oavsett vad de gör.

## <a name="value-of-push-notifications"></a>Värde för push-meddelanden
Push-meddelanden tillhandahåller värde för både slutanvändare och företaget.

Företag kan:
- **Kommunicera direkt med användarna** genom att skicka meddelanden till den plattform som stöds vid rätt tidpunkt.
- **Förbättra** användarnas engagemang genom att skicka real tids uppdateringar och påminnelser till dina användare, så att de kan samar beta med ditt program med jämna mellanrum.
- **Behåll användare** och återanslut med inaktiva användare som har laddat ned programmet men Använd det inte för att bli aktivt igen.
- **Öka konverterings takten** genom att analysera slutanvändarens beteende, segmentera slutanvändare och använda kampanjer baserade på mobila push-meddelanden.
- **Marknadsför produkter och tjänster** genom att skicka push-meddelanden och uppdatera dem i rätt tid till användarna.
- **Rikta in användarna** genom att skicka dem till personliga push-meddelanden.

Push-meddelanden för program användare:
- **Ange värde och information** i real tid.
- **Påminn användarna** om att använda programmet.

Använd följande tjänster för att aktivera push-meddelanden i dina mobila appar.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Med [App Center push](/appcenter/push/) -tjänsten kan du interagera med dina användare genom att skicka dem riktade meddelanden till iOS-, Android-och Windows-användare utan att behöva hantera processen för att skicka meddelanden till enheter med Push-Notification Services (PNS). Den här tjänsten är byggd på Azure-Notification Hubs och eliminerar de komplexa funktioner som är kopplade till att skicka meddelanden manuellt genom att tillhandahålla en kraftfull instrument panel.

**Viktiga funktioner**
- **Skicka push-meddelanden till mobila enheter** på olika plattformar.
- Använd meddelanden för att skicka data till ett program, Visa ett meddelande till användaren eller utlösa en åtgärd i programmet.
- Meddelande mål: 
    - Skicka Broadcast-meddelanden till **alla registrerade enheter**.
    - Skicka meddelanden till **mål grupper** som skapats baserat på enhets information och anpassade egenskaper.
    - Skicka meddelanden till **vissa användare**.
    - Skicka meddelanden till **vissa enheter**.
- **Avancerad telemetri** om push, enhet och fel finns i App Center-portalen.
- **Plattforms stöd** – iOS, Android, MacOS, Xamarin, reakta Native, Unity, Cordova.

**Reference**
- [Registrera dig med App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Kom igång med App Center push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
[Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) tillhandahålla en lättanvänd och skalad push-motor som gör att du kan skicka meddelanden till valfri plattform och från valfri Server del (moln eller lokalt).

**Viktiga funktioner**
- **Skicka push-meddelanden** till valfri plattform från valfri Server del, i molnet eller lokalt.
- **Snabb sändnings** -push till miljon tals mobila enheter med ett enda API-anrop.
- Skräddarsy push-meddelanden efter kund, språk och plats.
- Definiera och meddela **kund segment**dynamiskt.
- **Skala direkt** till miljon tals mobila enheter.
- **Plattforms stöd** – iOS, Android, Windows, Kindle, Baidu.
        
**Reference**
- [Azure-portalen](https://portal.azure.com) 
- [Kom igång med Azure Notification Hubs](/azure/notification-hubs/)   
- [Snabbstarter](/azure/notification-hubs/create-notification-hub-portal)
- [Exempel](/azure/notification-hubs/samples)
