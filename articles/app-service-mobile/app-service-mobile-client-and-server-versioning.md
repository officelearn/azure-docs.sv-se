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
ms.openlocfilehash: cfa6a363725c35083b32d6de1dd1371777f91907
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240290"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Versionshantering för klienter och server i Mobile Apps och Mobile Services
Den senaste versionen av Azure mobiltjänster är den **Mobile Apps** funktionen i Azure App Service.

Mobile Apps klient- och SDK: erna är ursprungligen baserat på vilka i mobiltjänster, men de är *inte* kompatibla med varandra.
Det vill säga måste du använda en *Mobile Apps* klient-SDK med en *Mobile Apps* SDK-server och på liknande sätt för *mobiltjänster*. Det här kontraktet tillämpas via en särskild huvudvärde som används av klienten och servern SDK: er, `ZUMO-API-VERSION`.

Obs: när det här dokumentet avser en *mobiltjänster* serverdelen, inte nödvändigtvis behöver finnas på mobiltjänster. Nu är det möjligt att migrera en mobil tjänst ska köras på App Service utan kodändringar, men tjänsten skulle fortfarande använda *mobiltjänster* SDK-versioner.

Mer information om hur du migrerar till App Service utan kodändringar, finns i artikeln [migrera en Mobiltjänst till Azure App Service].

## <a name="header-specification"></a>Huvud-specifikation
Nyckeln `ZUMO-API-VERSION` kan anges i HTTP-rubriken eller frågesträngen. Värdet är en versionssträng i formuläret **x.y.z**.

Exempel:

GET https://service.azurewebsites.net/tables/TodoItem

RUBRIKER: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Väljer bort versionskontroll
Du kan välja bort versionskontroll genom att ange ett värde av **SANT** för appinställningen **MS_SkipVersionCheck**. Ange detta i din web.config eller i avsnittet Inställningar för program i Azure-portalen.

> [!NOTE]
> Det finns ett antal beteendeförändringar mellan mobiltjänster och Mobile Apps, särskilt i områden som offline-synkronisering, autentisering och push-meddelanden. Du bör endast välja bort versionskontroll när du slutför testning för att säkerställa att ändringarna beteendeanalys inte skadar appens funktioner.

## <a name="2.0.0"></a>Azure Mobile Apps-klient och server
### <a name="MobileAppsClients"></a> Mobile *appar* klient-SDK: er
Versionskontroll introducerades från och med följande versioner av klient-SDK för **Azure Mobile Apps**:

| Klientplattform | Version | Värdet för versionshuvudet |
| --- | --- | --- |
| Hanterad klient (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

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

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
