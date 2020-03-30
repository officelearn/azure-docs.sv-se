---
title: Klient- och server-SDK-versionshantering
description: Lista över klient-SDK:er och kompatibilitet med server-SDK-versioner för mobila tjänster och Azure Mobile Apps.
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: a9ba442c00ec2498139ee34a1ff7497c98f17ede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293490"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Klient- och serverversionering i mobilappar och mobila tjänster

Den senaste versionen av Azure Mobile Services är funktionen **Mobilappar i** Azure App Service.

Mobile Apps-klienten och serverns SDK:er baseras ursprungligen på dem i Mobila tjänster, men de är *inte* kompatibla med varandra.
Det vill säga, du måste använda en *Mobile Apps klient* SDK med en Mobile *Apps* server SDK och på samma sätt för *mobila tjänster*. Det här kontraktet tillämpas genom ett särskilt huvudvärde som används `ZUMO-API-VERSION`av klient- och server-SDK:erna.

När det här dokumentet refererar till en *mobile services-backend* behöver det inte nödvändigtvis finnas på mobila tjänster. Det är nu möjligt att migrera en mobil tjänst för att köras på App Service utan några kodändringar, men tjänsten skulle fortfarande använda *Mobile Services* SDK-versioner.

## <a name="header-specification"></a>Huvudspecifikation
Nyckeln `ZUMO-API-VERSION` kan anges i HTTP-huvudet eller frågesträngen. Värdet är en versionssträng i formuläret **x.y.z**.

Ett exempel:

`GET https://service.azurewebsites.net/tables/TodoItem`

RUBRIKER: ZUMO-API-VERSION: 2.0.0

`POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0`

## <a name="opting-out-of-version-checking"></a>Välja bort versionskontroll
Du kan välja bort versionskontrollen genom att ange värdet **sant** för appinställningen **MS_SkipVersionCheck**. Ange detta antingen i din web.config eller i avsnittet Programinställningar i Azure-portalen.

> [!NOTE]
> Det finns ett antal beteendeändringar mellan mobila tjänster och mobilappar, särskilt när det gäller offlinesynkronisering, autentisering och push-meddelanden. Du bör bara välja bort versionskontroll efter fullständig testning för att säkerställa att dessa beteendeförändringar inte bryter appens funktioner.

## <a name="azure-mobile-apps-client-and-server"></a><a name="2.0.0"></a>Azure Mobile Apps klient och server
### <a name="mobile-apps-client-sdks"></a><a name="MobileAppsClients"></a>SDK:er för mobile *apps-klient*
Versionskontrollen introducerades med början i följande versioner av klienten SDK för **Azure Mobile Apps:**

| Klient plattform | Version | Värdet för versionshuvudet |
| --- | --- | --- |
| Hanterad klient (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>SDK:er för *mobilappar*
Versionskontroll ingår i följande server-SDK-versioner:

| Serverplattform | SDK | Godkänt versionshuvud |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobilappar)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Beteende av mobile apps backends
| ZUMO-API-VERSION | Värdet på MS_SkipVersionCheck | Svar |
| --- | --- | --- |
| x.y.z eller Null |True |200 - OK |
| Null |Falskt/har inte angetts |400 – felaktig begäran |
| 1.x.y |Falskt/har inte angetts |400 – felaktig begäran |
| 2.0.0-2.x.y |Falskt/har inte angetts |200 - OK |
| 3.0.0-3.x.y |Falskt/har inte angetts |400 – felaktig begäran |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
