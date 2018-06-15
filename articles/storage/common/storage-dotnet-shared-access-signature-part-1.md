---
title: Använda signaturer för delad åtkomst (SAS) i Azure Storage | Microsoft Docs
description: Lär dig använda signaturer för delad åtkomst (SAS) för att delegera åtkomst till Azure Storage-resurser, inklusive blobbar, köer, tabeller och filer.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 04/18/2017
ms.author: cshoe
ms.openlocfilehash: 4f20e79ea6cb2d9d403f4451f595516d5c2e9373
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650748"
---
# <a name="using-shared-access-signatures-sas"></a>Använda signaturer för delad åtkomst (SAS)

En signatur för delad åtkomst (SAS) ger dig ett sätt att ge begränsad åtkomst till objekt i ditt lagringskonto till andra klienter, utan att utsätta din kontonyckel. I den här artikeln vi ger en översikt över SAS-modellen, metodtips för SAS och titta på några exempel.

Ytterligare kodexempel med hjälp av SAS utöver de som visas här finns [komma igång med Azure Blob Storage i .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) och andra exempel som är tillgängliga i den [Azure kodexempel](https://azure.microsoft.com/documentation/samples/?service=storage) bibliotek. Du kan hämta exempelprogrammen och köra dem eller bläddra koden på GitHub.

## <a name="what-is-a-shared-access-signature"></a>Vad är en signatur för delad åtkomst?
En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagringskonto. Med en SAS beviljar du klienter åtkomst till resurser i ditt lagringskonto, utan att dela dina nycklar för kontot. Det här är en viktig aspekt av att använda signaturer för delad åtkomst i dina program – en SAS är ett säkert sätt att dela dina lagringsresurser utan att kompromissa med lagringsnycklar.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

En SAS ger detaljerad kontroll över typ av åtkomst som du har gett till klienter som har SAS, inklusive:

* Tidsintervall som SAS är giltiga, inklusive starttid och förfallotiden.
* De behörigheter som utfärdats av SAS. Till exempel kan en SAS för en blob bevilja läsbehörighet och skrivbehörighet till blobben, men inte att ta bort behörigheter.
* En valfri IP-adress eller intervall av IP-adresser som Azure Storage godkänner SAS. Du kan till exempel ange ett intervall med IP-adresser som tillhör din organisation.
* Det protokoll som du ska ta emot SAS Azure Storage. Du kan använda den här valfria parametern för att begränsa åtkomsten till klienter som använder HTTPS.

## <a name="when-should-you-use-a-shared-access-signature"></a>När bör du använda en signatur för delad åtkomst?
Du kan använda en SAS när du vill ge åtkomst till resurser i ditt lagringskonto till alla klienter som inte har åtkomstnycklarna för ditt lagringskonto. Ditt lagringskonto innehåller både en primär och sekundär snabbtangent, som båda ha administrativ åtkomst till ditt konto och alla resurser i den. Ditt konto möjligheten att skadlig eller bristande öppnas exponering av någon av dessa nycklar. Signaturer för delad åtkomst är en säker alternativ som tillåter klienter att läsa, skriva och ta bort data i ditt lagringskonto enligt de behörigheter som du uttryckligen har beviljats och utan behovet av att någon kontonyckel.

Ett vanligt scenario där en SAS är användbar är en tjänst där användare läsa och skriva sina egna data till ditt lagringskonto. Det finns två vanliga designmönster i ett scenario där ett lagringskonto lagrar användardata:

1. Klienter ladda upp och hämta data via en frontend-proxytjänst som utför autentisering. Proxytjänsten frontend-har fördelen att det tillåter validering av affärsregler, men för stora mängder data eller omfattande transaktioner, skapa en tjänst som kan skalas för att möta efterfrågan kan vara dyra eller svårt.

  ![Scenariot diagram: frontend-proxytjänst](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png)   

1. En förenklad tjänst autentiserar klienten efter behov och genererar en SAS. När klienten får SAS, kan de komma åt kontot lagringsresurser direkt med de behörigheter som definierats av SAS och för intervallet som tillåts av SAS. SAS minskar behovet av att alla data via proxytjänsten frontend-routning.

  ![Scenariot diagram: SAS-providertjänst](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png)   

Många verkliga tjänster kan använda en kombination av dessa två sätt. Vissa data kan till exempel bearbetas och godkänts via frontend proxy, medan andra data är sparade och/eller läsa med hjälp av SAS.

Dessutom behöver du använda en SAS för att autentisera källobjektet i en kopieringsåtgärd i vissa scenarier:

* När du kopierar en blobb till en annan blob som finns i ett annat lagringskonto måste du använda en SAS för att autentisera käll-blob. Du kan eventuellt använda en SAS för att autentisera samt mål-blob.
* När du kopierar en fil till en annan fil som finns i ett annat lagringskonto, måste du använda en SAS för att autentisera källfilen. Du kan eventuellt använda en SAS för att autentisera till målfilen.
* När du kopierar en blobb till en fil eller en fil till en blobb, måste du använda en SAS för att autentisera källobjektet, även om käll- och objekt som finns inom samma lagringskonto.

## <a name="types-of-shared-access-signatures"></a>Typer av signaturer för delad åtkomst
Du kan skapa två typer av signaturer för delad åtkomst:

* **Tjänst-SAS.** En tjänst-SAS delegerar åtkomst till en resurs i en av lagringstjänsterna: blobb-, kö-, tabell- eller filtjänsten. Se [hur du skapar en tjänst-SAS](https://msdn.microsoft.com/library/dn140255.aspx) och [Service SAS exempel](https://msdn.microsoft.com/library/dn140256.aspx) för detaljerad information om hur du skapar service SAS-token.
* **Konto-SAS.** Den konto SAS delegater åtkomsten till resurser i en eller flera av lagringstjänsterna. Alla åtgärder som är tillgängliga via en tjänst-SAS finns också tillgängliga via en konto-SAS. Med konto-SAS, kan du dessutom delegera åtkomst till åtgärder som gäller för en viss tjänst som **Get/Set-tjänstegenskaper** och **få statistik för tjänsten**. Du kan också delegera åtkomst till läs-, skriv- och borttagningsåtgärder i blobbbehållare, tabeller, köer och filresurser som inte tillåts med en tjänst-SAS. Se [hur du skapar ett konto-SAS](https://msdn.microsoft.com/library/mt584140.aspx) för detaljerad information om hur du skapar kontot SAS-token.

## <a name="how-a-shared-access-signature-works"></a>Så här fungerar en signatur för delad åtkomst
En signatur för delad åtkomst är en signerad URI som pekar på en eller flera lagringsresurser och innehåller en token som innehåller en särskild uppsättning Frågeparametrar. Token som anger hur resurserna som kan användas av klienten. En av frågeparametrarna signatur, skapas från SAS-parametrar och signerad med kontonyckel. Den här signaturen används av Azure Storage för att autentisera SAS.

Här är ett exempel på en SAS-URI, visar resurs-URI och SAS-token:

![Komponenter för en SAS-URI](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-uri.png)   

SAS-token är en sträng som du skapar på den *klienten* sida (finns i [SAS exempel](#sas-examples) avsnittet kodexempel). En SAS-token som du skapar med storage-klientbiblioteket, till exempel spåras inte av Azure Storage på något sätt. Du kan skapa ett obegränsat antal SAS-token på klientsidan.

När en klient innehåller ett SAS-URI till Azure Storage som en del av en begäran, kontrollerar tjänsten SAS parametrar och signatur för att kontrollera att den är giltig för att autentisera begäran. Om tjänsten verifierar att signaturen är giltig och sedan autentisera begäran. Annars har begäran nekats med felkoden 403 (förbjuden).

## <a name="shared-access-signature-parameters"></a>Parametrar för signatur för delad åtkomst
Konto-SAS och tjänsten SAS-token innehåller några vanliga parametrar och tar några parametrar som skiljer sig också.

### <a name="parameters-common-to-account-sas-and-service-sas-tokens"></a>Parametrar som är gemensamma för konto-SAS och tjänsten SAS-token
* **API-versionen** en valfri parameter som anger storage service-version du använder för att utföra begäran.
* **Service-version** en obligatorisk parameter som anger storage service-version du använder för att autentisera begäran.
* **Starttid.** Det här är den tid då SAS börjar gälla. Starttiden för en signatur för delad åtkomst är valfritt. Om en starttid utelämnas är SAS gälla omedelbart. Starttiden måste anges i UTC (Coordinated Universal Time) med en särskild UTC beteckning (”Z”), till exempel `1994-11-05T13:15:30Z`.
* **Förfallotiden.** Detta är den tid då SAS inte är giltig längre. Bästa praxis rekommenderar att du antingen ange en förfallotiden för en SAS eller koppla den till en princip för lagrade åtkomst. Förfallotid måste anges i UTC (Coordinated Universal Time) med en särskild UTC beteckning (”Z”), till exempel `1994-11-05T13:15:30Z` (se mer nedan).
* **Behörigheter.** Behörigheter som anges på SAS visar vilka åtgärder som klienten kan utföra mot storage-resursen med hjälp av SAS. Tillgängliga behörigheter skiljer sig för en konto-SAS och en tjänst-SAS.
* **IP-ADRESSEN.** En valfri parameter som anger en IP-adress eller ett intervall med IP-adresser utanför Azure (finns i avsnittet [konfiguration av Routning sessionstillstånd](../../expressroute/expressroute-workflows.md#routing-session-configuration-state) för Express Route) som du vill acceptera förfrågningar från.
* **Protokoll.** En valfri parameter som anger vilket protokoll som tillåts för en begäran. Möjliga värden är HTTPS- och HTTP (`https,http`), vilket är standardvärdet eller HTTPS endast (`https`). Observera att HTTP endast inte är tillåtna värdet.
* **Signatur.** Signaturen har skapats från de andra parametrar som angetts som en del token och sedan krypteras. Den används för att autentisera SAS.

### <a name="parameters-for-a-service-sas-token"></a>Parametrar för en tjänst-SAS-token
* **Storage-resursen.** Storage-resurser som du kan delegera åtkomst med en tjänst SAS inkluderar:
  * Behållare och blobbar
  * Filresurser och filer
  * Köer
  * Tabeller och intervallen för tabellentiteter.

### <a name="parameters-for-an-account-sas-token"></a>Parametrar för ett konto SAS-token
* **Tjänsten eller tjänster.** En konto-SAS kan delegera åtkomst till en eller flera av lagringstjänsterna. Du kan till exempel skapa en konto-SAS som ombud åtkomsten till tjänsten Blob och filen. Eller så kan du skapa en SAS att delegater åtkomst till alla fyra tjänster (Blob, kön, tabell och filen).
* **Typer av lagring.** Ett konto SAS gäller för en eller flera klasser av lagringsresurser, i stället för en viss resurs. Du kan skapa en konto-SAS att delegera åtkomst till:
  * Servicenivå API: er, som kallas mot lagringsresurs för kontot. Exempel inkluderar **Get/Set-tjänstegenskaper**, **få statistik för tjänsten**, och **lista behållare/köer/tabeller/resurser**.
  * Behållare på objektnivå API: er, som kallas mot behållaren objekten för varje tjänst: blob-behållare, köer, tabeller och filresurser. Exempel inkluderar **skapa/ta bort behållaren**, **skapa/ta bort kön**, **skapa/ta bort tabellen**, **skapa/ta bort resursen**, och  **Lista över BLOB-filer och kataloger**.
  * På objektnivå API: er, som kallas mot BLOB, Kömeddelanden, tabellentiteter och filer. Till exempel **placera Blob**, **frågan entiteten**, **få meddelanden**, och **Create File**.

## <a name="examples-of-sas-uris"></a>Exempel på SAS URI: er

### <a name="service-sas-uri-example"></a>Tjänsten SAS-URI-exempel

Här är ett exempel på en tjänst SAS-URI som ger Läs- och skrivbehörighet till en blob. Tabellen uppdelad varje del för att förstå hur den bidrar till SAS-URI:

```
https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
```

| Namn | SAS-delen | Beskrivning |
| --- | --- | --- |
| BLOB-URI |`https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt` |Adressen till blob. Observera att med hjälp av HTTPS rekommenderas starkt. |
| Storage services version |`sv=2015-04-05` |För lagringstjänster version 12-02-2012 och senare kan den här parametern anger versionen som ska användas. |
| Starttid |`st=2015-04-29T22%3A18%3A26Z` |Angivet i UTC-tid. Om du vill SAS ska gälla omedelbart, utelämna starttiden. |
| Förfallotid |`se=2015-04-30T02%3A23%3A26Z` |Angivet i UTC-tid. |
| Resurs |`sr=b` |Resursen är en blob. |
| Behörigheter |`sp=rw` |De behörigheter som utfärdats av SAS omfattar Read (r) och skriva (b). |
| IP-intervall |`sip=168.1.5.60-168.1.5.70` |Intervallet av IP-adresser som en begäran kommer att accepteras. |
| Protokoll |`spr=https` |Endast de begäranden som använder HTTPS tillåts. |
| Signatur |`sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D` |Används för att autentisera åtkomst till blob. Signaturen är en HMAC beräknas över ett sträng-inloggning och nyckeln med hjälp av SHA256-algoritmen och sedan kodad med Base64-kodning. |

### <a name="account-sas-uri-example"></a>Kontot SAS-URI-exempel

Här är ett exempel på en konto-SAS som använder samma gemensamma parametrar i token. Eftersom dessa parametrar beskrivs ovan, beskrivs de inte här. Endast parametrarna som är specifika för kontot SAS beskrivs i tabellen nedan.

```
https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B
```

| Namn | SAS-delen | Beskrivning |
| --- | --- | --- |
| Resurs-URI |`https://myaccount.blob.core.windows.net/?restype=service&comp=properties` |Blob-tjänstens slutpunkt, med parametrar för att hämta tjänstegenskaper (när den anropas med GET) eller ange tjänstegenskaper (när den anropas med). |
| Tjänster |`ss=bf` |SAS gäller Blob och Filtjänster |
| Typer av resurser |`srt=s` |SAS gäller för servicenivå operationer. |
| Behörigheter |`sp=rw` |Behörigheterna som beviljar åtkomst Läs-och skrivåtgärder. |

Med hänsyn till att behörigheter är begränsade till servicenivån tillgängliga åtgärder med den här SAS är **hämta egenskaper för Blob-tjänsten** (läsa) och **ange egenskaper för Blob-tjänsten** (Skriv). Men med en annan resurs-URI, samma SAS-token kan även användas att delegera åtkomst till **få statistik för Blob-tjänsten** (läsa).

## <a name="controlling-a-sas-with-a-stored-access-policy"></a>Kontrollera en SAS med en princip lagrade åtkomst
En signatur för delad åtkomst kan göra något av två typer:

* **Ad hoc-SAS:** när du skapar en ad hoc-SAS starttid, förfallotid, och behörigheterna för SAS är alla angivna i SAS-URI (eller underförstådda, i de fall där starttiden utelämnas). Den här typen av SAS kan skapas som ett konto SAS eller en tjänst-SAS.
* **SAS med lagrade åtkomstprincip:** lagrade åtkomstprincip har definierats för en resurs behållare – en blob-behållare, tabell, kö, eller filresurs-- och kan användas för att hantera begränsningar för en eller flera signaturer för delad åtkomst. När du kopplar en SAS med en lagrad till ärver SAS begränsningarna--starttid, förfallotiden och behörigheter--har definierats för den lagrade åtkomstprincipen.

> [!NOTE]
> För närvarande måste en konto-SAS vara en ad hoc-SAS. Lagrade åtkomst principer ännu inte stöds för konto-SAS.

Skillnaden mellan de två formulär är viktigt för ett key-scenario: återkallade certifikat. Eftersom en SAS-URI är en URL, kan alla som erhåller SAS använda det, oavsett vem som skapade den. Om en SAS publiceras offentligt, kan den användas av vem som helst i världen. En SAS ger åtkomst till resurser till alla som har den tills något av följande händer:

1. Förfallotiden som anges på SAS har nåtts.
2. Förfallotiden som angetts på den lagrade åtkomstprincip som refereras av SAS har uppnåtts (om en lagrad åtkomstprincip refereras, och det anger en förfallotiden). Detta kan inträffa eftersom intervallet långa eller eftersom du har ändrat lagrade åtkomstprincipen med en förfallotiden tidigare, vilket är ett sätt att återkalla SAS.
3. Lagrade åtkomstprincipen som refereras av SAS tas bort, vilket är ett annat sätt att återkalla SAS. Observera att om du återskapa den lagrade åtkomstprincipen med exakt samma namn, alla befintliga SAS-token igen är giltiga enligt de behörigheter som är kopplade till den lagrade åtkomstprincipen (förutsatt att som förfallotiden på SAS inte har passerats). Om du avsikten är att återkalla SAS, måste du använda ett annat namn om du återskapa åtkomstprincipen med ett förfallodatum i framtiden.
4. Nyckeln för kontot som används för att skapa SAS genereras. Återskapande av någon kontonyckel kommer alla programkomponenter som använder nyckeln för att kunna autentisera förrän de har uppdaterats för att använda den andra nyckeln i giltigt konto eller nyligen återskapats kontonyckel.

> [!IMPORTANT]
> En signatur för delad åtkomst URI som är associerad med kontonyckel som används för att skapa signaturen och den associerade lagras åtkomstprincip (eventuella). Om inga lagrade åtkomstprincip anges är det enda sättet att återkalla en signatur för delad åtkomst att ändra nyckeln för kontot.

## <a name="authenticating-from-a-client-application-with-a-sas"></a>Autentisering från ett klientprogram med en SAS
En klient som innehar en SAS kan använda SAS för att autentisera en begäran mot ett lagringskonto som de inte har nycklar för kontot. En SAS kan ingå i en anslutningssträng eller användas direkt från lämplig konstruktor eller metod.

### <a name="using-a-sas-in-a-connection-string"></a>Med hjälp av en SAS i en anslutningssträng
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

### <a name="using-a-sas-in-a-constructor-or-method"></a>Med hjälp av en SAS i en konstruktor eller metod
Flera Azure Storage client biblioteket konstruktorer och metoden överlagringar erbjuder en SAS-parameter, så att du kan autentisera en begäran till tjänsten med en SAS.

Till exempel används här SAS-URI för att skapa en referens till en blockblobb. SAS innehåller de enda autentiseringsuppgifter som krävs för begäran. Blockblobben används sedan för en skrivåtgärd:

```csharp
string sasUri = "https://storagesample.blob.core.windows.net/sample-container/" +
    "sampleBlob.txt?sv=2015-07-08&sr=b&sig=39Up9JzHkxhUIhFEjEH9594DJxe7w6cIRCg0V6lCGSo%3D" +
    "&se=2016-10-18T21%3A51%3A37Z&sp=rcw";

CloudBlockBlob blob = new CloudBlockBlob(new Uri(sasUri));

// Create operation: Upload a blob with the specified name to the container.
// If the blob does not exist, it will be created. If it does exist, it will be overwritten.
try
{
    MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    msWrite.Position = 0;
    using (msWrite)
    {
        await blob.UploadFromStreamAsync(msWrite);
    }

    Console.WriteLine("Create operation succeeded for SAS {0}", sasUri);
    Console.WriteLine();
}
catch (StorageException e)
{
    if (e.RequestInformation.HttpStatusCode == 403)
    {
        Console.WriteLine("Create operation failed for SAS {0}", sasUri);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    else
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}

```

## <a name="best-practices-when-using-sas"></a>Bästa metoder när du med hjälp av SAS
När du använder signaturer för delad åtkomst i dina program, måste du vara medveten om två potentiella risker:

* Om en SAS har läckts kan den användas av alla som erhåller, vilket potentiellt kan påverka ditt lagringskonto.
* Om en SAS som ett klientprogram upphör att gälla och programmet kan inte hämta en ny SAS från din tjänst och programmets funktioner kan hindras.

Följande rekommendationer för att använda signaturer för delad åtkomst kan minska riskerna:

1. **Använder alltid HTTPS** att skapa eller distribuera en SAS. Om en SAS skickas via HTTP och snappas upp, är en obehörig som utför en man-in-the-middle-attacker kan läsa SAS och sedan använda den precis som den avsedda användaren kan ha potentiellt kompromettera känsliga data eller så att data skadas av skadliga användare.
2. **Referens lagrad åtkomstprinciper där det är möjligt.** Lagrade åtkomstprinciper ger dig möjlighet att återkalla behörigheter utan att behöva återskapa lagringskontonycklar. Ange upphör att gälla på dessa mycket långt fram i tiden (eller oändlig) och kontrollera att uppdateras regelbundet flyttas längre i framtiden.
3. **Använd kortsiktig giltighetstid gånger på en ad hoc-SAS.** På så sätt kan även om en SAS äventyras, är det bara giltig för en kort tid. Den här övningen är särskilt viktigt om du inte kan referera till en princip för lagrade åtkomst. Kortsiktig giltighetstid gånger också begränsa mängden data som kan skrivas till en blobb genom att begränsa tid som är tillgängliga att överföra till den.
4. **Ha klienter automatiskt förnya SAS om det behövs.** Klienter måste förnya SAS före förfallodatum, för att ge tid för nya försök om att tillhandahålla SAS-tjänsten är inte tillgänglig. Om din SAS är avsedd att användas för ett litet antal omedelbara och tillfällig åtgärder som förväntas slutföras inom den förfallotid perioden, sedan kan det vara onödiga som SAS inte förväntas förnyas. Men om du har klienten som regelbundet göra begäranden via SAS kommer sedan möjligheten att upphör att gälla in i play. Nyckelfaktor är att balansera behovet av att vara tillfällig SAS (som tidigare nämnts anger) med behovet av att säkerställa att klienten begär förnyelse tidigt nog (för att undvika störningar på grund av SAS ut före lyckade förnyelse).
5. **Var försiktig med SAS starttiden.** Om du anger starttiden för en SAS för **nu**, sedan skeva (skillnader i aktuell tid enligt olika datorer) på grund av klockan, fel kan observeras ibland för först få minuter. I allmänhet Ange starttid ska vara minst 15 minuter i förflutna. Eller inte anger den, vilket gör det giltiga omedelbart i samtliga fall. Samma gäller vanligtvis förfallotiden samt--Kom ihåg att du kan se upp till 15 minuter klockan skeva i vardera riktning på varje begäran. För klienter som använder en REST-version innan 2012-02-12, är maximal varaktighet för en Signatur som inte refererar till en lagrad åtkomstprincip 1 timme och policyer för att ange längre sikt än vad som kommer att misslyckas.
6. **Måste vara specifikt med resursen som ska användas.** Av säkerhetsskäl är att ge en användare de minsta behörigheter som krävs. Om en användare behöver bara läsbehörighet till en enda enhet, ge dem tillgång till läsåtkomst till den enda entiteten och inte läsa/skriva och ta bort åtkomst till alla enheter. Det hjälper även minska skadan om en SAS äventyras eftersom SAS har mindre ström i händerna på en angripare.
7. **Förstå att ditt konto kommer att debiteras för användning, inklusive göra med SAS.** Om du anger skrivåtkomst till en blob kan kan en användare välja att överföra en blob 200GB. Om du har gett dem samt läsbehörighet, kan de välja att hämta den 10 gånger medför 2 TB i kostnader för nätverksegress för dig. Igen, ange begränsade behörigheter för att minska de potentiella åtgärderna av obehöriga användare. Använd tillfällig SAS för att minska hotet (men Tänk också på klockavvikelser på sluttid).
8. **Validera data som skrivs med hjälp av SAS.** När ett klientprogram skriver data till ditt lagringskonto, Kom ihåg att det kan finnas problem med dessa data. Om ditt program kräver att dessa data att verifieras eller behörighet innan den är klar att använda, bör du utföra verifieringen när data skrivs och innan den används av ditt program. Den här övningen även skyddar mot skadad eller skadliga data skrivs till ditt konto, antingen av en användare som korrekt förvärvade SAS eller av en användare som utnyttjar en känd SAS.
9. **Inte alltid att använda SAS.** Ibland uppväger risker med en viss åtgärd mot ditt lagringskonto fördelarna med SAS. Skapa en mellannivå-tjänst som skriver till ditt lagringskonto efter att ha genomfört företag för dessa åtgärder regel verifiering, autentisering och granskning. Dessutom är ibland det enklare att hantera åtkomst på annat sätt. Till exempel om du vill se alla blobbar i en behållare kan läsas offentligt du behållaren allmänheten, i stället för att tillhandahålla en SAS till alla klienter för åtkomst.
10. **Använd Storage Analytics för övervakning av programmet.** Du kan använda loggning och mått om du vill se alla topp i antal autentiseringsfel på grund av ett avbrott i tjänsten din SAS-provider eller oavsiktlig borttagning av en princip för lagrade åtkomst. Finns det [Azure Storage-teamets blogg](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) för ytterligare information.

## <a name="sas-examples"></a>SAS-exempel
Nedan följer några exempel på båda typerna av signaturer för delad åtkomst, konto-SAS och tjänst-SAS.

Om du vill köra dessa C#-exempel, måste du referera till följande NuGet-paketen i projektet:

* [Azure Storage-klientbibliotek för .NET](http://www.nuget.org/packages/WindowsAzure.Storage), version 6.x eller senare (för att använda kontot SAS).
* [Azure Configuration Manager](http://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager)

Ytterligare exempel som visar hur du skapar och testar en SAS finns [Azure kodexempel för lagring](https://azure.microsoft.com/documentation/samples/?service=storage).

### <a name="example-create-and-use-an-account-sas"></a>Exempel: Skapa och använda en konto-SAS
Följande exempel skapar ett konto SA som är giltig för Blob och Filtjänster och ger klienten behörigheterna Läs-, Skriv- och listbehörigheter servicenivå API: er. Konto-SAS begränsar protokollet till HTTPS, så begäran måste göras med HTTPS.

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use your shared key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

Skapa ett objekt för Blob med hjälp av SAS- och slutpunkt för Blob-lagring för lagringskontot för att använda kontot SAS servicenivå API: er för Blob-tjänsten.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "account-name", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

### <a name="example-create-a-stored-access-policy"></a>Exempel: Skapa en princip för lagrade åtkomst
Följande kod skapar en åtkomstprincip som är lagrade på en behållare. Du kan använda för att ange begränsningar för en tjänst-SAS på behållaren eller dess blobbar.

```csharp
private static async Task CreateSharedAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new shared access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
        // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

### <a name="example-create-a-service-sas-on-a-container"></a>Exempel: Skapa en tjänst-SAS på en behållare
Följande kod skapar en SAS för en behållare. Om namnet på en befintlig lagrade åtkomstprincip är principen associerad med SAS. Om inga lagrade åtkomstprincip skapar koden sedan en ad hoc-SAS på behållaren.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad-hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad-hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

### <a name="example-create-a-service-sas-on-a-blob"></a>Exempel: Skapa en tjänst-SAS på en blob
Följande kod skapar en SAS för en blob. Om namnet på en befintlig lagrade åtkomstprincip är principen associerad med SAS. Om inga lagrade åtkomstprincip skapar koden en ad hoc-SAS på blob.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad-hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

## <a name="conclusion"></a>Sammanfattning
Signaturer för delad åtkomst är användbara för att tillhandahålla begränsade behörigheter till ditt lagringskonto till klienter som inte ska ha kontonyckel. Därför att de är en viktig del av säkerhetsmodellen för alla program som använder Azure Storage. Om du följer bästa praxis som anges här kan du använda SAS för större flexibilitet för åtkomst till resurser i ditt lagringskonto, utan att kompromettera säkerheten för ditt program.

## <a name="next-steps"></a>Nästa steg
* [Signaturer för delad åtkomst, del 2: Skapa och använda en SAS med Blob storage](../blobs/storage-dotnet-shared-access-signature-part-2.md)
* [Hantera anonym läsbehörighet till behållare och blobbar](../blobs/storage-manage-access-to-resources.md)
* [Delegera åtkomst med signatur för delad åtkomst](http://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Introduktion till tabellen och kön SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
