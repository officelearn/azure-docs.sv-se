---
title: Vikten av push-meddelanden i dina mobilappar med Visual Studio App Center och Azure Notification Hubs
description: Lär dig mer om tjänster som Visual Studio App Center som du kan använda för att interagera med dina användare av mobilappar.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: bc59857cdb843dfff54e69f2226f13cfe70df8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235336"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Interagera med dina programanvändare genom att skicka push-meddelanden

Oavsett om du bygger konsument- eller företagsprogram vill du att användarna ska använda programmet aktivt. Det finns ofta tillfällen när du vill dela senaste nytt, speluppdateringar, spännande erbjudanden, produktuppdateringar eller annan relevant information med användarna. För att öka engagemanget med användarna och få dem att återgå till ditt program behöver du ett sätt att kommunicera med användarna i realtid.

Push-meddelanden är ett snabbt och effektivt sätt att kommunicera med dina programanvändare. Du kan nå ut till användarna vid rätt tidpunkt och meddela dem om önskad information, vanligtvis i ett popup-objekt eller en dialogruta på en mobil enhet, oavsett vad de gör.

## <a name="value-of-push-notifications"></a>Värdet av push-meddelanden
Push-meddelanden ger värde till användare och företag.

Företag kan:
- Kommunicera direkt med användare genom att skicka meddelanden på plattformar som stöds vid rätt tidpunkt.
- Öka användarnas engagemang genom att skicka uppdateringar och påminnelser i realtid till användarna, uppmuntra dem att engagera sig i programmet regelbundet.
- Behåll användare och återengagering med inaktiva användare som hämtat programmet men inte använder det för att bli aktiv igen.
- Öka konverteringsfrekvensen genom att analysera användarbeteende, segmentera användare och utnyttja kampanjer baserat på mobila push-meddelanden.
- Marknadsför produkter och tjänster genom att skicka push-meddelanden och uppdateringar i tid till användarna.
- Rikta in användarna genom att skicka anpassade push-meddelanden.

För programanvändare, push-meddelanden:
- Ge värde och information i realtid.
- Påminn användarna om att använda programmet.

Använd följande tjänster för att aktivera push-meddelanden i dina mobilappar.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Med [App Center Push](/appcenter/push/)kan du skicka riktade meddelanden till iOS-, Android- och Windows-användare utan att behöva hantera processen med att skicka meddelanden till enheter med hjälp av push-meddelandetjänster (PNS). Den här tjänsten bygger på Azure Notification Hubs och eliminerar komplexiteter som är förknippade med att skicka meddelanden manuellt genom att tillhandahålla en kraftfull instrumentpanel.

**Huvudfunktioner**
- Skicka push-meddelanden till mobila enheter på en mängd olika plattformar.
- Använd meddelanden för att skicka data till ett program, visa ett meddelande till användaren eller utlösa en åtgärd av programmet.
- Använd meddelandemål för att: 
    - Skicka meddelanden till alla registrerade enheter.
    - Skicka aviseringar till målgrupper baserat på enhetsinformation och anpassade egenskaper.
    - Skicka meddelanden till specifika användare.
    - Skicka meddelanden till specifika enheter.
- Använd den omfattande telemetrin på pushes, enheter och fel som är tillgängliga i App Center-portalen.
- Få plattformsstöd för iOS, Android, macOS, Xamarin, React Native, Unity och Cordova.

**Referenser**
- [Registrera dig med Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Komma igång med App Center Push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
[Meddelandehubbar](/azure/notification-hubs/notification-hubs-push-notification-overview) ger en lättanvänd och utskalad push-motor. Du kan använda den för att skicka meddelanden till valfri plattform och från vilken backdel som helst i molnet eller lokalt.

**Huvudfunktioner**
- Skicka push-meddelanden till valfri plattform från vilken backdel som helst, i molnet eller lokalt.
- Snabb broadcast push till miljontals mobila enheter med ett enda API-samtal.
- Skräddarsy push-meddelanden efter kund, språk och plats.
- Definiera och meddela kundsegment dynamiskt.
- Skala direkt till miljontals mobila enheter.
- Få plattformsstöd för iOS, Android, Windows, Kindle och Baidu.
        
**Referenser**
- [Azure-portal](https://portal.azure.com) 
- [Komma igång med Azure Notification Hubs](/azure/notification-hubs/)
- [Snabbstarter](/azure/notification-hubs/create-notification-hub-portal)
- [Prover](/azure/notification-hubs/samples)
