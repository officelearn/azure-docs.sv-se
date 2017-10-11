## <a name="configure-your-application-to-access-azure-storage"></a>Konfigurera ditt program för att få åtkomst till Azure Storage
Det finns två sätt att autentisera ditt program för att få åtkomst till lagringstjänster:

* Delad nyckel: Använd delad nyckel endast för testning
* Signatur för delad åtkomst (SAS): Använd SAS för program i produktion

### <a name="shared-key"></a>Delad nyckel
Autentisering med delad nyckel innebär att programmet använder kontonamn och kontonyckel åtkomst till lagringstjänster. Vid tillämpningen av snabbt visar hur du använder det här biblioteket, kommer vi att använda autentisering med delad nyckel i den här komma igång.

> [!WARNING] 
> **Endast använda autentisering med delad nyckel för testning!** Kontonamn och kontonyckel, vilket ger fullständig åtkomst till det associera lagringskontot kommer att distribueras till alla personer som hämtas av din app. Detta är **inte** en bra rutin som riskerar du att din nyckel av icke-betrodda klienter.
> 
> 

När du använder autentisering med delad nyckel, skapar du en [anslutningssträngen](../articles/storage/common/storage-configure-connection-string.md). Anslutningssträngen består av:  

* Den **DefaultEndpointsProtocol** -du kan välja HTTP eller HTTPS. Men rekommenderas om du använder HTTPS starkt.
* Den **kontonamn** -namnet på ditt lagringskonto
* Den **Kontonyckel** – på den [Azure Portal](https://portal.azure.com), navigera till ditt lagringskonto och klicka på den **nycklar** ikon för att hitta den här informationen.
* (Valfritt) **EndpointSuffix** -används för lagringstjänster i områden med olika endpoint suffix, till exempel Azure Kina eller Azure-styrning.

Här är ett exempel på anslutningssträngen med hjälp av autentisering med delad nyckel:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Signaturer för delad åtkomst (SAS)
För mobila program är den rekommenderade metoden för att autentisera en begäran från en klient mot Azure Storage-tjänsten med hjälp av en delad signatur åtkomst (SAS). SAS kan du bevilja en klientåtkomst till en resurs för en angiven tidsperiod, med en angiven uppsättning behörigheter.
Som lagringskontoägaren måste du generera en SAS för mobila klienter att använda. För att generera SAS, vill du förmodligen skriva en separat tjänst som genererar SAS ska distribueras till dina klienter. I testsyfte kan du använda den [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com) eller [Azure Portal](https://portal.azure.com) Generera en SAS. När du skapar SAS kan ange du tidsintervallet som SAS är giltigt och vilka behörigheter som SAS ger till klienten.

I följande exempel visas hur du använder Microsoft Azure Lagringsutforskaren för att generera en SAS.

1. Om du inte redan har gjort [installera Microsoft Azure Lagringsutforskaren](http://storageexplorer.com)
2. Ansluta till din prenumeration.
3. Klicka på ditt lagringskonto och klicka på fliken ”åtgärder” längst ned till vänster. Klicka på ”Hämta signatur för delad åtkomst” för att generera en ”anslutningssträng” för din SAS.
4. Här är ett exempel på en SAS-anslutningssträng att ger Läs- och skrivbehörighet på tjänsten, behållare och objektnivå för blob-tjänsten för lagringskontot.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Som du kan se när du använder en SAS, är det inte att exponera din kontonyckel i ditt program. Du kan lära dig mer om SAS och bästa praxis för att använda SAS genom att checka ut [signaturer för delad åtkomst: Förstå SAS-modellen](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md).

