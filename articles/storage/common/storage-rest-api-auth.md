---
title: Anropa Azure Storage Services REST API-åtgärder, inklusive autentisering | Microsoft Docs
description: Anropa Azure Storage Services REST API-åtgärder, inklusive autentisering
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 3018eb1dd968cdef7d972351aad656ea60695c65
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141183"
---
# <a name="using-the-azure-storage-rest-api"></a>Använda Azure Storage REST API

Den här artikeln visar hur du använder den Blob Storage Service REST API: er och hur du autentiserar anropet till tjänsten. Den skrivs utifrån perspektivet för någon som vet ingenting om REST och aning hur du gör ett REST-anrop, men är en utvecklare. Vi tittar på referensdokumentation för REST-anrop och se hur att översätta det till ett faktiska REST-anrop – vilka fält som gå var? När du lära dig hur du ställer in ett REST-anrop, kan du använda den här kunskapen om du vill använda någon av de andra Storage Service REST API: er.

## <a name="prerequisites"></a>Nödvändiga komponenter 

Programmet visas behållare i blob-lagring för ett lagringskonto. Om du vill testa koden i den här artikeln behöver du följande objekt: 

* Installera [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) med följande arbetsbelastning:
    - Azure Development

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Ett allmänt lagringskonto. Om du ännu inte har ett lagringskonto kan du läsa [skapa ett lagringskonto](storage-quickstart-create-account.md).

* I exemplet i den här artikeln visar hur du listar behållare i ett lagringskonto. Lägga till vissa behållare till blob storage i storage-konto innan du startar om du vill se utdata.

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

Exempelprogrammet är ett konsolprogram som skrivits i C#.

Använd [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Det här kommandot klonar lagret till den lokala git-mappen. Öppna Visual Studio-lösningen genom att leta efter mappen storage-dotnet-rest-api-with-auth, öppna den och dubbelklicka på StorageRestApiAuth.sln. 

## <a name="what-is-rest"></a>Vad är REST?

REST-innebär *representational tillstånd överföring*. Kolla in för en viss definition [Wikipedia](https://en.wikipedia.org/wiki/Representational_state_transfer).

I princip REST är en arkitektur som du kan använda när du anropar API: er eller genom att göra tillgängliga för att anropa API: er. Det är oberoende av vad som händer på endera sidan och vilka andra program som används när du skickade eller tog emot RESTEN-anrop. Du kan skriva ett program som körs på en Mac, Windows, Linux, Android-telefon eller surfplatta, iPhone, iPod eller webbplatsen och använder samma REST API för alla dessa plattformar. Data kan skickas i och/eller ut när REST API anropas. REST API hand inte från vilken plattform som kallas – det viktiga är den information som angavs i begäran och de data som anges i svaret.

Att veta hur du använder REST är en användbar färdighet. Azure produktteamet släpper ofta nya funktioner. Många gånger de nya funktionerna kan nås genom REST-gränssnittet, men ännu inte har visas via **alla** storage-klientbibliotek eller Användargränssnittet (till exempel Azure portal). Om du alltid vill använda det senaste och bästa learning REST är ett krav. Om du vill skriva egna bibliotek för att interagera med Azure Storage, eller om du vill komma åt Azure Storage med ett programmeringsspråk som inte har ett SDK eller storage-klientbiblioteket, kan du använda REST-API.

## <a name="about-the-sample-application"></a>Om exempelprogrammet

Exempelprogrammet visar behållarna i ett lagringskonto. När du förstår hur informationen i REST API-dokumentationen motsvarar dina faktiska kod kan är andra REST-anrop lättare att ta reda på. 

Om du tittar på den [REST-API för Blob Service](/rest/api/storageservices/Blob-Service-REST-API), visas alla de åtgärder som du kan utföra på blob-lagring. Lagringsklientbiblioteken är omslutningar runt REST-API: er – de gör det enkelt för dig att komma åt lagringsutrymme utan att använda REST-API: er direkt. Men ovanstående du vill ibland använda REST-API i stället för ett storage-klientbiblioteket.

## <a name="rest-api-reference-list-containers-api"></a>REST API-referens: Lista behållare API

Låt oss titta på sidan i REST API-referens för den [ListContainers](/rest/api/storageservices/List-Containers2) igen så att du förstå några av fälten varifrån i begäran och svaret i nästa avsnitt med kod.

**Begärandemetod**: HÄMTA. Den här verbet är HTTP-metod som du anger som en egenskap för Begäranobjektet. Andra värden för den här verb är HEAD, PUT och DELETE, beroende på du anropar API: et.

**Begärande-URI**: https://myaccount.blob.core.windows.net/?comp=list  Det här skapas från blob storage-konto-slutpunkten `http://myaccount.blob.core.windows.net` och resurssträngen `/?comp=list`.

[URI-parametrar](/rest/api/storageservices/List-Containers2#uri-parameters): Det finns ytterligare frågeparametrar som du kan använda när du anropar ListContainers. Några av parametrarna är *timeout* för anrop (i sekunder) och *prefix*, som används för filtrering.

En annan bra parametern är *maxresults:* om fler behållare är tillgängliga än det här värdet, svarstexten innehåller en *NextMarker* element som anger nästa behållaren ska returneras vid nästa begäran. Om du vill använda denna funktion kan du ange den *NextMarker* värdet som den *markör* parameter i URI: N när du gör nästa begäran. Det är detsamma som sidindelning igenom resultat som när du använder den här funktionen. 

Om du vill använda ytterligare parametrar, lägger du till dem till resurssträngen med värde, som i följande exempel:

```
/?comp=list&timeout=60&maxresults=100
```

[Begärandehuvuden](/rest/api/storageservices/List-Containers2#request-headers)**:** Det här avsnittet innehåller obligatoriska och valfria begärandehuvuden. Tre av rubrikerna som krävs: en *auktorisering* rubrik, *x-ms-date* (innehåller UTC-tid för begäran), och *x-ms-version* (anger vilken version av RESTEN API för användning). Inklusive *x-ms-client-request-id* i sidhuvud är valfritt – du kan ange värdet för det här fältet till något; de skrivs till loggarna storage analytics när loggning är aktiverat.

[Brödtext i begäran](/rest/api/storageservices/List-Containers2#request-body)**:** Det finns inga begärandetexten för ListContainers. Begärandetexten används på alla PUT-åtgärder när du överför blobbar, samt SetContainerAccessPolicy, vilket gör att du skickar in en XML-lista över åtkomstprinciper har lagrats tillämpas. Åtkomstprinciper har lagrats beskrivs i artikeln [med signaturer för delad åtkomst (SAS)](storage-dotnet-shared-access-signature-part-1.md).

[Svarets statuskod](/rest/api/storageservices/List-Containers2#status-code)**:** Talar om för alla statuskoder som du behöver veta. I det här exemplet är en HTTP-statuskod 200 ok. En fullständig lista över HTTP-statuskoder finns [statuskoddefinitioner](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Felkoder som är specifika för Storage REST API: er finns i [vanliga REST API-felkoder](/rest/api/storageservices/common-rest-api-error-codes)

[Svarshuvuden](/rest/api/storageservices/List-Containers2#response-headers)**:** Dessa inkluderar *innehållstyp*; *x-ms-request-id* (begäran-id du löpt ut, om tillämpligt). *x-ms-version* (anger versionen av den Blob-tjänst som används), och *datum* (UTC, anges vilken tid en begäran har gjorts).

[Svarstexten](/rest/api/storageservices/List-Containers2#response-body): Det här fältet är en XML-struktur som tillhandahåller data som begärs. I det här exemplet är svaret en lista över behållare och deras egenskaper.

## <a name="creating-the-rest-request"></a>Skapar en REST-förfrågan

Några anmärkningar innan du startar – för säkerhet vid körning i produktion kan alltid använda HTTPS snarare än HTTP. För den här övningen, bör du använda HTTP så att du kan visa data för begäran och svar. Om du vill visa informationen om begäran och svar i de faktiska REST-anrop, kan du ladda ned [Fiddler](https://www.telerik.com/fiddler) eller ett liknande program. I Visual Studio-lösningen lagringskontonamn och nyckel är hårdkodad i klassen och metoden ListContainersAsyncREST skickar lagringskontonamn och lagringskontonyckel till de metoder som används för att skapa de olika komponenterna i REST-begäran . I en verklig tillämpning lagringskontonamnet och nyckeln skulle finnas i en konfigurationsfil, miljövariabler, eller hämtas från ett Azure Key Vault.

I vår exempelprojektet är koden för att skapa auktoriseringsrubriken i en separat klass, med avsikt att kan du ta hela klassen och lägga till den i din egen lösning och använda den ”i befintligt skick”. Huvudet auktoriseringskod fungerar för de flesta REST API-anrop till Azure Storage.

Om du vill skapa den begäran som är ett HttpRequestMessage-objekt, går du till ListContainersAsyncREST i Program.cs. Stegen för att skapa begäran är: 

* Skapa URI som ska användas för att anropa tjänsten. 
* Skapa HttpRequestMessage-objekt och ange nyttolasten. Nyttolasten är null för ListContainersAsyncREST eftersom vi inte skicka något i.
* Lägg till begärandehuvuden för x-ms-date- och x-ms-version.
* Få auktoriseringsrubriken och lägga till den.

Grundläggande information du behöver: 

*  För ListContainers, den **metoden** är `GET`. Det här värdet anges när instanser skapades av begäran. 
*  Den **resource** är frågan delen av den URI som anger vilken API anropas så är värdet `/?comp=list`. Som tidigare nämnts resursen finns på sidan referens dokumentation som visar information om den [ListContainers API](/rest/api/storageservices/List-Containers2).
*  URI: N skapas genom att skapa Blob service-slutpunkt för det lagringskontot och sammanfoga resursen. Värdet för **begärande-URI** identisk som `http://contosorest.blob.core.windows.net/?comp=list`.
*  För ListContainers, **requestBody** är null och det finns inga extra **rubriker**.

Olika API: er kan ha andra parametrar för att skicka in som *ifMatch*. Ett exempel på där du kan använda ifMatch är när du anropar PutBlob. I så fall kan du ange ifMatch till en eTag och blob uppdateras endast om du anger eTag matchar aktuell eTag för blobben. Om någon annan har uppdaterats blob sedan hämtar eTag, åsidosättas sina ändringar inte. 

Ställ först in den `uri` och `payload`. 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Därefter skapa en instans av begäran, ställa in metoden `GET` och ange URI: N.

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Lägg till begärandehuvuden för x-ms-date- och x-ms-version. Denna plats i koden är också där du lägger till eventuella ytterligare begärandehuvuden som krävs för anropet. Det finns inga ytterligare rubriker i det här exemplet. Ett exempel på ett API som skickar extra meddelandehuvuden är SetContainerACL. Den lägger till en rubrik som heter ”x-ms-blob-public-åtkomst” och värdet för åtkomstnivå för Blob storage.

```csharp
    // Add the request headers for x-ms-date and x-ms-version.
    DateTime now = DateTime.UtcNow;
    httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
    httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
    // If you need any additional headers, add them here before creating
    //   the authorization header. 
```

Anropa metoden som skapar auktoriseringsrubriken och lägga till den i de begärda rubrikerna. Du lär dig att skapa auktoriseringsrubriken senare i artikeln. Metodnamnet är GetAuthorizationHeader som du ser i det här kodfragmentet:

```csharp
    // Get the authorization header and add it.
    httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
        storageAccountName, storageAccountKey, now, httpRequestMessage);
```

Nu `httpRequestMessage` innehåller REST-begäran med auktorisering rubriker. 

## <a name="call-the-rest-api-with-the-request"></a>Anropa REST-API med begäran

Nu när du har begäran kan du anropa SendAsync för att skicka REST-begäran. SendAsync anropar API: et och får svaret tillbaka. Granska svaret StatusCode (200 är OK), parsa svaret. I det här fallet kan du hämta ett XML-lista över behållare. Nu ska vi titta på koden för att anropa metoden GetRESTRequest för att skapa begäran, utföra begäran och sedan granska svaret för listan över behållare.

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

Om du kör en nätverksavsökare som [Fiddler](https://www.telerik.com/fiddler) vid anrop till SendAsync måste du se informationen om begäran och svar. Låt oss ta en titt. Namnet på lagringskontot är *contosorest*.

**Begäran:**

```
GET /?comp=list HTTP/1.1
```

**Rubriker i begäran:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Status och svarshuvuden returnerades efter körning:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Svarstext (XML):** Detta visar listan över behållare och deras egenskaper för ListContainers.

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

Nu när du vet hur du skapar begäran, anropar tjänsten och tolka resultaten kan du nu ska vi se hur du skapar auktoriseringsrubriken. Det är komplicerat att skapa den rubriken, men den goda nyheten är att när du har du koden fungerar och den fungerar för alla Storage Service REST API: er.

## <a name="creating-the-authorization-header"></a>Skapa auktoriseringsrubriken

> [!TIP]
> Azure Storage stöder nu integrering med Azure Active Directory (Azure AD) för blobbar och köer. Azure AD erbjuder en mycket enklare upplevelse för att auktorisera en begäran till Azure Storage. Läs mer om hur du använder Azure AD för att auktorisera REST-åtgärder, [autentisera med Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory). En översikt över Azure AD-integrering med Azure Storage finns i [autentisera åtkomsten till Azure Storage med Azure Active Directory](storage-auth-aad.md).

Det finns en artikel som förklarar begreppsmässigt (ingen kod) hur du utför [autentisering för Azure Storage-tjänster](/rest/api/storageservices/Authorization-for-the-Azure-Storage-Services).
Nu ska vi hämta innehållsrik artikeln ned till exakt behövs och visa koden.

Använd först en autentisering med delad nyckel. Auktorisering-huvudformat ser ut så här:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Signaturfältet är en hashbaserad meddelandeautentiseringskod (HMAC) som skapats från begäran och beräknas med SHA256-algoritmen och sedan kodad med Base64-kodning. Har du som? (Lägg där, ännu inte har du hört ordet *kanoniseras* ännu.)

Det här kodstycket visar formatet för strängen som signatur för delad nyckel:

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

De flesta av de här fälten används sällan. För Blob-lagring anger du VERB, md5, innehållslängd, Kanoniseras rubriker och Kanoniseras resurs. Du kan lämna de andra tomt (men placera i den `\n` så att den vet att de är tom).

Vad är CanonicalizedHeaders och CanonicalizedResource? Bra fråga. I själva verket vad gör kanoniseras medelvärdet? Inte ens att känna igen det som ett ord Microsoft Word. Här är vad [Wikipedia säger om auktorisering](https://en.wikipedia.org/wiki/Canonicalization): *I datavetenskap är auktorisering (ibland standardisering eller normalisering) en process för att konvertera data som har mer än en möjlig återgivning till en ”standard”, ”normal” eller kanoniska form.* I normal talar, det innebär att listan över objekt (till exempel rubriker när det gäller Kanoniseras huvuden) och standardisera dem i ett format som krävs. I praktiken, Microsoft valt ett format och du behöver för matchning.

Låt oss börja med dessa två av kanoniserade fält, eftersom de behövs för att skapa auktoriseringsrubriken.

**Av kanoniserade rubriker**

Hämta sidhuvuden som börjar med ”x - ms-” och sortera dem för att skapa det här värdet, och sedan formatera dem till en sträng med `[key:value\n]` instanser, sammanfogas till en sträng. I det här exemplet av kanoniserade rubrikerna se ut så här: 

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

    StringBuilder headersBuilder = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. https://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        headersBuilder.Append(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValue in kvp.Value)
        {
            string trimmedValue = headerValue.TrimStart().Replace("\r\n", string.Empty);
            headersBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used
            // if there are multiple values for one of the headers.
            separator = ',';
        }

        headersBuilder.Append("\n");
    }

    return headersBuilder.ToString();
}
```

**Av kanoniserade resurs**

Den här delen av signatur-strängen representerar det lagringskonto som mål för begäran. Kom ihåg att begäran-URI är `<http://contosorest.blob.core.windows.net/?comp=list>`, med faktiska kontonamn (`contosorest` i det här fallet). I det här exemplet returneras:

```
/contosorest/\ncomp:list
```

Om du har frågeparametrar kan inkluderar detta de också. Här är den kod som hanterar också ytterligare frågeparametrar och frågeparametrar med flera värden. Kom ihåg att du skapar den här koden ska fungera för alla REST API: er, så att du vill inkludera alla möjligheter, även om metoden ListContainers inte behöver alla.

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

Nu när av kanoniserade strängar är inställda vi titta på hur du skapar auktoriseringsrubriken själva. Börja med att skapa en sträng med meddelandesignaturen i formatet StringToSign tidigare visas i den här artikeln. Det här konceptet är enklare för att förklara med kommentarer i koden, så det är, den sista metoden som returnerar Auktoriseringsrubriken:

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

Här är det slutliga värdet för AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

AuthorizationHeader är rubriken senaste placeras i huvudena för begäran innan du publicerar svaret.

Som täcker allt du behöver veta tillsammans med koden, sätta ihop en klass som du kan använda för att skapa en begäran som ska användas för att anropa Storage Services REST API: er.

## <a name="how-about-another-example"></a>Vad sägs om ett annat exempel? 

Låt oss titta på hur du ändrar koden för att anropa ListBlobs för behållaren *container-1*. Det här är nästan identisk i koden för att lista behållare, endast skillnaderna är URI: N och hur du parsa svaret. 

Om du tittar på referensdokumentation för [ListBlobs](/rest/api/storageservices/List-Blobs), du tycker att metoden är *hämta* och RequestURI är:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

I ListContainersAsyncREST, ändrar du koden som anger URI för API: t för ListBlobs. Behållarens namn är **container-1**.

```csharp
String uri = 
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Sedan där du kan hantera svaret, ändra koden för att leta efter blobar i stället för behållare.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

När du kör det här exemplet ger resultat som liknar följande:

**Av kanoniserade rubriker:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Av kanoniserade resursen:**

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

Följande värden är från [Fiddler](https://www.telerik.com/fiddler):

**Begäran:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Rubriker i begäran:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Status och svarshuvuden returnerades efter körning:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Svarstext (XML):** Den här XML-svaret visar en lista över BLOB-objekt och deras egenskaper. 

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

I den här artikeln har du lärt dig hur du gör en begäran till blob storage REST API för att hämta en lista över behållare eller en lista över blobarna i en behållare. Du också lärt dig hur du skapar auktorisering signaturen för REST API-anrop, hur du använder den i REST-begäran och så här undersöker du svaret.

## <a name="next-steps"></a>Nästa steg

* [REST API för BLOB Service](/rest/api/storageservices/blob-service-rest-api)
* [File Service REST API](/rest/api/storageservices/file-service-rest-api)
* [REST API för kötjänst](/rest/api/storageservices/queue-service-rest-api)
