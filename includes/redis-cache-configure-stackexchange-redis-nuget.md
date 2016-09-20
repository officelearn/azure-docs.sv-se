.NET-program kan använda cacheklienten **StackExchange.Redis**. Den kan konfigureras i Visual Studio med ett NuGet-paket som förenklar konfigurationen av cacheklientprogram. 

>[AZURE.NOTE] Mer information finns på GitHub-sidan [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) och i [Cacheklientdokumentation för StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis#documentation).

Om du vill konfigurera ett klientprogram i Visual Studio med hjälp av NuGet-paketet för StackExchange.Redis, högerklickar du på projektet i **Solution Explorer** och väljer **Hantera NuGet-paket**. 

![Hantera NuGet-paket](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Skriv **StackExchange.Redis** eller **StackExchange.Redis.StrongName** i sökrutan, markera den önskade versionen i resultaten och klicka på **Installera**.

>[AZURE.NOTE] Om du vill använda en starkt krypterad version av klientbiblioteket **StackExchange.Redis** väljer du **StackExchange.Redis.StrongName**, annars väljer du **StackExchange.Redis**.

![NuGet-paket för StackExchange.Redis](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

NuGet-paketet hämtar och lägger till de nödvändiga sammansättningsreferenserna för klientprogrammet för att få åtkomst till Azure Redis Cache med cacheklienten StackExchange.Redis.

>[AZURE.NOTE] Om du tidigare har konfigurerat ditt projekt till att använda StackExchange.Redis kan du söka efter uppdateringar till paketet från **NuGet Package Manager**. Om du vill söka efter och installera uppdaterade versioner av paketet StackExchange.Redis NuGet, klickar du på **Uppdateringar** i fönstret **NuGet Package Manager**. Om det finns en uppdatering av paketet StackExchange.Redis NuGet kan du uppdatera ditt projekt för att använda den uppdaterade versionen.




<!--HONumber=sep16_HO1-->


