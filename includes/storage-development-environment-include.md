## <a name="set-up-your-development-environment"></a>Ställ in din utvecklingsmiljö
Härnäst ska du ställa in din utvecklingsmiljö i Visual Studio så att du är redo att testa kodexemplen i den här guiden.

### <a name="create-a-windows-console-application-project"></a>Skapa ett Windows-konsolprogramprojekt
Skapa ett nytt Windows-konsolprogram i Visual Studio så här:

![Skapa ett Windows-konsolprogram](./media/storage-development-environment-include/storage-development-environment-include-1.png)

Alla kodexempel i den här guiden kan läggas till i **Main()**-metoden i `program.cs` i konsolprogrammet.

Observera att du kan använda Azure Storage-klientbiblioteket från valfri typ av .NET-program, inklusive en Azure-molntjänst, en Azure-webbapp, ett skrivbordsprogram eller ett mobilprogram. I den här guiden använder vi oss av en konsolapp för enkelhetens skull.

### <a name="use-nuget-to-install-the-required-packages"></a>Använd NuGet för att installera de paket som behövs
Du kommer att behöva installera två paket till ditt projekt för att slutföra den här guiden:

* [Microsoft Azure Storage-klientbibliotek för .NET](https://www.nuget.org/packages/WindowsAzure.Storage/): det här paketet ger programmatisk åtkomst till dataresurser i ditt lagringskonto.
* [Microsoft Azure-konfigurationshanterarens bibliotek för .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) erbjuder en klass för parsning av en anslutningssträng från en konfigurationsfil, oavsett vart ditt program körs.

Du kan använda NuGet för att hämta båda paketen. Följ de här stegen:

1. Högerklicka på ditt projekt i **Solution Explorer** och välj **Hantera NuGet-paket**.
2. Sök online efter ”WindowsAzure.Storage” och klicka på **Installera** för att installera Storage-klientbiblioteket och alla dess beroenden.
3. Sök online efter ”ConfigurationManager” och klicka på **installera** för att installera Azure-konfigurationshanteraren.

> [!NOTE]
> Storage-klientbibliotekspaketet finns också inkluderat i [Azure SDK för .NET](https://azure.microsoft.com/downloads/). Vi rekommenderar dock att du även installerar Storage-klientbiblioteket från NuGet för att säkerställa att du alltid har den senaste versionen av klientbiblioteket.
> 
> ODataLib-beroenden i Storage-klientbiblioteket för .NET matchas via ODataLib-paket (version 5.0.2 och nyare) som finns tillgängliga via NuGet och inte via WCF Data Services. ODataLib-biblioteken kan hämtas direkt eller refereras till i ditt kodprojekt via NuGet. De specifika ODataLib-paket som används av Storage-klientbiblioteket är [OData](http://nuget.org/packages/Microsoft.Data.OData/5.0.2), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/5.0.2) och [Spatial](http://nuget.org/packages/System.Spatial/5.0.2). Även om de här biblioteken används av Azure-tabellagringsklasserna så är de nödvändiga beroenden för programmering med Storage-klientbiblioteket.
> 
> 

### <a name="determine-your-target-environment"></a>Fastställ målmiljön
Du har två miljöalternativ för att köra exemplen i den här guiden:

* Du kan köra din kod mot ett Azure Storage-konto i molnet. 
* Du kan köra din kod mot en Azure-lagringsemulator. Lagringsemulatorn är en lokal miljö som emulerar ett Azure Storage-konto i molnet. Emulatorn är ett kostnadsfritt alternativ för att testa och felsöka din kod medan ditt program är under utveckling. Emulatorn använder sig av ett välkänt konto och nyckel. Mer information finns i [Använd Azure Storage-emulatorn för utveckling och testning](../articles/storage/storage-use-emulator.md)

Om målet är ett lagringskonto i molnet, kopierar du den primära åtkomstnyckeln för ditt lagringskonto från Azure Portal. Mer information finns i [Visa och kopiera åtkomstnycklar för lagring](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys).

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

Konfigurationsinställningen kan till exempel likna följande:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln6fT7mvY+rxu2iWAEyzPKITGkhM88J8HUoyofvK7C6fHcZc2kRZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />
```

För att använda lagringsemulatorn kan du använda ett kortkommando som mappar till det välkända kontonamnet och nyckeln. I så fall kommer inställningen för anslutningssträngen att vara:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```



<!--HONumber=Nov16_HO3-->


