---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 8ebf5ddfa118e0aeadeab0c00a981871a4b5708e
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53430051"
---
.NET-program kan använda cacheklienten **StackExchange.Redis**. Den kan konfigureras i Visual Studio med ett NuGet-paket som förenklar konfigurationen av cacheklientprogram. 

> [!NOTE]
> Mer information finns i den [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) GitHub-sidan och [StackExchange.Azure Cache för dokumentationen för Redis-klient](http://github.com/StackExchange/StackExchange.Redis#documentation).
>
>

Om du vill konfigurera ett klientprogram i Visual Studio med hjälp av NuGet-paketet för StackExchange.Redis, högerklickar du på projektet i **Solution Explorer** och väljer **Hantera NuGet-paket**. 

![Hantera NuGet-paket](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Skriv **StackExchange.Redis** eller **StackExchange.Redis.StrongName** i sökrutan, markera den önskade versionen i resultaten och klicka på **Installera**.

> [!NOTE]
> Om du vill använda en starkt krypterad version av klientbiblioteket **StackExchange.Redis** väljer du **StackExchange.Redis.StrongName**, annars väljer du **StackExchange.Redis**.
>
>

![NuGet-paket för StackExchange.Redis](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

NuGet-paketet hämtar och lägger till de nödvändiga sammansättningsreferenserna för klientprogrammet att få åtkomst till Azure Cache för Redis med StackExchange.Azure Cache för Redis-klient.

> [!NOTE]
> Om du tidigare har konfigurerat ditt projekt till att använda StackExchange.Redis kan du söka efter uppdateringar till paketet från **NuGet Package Manager**. Om du vill söka efter och installera uppdaterade versioner av paketet StackExchange.Redis NuGet, klickar du på **uppdateringar** i den **NuGet-Pakethanteraren** fönster. Om det finns en uppdatering av paketet StackExchange.Redis NuGet kan du uppdatera ditt projekt för att använda den uppdaterade versionen.
>
>

Du kan också installera StackExchange.Redis NuGet-paketet genom att klicka på **NuGet Package Manager**, klicka på **Package Manager Console** på **Tools**-menyn och köra följande kommando från fönstret **Package Manager-konsol**.

```
Install-Package StackExchange.Redis
```
