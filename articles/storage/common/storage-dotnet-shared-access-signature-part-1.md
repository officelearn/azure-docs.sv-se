---
title: Använda signaturer för delad åtkomst (SAS) i Azure Storage | Microsoft Docs
description: Lär dig att använda signaturer för delad åtkomst (SAS) för att delegera åtkomst till Azure Storage-resurser, inklusive blobbar, köer, tabeller och filer.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/18/2017
ms.author: tamram
ms.component: common
ms.openlocfilehash: 94783e6c6ee662c77d6bf60e96bbe7d171cefeef
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140442"
---
# <a name="using-shared-access-signatures-sas"></a>Använda signaturer för delad åtkomst (SAS)

En signatur för delad åtkomst (SAS) ger dig ett sätt att bevilja begränsad åtkomst till objekt i ditt storage-konto till andra klienter, utan att exponera din kontonyckel. I den här artikeln har vi ger en översikt över SAS-modellen, metodtips för SAS och titta på några exempel.

Fler kodexempel med hjälp av SAS utöver de som presenteras här, se [komma igång med Azure Blob Storage i .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) och andra exempel som är tillgängliga i den [kodexempel för Azure](https://azure.microsoft.com/documentation/samples/?service=storage) biblioteket. Du kan ladda ned exempelprogrammen och kör dem, eller bläddra i koden på GitHub.

## <a name="what-is-a-shared-access-signature"></a>Vad är en signatur för delad åtkomst?
En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt storage-konto. Med en SAS kan kan du ge klienterna åtkomst till resurser i ditt storage-konto utan att dela dina kontonycklar. Det här är en viktig aspekt av att använda signaturer för delad åtkomst i dina program – en SAS är ett säkert sätt att dela dina lagringsresurser utan att kompromissa med lagringsnycklar.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

En SAS ger dig detaljerad kontroll över typ av åtkomst som du har gett till klienter som har SAS, inklusive:

* Intervallet över vilket Signaturen är giltig, inklusive starttiden och förfallotiden.
* De behörigheter som beviljats av SAS. Till exempel kan en SAS för en blob bevilja läsbehörighet och skrivbehörighet till blobben, men inte ta bort behörigheter.
* En valfri IP-adress eller IP-adressintervall som Azure Storage godkänner SAS. Du kan till exempel ange ett intervall med IP-adresser som tillhör din organisation.
* Det protokoll som du ska ta emot SAS Azure Storage. Du kan använda den här valfria parametern för att begränsa åtkomsten till klienter som använder HTTPS.

## <a name="when-should-you-use-a-shared-access-signature"></a>När ska du använda en signatur för delad åtkomst?
Du kan använda en SAS när du vill ge åtkomst till resurser i ditt storage-konto till alla klienter som inte har åtkomstnycklarna för ditt lagringskonto. Ditt lagringskonto innehåller både en primär och sekundär åtkomstnyckel, som båda ha administrativ åtkomst till ditt konto och alla resurser i den. Exponera något av de här nycklarna öppnas ditt konto så att risken för skadliga eller oaktsamhet. Signaturer för delad åtkomst är en säker alternativ som tillåter klienter att läsa, skriva och ta bort data i ditt lagringskonto enligt de behörigheter som du uttryckligen har gett, och utan behov av en kontonyckel.

Ett vanligt scenario där en SAS är användbart är en tjänst där användare läsa och skriva sina egna data till ditt lagringskonto. Det finns två vanliga designmönster i ett scenario där ett lagringskonto lagrar användardata:

1. Klienter ladda upp och ned data via en för frontend-proxytjänst som utför autentisering. Den här tjänsten för klientdelen proxy har fördelen att det tillåter validering av affärsregler, men för stora mängder data eller hög volym transaktioner, skapa en tjänst som kan skalas för att möta efterfrågan kan vara dyrt eller svårt.

  ![Diagram över scenariot: frontend proxy-tjänst](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png)   

1. En förenklad tjänst autentiserar klienten efter behov och genererar en SAS. När klienten får SAS, kan de komma åt lagringskontoresurserna direkt med de behörigheter som definierats av SAS och för intervallet som tillåts av SAS. SAS minskar behovet av Routning av alla data via frontend proxytjänsten.

  ![Diagram över scenariot: SAS provider-tjänsten](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png)   

Många verkliga tjänster kan använda en kombination av dessa två metoder. Exempelvis kanske vissa data bearbetas och godkänts via frontend proxy, medan andra data har sparats och/eller läsa direkt med hjälp av SAS.

Dessutom behöver du använder en SAS för att bevilja åtkomst till objektet i en kopieringsåtgärd i vissa scenarion:

* När du kopierar en blob till en annan blob som finns i ett annat lagringskonto måste du använda en SAS för att bevilja åtkomst till källbloben. Du kan också använda en SAS för att bevilja åtkomst till målblobben samt.
* När du kopierar en fil till en annan fil som finns i ett annat lagringskonto, måste du använda en SAS för att bevilja åtkomst till källfilen. Du kan också använda en SAS för att bevilja åtkomst till målfilen.
* När du kopierar en blob till en fil eller en fil till en blob måste du använda en SAS för att bevilja åtkomst till källobjektet, även om käll- och objekt som finns på samma lagringskonto.

## <a name="types-of-shared-access-signatures"></a>Typer av signaturer för delad åtkomst
Du kan skapa två typer av signaturer för delad åtkomst:

* **Tjänst-SAS.** En tjänst-SAS delegerar åtkomst till en resurs i en av lagringstjänsterna: blobb-, kö-, tabell- eller filtjänsten. Se [konstruera en SAS för tjänst](https://msdn.microsoft.com/library/dn140255.aspx) och [Service SAS exempel](https://msdn.microsoft.com/library/dn140256.aspx) detaljerad information om hur du skapar SAS-token för tjänsten.
* **SAS-konto.** Konto SAS delegerar åtkomst till resurser i en eller flera av lagringstjänsterna. Alla åtgärder som är tillgängliga via en tjänst-SAS är också tillgängliga via en konto-SAS. Med kontot med delad Åtkomstsignatur, kan du dessutom delegera åtkomst till åtgärder som gäller för en viss tjänst, till exempel **Get/Set-tjänstegenskaper** och **få statistik för tjänsten**. Du kan också delegera åtkomst till läs-, skriv- och borttagningsåtgärder i blobcontainrar, tabeller, köer och filresurser som inte tillåts med en tjänst-SAS. Se [konstruera en konto-SAS](https://msdn.microsoft.com/library/mt584140.aspx) detaljerad information om hur du skapar kontot SAS-token.

## <a name="how-a-shared-access-signature-works"></a>Så här fungerar en signatur för delad åtkomst
En signatur för delad åtkomst är en signerad URI som pekar på en eller flera storage-resurser och innehåller en token som innehåller en särskild uppsättning Frågeparametrar. Token anger hur resurserna som kan användas av klienten. En av frågeparametrar, signatur, skapas från SAS-parametrarna och signerad med kontonyckeln. Den här signaturen används av Azure Storage för att auktorisera åtkomst till storage-resurs.

Här är ett exempel på en SAS-URI som visar resurs-URI och SAS-token:

![Komponenterna i en SAS-URI](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-uri.png)   

SAS-token är en sträng som du genererar på den *klienten* sida (se den [SAS exempel](#sas-examples) avsnittet kodexempel). En SAS-token som du genererar med storage-klientbiblioteket, till exempel spåras inte av Azure Storage på något sätt. Du kan skapa ett obegränsat antal SAS-token på klientsidan.

När en klient innehåller en SAS-URI till Azure Storage som en del av en begäran, kontrollerar tjänsten SAS-parametrarna och signaturen för att kontrollera att den är giltig för att autentisera begäran. Om tjänsten verifierar att signaturen är giltig och begäran har behörighet. Annars avvisas begäran med felkoden 403 (förbjudet).

## <a name="shared-access-signature-parameters"></a>Parametrar för signaturer för delad åtkomst
SAS-konto och tjänsten SAS-token innehåller vissa vanliga parametrar och även vidta några parametrar som skiljer sig.

### <a name="parameters-common-to-account-sas-and-service-sas-tokens"></a>Parametrar som är gemensamma för kontot med delad Åtkomstsignatur och service SAS-token
* **API-versionen** en valfri parameter som anger storage service-version du använder för att utföra begäran.
* **Tjänstversionen** en obligatorisk parameter som anger storage service-version du använder för att auktorisera begäran.
* **Starttid.** Detta är den tid då SAS blir giltigt. Starttiden för signatur för delad åtkomst är valfritt. Om en starttid utelämnas är SAS omedelbart verksam. Starttiden måste anges i UTC (Coordinated Universal Time), med en särskild enhetsbeteckning för UTC (”Z”), till exempel `1994-11-05T13:15:30Z`.
* **Förfallotid.** Detta är den tid då Signaturen är inte längre är giltig. Bästa praxis rekommenderar att du antingen ange en förfallotid för en SAS eller koppla den till en lagrad åtkomstprincip. Förfallotiden måste anges i UTC (Coordinated Universal Time), med en särskild enhetsbeteckning för UTC (”Z”), till exempel `1994-11-05T13:15:30Z` (se mer nedan).
* **Behörigheter.** Behörigheter som anges för SAS visar vilka åtgärder som klienten kan utföra mot storage-resurs med hjälp av SAS. Tillgängliga behörigheter skiljer sig åt för en konto-SAS och en tjänst-SAS.
* **IP.** En valfri parameter som anger en IP-adress eller ett intervall med IP-adresser utanför Azure (se avsnittet [routning sessionstillstånd configuration](../../expressroute/expressroute-workflows.md#routing-session-configuration-state) för Express Route) som du vill acceptera begäranden från.
* **Protokoll.** En valfri parameter som anger vilket protokoll som tillåts för en förfrågan. Möjliga värden är både HTTPS och HTTP (`https,http`), vilket är standardvärdet eller HTTPS endast (`https`). Observera att HTTP endast inte är tillåtna värdet.
* **Signatur.** Signaturen skapas från de andra parametrarna som angetts som en del token och sedan krypteras. Signaturen används för att bevilja åtkomst till de angivna lagringsresurserna.

### <a name="parameters-for-a-service-sas-token"></a>Parametrar för en tjänst-SAS-token
* **Lagringsresurs.** Storage-resurser som du kan delegera åtkomst med en tjänst SAS är:
  * Behållare och blobbar
  * Filresurser och filer
  * Köer
  * Tabeller och intervall för tabellenheter.

### <a name="parameters-for-an-account-sas-token"></a>Parametrar för en konto-SAS-token
* **Tjänsten eller tjänster.** En konto-SAS kan delegera åtkomst till en eller flera av lagringstjänsterna. Du kan till exempel skapa en konto-SAS som delegerar åtkomst till Blob- och tjänsten. Eller så kan du skapa en SAS att delegerar åtkomst till alla fyra tjänster (Blob, kö, tabell och fil).
* **Storage-resurstyper.** Ett konto gäller SAS för en eller flera klasser av lagringsresurser i stället för en viss resurs. Du kan skapa en konto-SAS att delegera åtkomst till:
  * Tjänstnivå API: er, som kallas mot resursen för lagringskonton. Exempel är **Get/Set-tjänstegenskaper**, **få statistik för tjänsten**, och **lista behållare/köer/tabeller/resurser**.
  * Behållarenivån API: er, som kallas mot behållaren objekten för varje tjänst: blob-behållare, köer, tabeller och filresurser. Exempel är **skapa/ta bort behållaren**, **skapa/ta bort kön**, **skapa/ta bort tabellen**, **skapa/ta bort resursen**, och  **Listar Blobbar/filer och kataloger**.
  * På objektnivå API: er, som kallas mot blobar, Kömeddelanden, tabellenheter och filer. Till exempel **placera Blob**, **Frågeidentitet**, **hämta meddelanden**, och **Create File**.

## <a name="examples-of-sas-uris"></a>Exempel på SAS URI: er

### <a name="service-sas-uri-example"></a>Tjänsten SAS-URI-exempel

Här är ett exempel på en tjänst SAS-URI som ger Läs- och skrivbehörighet till en blob. Tabellen delar upp varje del för att förstå hur den bidrar till SAS-URI:

```
https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
```

| Namn | SAS-delen | Beskrivning |
| --- | --- | --- |
| BLOB-URI |`https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt` |Adressen till bloben. Observera att med hjälp av HTTPS rekommenderas starkt. |
| Storage services-versionen |`sv=2015-04-05` |För storage services version 2012-02-12 och senare, den här parametern anger versionen som ska användas. |
| Starttid |`st=2015-04-29T22%3A18%3A26Z` |Anges i UTC-tid. Om du vill att Signaturen ska vara giltigt omedelbart utelämna starttiden. |
| Förfallotid |`se=2015-04-30T02%3A23%3A26Z` |Anges i UTC-tid. |
| Resurs |`sr=b` |Resursen är en blob. |
| Behörigheter |`sp=rw` |De behörigheter som beviljats av SAS omfattar Read (r) och skriva (w). |
| IP-intervall |`sip=168.1.5.60-168.1.5.70` |IP-adressintervall som en begäran tas emot. |
| Protokoll |`spr=https` |Endast begäranden med hjälp av HTTPS tillåts. |
| Signatur |`sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D` |Används för att auktorisera åtkomst till bloben. Signaturen är en HMAC beräknas över en inloggning till sträng och nyckeln med SHA256-algoritmen och sedan kodad med Base64-kodning. |

### <a name="account-sas-uri-example"></a>Exempel för konto-SAS-URI

Här är ett exempel på en konto-SAS som använder de gemensamma parametrarna för samma i token. Eftersom dessa parametrar beskrivs ovan, som de inte beskrivs här. Endast parametrarna som är specifika för kontot SAS beskrivs i tabellen nedan.

```
https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B
```

| Namn | SAS-delen | Beskrivning |
| --- | --- | --- |
| Resurs-URI |`https://myaccount.blob.core.windows.net/?restype=service&comp=properties` |Blob-tjänstens slutpunkt, med parametrar för att hämta egenskaper (när den anropas med GET) eller ange tjänstegenskaper (när den anropas med). |
| Tjänster |`ss=bf` |SAS som gäller för Blob- och -tjänster |
| Resurstyper |`srt=s` |SAS gäller för servicenivå åtgärder. |
| Behörigheter |`sp=rw` |Behörigheterna som beviljar åtkomst Läs-och skrivåtgärder. |

Tanke på att behörigheterna är begränsade till servicenivån, tillgängliga åtgärder med den här SAS är **hämta egenskaper för Blob Service** (läsa) och **ange egenskaper för Blob Service** (skriva). Men med en annan resurs URI samma SAS-token kan också användas att delegera åtkomst till **få statistik för Blob-tjänsten** (läsa).

## <a name="controlling-a-sas-with-a-stored-access-policy"></a>Kontrollera en SAS med en lagrad åtkomstprincip
En signatur för delad åtkomst kan ta ett av två sätt:

* **Ad hoc-SAS:** när du skapar en ad hoc-SAS starttid, förfallotid, och behörigheter för SAS är alla angivna i SAS-URI (eller underförstådda, i de fall där starttiden utelämnas). Den här typen av SAS kan skapas som en konto-SAS eller en tjänst-SAS.
* **SAS med lagrad åtkomstprincip:** en lagrad åtkomstprincip har definierats för en resurs behållare – en blob-behållare, tabell, kö, eller filresurs – och kan användas för att hantera begränsningar för en eller flera signaturer för delad åtkomst. När du kopplar en SAS med en lagrad åtkomstprincip, ärver SAS begränsningar, starttid, förfallotid och behörigheter--har definierats för lagrad åtkomstprincip.

> [!NOTE]
> För närvarande måste en konto-SAS vara en ad hoc-SAS. Lagrade åtkomst principer inte stöds ännu för SAS-konto.

Skillnaden mellan de två formulär är viktigt för ett viktiga scenario: återkallade certifikat. Eftersom en SAS-URI är en URL, kan vem som hämtar signaturen för delad åtkomst använda den, oavsett vem som skapade den. Om en SAS publiceras offentligt, kan den användas av vem som helst i världen. En SAS ger åtkomst till resurser till alla som har det tills något av följande händer:

1. Förfallotiden som anges för SAS har nåtts.
2. Förfallotiden som anges på lagrad åtkomstprincip som refereras av SAS har uppnåtts (om en lagrad åtkomstprincip refereras, och den anger en förfallotid). Detta kan inträffa eftersom intervallet ska gå eller eftersom du har ändrat lagrad åtkomstprincip med en förfallotid tidigare, vilket är ett sätt att återkalla signaturen för delad åtkomst.
3. Lagrad åtkomstprincip som refereras av SAS tas bort, vilket är ett annat sätt att återkalla signaturen för delad åtkomst. Observera att om du återskapar lagrad åtkomstprincip med exakt samma namn, alla befintliga SAS-token igen är giltiga enligt de behörigheter som är associerade med den lagrad åtkomstprincipen (förutsatt att som förfallotiden för SAS inte har passerats). Om du avsikten är att återkalla signaturen för delad åtkomst, måste du använda ett annat namn om du återskapar åtkomstprincip med en förfallotid i framtiden.
4. Den kontonyckel som används för att skapa SAS återskapas. Återskapar en kontonyckel gör att alla programkomponenter med hjälp av nyckeln inte kan auktorisera förrän de har uppdaterats för att använda andra giltiga kontonyckeln eller nyligen återskapade kontonyckeln.

> [!IMPORTANT]
> En signatur för delad åtkomst URI: N är associerad med den kontonyckel som används för att skapa signaturen och den associerade lagras åtkomstprincip (om sådan finns). Om ingen lagrad åtkomstprincip anges, är det enda sättet att återkalla en signatur för delad åtkomst för att ändra kontonyckeln.

## <a name="authenticating-from-a-client-application-with-a-sas"></a>Autentisering från ett klientprogram med en SAS
En klient som har tillgång för en SAS kan använda SAS för att auktorisera en begäran mot ett storage-konto som de inte har nycklar för kontot. En SAS kan ingår i en anslutningssträng eller användas direkt från rätt konstruktor nebo metodu.

### <a name="using-a-sas-in-a-connection-string"></a>Med hjälp av en SAS i en anslutningssträng
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

### <a name="using-a-sas-in-a-constructor-or-method"></a>Med hjälp av en SAS i konstruktorn och metoder
Flera Azure Storage client library konstruktorer och metoden överlagringar erbjuder en SAS-parameter så att du kan auktorisera en begäran till tjänsten med en SAS.

Till exempel används här en SAS-URI för att skapa en referens till en blockblob. SAS ger de enda autentiseringsuppgifter som krävs för begäran. Referensen för blockblobben används sedan för skrivning:

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

## <a name="best-practices-when-using-sas"></a>Metodtips för att med hjälp av SAS
När du använder signaturer för delad åtkomst i dina program, måste du vara medveten om två potentiella risker:

* Om en SAS har läckts kan den användas av alla som erhåller, vilket potentiellt kan påverka ditt storage-konto.
* Om en SAS tillhandahålls till ett klientprogram upphör att gälla och programmet kan inte hämta en ny SAS från din tjänst och programmets funktioner kan hindras.

Följande rekommendationer för att använda signaturer för delad åtkomst kan hjälpa att undvika detta ställer:

1. **Använder alltid HTTPS** att skapa eller distribuera en SAS. Om en SAS skickas via HTTP och fångas upp, kan en obehörig som utför en man-in-the-middle-attack läsa SAS och använder den precis som de avsedda användarna kan ha potentiellt att kompromettera känsliga data eller så att data skadas av skadliga användare.
2. **Referera till åtkomstprinciper har lagrats där det är möjligt.** Åtkomstprinciper har lagrats ger dig möjlighet att återkalla behörigheter utan att behöva återskapa lagringskontonycklarna. Ange förfallodatum för dessa mycket långt fram i tiden (eller oändlig) och kontrollera att uppdateras regelbundet för att flytta den längre i framtiden.
3. **Använd närtid upphör att gälla gånger på en ad hoc-SAS.** På så sätt kan även om en SAS komprometteras så är det bara giltiga för en kort tid. Den här metoden är särskilt viktigt om du inte kan referera till en lagrad åtkomstprincip. Kortsiktig upphör att gälla gånger också begränsa hur mycket data som kan skrivas till en blob genom att begränsa tid som är tillgängliga för att ladda upp till den.
4. **Ha klienter automatiskt förnya SAS om det behövs.** Klienter bör förnya SAS innan den upphör att gälla, tid avsätts för återförsök om tjänsten som tillhandahåller Signaturen är inte tillgänglig. Om din SAS är avsedd att användas för ett litet antal omedelbar, tillfälliga åtgärder som förväntas slutföras inom giltighetsperiod, kan sedan detta vara onödiga som SAS inte förväntas förnyas. Men om du har klienten som regelbundet gör förfrågningar via SAS kan kommer sedan om risken för upphör att gälla betydelse. Nyckelfaktor är att balansera behovet av SAS vara kortlivade (som tidigare nämnts) behovet av att se till att klienten begär förnyelse tidigt tillräckligt med (för att undvika störningar på grund av den SAS som upphör att gälla innan lyckad förnyelse).
5. **Var försiktig med SAS starttiden.** Om du anger starttiden för en SAS till **nu**, sedan ge skeva (skillnader i aktuell tid enligt olika datorer) på grund av klockan, fel kan observeras periodvis för första några minuter. I allmänhet Ange starttid ska vara minst 15 minuter tidigare. Eller så kan inte ange den alls, vilket gör det giltiga omedelbart i samtliga fall. Samma gäller generellt för förfallotid samt--Kom ihåg att du kan se upp till 15 minuter klockan skeva i endera riktningen på varje begäran. För klienter som använder en REST-version äldre än 2012-02-12, är maximal varaktighet för en Signatur som inte refererar till en lagrad åtkomstprincip 1 timme, och alla principer för att ange längre sikt än vad som kommer att misslyckas.
6. **Vara specifika med resursen som ska användas.** Av säkerhetsskäl är att ge en användare med lägsta behörighet. Om användaren behöver bara läsbehörighet till en enda enhet, sedan ger dem tillgång till läsåtkomst till den enda entiteten och inte läsning/skrivning/ta bort åtkomst till alla entiteter. Detta hjälper även minska skador om en SAS komprometteras eftersom signaturen för delad åtkomst har mindre ström i händerna på en angripare.
7. **Förstå att ditt konto kommer att faktureras för eventuell användning, inklusive att göra med SAS.** Om du anger skrivåtkomst till en blob kan kan en användare välja att ladda upp en blob 200GB. Om du har gett dem samt läsbehörighet, kan de välja att hämta den 10 gånger medför 2 TB i kostnader för nätverksegress för dig. Igen, ange begränsade behörigheter för att minska potentiella åtgärder av obehöriga användare. Använda tillfällig SAS för att minska det här hotet (men Tänk också på klockan skeva på sluttid).
8. **Validera data som skrivits med hjälp av SAS.** När ett klientprogram skriver data till ditt lagringskonto, Kom ihåg att det kan finnas problem med dessa data. Om programmet kräver att data ska verifieras eller behörighet innan det är klart att användas, bör du utföra den här verifieringen när data skrivs och innan den används av ditt program. Den här metoden skyddar även mot skadad eller skadliga data som skrivs till ditt konto, antingen av en användare som har köpt SAS korrekt eller av en användare som utnyttjar en läckta SAS.
9. **Inte alltid att använda SAS.** Ibland uppväga riskerna med en viss åtgärd mot ditt lagringskonto fördelarna med SAS. För sådana åtgärder, skapar du en tjänst i mittennivå som skriver till ditt storage-konto när du har utfört företag regel verifiering, autentisering och granskning. Dessutom är ibland det enklare att hantera åtkomst på andra sätt. Till exempel om du vill se alla blobbar i en behållare kan läsas offentligt du behållaren offentlig, i stället för att tillhandahålla en SAS på alla klienter för åtkomst.
10. **Använda Storage Analytics för att övervaka program.** Du kan använda loggning och mått för att se alla topp i autentiseringsfel på grund av ett avbrott i din SAS provider-tjänsten eller oavsiktlig borttagning av en lagrad åtkomstprincip. Se den [Azure Storage-teamets blogg](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) för ytterligare information.

## <a name="sas-examples"></a>SAS-exempel
Nedan följer några exempel på båda typerna av signaturer för delad åtkomst, kontot med delad Åtkomstsignatur och tjänst-SAS.

Om du vill köra dessa C#-exempel, måste du referera till följande NuGet-paketen i projektet:

* [Azure Storage-klientbiblioteket för .NET](http://www.nuget.org/packages/WindowsAzure.Storage), version 6.x eller senare (för att använda SAS-konto).
* [Azure Configuration Manager](http://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager)

Ytterligare exempel som visar hur du skapar och testar en SAS finns i [kodexempel för Azure för lagring](https://azure.microsoft.com/documentation/samples/?service=storage).

### <a name="example-create-and-use-an-account-sas"></a>Exempel: Skapa och använda en konto-SAS
Följande exempel skapar ett konto SAS som är giltig för Blob- och -tjänster och ger klienten behörigheter som Läs-, Skriv- och listbehörigheter att få åtkomst till API: er för servicenivå. Kontot med delad Åtkomstsignatur begränsar protokollet till HTTPS, så att begäran måste göras med HTTPS.

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

Skapa en Blob-klientobjekt som använder SAS och Blob storage-slutpunkten för ditt storage-konto om du vill använda kontot med delad Åtkomstsignatur att komma åt tjänstnivå API: er för Blob-tjänsten.

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

### <a name="example-create-a-stored-access-policy"></a>Exempel: Skapa en lagrad åtkomstprincip
Följande kod skapar en lagrad åtkomstprincip för en behållare. Du kan använda åtkomstprincipen för att ange begränsningar för en tjänst-SAS på behållaren eller dess blobar.

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

### <a name="example-create-a-service-sas-on-a-container"></a>Exempel: Skapa en tjänst-SAS för en behållare
Följande kod skapar en SAS för en behållare. Om namnet på en lagrad åtkomstprincip är principen associerad med SAS. Om ingen lagrad åtkomstprincip anges, sedan skapar koden en ad hoc-SAS för behållaren.

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

### <a name="example-create-a-service-sas-on-a-blob"></a>Exempel: Skapa en tjänst-SAS för en blob
Följande kod skapar en SAS för en blob. Om namnet på en lagrad åtkomstprincip är principen associerad med SAS. Om ingen lagrad åtkomstprincip anges, sedan skapar koden en ad hoc-SAS på blob.

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
Signaturer för delad åtkomst är användbara för att tillhandahålla begränsade behörigheter till ditt lagringskonto till klienter som inte ska ha kontonyckeln. Det innebär att de är en viktig del av säkerhetsmodellen för alla program som använder Azure Storage. Om du har följt de rekommenderade metoder som beskrivs här måste använda du SAS för större flexibilitet för åtkomst till resurser i ditt lagringskonto utan att kompromissa med säkerheten för ditt program.

## <a name="next-steps"></a>Nästa steg
* [Signaturer för delad åtkomst, del 2: Skapa och använda en SAS med Blob storage](../blobs/storage-dotnet-shared-access-signature-part-2.md)
* [Hantera anonym läsbehörighet till behållare och blobbar](../blobs/storage-manage-access-to-resources.md)
* [Delegera åtkomst med signatur för delad åtkomst](http://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Introduktion till tabell och kö-SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
