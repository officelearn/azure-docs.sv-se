---
title: Anropar Azure Storage Services REST API-funktioner inklusive autentisering | Microsoft Docs
description: Anropar Azure Storage Services REST API-funktioner inklusive autentisering
services: storage
author: tamram
manager: twooley
ms.service: storage
ms.topic: how-to
ms.date: 05/22/2018
ms.author: tamram
ms.openlocfilehash: 6009ebd18eb089b21c98d6f7d9f49044a8d96098
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650459"
---
# <a name="using-the-azure-storage-rest-api"></a>Använda Azure Storage REST API

Den här artikeln visar hur du använder den Blob Storage Service REST API: er och hur du autentiserar anropet till tjänsten. De skrivs från synsätt på den person som vet ingenting om REST och aning göra ett REST-anrop, men är en utvecklare. Vi titta på referensdokumentationen för ett REST-anrop och se hur att översätta det till ett verkligt REST-anrop – vilka fält gå var? Du kan använda denna kunskap om du vill använda någon av de andra Storage Service REST API: er när du lär dig hur du ställer in ett REST-anrop.

## <a name="prerequisites"></a>Förutsättningar 

Programmet visar behållarna i blob storage för ett lagringskonto. Om du vill prova koden i den här artikeln behöver du följande: 

* Installera [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) med följande arbetsbelastning:
    - Azure Development

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Ett allmänt lagringskonto. Om du ännu inte har ett lagringskonto, se [skapa ett lagringskonto](storage-quickstart-create-account.md).

* Exemplet i den här artikeln visar hur du listar behållare i ett lagringskonto. Lägga till vissa behållare för blob storage i storage-konto innan du startar om du vill se utdata.

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

Exempelprogrammet är ett konsolprogram som skrivits i C#.

Använd [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Det här kommandot klonar lagret till den lokala git-mappen. Öppna Visual Studio-lösning, letar du upp mappen storage-dotnet-rest-api-with-auth, öppna det och dubbelklicka på StorageRestApiAuth.sln. 

## <a name="what-is-rest"></a>Vad är REST?

REST innebär *representational tillstånd överföring*. För en specifik definition kolla [Wikipedia](http://en.wikipedia.org/wiki/Representational_state_transfer).

I princip REST är en arkitektur som du kan använda när du anropar API: er eller göra tillgängliga för att anropa API: er. Det är oberoende av vad som händer på endera sidan och vilka andra program som används när skickas eller tas emot av REST-anrop. Du kan skriva ett program som körs på en Mac, Windows, Linux, Android-telefon eller surfplatta, iPhone, iPad eller webbplats och använder samma REST API för alla dessa plattformar. Data kan överföras i och/eller ut när REST API anropas. REST API hand inte från vilken plattform som kallas – vad som är viktigt är den information som angavs i begäran och de data som finns i svaret.

Om du vet hur du använder REST är en användbar kunskap. Produktteamet Azure Frigör ofta nya funktioner. Många gånger de nya funktionerna kan nås via REST-gränssnittet, men ännu inte har angetts via **alla** lagringsklientbiblioteken eller Användargränssnittet (till exempel Azure-portalen). Om du alltid vill använda det senaste och bästa learning REST är ett krav. Om du vill skriva egna bibliotek för att interagera med Azure Storage, eller om du vill ha åtkomst till Azure Storage med ett programmeringsspråk som inte har en SDK eller storage-klientbiblioteket, kan du använda REST API.

## <a name="about-the-sample-application"></a>Om exempelprogrammet

Exempelprogrammet visar behållarna i ett lagringskonto. Andra REST-anrop är enklare att ta reda på när du förstår hur informationen i REST API-dokumentation motsvarar den faktiska koden. 

Om du tittar på det [REST-API för Blob-tjänsten](/rest/api/storageservices/fileservices/Blob-Service-REST-API), visas alla åtgärder som du kan utföra på blob-lagring. Lagringsklientbiblioteken är omslutningar runt REST-API: er – de gör det lättare för dig att komma åt lagringsutrymme utan att använda REST-API: er direkt. Men som nämns ovan, du vill ibland använda REST-API i stället för en storage-klientbiblioteket.

## <a name="rest-api-reference-list-containers-api"></a>REST API-referens: Listan behållare API

Nu ska vi titta på en sida i REST API-referens för den [ListContainers](/rest/api/storageservices/fileservices/List-Containers2) igen så att du förstår vissa fält varifrån i begäran och svar i nästa avsnitt med kod.

**Begärandemetod**: hämta. Detta verb är HTTP-metod som du anger som en egenskap för request-objektet. Andra värden för den här verbet är HEAD, PUT och ta bort, beroende på hur du anropar API: et.

**Begärande-URI**: https://myaccount.blob.core.windows.net/?comp=list detta skapas från slutpunkt för blob storage-konto `http://myaccount.blob.core.windows.net` och resurssträngen `/?comp=list`.

[URI-parametrar](/rest/api/storageservices/fileservices/List-Containers2#uri-parameters): det finns ytterligare frågeparametrar du kan använda när du anropar ListContainers. Några av dessa parametrar är *timeout* för anrop (i sekunder) och *prefix*, som används för filtrering.

En annan användbar parametern är *maxresults:* om fler behållare är tillgängliga än detta värde svarstexten innehåller en *NextMarker* element som anger att nästa behållaren ska returnera på nästa begäran. Om du vill använda den här funktionen måste du ange den *NextMarker* värdet som det *markör* parameter i URI: N vid nästa förfrågan. När du använder den här funktionen, är det detsamma som sidindelning i sökresultaten. 

Om du vill använda ytterligare parametrar måste du lägga till dem i resurssträngen med värdet som det här exemplet:

```
/?comp=list&timeout=60&maxresults=100
```

[Begäransrubriker](/rest/api/storageservices/fileservices/List-Containers2#request-headers)**:** det här avsnittet innehåller obligatoriska och valfria begärandehuvudena. Tre av rubrikerna som krävs: en *auktorisering* huvudet *x-ms-date* (innehåller UTC-tid för begäran) och *x-ms-version* (anger versionen av RESTEN API för att använda). Inklusive *x-ms-client-request-id* i radrubrikerna är valfri – du kan ange värdet för det här fältet till något; skrivs till loggarna storage analytics när loggning är aktiverat.

[Begärandetext](/rest/api/storageservices/fileservices/List-Containers2#request-body)**:** det finns ingen begärandetexten för ListContainers. Begärandetexten används på alla PUT-åtgärder när du överför blobbar, samt SetContainerAccessPolicy, där du kan skicka i en XML-lista över lagrade åtkomstprinciper ska gälla. Lagrade åtkomstprinciper beskrivs i artikeln [med delad åtkomst signaturer (SAS)](storage-dotnet-shared-access-signature-part-1.md).

[Svarets statuskod](/rest/api/storageservices/fileservices/List-Containers2#status-code)**:** Tells av alla statuskoder som du bör känna till. I det här exemplet är en HTTP-statuskod 200 ok. En fullständig lista över HTTP-statuskoder kolla [Status kod definitioner](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Felkoder som är specifika för REST-API: er för lagring finns [vanliga REST API-felkoder](/rest/api/storageservices/common-rest-api-error-codes)

[Svarsrubriker](/rest/api/storageservices/fileservices/List-Containers2#response-headers)**:** bland *Content-Type*; *x-ms-begäran-id* (begäran-id du löpt ut, om tillämpligt). *x-ms-version* (anger versionen av Blob-tjänst som används) och *datum* (UTC, anger vilken tid begäran gjordes).

[Svarstexten](/rest/api/storageservices/fileservices/List-Containers2#response-body): det här fältet är en XML-struktur som tillhandahåller data som begärdes. I det här exemplet är en lista över behållare och deras egenskaper i svaret.

## <a name="creating-the-rest-request"></a>REST-begäran skapas

Några anteckningar innan du startar – för säkerhet när du kör i produktion kan alltid använda HTTPS snarare än HTTP. Vid tillämpningen av den här övningen ska du använda HTTP så att du kan visa data för begäran och svar. Om du vill visa information om begäran och svar i de faktiska REST-anrop, kan du hämta [Fiddler](http://www.telerik.com/fiddler) eller ett liknande program. Lagringskontonamn och nyckel är hårdkodad i klassen i Visual Studio-lösning och metoden ListContainersAsyncREST skickar lagringskontonamn och lagringskontonyckel till de metoder som används för att skapa de olika komponenterna i REST-begäran . I ett verkligt program lagringskontonamn och nyckel skulle finnas i en konfigurationsfil, miljövariabler eller hämtas från ett Azure Key Vault.

I vår exempelprojektet är koden för att skapa Authorization-huvud i en separat klass med avsikt att kan du ta hela klassen och lägga till den i din egen lösning och använda den ”i befintligt skick”. Huvudet auktoriseringskod fungerar för de flesta REST API-anrop till Azure Storage.

Gå till ListContainersAsyncREST i Program.cs för att skapa den begäran som är ett HttpRequestMessage-objekt. Stegen för att skapa begäran är: 

* Skapa URI som ska användas för att anropa tjänsten. 
* Skapa HttpRequestMessage-objektet och ange nyttolasten. Nyttolasten är null för ListContainersAsyncREST eftersom vi inte skickar någonting i.
* Lägg till begärandehuvuden för x-ms-date- och x-ms-version.
* Hämta authorization-huvud och lägga till den.

Grundläggande information du behöver: 

*  För ListContainers, den **metoden** är `GET`. Det här värdet anges när en instans skapades av begäran. 
*  Den **resurs** är frågan delen av den URI som anger vilken API anropas, så är värdet `/?comp=list`. Som tidigare nämnts resursen finns på sidan-dokumentation referens som visar information om den [ListContainers API](/rest/api/storageservices/fileservices/List-Containers2).
*  URI: N har skapats genom att skapa Blob-tjänsteslutpunkt för det lagringskontot och sammanbinda resursen. Värdet för **begärande-URI** slutar att `http://contosorest.blob.core.windows.net/?comp=list`.
*  För ListContainers, **requestBody** är null och det finns inga extra **huvuden**.

Olika API: er kan ha andra parametrar för att skicka in som *ifMatch*. Ett exempel på där du kan använda ifMatch är när du anropar PutBlob. I så fall kan du ifMatch till en eTag, och blob uppdateras endast om en eTag som du anger matchar den aktuella eTag på blob. Om någon annan har uppdaterat blob sedan hämtar eTag, åsidosättas sina ändringar inte. 

Ange först den `uri` och `payload`. 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Därefter skapa en instans av begäran om metoden `GET` och ge URI: N.

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Lägg till begärandehuvuden för x-ms-date- och x-ms-version. Denna plats i koden är också där du lägger till eventuella ytterligare begärandehuvuden som krävs för anropet. I det här exemplet finns det några ytterligare huvuden. Ett exempel på en API som skickar extra sidhuvudena är SetContainerACL. Det lägger till ett sidhuvud som kallas ”x-ms-blob-allmän åtkomst” och värdet för åtkomstnivå för Blob storage.

```csharp
    // Add the request headers for x-ms-date and x-ms-version.
    DateTime now = DateTime.UtcNow;
    httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
    httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
    // If you need any additional headers, add them here before creating
    //   the authorization header. 
```

Anropa metoden som skapar authorization-huvud och lägga till den huvuden för begäran. Du lär dig att skapa authorization-huvud senare i artikeln. Metodnamnet är GetAuthorizationHeader som du kan se i det här kodstycket:

```csharp
    // Get the authorization header and add it.
    httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
        storageAccountName, storageAccountKey, now, httpRequestMessage);
```

Nu `httpRequestMessage` innehåller REST-begäran slutfördes med rubriker för auktorisering. 

## <a name="call-the-rest-api-with-the-request"></a>Anropa REST-API med begäran

Nu när du har begäran, kan du anropa SendAsync om du vill skicka REST-begäran. SendAsync anropar API: et och hämtar svaret tillbaka. Granska svaret StatusCode (200 är OK), tolka svaret. I det här fallet kan du hämta ett XML-listan över behållare. Nu ska vi titta på koden för att anropa metoden GetRESTRequest för att skapa begäran, utföra begäran och undersöka svar för listan över behållare.

```csharp 
    // Send the request.
    using (HttpResponseMessage httpResponseMessage = 
      await new HttpClient().SendAsync(httpRequestMessage, cancellationToken))
    {
        // If successful (status code = 200), 
        //   parse the XML response for the container names.
        if (httpResponseMessage.StatusCode == HttpStatusCode.OK)
        {
            String xmlString = await httpResponseMessage.Content.ReadAsStringAsync();
            XElement x = XElement.Parse(xmlString);
            foreach (XElement container in x.Element("Containers").Elements("Container"))
            {
                Console.WriteLine("Container name = {0}", container.Element("Name").Value);
            }
        }
    }
}
```

Om du kör en nätverksavsökare som [Fiddler](https://www.telerik.com/fiddler) när du gör anropet till SendAsync kan du se informationen om begäran och svar. Låt oss ta en titt. Namnet på lagringskontot är *contosorest*.

**Begäran:**

```
GET /?comp=list HTTP/1.1
```

**Huvuden för begäran:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Status koden och svarshuvuden returnerades efter körning:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Svarstexten (XML):** för ListContainers detta visar en lista över behållare och deras egenskaper.

```xml  
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
  ServiceEndpoint="http://contosorest.blob.core.windows.net/">
  <Containers>
    <Container>
      <Name>container-1</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:39:48 GMT</Last-Modified>
        <Etag>"0x8D46CBD5A7C301D"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-2</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:40:50 GMT</Last-Modified>
        <Etag>"0x8D46CBD7F49E9BD"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-3</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:10 GMT</Last-Modified>
        <Etag>"0x8D46CBD8B243D68"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-4</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:25 GMT</Last-Modified>
        <Etag>"0x8D46CBD93FED46F"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
        </Properties>
      </Container>
      <Container>
        <Name>container-5</Name>
        <Properties>
          <Last-Modified>Thu, 16 Mar 2017 22:41:39 GMT</Last-Modified>
          <Etag>"0x8D46CBD9C762815"</Etag>
          <LeaseStatus>unlocked</LeaseStatus>
          <LeaseState>available</LeaseState>
        </Properties>
      </Container>
  </Containers>
  <NextMarker />
</EnumerationResults>
```

Nu när du vet hur du skapar begäran, anropa tjänsten och tolka resultaten ska vi se hur du skapar authorization-huvud. Skapa det sidhuvudet är komplicerad, men bra är att när du har koden fungerar det fungerar för alla Storage Service REST-API: er.

## <a name="creating-the-authorization-header"></a>Skapa authorization-huvud

> [!TIP]
> Azure Storage stöder nu integrering med Azure Active Directory (Azure AD) för Blob- och köegenskaper services (förhandsversion). Azure AD erbjuder en mycket enklare upplevelse för att auktorisera en begäran till Azure Storage. Läs mer om hur du använder Azure AD för att auktorisera REST-åtgärder, [autentisera med Azure Active Directory (förhandsgranskning)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory). En översikt över Azure AD-integrering med Azure Storage finns [autentisera åtkomst till Azure Storage med hjälp av Azure Active Directory (förhandsgranskning)](storage-auth-aad.md).

Det finns en artikel som förklarar begreppsmässigt (ingen kod) hur du utför [autentisering för Azure Storage-tjänster](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services).
Vi destillera artikeln ned till exakt behövs och visa koden.

Använd först en autentisering med delad nyckel. Auktorisering huvudformat ser ut så här:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Signaturfältet är en hashbaserad meddelandeautentiseringskod (HMAC) skapas från begäran och beräknas med SHA256-algoritmen och sedan kodad med Base64-kodning. Förstått? (Låser sig i där, du ännu inte har hört senaste *av kanoniserade* ännu.)

Det här kodstycket visar format för delad nyckel signatur sträng:

```csharp  
StringToSign = VERB + "\n" +  
               Content-Encoding + "\n" +  
               Content-Language + "\n" +  
               Content-Length + "\n" +  
               Content-MD5 + "\n" +  
               Content-Type + "\n" +  
               Date + "\n" +  
               If-Modified-Since + "\n" +  
               If-Match + "\n" +  
               If-None-Match + "\n" +  
               If-Unmodified-Since + "\n" +  
               Range + "\n" +  
               CanonicalizedHeaders +  
               CanonicalizedResource;  
```

De flesta av dessa fält används sällan. För Blob storage anger du VERB, md5, innehållslängd, av kanoniserade huvuden och av kanoniserade resurs. Du kan lämna de andra tomt (men placera i den `\n` så att den vet att de är tomt).

Vad är CanonicalizedHeaders och CanonicalizedResource? Bra fråga. I praktiken vad av kanoniserade medelvärdet? Microsoft Word tolkar inte även det som ett ord. Här är vad [Wikipedia säger om kanonikaliseringen](http://en.wikipedia.org/wiki/Canonicalization): *datavetenskap, auktorisering (ibland standardisering eller normalisering) är en process för att konvertera data som har mer än ett möjligt representation till en ”standard”, ”normal” eller kanoniska form.* I normal tala, det innebär att listan med objekt (till exempel huvuden vid av kanoniserade huvuden) och standardisera dem till ett format som krävs. I princip Microsoft valt ett format och du måste matcha den.

Låt oss börja med dessa två av kanoniserade fält, eftersom de krävs för att skapa Authorization-huvud.

**Av kanoniserade rubriker**

Hämta sidhuvuden som börjar med ”x - ms-” och sortera dem för att skapa det här värdet, och sedan formatera dem till en sträng med `[key:value\n]` instanser, sammanfogas till en sträng. I det här exemplet av kanoniserade huvuden se ut så här: 

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Här är koden som används för att skapa dessa utdata:

```csharp 
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
                    where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
                    orderby kvp.Key
                    select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder sb = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. http://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        StringBuilder headerBuilder = new StringBuilder(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValues in kvp.Value)
        {
            string trimmedValue = headerValues.TrimStart().Replace("\r\n", String.Empty);
            headerBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used 
            //   if there are multiple values for one of the headers.
            separator = ',';
        }
        sb.Append(headerBuilder.ToString()).Append("\n");
    }
    return sb.ToString();
}      
```

**Av kanoniserade resurs**

Den här delen av strängen signatur representerar det lagringskonto som mål för begäran. Kom ihåg att Begärd URI är `<http://contosorest.blob.core.windows.net/?comp=list>`, med det faktiska kontonamnet (`contosorest` i det här fallet). I det här exemplet returneras:

```
/contosorest/\ncomp:list
```

Om du har frågeparametrar ingår här de också. Det här är den kod som hanterar också ytterligare frågeparametrar och frågeparametrar med flera värden. Kom ihåg att du bygger upp den här koden fungerar för alla REST API: er, så att du vill inkludera alla möjligheter, även om metoden ListContainers inte behöver alla.

```csharp 
private static string GetCanonicalizedResource(Uri address, string storageAccountName)
{
    // The absolute path will be "/" because for we're getting a list of containers.
    StringBuilder sb = new StringBuilder("/").Append(storageAccountName).Append(address.AbsolutePath);

    // Address.Query is the resource, such as "?comp=list".
    // This ends up with a NameValueCollection with 1 entry having key=comp, value=list.
    // It will have more entries if you have more query parameters.
    NameValueCollection values = HttpUtility.ParseQueryString(address.Query);

    foreach (var item in values.AllKeys.OrderBy(k => k))
    {
        sb.Append('\n').Append(item).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Nu när av kanoniserade strängar ställs nu ska vi titta på hur du skapar det authorization-huvudet. Börja med att skapa en sträng med meddelandesignaturen i formatet StringToSign som visas tidigare i den här artikeln. Det här konceptet är enklare för att förklara med kommentarer i koden så här det är den sista metoden som returnerar Authorization-huvud:

```csharp
internal static AuthenticationHeaderValue GetAuthorizationHeader(
    string storageAccountName, string storageAccountKey, DateTime now,
    HttpRequestMessage httpRequestMessage, string ifMatch = "", string md5 = "")
{
    // This is the raw representation of the message signature.
    HttpMethod method = httpRequestMessage.Method;
    String MessageSignature = String.Format("{0}\n\n\n{1}\n{5}\n\n\n\n{2}\n\n\n\n{3}{4}",
                method.ToString(),
                (method == HttpMethod.Get || method == HttpMethod.Head) ? String.Empty
                  : httpRequestMessage.Content.Headers.ContentLength.ToString(),
                ifMatch,
                GetCanonicalizedHeaders(httpRequestMessage),
                GetCanonicalizedResource(httpRequestMessage.RequestUri, storageAccountName),
                md5);

    // Now turn it into a byte array.
    byte[] SignatureBytes = Encoding.UTF8.GetBytes(MessageSignature);

    // Create the HMACSHA256 version of the storage key.
    HMACSHA256 SHA256 = new HMACSHA256(Convert.FromBase64String(storageAccountKey));

    // Compute the hash of the SignatureBytes and convert it to a base64 string.
    string signature = Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes));

    // This is the actual header that will be added to the list of request headers.
    AuthenticationHeaderValue authHV = new AuthenticationHeaderValue("SharedKey",
        storageAccountName + ":" + Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes)));
    return authHV;
}
```

När du kör den här koden resulterande MessageSignature ser ut så här:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Här är det sista värdet för AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

AuthorizationHeader är rubriken senaste placeras i begärandehuvudena före bokföring svaret.

Som omfattar allt du behöver veta tillsammans med koden, sätta ihop en klass som du kan använda för att skapa en förfrågan som används för att anropa Storage Services REST API: er.

## <a name="how-about-another-example"></a>Hur är det med ett annat exempel? 

Nu ska vi titta på hur du ändrar koden för att anropa ListBlobs för behållaren *behållare 1*. Detta är nästan identisk med koden för att lista behållare, bara skillnaderna är URI: N och hur du tolka svaret. 

Om du tittar på referensdokumentationen för [ListBlobs](/rest/api/storageservices/fileservices/List-Blobs), du tycker att metoden är *hämta* och RequestURI är:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

I ListContainersAsyncREST, ändrar du den kod som anger URI-API: et för ListBlobs. Behållarens namn är **behållare 1**.

```csharp
String uri = 
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Sedan där du ska hantera svar, ändra koden för att leta efter blobbar i stället för behållare.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

När du kör det här exemplet ger resultat som liknar följande:

**Av kanoniserade huvuden:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Av kanoniserade resurs:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**MessageSignature:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**AuthorizationHeader:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

Följande värden är från [Fiddler](http://www.telerik.com/fiddler):

**Begäran:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Huvuden för begäran:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Status koden och svarshuvuden returnerades efter körning:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Svarstexten (XML):** detta XML-svaret innehåller en lista över blobbar och deras egenskaper. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
    ServiceEndpoint="http://contosorest.blob.core.windows.net/" ContainerName="container-1">
    <Blobs>
        <Blob>
            <Name>DogInCatTree.png</Name>
            <Properties><Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
            <Etag>0x8D52D5C4A4C96B0</Etag>
            <Content-Length>419416</Content-Length>
            <Content-Type>image/png</Content-Type>
            <Content-Encoding />
            <Content-Language />
            <Content-MD5 />
            <Cache-Control />
            <Content-Disposition />
            <BlobType>BlockBlob</BlobType>
            <LeaseStatus>unlocked</LeaseStatus>
            <LeaseState>available</LeaseState>
            <ServerEncrypted>true</ServerEncrypted>
            </Properties>
        </Blob>
        <Blob>
            <Name>GuyEyeingOreos.png</Name>
            <Properties>
                <Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
                <Etag>0x8D52D5C4A25A6F6</Etag>
                <Content-Length>167464</Content-Length>
                <Content-Type>image/png</Content-Type>
                <Content-Encoding />
                <Content-Language />
                <Content-MD5 />
                <Cache-Control />
                <Content-Disposition />
                <BlobType>BlockBlob</BlobType>
                <LeaseStatus>unlocked</LeaseStatus>
                <LeaseState>available</LeaseState>
                <ServerEncrypted>true</ServerEncrypted>
            </Properties>
            </Blob>
        </Blobs>
    <NextMarker />
</EnumerationResults>
```

## <a name="summary"></a>Sammanfattning

I den här artikeln beskrivs hur du gör en förfrågan till blob storage REST API för att hämta en lista över behållare eller en lista över blobbar i en behållare. Du också lära dig hur du skapar auktorisering signaturen för REST API-anrop, hur du använder i REST-begäran och hur du granskar svaret.

## <a name="next-steps"></a>Nästa steg

* [BLOB-tjänst REST-API](/rest/api/storageservices/blob-service-rest-api)
* [File Service REST-API](/rest/api/storageservices/file-service-rest-api)
* [Kön Service REST-API](/rest/api/storageservices/queue-service-rest-api)