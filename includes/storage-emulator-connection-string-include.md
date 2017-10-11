Storage-emulatorn stöder ett fast konto och en välkänd autentiseringsnyckel för autentisering med delad nyckel. Det här kontot och nyckeln är endast delad nyckel autentiseringsuppgifterna som tillåts för användning med storage-emulatorn. De är:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Autentiseringsnyckeln stöds av storage-emulatorn är endast avsett för testning funktionerna i din klientkod för autentisering. Den har inte något syfte säkerhet. Du kan inte använda ditt lagringskonto för produktion och en nyckel med storage-emulatorn. Du bör inte använda kontot för utveckling med produktionsdata.
> 
> Storage-emulatorn stöder endast anslutningar via HTTP. HTTPS är dock rekommenderade protokollet för att komma åt resurser i en Azure storage-konto.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Anslut till emulatorn-konto med hjälp av en genväg
Det enklaste sättet att ansluta till storage-emulatorn från ditt program är att konfigurera en anslutningssträng i programmets konfigurationsfil som refererar till en genväg till `UseDevelopmentStorage=true`. Här är ett exempel på en anslutningssträng till storage-emulatorn i en *app.config* fil: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Anslut till emulatorn-konto med hjälp av det välkända kontonamnet och nyckeln
Om du vill skapa en anslutningssträng som refererar till emulatorn kontonamnet och nyckeln måste du ange slutpunkterna för var och en av de tjänster som du vill använda från emulatorn i anslutningssträngen. Detta är nödvändigt så att anslutningssträngen refererar emulatorn slutpunkter, vilket skiljer sig från de för ett lagringskonto för produktion. Värdet för anslutningssträngen ska se ut så här:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Det här värdet är identisk med en genväg som visas ovan, `UseDevelopmentStorage=true`.

#### <a name="specify-an-http-proxy"></a>Ange en HTTP-proxy
Du kan också ange en HTTP-proxy ska användas när du testar din tjänst mot storage-emulatorn. Detta kan vara användbart för sett HTTP-begäranden och -svar när du felsöker åtgärder mot storage-tjänster. Ange en proxy genom att lägga till den `DevelopmentStorageProxyUri` alternativet i anslutningssträngen och ange värdet till proxy-URI. Här är till exempel en anslutningssträng som pekar på storage-emulatorn och konfigurerar en HTTP-proxy:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

