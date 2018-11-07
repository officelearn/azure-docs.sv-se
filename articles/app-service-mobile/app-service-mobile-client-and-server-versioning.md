---
title: Versionshantering för klienter och server SDK i Mobile Apps och Mobile Services | Microsoft Docs
description: 'Lista över klient-SDK: er och kompatibilitet med server SDK-versioner för mobiltjänster och Azure Mobile Apps'
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
ms.openlocfilehash: 1f5626b1001f2d5deb3fa84d64eab6acf6c94b7e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239533"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Versionshantering för klienter och server i Mobile Apps och Mobile Services
Den senaste versionen av Azure mobiltjänster är den **Mobile Apps** funktionen i Azure App Service.

Mobile Apps klient- och SDK: erna är ursprungligen baserat på vilka i mobiltjänster, men de är *inte* kompatibla med varandra.
Det vill säga måste du använda en *Mobile Apps* klient-SDK med en *Mobile Apps* SDK-server och på liknande sätt för *mobiltjänster*. Det här kontraktet tillämpas via en särskild huvudvärde som används av klienten och servern SDK: er, `ZUMO-API-VERSION`.

Obs: när det här dokumentet avser en *mobiltjänster* serverdelen, inte nödvändigtvis behöver finnas på mobiltjänster. Nu är det möjligt att migrera en mobil tjänst ska köras på App Service utan kodändringar, men tjänsten skulle fortfarande använda *mobiltjänster* SDK-versioner.

Mer information om hur du migrerar till App Service utan kodändringar, finns i artikeln [Migrera en mobil tjänst till Azure App Service].

## <a name="header-specification"></a>Huvud-specifikation
Nyckeln `ZUMO-API-VERSION` kan anges i HTTP-rubriken eller frågesträngen. Värdet är en versionssträng i formuläret **x.y.z**.

Exempel:

HÄMTA https://service.azurewebsites.net/tables/TodoItem

RUBRIKER: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Väljer bort versionskontroll
Du kan välja bort versionskontroll genom att ange ett värde av **SANT** för appinställningen **MS_SkipVersionCheck**. Ange detta i din web.config eller i avsnittet Inställningar för program i Azure-portalen.

> [!NOTE]
> Det finns ett antal beteendeförändringar mellan mobiltjänster och Mobile Apps, särskilt i områden som offline-synkronisering, autentisering och push-meddelanden. Du bör endast välja bort versionskontroll när du slutför testning för att säkerställa att ändringarna beteendeanalys inte skadar appens funktioner.
>
>

## <a name="summary-of-compatibility-for-all-versions"></a>Sammanfattning av kompatibilitet för alla versioner
Diagrammet nedan visar kompatibiliteten mellan alla typer av klienten och servern. En serverdel klassificeras som antingen Mobile **Services** eller Mobile **appar** baserat på servern SDK som används.

|  | **Mobila tjänster** Node.js- eller .NET | **Mobilappar** Node.js- eller .NET |
| --- | --- | --- |
| [Klienter för mobiltjänster] |OK |Fel\* |
| [Mobile Apps-klienter] |Fel\* |OK |

\*Detta kan kontrolleras genom att ange **MS_SkipVersionCheck**.

<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Mobile Services-klient och server
Klient-SDK: er i tabellen nedan är kompatibla med **mobiltjänster**.

Obs: Mobile Services-klient SDK: er *inte* skicka ett huvudvärde för `ZUMO-API-VERSION`. Om tjänsten tar emot den här rubriken eller frågesträngsvärdet kommer att returneras ett fel om du uttryckligen har valt ut enligt beskrivningen ovan.

### <a name="MobileServicesClients"></a> Mobile *Services* klient-SDK: er
| Klientplattform | Version | Värdet för versionshuvudet |
| --- | --- | --- |
| Hanterad klient (Windows, Xamarin) |[1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) |Saknas |
| iOS |[2.2.2](https://aka.ms/gc6fex) |Saknas |
| Android |[2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126) |Saknas |
| HTML |[1.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) |Saknas |

### <a name="mobile-services-server-sdks"></a>Mobile *Services* server SDK: er
| Serverplattform | Version | Godkänd version-huvud |
| --- | --- | --- |
| .NET |[WindowsAzure.MobileServices.Backend.* Version 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) |** Inga versionshuvudet ** |
| Node.js |(kommer snart) |**Inga versionshuvudet** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Beteendet för serverdelar för mobiltjänster
| ZUMO-API-VERSION | Värdet för MS_SkipVersionCheck | Svar |
| --- | --- | --- |
| Ej specificerat |Alla |200 - OK |
| Ett värde |True |200 - OK |
| Ett värde |FALSKT/inte angetts |400 - Felaktig begäran |

## <a name="2.0.0"></a>Azure Mobile Apps-klient och server
### <a name="MobileAppsClients"></a> Mobile *appar* klient-SDK: er
Versionskontroll introducerades från och med följande versioner av klient-SDK för **Azure Mobile Apps**:

| Klientplattform | Version | Värdet för versionshuvudet |
| --- | --- | --- |
| Hanterad klient (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>Mobile *appar* server SDK: er
Versionskontroll ingår i följande server SDK-versioner:

| Serverplattform | SDK | Godkänd version-huvud |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Beteendet för Mobile Apps-serverdelen
| ZUMO-API-VERSION | Värdet för MS_SkipVersionCheck | Svar |
| --- | --- | --- |
| x.y.z eller Null |True |200 - OK |
| Null |FALSKT/inte angetts |400 - Felaktig begäran |
| 1.x.y |FALSKT/inte angetts |400 - Felaktig begäran |
| 2.0.0-2.x.y |FALSKT/inte angetts |200 - OK |
| 3.0.0-3.x.y |FALSKT/inte angetts |400 - Felaktig begäran |

## <a name="next-steps"></a>Nästa steg
* [Migrera en mobil tjänst till Azure App Service]

[Klienter för mobiltjänster]: #MobileServicesClients
[Mobile Apps-klienter]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Migrera en mobil tjänst till Azure App Service]: app-service-mobile-migrating-from-mobile-services.md
