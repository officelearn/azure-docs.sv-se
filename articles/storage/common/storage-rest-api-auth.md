---
title: Anropa REST API-åtgärder med auktorisering av delad nyckel
titleSuffix: Azure Storage
description: Använd AZURE Storage REST API för att göra en begäran till Blob-lagring med hjälp av auktorisering för delad nyckel.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5c6125b850062450516e7fc0b19c2e0d5d6f577
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916072"
---
# <a name="call-rest-api-operations-with-shared-key-authorization"></a>Anropa REST API-åtgärder med auktorisering av delad nyckel

Den här artikeln visar hur du anropar Azure Storage REST API:er, inklusive hur du bildar auktoriseringshuvudet. Det är skrivet ur synvinkel av en utvecklare som inte vet något om REST och ingen aning om hur man gör en REST samtal. När du har lärt dig hur du anropar en REST-åtgärd kan du utnyttja den här kunskapen för att använda andra Azure Storage REST-åtgärder.

## <a name="prerequisites"></a>Krav

Exempelprogrammet visar blob-behållarna för ett lagringskonto. Om du vill prova koden i den här artikeln behöver du följande:

- Installera [Visual Studio 2019](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) med **Azure-utvecklingsarbetsbelastningen.**

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

- Ett allmänt lagringskonto. Om du ännu inte har ett lagringskonto läser du [Skapa ett lagringskonto](storage-account-create.md).

- Exemplet i den här artikeln visar hur du listar behållarna i ett lagringskonto. Om du vill se utdata lägger du till några behållare för bloblagring i lagringskontot innan du börjar.

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

Exempelprogrammet är ett konsolprogram skrivet i C#.

Använd [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Det här kommandot klonar lagret till den lokala git-mappen. Om du vill öppna Visual Studio-lösningen letar du efter mappen storage-dotnet-rest-api-with-auth, öppnar den och dubbelklickar på StorageRestApiAuth.sln.

## <a name="about-rest"></a>Om REST

REST står för *representativ statlig överföring*. För en specifik definition, kolla in [Wikipedia](https://en.wikipedia.org/wiki/Representational_state_transfer).

REST är en arkitektur som gör att du kan interagera med en tjänst via ett internetprotokoll, till exempel HTTP/HTTPS. REST är oberoende av programvaran som körs på servern eller klienten. REST API kan anropas från alla plattformar som stöder HTTP/HTTPS. Du kan skriva ett program som körs på en Mac, Windows, Linux, en Android-telefon eller -surfplatta, iPhone, iPod eller webbplats och använda samma REST API för alla dessa plattformar.

Ett anrop till REST API består av en begäran, som görs av klienten, och ett svar, som returneras av tjänsten. I begäran skickar du en URL med information om vilken åtgärd du vill anropa, vilken resurs du ska agera på, eventuella frågeparametrar och rubriker och beroende på vilken åtgärd som anropades en nyttolast med data. Svaret från tjänsten innehåller en statuskod, en uppsättning svarshuvuden och beroende på vilken åtgärd som anropades, en nyttolast med data.

## <a name="about-the-sample-application"></a>Om exempelprogrammet

Exempelprogrammet visar behållarna i ett lagringskonto. När du förstår hur informationen i REST API-dokumentationen korrelerar med din faktiska kod, är andra REST-anrop lättare att räkna ut.

Om du tittar på [REST-API:et för Blob-tjänsten](/rest/api/storageservices/Blob-Service-REST-API)visas alla åtgärder som du kan utföra på blob-lagring. Lagringsklientbiblioteken är omslag runt REST-API:erna – de gör det enkelt för dig att komma åt lagring utan att använda REST-API:erna direkt. Men som nämnts ovan, ibland vill du använda REST API i stället för ett lagringsklientbibliotek.

## <a name="list-containers-operation"></a>Åtgärden Lista behållare

Granska referensen för [listcontainers-åtgärden.](/rest/api/storageservices/List-Containers2) Den här informationen hjälper dig att förstå var några av fälten kommer ifrån i begäran och svaret.

**Metod för begäran:** GET. Det här verbet är den HTTP-metod som du anger som egenskap för begärandeobjektet. Andra värden för det här verbet inkluderar HEAD, PUT och DELETE, beroende på vilket API du anropar.

**Begär URI**: `https://myaccount.blob.core.windows.net/?comp=list`.Begäran URI skapas från blob `http://myaccount.blob.core.windows.net` storage-kontoslutpunkten `/?comp=list`och resurssträngen .

[URI-parametrar](/rest/api/storageservices/List-Containers2#uri-parameters): Det finns ytterligare frågeparametrar som du kan använda när du anropar ListContainers. Ett par av dessa parametrar är *timeout* för samtalet (i sekunder) och *prefix*, som används för filtrering.

En annan användbar parameter är *maxresultat:* om fler behållare är tillgängliga än det här värdet innehåller svarstexten ett *NextMarker-element* som anger nästa behållare att returnera på nästa begäran. Om du vill använda den här funktionen anger du *NextMarker-värdet* som *markörparameter* i URI:n när du gör nästa begäran. När du använder den här funktionen är det analogt med att söka igenom resultaten.

Om du vill använda ytterligare parametrar lägger du till dem i resurssträngen med värdet, till exempel:

```
/?comp=list&timeout=60&maxresults=100
```

[Begäranden :](/rest/api/storageservices/List-Containers2#request-headers)**I** det här avsnittet visas obligatoriska och valfria begäranderubriker. Tre av rubrikerna krävs: ett *auktoriseringshuvud,* *x-ms-datum* (innehåller UTC-tiden för begäran) och *x-ms-version* (anger vilken version av REST API som ska användas). Att inkludera *x-ms-client-request-id* i rubrikerna är valfritt – du kan ange värdet för det här fältet till vad som helst. Det skrivs till lagringsanalysloggarna när loggning är aktiverat.

[Begärandetext:](/rest/api/storageservices/List-Containers2#request-body)**:** Det finns inget förfrågningsorgan för ListContainers. Request Body används på alla PUT-åtgärder när du överför blobbar, samt SetContainerAccessPolicy, som gör att du kan skicka in en XML-lista över lagrade åtkomstprinciper som ska tillämpas. Principer för lagrad åtkomst beskrivs i artikeln [Använda SAS (Shared Access Signatures).](storage-sas-overview.md)

[Svarsstatuskod:](/rest/api/storageservices/List-Containers2#status-code)**:** Berättar om alla statuskoder du behöver veta. I det här exemplet är en HTTP-statuskod på 200 ok. En fullständig lista över HTTP-statuskoder finns i [Statuskoddefinitioner](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Information om hur du ser felkoder som är specifika för API:erna för lagrings-REST finns i [Vanliga REST API-felkoder](/rest/api/storageservices/common-rest-api-error-codes)

[Svarsrubriker:](/rest/api/storageservices/List-Containers2#response-headers)**:** Dessa inkluderar *innehållstyp*; *x-ms-request-id*, som är det begärande-ID som du skickade in. *x-ms-version*, som anger vilken version av Blob-tjänsten som används. och *datum ,* som är i UTC och talar om vilken tid begäran gjordes.

[Svarstext:](/rest/api/storageservices/List-Containers2#response-body)Det här fältet är en XML-struktur som tillhandahåller de begärda data. I det här exemplet är svaret en lista över behållare och deras egenskaper.

## <a name="creating-the-rest-request"></a>Skapa REST-begäran

För säkerhet när du kör i produktion, använd alltid HTTPS i stället för HTTP. I den här övningen bör du använda HTTP så att du kan visa begäran och svarsdata. Om du vill visa information om begäran och svar i själva REST-anropen kan du hämta [Fiddler](https://www.telerik.com/fiddler) eller ett liknande program. I Visual Studio-lösningen är lagringskontots namn och nyckel hårdkodade i klassen. ListContainersAsyncREST-metoden skickar lagringskontots namn och lagringskontonyckel till de metoder som används för att skapa de olika komponenterna i REST-begäran. I ett verkligt program skulle lagringskontots namn och nyckel finnas i en konfigurationsfil, miljövariabler eller hämtas från ett Azure Key Vault.

I vårt exempelprojekt finns koden för att skapa auktoriseringshuvudet i en separat klass. Tanken är att du kan ta hela klassen och lägga till den i din egen lösning och använda den "som den är." Koden för auktoriseringshuvudet fungerar för de flesta REST API-anrop till Azure Storage.

Om du vill skapa begäran, som är ett HttpRequestMessage-objekt, går du till ListContainersAsyncREST i Program.cs. Stegen för att skapa begäran är:

- Skapa URI som ska användas för att anropa tjänsten.
- Skapa HttpRequestMessage-objektet och ange nyttolasten. Nyttolasten är null för ListContainersAsyncREST eftersom vi inte passerar något i.
- Lägg till begäranden för x-ms-date och x-ms-version.
- Hämta auktoriseringshuvudet och lägg till det.

Några grundläggande information du behöver:

- För ListContainers **method** är `GET`metoden . Det här värdet ställs in när begäran instansieras.
- **Resursen** är frågedelen av URI:n som anger vilket API `/?comp=list`som anropas, så värdet är . Som nämnts tidigare finns resursen på referensdokumentationssidan som visar informationen om [ListContainers API](/rest/api/storageservices/List-Containers2).
- URI-uri:n skapas genom att skapa Blob-tjänstslutpunkten för det lagringskontot och sammanfoga resursen. Värdet för **uri för** `http://contosorest.blob.core.windows.net/?comp=list`begäran blir .
- För ListContainers är **requestBody** null och det finns inga extra **rubriker**.

Olika API:er kan ha andra parametrar att skicka in, till exempel *ifMatch*. Ett exempel på var du kan använda ifMatch är när du ringer PutBlob. I så fall ställer du in ifMatch till en eTag och uppdaterar bara blobben om den eTag du anger matchar den aktuella eTag-taggen på blobben. Om någon annan har uppdaterat blobben sedan du hämtade eTag åsidosätts inte ändringen.

Ställ först `uri` in `payload`och .

```csharp
// Construct the URI. It will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Provide the appropriate payload, in this case null.
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Därefter instansierar du begäran, ställer `GET` in metoden på och tillhandahåller URI.Next, instantiate the request, setting the method to and providing the URI.

```csharp
// Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Lägg till förfrånaden för `x-ms-date` och `x-ms-version`. Den här platsen i koden är också där du lägger till eventuella ytterligare begäranden som krävs för samtalet. I det här exemplet finns det inga ytterligare rubriker. Ett exempel på ett API som passerar i extra rubriker är åtgärden Ange behållare ACL. Det här API-anropet lägger till ett huvud som heter "x-ms-blob-public-access" och värdet för åtkomstnivån.

```csharp
// Add the request headers for x-ms-date and x-ms-version.
DateTime now = DateTime.UtcNow;
httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
// If you need any additional headers, add them here before creating
//   the authorization header.
```

Anropa metoden som skapar auktoriseringshuvudet och lägg till det i begäranhuvudena. Du ser hur du skapar auktoriseringshuvudet senare i artikeln. Metodnamnet är GetAuthorizationHeader, som du kan se i det här kodavsnittet:

```csharp
// Get the authorization header and add it.
httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
    storageAccountName, storageAccountKey, now, httpRequestMessage);
```

Nu `httpRequestMessage` innehåller rest-begäran komplett med auktoriseringshuvudena.

## <a name="send-the-request"></a>Skicka begäran

Nu när du har konstruerat begäran kan du anropa SendAsync-metoden för att skicka den till Azure Storage. Kontrollera att värdet för svarsstatuskoden är 200, vilket innebär att åtgärden har slutförts. Därefter tolkar du svaret. I det här fallet får du en XML-lista över behållare. Låt oss titta på koden för att anropa GetRESTRequest-metoden för att skapa begäran, köra begäran och sedan undersöka svaret för listan över behållare.

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

Om du kör en nätverkssniffare som [Fiddler](https://www.telerik.com/fiddler) när du ringer samtalet till SendAsync kan du se information om begäran och svar. Vi tar oss en titt. Namnet på *lagringskontot är contosorest*.

**Begäran:**

```
GET /?comp=list HTTP/1.1
```

**Begär rubriker:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Statuskod och svarshuvuden som returneras efter körning:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Svarstext (XML):** För åtgärden Listbehållare visas listan över behållare och deras egenskaper.

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

Nu när du förstår hur du skapar begäran, anropar tjänsten och tolkar resultatet ska vi se hur du skapar auktoriseringshuvudet. Det är komplicerat att skapa den rubriken, men den goda nyheten är att när du har koden fungerar, fungerar det för alla REST-API:er för lagringstjänsten.

## <a name="creating-the-authorization-header"></a>Skapa auktoriseringshuvudet

> [!TIP]
> Azure Storage stöder nu Azure Active Directory (Azure AD) integration för blobbar och köer. Azure AD erbjuder en mycket enklare upplevelse för att godkänna en begäran till Azure Storage. Mer information om hur du använder Azure AD för att auktorisera REST-åtgärder finns [i Auktorisera med Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory). En översikt över Azure AD-integrering med Azure Storage finns [i Autentisera åtkomst till Azure Storage med Azure Active Directory](storage-auth-aad.md).

Det finns en artikel som förklarar begreppsmässigt (ingen kod) hur du [auktoriserar begäranden till Azure Storage](/rest/api/storageservices/authorize-requests-to-azure-storage).

Låt oss destillera den artikeln ner till exakt behövs och visa koden.

Använd först auktorisering för delad nyckel. Formatet för auktoriseringshuvudet ser ut så här:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Signaturfältet är en Hash-baserad meddelandeautentiseringskod (HMAC) som skapats från begäran och beräknas med hjälp av SHA256-algoritmen och sedan kodas med Base64-kodning. Fattar du det? (Håll ut, du har inte ens hört ordet *kanonicalized* ännu.)

Det här kodavsnittet visar formatet för signatursträngen delad nyckel:

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

De flesta av dessa fält används sällan. För Blob-lagring anger du VERB, md5, innehållslängd, kanoniska rubriker och kanonisk resurs. Du kan lämna de andra tomma `\n` (men sätta i så att den vet att de är tomma).

Vad är CanonicalizedHeaders och CanonicalizedResource? Bra fråga! I själva verket, vad betyder kanonicalized? Det känns inte ens igen som ett ord. Här är vad [Wikipedia säger om kanonisering:](https://en.wikipedia.org/wiki/Canonicalization) *I datavetenskap, kanoniskisering (ibland standardisering eller normalisering) är en process för att konvertera data som har mer än en möjlig representation till en "standard", "normal" eller kanonisk form.* I normal-tala, detta innebär att ta listan över objekt (till exempel rubriker i fråga om Canonicalized Headers) och standardisera dem till ett obligatoriskt format. I grund och botten beslutade Microsoft om ett format och du måste matcha den.

Låt oss börja med dessa två kanoniska fält, eftersom de krävs för att skapa auktoriseringshuvudet.

### <a name="canonicalized-headers"></a>Kanoniska rubriker

Om du vill skapa det här värdet hämtar du rubrikerna som börjar med "x-ms-" och sorterar dem och formaterar dem sedan i en sträng med `[key:value\n]` instanser som sammanfogas till en sträng. I det här exemplet ser de kanonicaliserade rubrikerna ut så här:

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Här är koden som används för att skapa utdata:

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

### <a name="canonicalized-resource"></a>Kanonisk resurs

Den här delen av signatursträngen representerar lagringskontot som begäran riktar sig till. Kom ihåg att URI för begäran `<http://contosorest.blob.core.windows.net/?comp=list>`är`contosorest` , med det faktiska kontonamnet (i det här fallet). I det här exemplet returneras detta:

```
/contosorest/\ncomp:list
```

Om du har frågeparametrar innehåller det här exemplet även dessa parametrar. Här är koden, som också hanterar ytterligare frågeparametrar och frågeparametrar med flera värden. Kom ihåg att du skapar den här koden för att fungera för alla REST-API:er. Du vill inkludera alla möjligheter, även om listcontainers-metoden inte behöver dem alla.

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
        sb.Append('\n').Append(item.ToLower()).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Nu när de kanonicaliserade strängarna är inställda, låt oss titta på hur du skapar auktoriseringshuvudet själv. Du börjar med att skapa en sträng med meddelandesignaturen i formatet StringToSign som tidigare visats i den här artikeln. Det här konceptet är lättare att förklara med hjälp av kommentarer i koden, så här är det den slutliga metoden som returnerar auktoriseringshuvudet:

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
        storageAccountName + ":" + signature);
    return authHV;
}
```

När du kör den här koden ser den resulterande MessageSignature ut så här:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Här är det slutliga värdet för AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

AuthorizationHeader är det sista huvudet som placeras i begäranhuvudena innan svaret bokförs.

Det täcker allt du behöver veta för att sätta ihop en klass som du kan skapa en begäran om att anropa REST-API:erna för lagringstjänster.

## <a name="example-list-blobs"></a>Exempel: Listblobar

Låt oss titta på hur du ändrar koden för att anropa listblobar för *behållaren container-1*. Den här koden är nästan identisk med koden för att lista behållare, de enda skillnaderna är URI och hur du tolkar svaret.

Om du tittar på referensdokumentationen för [ListBlobs upptäcker](/rest/api/storageservices/List-Blobs)du att metoden är *GET* och RequestURI är:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

I ListContainersAsyncREST ändrar du koden som anger URI till API:et för ListBlobs. Behållarnamnet är **container-1**.

```csharp
String uri =
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Sedan där du hanterar svaret, ändra koden för att leta efter blobbar i stället för behållare.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

När du kör det här exemplet får du resultat som följande:

**Kanoniska rubriker:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Kanonisk resurs:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**Meddelandesignatur:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**Auktoriseringshuvud:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

Följande värden är från [Fiddler:](https://www.telerik.com/fiddler)

**Begäran:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Begär rubriker:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Statuskod och svarshuvuden som returneras efter körning:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Svarstext (XML):** Det här XML-svaret visar listan över blobbar och deras egenskaper.

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

I den här artikeln fick du lära dig hur du gör en begäran till REST-API:et för bloblagring. Med begäran kan du hämta en lista över behållare eller en lista med blobbar i en behållare. Du har lärt dig hur du skapar auktoriseringssignaturen för REST API-anropet och hur du använder den i REST-begäran. Slutligen lärde du dig att undersöka svaret.

## <a name="next-steps"></a>Nästa steg

- [Blob-tjänstens REST-API](/rest/api/storageservices/blob-service-rest-api)
- [Fil-tjänstens REST-API](/rest/api/storageservices/file-service-rest-api)
- [Kö-tjänstens REST-API](/rest/api/storageservices/queue-service-rest-api)
- [Tabelltjänstens REST-API](/rest/api/storageservices/table-service-rest-api)
