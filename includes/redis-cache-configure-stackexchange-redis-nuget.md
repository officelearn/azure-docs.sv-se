---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 570ed080e208a890bec867b665c4344c502e4d71
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53020216"
---
.NET-program kan använda cacheklienten **StackExchange.Redis**. Den kan konfigureras i Visual Studio med ett NuGet-paket som förenklar konfigurationen av cacheklientprogram. 

> [!NOTE]
> Mer information finns i den [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) github-sidan och [StackExchange.Azure Cache för dokumentationen för Redis-klient](http://github.com/StackExchange/StackExchange.Redis#documentation).
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

NuGet-paketet hämtar och lägger till de nödvändiga sammansättningsreferenserna för klientprogrammet att komma åt Azure Azure Cache för Redis med StackExchange.Azure Cache för Redis-klient.

> [!NOTE]
> Om du tidigare har konfigurerat ditt projekt till att använda StackExchange.Redis kan du söka efter uppdateringar till paketet från **NuGet Package Manager**. Om du vill söka efter och installera uppdaterade versioner av paketet StackExchange.Redis NuGet, klickar du på **uppdateringar** i den **NuGet-Pakethanteraren** fönster. Om det finns en uppdatering av paketet StackExchange.Redis NuGet kan du uppdatera ditt projekt för att använda den uppdaterade versionen.
> 
> 

Du kan också installera StackExchange.Redis NuGet-paketet genom att klicka på **NuGet Package Manager**, klicka på **Package Manager Console** på **Tools**-menyn och köra följande kommando från fönstret **Package Manager-konsol**.
    
```
Install-Package StackExchange.Redis
```
