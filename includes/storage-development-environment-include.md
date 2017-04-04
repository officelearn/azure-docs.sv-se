## <a name="set-up-your-development-environment"></a>Ställ in din utvecklingsmiljö
Konfigurera sedan din utvecklingsmiljö i Visual Studio så att du är redo att testa kodexemplen i den här guiden.

### <a name="create-a-windows-console-application-project"></a>Skapa ett Windows-konsolprogramprojekt
Skapa ett nytt Windows-konsolprogram i Visual Studio. Följande steg beskriver hur du skapar ett konsolprogram i Visual Studio 2017, men stegen är liknande i andra versioner av Visual Studio.

1. Välj **Arkiv** > **Nytt** > **Projekt**
2. Välj **Installerat** > **Mallar** > **Visual C#** > **Windows Classic Desktop**
3. Välj **Konsolprogram (.NET Framework)**
4. Ange ett namn för ditt program i fältet **Namn**
5. Välj **OK**

![Dialogruta för att skapa projekt i Visual Studio](./media/storage-development-environment-include/storage-development-environment-include-1.png)

Alla kodexempel i den här självstudiekursen kan läggas till i `Main()`-metoden i konsolprogrammets `Program.cs`-fil.

Du kan använda Azure Storage-klientbiblioteket i alla typer av .NET-program, t.ex. en Azure-molntjänst eller webbapp, eller i dator- och mobilprogram. I den här guiden använder vi oss av en konsolapp för enkelhetens skull.

### <a name="use-nuget-to-install-the-required-packages"></a>Använd NuGet för att installera de paket som behövs
Det finns två paket som du måste referera till i ditt projekt för att slutföra den här kursen:

* [Microsoft Azure Storage-klientbibliotek för .NET](https://www.nuget.org/packages/WindowsAzure.Storage/): det här paketet ger programmatisk åtkomst till dataresurser i ditt lagringskonto.
* [Microsoft Azure Configuration Manager-biblioteket för .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): det här paketet tillhandahåller en klass för parsning av en anslutningssträng i en konfigurationsfil, oavsett var ditt program körs.

Du kan använda NuGet för att hämta båda paketen. Följ de här stegen:

1. Högerklicka på ditt projekt i **Solution Explorer** och välj **Hantera NuGet-paket**.
2. Sök online efter ”WindowsAzure.Storage” och klicka på **Installera** för att installera Storage-klientbiblioteket och alla dess beroenden.
3. Sök online efter ”WindowsAzure.ConfigurationManager” och klicka på **Installera** för att installera Azure Configuration Manager.

> [!NOTE]
> Storage-klientbibliotekspaketet finns också inkluderat i [Azure SDK för .NET](https://azure.microsoft.com/downloads/). Vi rekommenderar dock att du även installerar Storage-klientbiblioteket från NuGet för att säkerställa att du alltid har den senaste versionen av klientbiblioteket.
> 
> ODataLib-beroenden i lagringsklientbiblioteket för .NET kan matchas med ODataLib-paketen som är tillgängliga på NuGet, inte från WCF Data Services. ODataLib-biblioteken kan hämtas direkt eller refereras till i ditt kodprojekt via NuGet. De specifika ODataLib-paket som används av Storage-klientbiblioteket är [OData](http://nuget.org/packages/Microsoft.Data.OData/), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/) och [Spatial](http://nuget.org/packages/System.Spatial/). Även om de här biblioteken används av Azure-tabellagringsklasserna så är de nödvändiga beroenden för programmering med Storage-klientbiblioteket.
> 
> 

### <a name="determine-your-target-environment"></a>Fastställ målmiljön
Du har två miljöalternativ för att köra exemplen i den här guiden:

* Du kan köra din kod mot ett Azure Storage-konto i molnet. 
* Du kan köra din kod mot en Azure-lagringsemulator. Lagringsemulatorn är en lokal miljö som emulerar ett Azure Storage-konto i molnet. Emulatorn är ett kostnadsfritt alternativ för att testa och felsöka din kod medan ditt program är under utveckling. Emulatorn använder sig av ett välkänt konto och nyckel. Mer information finns i [Använda Azure Storage-emulatorn för utveckling och testning](../articles/storage/storage-use-emulator.md)

Om målet är ett lagringskonto i molnet kopierar du den primära åtkomstnyckeln för ditt lagringskonto från Azure Portal. Mer information finns i [Visa och kopiera åtkomstnycklar för lagring](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys).

> [!NOTE]
> Du kan använda lagringsemulatorn för att undvika kostnader associerade med Azure Storage. Men även om du väljer att använda ett Azure-lagringskonto i molnet, kommer kostnaderna för att genomföra den här guiden vara minimala.
> 
> 

### <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring
Azure Storage-klientbiblioteket för .NET stöder användning av en anslutningssträng för lagring för att konfigurera slutpunkter och autentiseringsuppgifter för åtkomst till lagringstjänster. Det bästa sättet att underhålla anslutningssträngen för lagring är i en konfigurationsfil. 

Mer information om anslutningssträngar finns i [Konfigurera en anslutningssträng för Azure Storage](../articles/storage/storage-configure-connection-string.md).

> [!NOTE]
> Din nyckel för lagringskontot liknar rotlösenordet för lagringskontot. Var alltid noga med att skydda din lagringskontonyckel. Undvik att dela ut den till andra användare, hårdkoda den eller spara den i en oformaterad textfil som andra har åtkomst till. Återskapa din nyckel med hjälp av Azure Portal om du misstänker att den komprometterats.
> 
> 

För att konfigurera din anslutningssträng öppnar du `app.config`-filen i Solutions Explorer i Visual Studio. Lägg till innehållet i `<appSettings>`-elementet enligt nedan. Ersätt `account-name` med namnet på ditt lagringskonto och `account-key` med din åtkomstnyckel:

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Din konfigurationsinställning kan till exempel se ut så här:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln6fT7mvY+rxu2iWAEyzPKITGkhM88J8HUoyofvK7C6fHcZc2kRZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />
```

För att använda lagringsemulatorn kan du använda ett kortkommando som mappar till det välkända kontonamnet och nyckeln. I så fall är din inställning för anslutningssträngen:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

