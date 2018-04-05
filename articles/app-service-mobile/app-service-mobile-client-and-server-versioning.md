---
title: Klient- och SDK-versionshantering i Mobile Apps och Mobile Services | Microsoft Docs
description: 'Lista över klient-SDK: er och kompatibilitet med server SDK-versioner för Mobile Services och Azure Mobile Apps'
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 37bf36af535eb9b5c8b0ba38434b71f1a6686811
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Klienten och servern versionshantering i Mobile Apps och Mobile Services
Den senaste versionen av Azure Mobile Services är den **Mobile Apps** funktion i Azure App Service.

Mobile Apps-SDK för klienten och servern ursprungligen baseras på de i Mobile Services, men de är *inte* kompatibla med varandra.
Det vill säga måste du använda en *Mobilappar* klient-SDK med en *Mobilappar* server-SDK och på samma sätt för *Mobile Services*. Det här kontraktet tillämpas via en särskild huvudvärde som används av klienten och servern SDK: er, `ZUMO-API-VERSION`.

Obs: när det här dokumentet refererar till en *Mobile Services* serverdel inte nödvändigtvis behöver finnas i Mobile Services. Nu är det möjligt att migrera en Mobiltjänst ska köras på Apptjänst utan någon kodändringar, men tjänsten skulle fortfarande använder *Mobile Services* SDK-versioner.

Mer information om hur du migrerar till Apptjänst utan någon kodändringar finns artikeln [migrera en Mobiltjänst till Azure App Service].

## <a name="header-specification"></a>Specifikation för sidhuvud
Nyckeln `ZUMO-API-VERSION` kan anges i HTTP-huvudet eller frågesträngen. Värdet är en versionssträng i formuläret **x.y.z**.

Exempel:

HÄMTA https://service.azurewebsites.net/tables/TodoItem

HUVUDEN: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Väljer bort versionskontroll
Du kan välja bort versionskontroll genom att ange ett värde för **SANT** för appinställningen **MS_SkipVersionCheck**. Ange det i filen web.config eller i avsnittet programinställningar i Azure-portalen.

> [!NOTE]
> Det finns ett antal funktionsändringar mellan Mobile Services och Mobilappar, särskilt i områden som offlinesynkronisering, autentisering och push-meddelanden. Du bör endast välja bort versionskontroll efter slutförd tester för att säkerställa att dessa förändringar i beteendet inte bryter appens funktioner.
>
>

## <a name="summary-of-compatibility-for-all-versions"></a>Sammanfattning av kompatibilitet för alla versioner
Diagrammet nedan visar kompatibiliteten mellan alla typer av klienten och servern. En serverdel klassificeras som antingen Mobile **Services** eller Mobile **appar** baserat på servern SDK som används.

|  | **Mobiltjänster** Node.js eller .NET | **Mobilappar** Node.js eller .NET |
| --- | --- | --- |
| [Klienter för Mobile Services] |OK |Fel\* |
| [Mobile Apps-klienter] |Fel\* |OK |

\*Detta kan kontrolleras genom att ange **MS_SkipVersionCheck**.

<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Mobile Services-klienten och servern
Klient-SDK: er i tabellen nedan är kompatibla med **Mobile Services**.

Obs: Mobile Services klienten SDK *inte* skicka ett värde för `ZUMO-API-VERSION`. Om tjänsten tar emot det här sidhuvudet eller frågesträngsvärdet ett fel returneras om du uttryckligen har valt ut enligt beskrivningen ovan.

### <a name="MobileServicesClients"></a> Mobila *Services* client SDK
| Klientplattform | Version | Värdet för versionshuvudet |
| --- | --- | --- |
| Hanterad klient (Windows, Xamarin) |[1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) |Saknas |
| iOS |[2.2.2](http://aka.ms/gc6fex) |Saknas |
| Android |[2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126) |Saknas |
| HTML |[1.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) |Saknas |

### <a name="mobile-services-server-sdks"></a>Mobila *Services* server SDK
| Server-plattformen | Version | Godkänd version-huvud |
| --- | --- | --- |
| .NET |[WindowsAzure.MobileServices.Backend.* Version 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) |**No version header ** |
| Node.js |(kommer snart) |**Ingen version-huvud** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Beteendet för Mobile Services serverdelar
| ZUMO-API-VERSION | Värdet för MS_SkipVersionCheck | Svar |
| --- | --- | --- |
| Ej specificerat |Alla |200 - OK |
| Inget värde |True |200 - OK |
| Inget värde |FALSKT/inte angetts |400 – Felaktig begäran |

## <a name="2.0.0"></a>Azure Mobile Apps klient och server
### <a name="MobileAppsClients"></a> Mobila *appar* client SDK
Versionskontroll introducerades börjar med följande versioner av klient-SDK för **Azure Mobile Apps**:

| Klientplattform | Version | Värdet för versionshuvudet |
| --- | --- | --- |
| Hanterad klient (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>Mobila *appar* server SDK
Versionskontroll ingår i följande versioner av server-SDK:

| Server-plattformen | SDK | Godkänd version-huvud |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Beteendet för serverdelar för Mobilappar
| ZUMO-API-VERSION | Värdet för MS_SkipVersionCheck | Svar |
| --- | --- | --- |
| x.y.z eller Null |True |200 - OK |
| Null |FALSKT/inte angetts |400 – Felaktig begäran |
| 1.x.y |FALSKT/inte angetts |400 – Felaktig begäran |
| 2.0.0-2.x.y |FALSKT/inte angetts |200 - OK |
| 3.0.0-3.x.y |FALSKT/inte angetts |400 – Felaktig begäran |

## <a name="next-steps"></a>Nästa steg
* [migrera en Mobiltjänst till Azure App Service]

[Klienter för Mobile Services]: #MobileServicesClients
[Mobile Apps-klienter]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[migrera en Mobiltjänst till Azure App Service]: app-service-mobile-migrating-from-mobile-services.md
