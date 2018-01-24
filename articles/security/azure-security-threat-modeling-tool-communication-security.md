---
title: Kommunikation Security - verktyget Microsoft Threat modellering - Azure | Microsoft Docs
description: "ändringar för hot som exponeras i verktyget Modeling hot"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 71bbe53595f2afab50d6220f335d615ada957a85
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="security-frame-communication-security--mitigations"></a>Säkerhet ram: KOMMUNIKATIONSSÄKERHET | Åtgärder 
| Produkter eller tjänster | Artikel |
| --------------- | ------- |
| **Azure Event Hub** | <ul><li>[Säker kommunikation till Händelsehubb med hjälp av SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Kontrollera-tjänstkontot och kontrollera att den anpassade Services eller ASP.NET-sidor respekterar CRM-säkerhet](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Använda Data management gateway vid anslutning på lokal SQL Server till Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Identity Server** | <ul><li>[Se till att all trafik till Identity Server är över HTTPS-anslutning](#identity-https)</li></ul> |
| **Webbprogram** | <ul><li>[Verifiera X.509 certifikat som används för att autentisera SSL, TLS och DTLS anslutningar](#x509-ssltls)</li><li>[Konfigurera SSL-certifikat för den anpassade domänen i Azure App Service](#ssl-appservice)</li><li>[Tvinga all trafik till Azure App Service via HTTPS-anslutning](#appservice-https)</li><li>[Aktivera HTTP strikt transportsäkerhet (HSTS)](#http-hsts)</li></ul> |
| **Databas** | <ul><li>[Se till att SQL server-kryptering och certifikat anslutningsverifiering](#sqlserver-validation)</li><li>[Tvinga krypterad kommunikation till SQLServer](#encrypted-sqlserver)</li></ul> |
| **Azure Storage** | <ul><li>[Se till att kommunikation till Azure Storage är över HTTPS](#comm-storage)</li><li>[Validera MD5-hash när du har hämtat blob om det inte går att aktivera HTTPS](#md5-https)</li><li>[Använd kompatibel SMB 3.0-klienten för att säkerställa transit datakryptering till Azure-filresurser](#smb-shares)</li></ul> |
| **Mobil klient** | <ul><li>[Implementera fästa certifikat](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Aktivera HTTPS - Secure Transportkanalen](#https-transport)</li><li>[WCF: Meddelandesäkerhet ange skyddsnivån på EncryptAndSign](#message-protection)</li><li>[WCF: Använd ett konto med lägsta behörighet för att köra WCF-tjänst](#least-account-wcf)</li></ul> |
| **Webb-API** | <ul><li>[Tvinga all trafik till webb-API: er via HTTPS-anslutning](#webapi-https)</li></ul> |
| **Azure Redis Cache** | <ul><li>[Se till att kommunikation till Azure Redis-Cache är via SSL](#redis-ssl)</li></ul> |
| **Fältet för IoT-Gateway** | <ul><li>[Skydda enhet till fältet Gateway-kommunikation](#device-field)</li></ul> |
| **Gateway för IoT-moln** | <ul><li>[Skydda enhet till Molngateway kommunikation via SSL/TLS](#device-cloud)</li></ul> |

## <a id="comm-ssltls"></a>Säker kommunikation till Händelsehubb med hjälp av SSL/TLS

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Event Hub | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Autentisering och säkerhet modellen översikt över Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Steg** | Skydda AMQP eller HTTP-anslutningar till Händelsehubb med hjälp av SSL/TLS |

## <a id="priv-aspnet"></a>Kontrollera-tjänstkontot och kontrollera att den anpassade Services eller ASP.NET-sidor respekterar CRM-säkerhet

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Kontrollera-tjänstkontot och kontrollera att den anpassade Services eller ASP.NET-sidor respekterar CRM-säkerhet |

## <a id="sqlserver-factory"></a>Använda Data management gateway vid anslutning på lokal SQL Server till Azure Data Factory

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Data Factory | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Länkade tjänsttyper - Azure och på lokal |
| **Referenser**              |[Flytta data mellan för lokal och Azure Data Factory](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway), [Data management gateway](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **Steg** | <p>Verktyget Data Management Gateway (DMG) krävs för att ansluta till datakällor som är skyddade bakom corpnet eller brandvägg.</p><ol><li>Låsa datorn isolerar DMG-verktyget och förhindrar att felaktiga program från att skadas eller snooping på källdatorn data. (T.ex.) senaste uppdateringar måste installeras, aktivera minsta nödvändiga portar kontrollerade konton allokering granskning aktiverad disk Aktivera kryptering osv.)</li><li>Data Gateway-nyckeln måste roteras med återkommande intervall eller när du förnyar DMG tjänstkontots lösenord</li><li>Data eltransit via länken Service måste krypteras</li></ol> |

## <a id="identity-https"></a>Se till att all trafik till Identity Server är över HTTPS-anslutning

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identity Server | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [IdentityServer3 - nycklar, signaturer och kryptografi](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 - distribution](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Steg** | Som standard måste IdentityServer alla inkommande anslutningar för att komma över HTTPS. Det är absolut nödvändigt att kommunikation med IdentityServer görs över skyddade transporter. Det finns vissa scenarier för distribution som SSL-avlastning där detta krav kan mjukas upp. Se sidan Identity Server-distribution i referenser till mer information. |

## <a id="x509-ssltls"></a>Verifiera X.509 certifikat som används för att autentisera SSL, TLS och DTLS anslutningar

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Program som använder SSL, TLS och DTLS Kontrollera fullständigt X.509-certifikat för de entiteter som de ansluter till. Detta inkluderar verifiering av certifikat:</p><ul><li>Domännamn</li><li>Giltighetsdatum (både början och förfallodatum datum)</li><li>Återställningsstatus</li><li>Användning (exempelvis serverautentisering för servrar, klientautentisering för klienter)</li><li>Förtroende kedjan. Certifikat måste kedja till en rotcertifikatutfärdare (CA) som är betrodda av plattformen eller uttryckligen har konfigurerats av administratören</li><li>Nyckellängden på certifikatets offentliga nyckel måste vara > 2 048 bitar</li><li>Hash-algoritmen måste vara SHA256 och senare |

## <a id="ssl-appservice"></a>Konfigurera SSL-certifikat för den anpassade domänen i Azure App Service

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | EnvironmentType – Azure |
| **Referenser**              | [Aktivera HTTPS för en app i Azure App Service](../app-service/app-service-web-tutorial-custom-ssl.md) |
| **Steg** | Som standard redan kan HTTPS för varje app med ett jokerteckencertifikat för den *. azurewebsites.net domän. Precis som alla Jokerteckendomäner det är dock inte lika säker som använder en anpassad domän med egna certifikat [finns](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Det rekommenderas att aktivera SSL för den anpassade domänen som den distribuerade appen som kan nås via|

## <a id="appservice-https"></a>Tvinga all trafik till Azure App Service via HTTPS-anslutning

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | EnvironmentType – Azure |
| **Referenser**              | [Använda HTTPS i Azure Apptjänst](../app-service/app-service-web-tutorial-custom-ssl.md#enforce-https) |
| **Steg** | <p>Även om Azure aktiverar redan HTTPS för Azure-app-tjänster med ett jokerteckencertifikat för domänen *. azurewebsites.net, tillämpar inte HTTPS. Besökare kan fortfarande få tillgång till appen med HTTP, vilket kan äventyra säkerheten appens och därför HTTPS måste vara tvingande explicit. ASP.NET MVC-program ska använda den [RequireHttps filter](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) som tvingar en osäker HTTP-begäran skickas igen via HTTPS.</p><p>URL-omskrivning om modulen, som ingår i Azure App Service kan också användas för att genomdriva HTTPS. URL-omskrivning om modulen gör att utvecklare kan definiera regler som tillämpas på inkommande begäranden innan begäranden lämnas i tillämpningsprogrammet. URL-omskrivning om regler har definierats i en web.config-fil som lagras i roten av programmet</p>|

### <a name="example"></a>Exempel
I följande exempel innehåller en grundläggande URL-omskrivning om regeln som tvingar all inkommande trafik för att använda HTTPS
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
Den här regeln fungerar genom att returnera ett HTTP-statuskoden 301 (permanent omdirigering) när användaren begär en sida med HTTP. 301 dirigerar begäran till samma URL som användaren begärdes, men ersätter den HTTP-delen av begäran med HTTPS. Till exempel omdirigeras HTTP://contoso.com till HTTPS://contoso.com. 

## <a id="http-hsts"></a>Aktivera HTTP strikt transportsäkerhet (HSTS)

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [OWASP HTTP strikt transportsäkerhet Cheat blad](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| **Steg** | <p>HTTP strikt Transport säkerhet (HSTS) är en säkerhetsförbättring av opt-in som anges av ett webbprogram genom att använda en särskild Svarsrubrik. När en webbläsare som stöds tar emot det här sidhuvudet webbläsaren förhindrar all kommunikation som skickas via HTTP till den angivna domänen och i stället skickar all kommunikation via HTTPS. Det förhindrar också HTTPS klicka dig igenom anvisningarna i webbläsare.</p><p>Om du vill implementera HSTS har följande svarshuvud konfigureras för en webbplats globalt i kod eller i konfig. Strikt--transportsäkerhet: max-ålder = 300; includeSubDomains HSTS löser följande kod:</p><ul><li>Användaren bokmärken eller manuellt skriver http://example.com och regleras en man-in-the-middle-angripare: HSTS automatiskt dirigerar HTTP-begäranden till HTTPS för måldomänen</li><li>Webbprogram som är avsedd att vara enbart HTTPS oavsiktligt innehåller http-länkar eller fungerar innehåll över HTTP: HSTS automatiskt dirigerar HTTP-begäranden till HTTPS för måldomänen</li><li>En man-in-the-middle-angripare försöker komma åt trafik från en drabbade användare som använder ett ogiltigt certifikat och hopes användaren accepterar ogiltigt certifikat: HSTS tillåter inte en användare för att åsidosätta meddelandet ogiltigt certifikat</li></ul>|

## <a id="sqlserver-validation"></a>Se till att SQL server-kryptering och certifikat anslutningsverifiering

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | SQL Azure  |
| **Attribut**              | SQL Version - V12 |
| **Referenser**              | [Bästa metoder för att skriva säker anslutningssträngar för SQL-databas](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Steg** | <p>All kommunikation mellan SQL-databas och ett klientprogram krypteras med Secure Sockets Layer (SSL) vid alla tidpunkter. SQL Database stöder inte okrypterade anslutningar. Om du vill verifiera certifikat med programkod eller verktyg, uttryckligen begära en krypterad anslutning och inte lita på servercertifikat som. Om din programkod eller verktyg inte begär en krypterad anslutning, får de fortfarande krypterade anslutningar</p><p>Men de kan inte verifiera servercertifikat och därmed kommer att utsättas för angrepp med ”man i mitten”. Om du vill verifiera certifikat med ADO.NET-programkod ange `Encrypt=True` och `TrustServerCertificate=False` i anslutningssträngen för databasen. Öppna den dialogrutan Anslut till servern för att verifiera certifikat via SQL Server Management Studio. Klicka på kryptera anslutning på fliken Egenskaper för anslutning</p>|

## <a id="encrypted-sqlserver"></a>Tvinga krypterad kommunikation till SQLServer

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | OnPrem |
| **Attribut**              | SQL Version - MsSQL2016, SQL Version - MsSQL2012, SQL Version - MsSQL2014 |
| **Referenser**              | [Aktivera krypterade anslutningar till databasmotorn](https://msdn.microsoft.com/library/ms191192)  |
| **Steg** | Om du aktiverar SSL-kryptering ökar säkerheten för data som överförs över nätverk mellan instanser av SQL Server och program. |

## <a id="comm-storage"></a>Se till att kommunikation till Azure Storage är över HTTPS

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Azure Storage transportnivå kryptering – med hjälp av HTTPS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Steg** | För att säkerställa säkerheten för Azure Storage data under överföring, Använd alltid HTTPS-protokollet anropa REST-API: er eller få åtkomst till objekt i lagringen. Dessutom innehåller signaturer för delad åtkomst, som kan användas för att delegera åtkomst till Azure Storage-objekt, ett alternativ för att ange att endast HTTPS-protokollet kan användas när du använder signaturer för delad åtkomst, se till att vem som helst skicka länkar med SAS-token ska använda rätt protokoll.|

## <a id="md5-https"></a>Validera MD5-hash när du har hämtat blob om det inte går att aktivera HTTPS

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | StorageType - Blob |
| **Referenser**              | [Översikt över Windows Azure Blob-MD5](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Steg** | <p>Windows Azure Blob-tjänsten tillhandahåller mekanismer för att garantera dataintegriteten både på program- och transport lager. Om du av någon anledning som du måste använda HTTP i stället för HTTPS och du arbetar med blockblobbar, kan du använda MD5-kontroll för att kontrollera integriteten för de blobbar som överförs</p><p>Detta hjälper med skydd från nätverket/transport layer fel, men inte nödvändigtvis med mellanliggande attacker. Om du kan använda HTTPS, vilket ger säkerhet på radnivå transport är med hjälp av MD5 kontrollerar redundant och onödiga.</p>|

## <a id="smb-shares"></a>Använd kompatibel SMB 3.0-klienten för att säkerställa transit datakryptering till Azure-filresurser

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobil klient | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | StorageType - fil |
| **Referenser**              | [Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [Azure File Storage SMB-stöd för Windows-klienter](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Steg** | Azure File Storage stöder HTTPS när du använder REST-API, men är mer används ofta som en SMB filresurs kopplad till en virtuell dator. SMB 2.1 stöder inte kryptering, så anslutningar tillåts bara inom samma region i Azure. Dock SMB 3.0 stöder kryptering och kan användas med Windows Server 2012 R2, Windows 8, Windows 8.1 och Windows 10, vilket gör att flera region åtkomst och även åtkomst på skrivbordet. |

## <a id="cert-pinning"></a>Implementera fästa certifikat

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Allmän och Windows Phone |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Certifikat och offentlig nyckel fästning](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| **Steg** | <p>Certifikatet fästning skyddar mot Man-In-The-Middle attacker. Fästning är en process för att associera en värd med deras förväntade X509 certifikat eller offentlig nyckel. När ett certifikat eller en offentlig nyckel är känt eller för en värd, är certifikatet eller den offentliga nyckeln associerad eller Fäst på värden. </p><p>Därför när en angriparen försöker göra SSL MITM-attacker, under SSL-handskakningen nyckeln från angriparens servern kommer att skiljer sig från Fäst certifikatets nyckel och begäran ignoreras, vilket gör MITM certifikat fästning kan uppnås genom Implementera Servicepointmanager's `ServerCertificateValidationCallback` delegera.</p>|

### <a name="example"></a>Exempel
```csharp
using System;
using System.Net;
using System.Net.Security;
using System.Security.Cryptography;

namespace CertificatePinningExample
{
    class CertificatePinningExample
    {
        /* Note: In this example, we're hardcoding a the certificate's public key and algorithm for 
           demonstration purposes. In a real-world application, this should be stored in a secure
           configuration area that can be updated as needed. */

        private static readonly string PINNED_ALGORITHM = "RSA";

        private static readonly string PINNED_PUBLIC_KEY = "3082010A0282010100B0E75B7CBE56D31658EF79B3A1" +
            "294D506A88DFCDD603F6EF15E7F5BCBDF32291EC50B2B82BA158E905FE6A83EE044A48258B07FAC3D6356AF09B2" +
            "3EDAB15D00507B70DB08DB9A20C7D1201417B3071A346D663A241061C151B6EC5B5B4ECCCDCDBEA24F051962809" +
            "FEC499BF2D093C06E3BDA7D0BB83CDC1C2C6660B8ECB2EA30A685ADE2DC83C88314010FFC7F4F0F895EDDBE5C02" +
            "ABF78E50B708E0A0EB984A9AA536BCE61A0C31DB95425C6FEE5A564B158EE7C4F0693C439AE010EF83CA8155750" +
            "09B17537C29F86071E5DD8CA50EBD8A409494F479B07574D83EDCE6F68A8F7D40447471D05BC3F5EAD7862FA748" +
            "EA3C92A60A128344B1CEF7A0B0D94E50203010001";


        public static void Main(string[] args)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://azure.microsoft.com");
            request.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
            {
                if (certificate == null || sslPolicyErrors != SslPolicyErrors.None)
                {
                    // Error getting certificate or the certificate failed basic validation
                    return false;
                }

                var targetKeyAlgorithm = new Oid(certificate.GetKeyAlgorithm()).FriendlyName;
                var targetPublicKey = certificate.GetPublicKeyString();
                
                if (targetKeyAlgorithm == PINNED_ALGORITHM &&
                    targetPublicKey == PINNED_PUBLIC_KEY)
                {
                    // Success, the certificate matches the pinned value.
                    return true;
                }
                // Reject, either the key or the algorithm does not match the expected value.
                return false;
            };

            try
            {
                var response = (HttpWebResponse)request.GetResponse();
                Console.WriteLine($"Success, HTTP status code: {response.StatusCode}");
            }
            catch(Exception ex)
            {
                Console.WriteLine($"Failure, {ex.Message}");
            }
            Console.WriteLine("Press any key to end.");
            Console.ReadKey();
        }
    }
}
```

## <a id="https-transport"></a>Aktivera HTTPS - Secure Transportkanalen

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | NET Framework 3 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning kungariket](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Steg** | Programkonfigurationen bör se till att HTTPS används för all åtkomst till känslig information.<ul><li>**Förklaring:** om ett program som hanterar känslig information och inte använder meddelandenivå kryptering och sedan bara ska tillåtas att kommunicera via en krypterad Transportkanalen.</li><li>**REKOMMENDATIONER:** se till att HTTP-transport är inaktiverat och aktivera HTTPS-transport i stället. Ersätt till exempel den `<httpTransport/>` med `<httpsTransport/>` tagg. Förlita dig inte på en nätverkskonfiguration (brandvägg) för att garantera att programmet bara kan nås via en säker kanal. Programmet bör inte beror på nätverket för säkerheten från filosofisk synvinkel.</li></ul><p>Från en praktisk synvinkel spåra personer som ansvarar för att skydda nätverket alltid inte säkerhetskraven för programmet som de utvecklas.</p>|

## <a id="message-protection"></a>WCF: Meddelandesäkerhet ange skyddsnivån på EncryptAndSign

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | .NET Framework 3 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Steg** | <ul><li>**Förklaring:** när skydd nivå har angetts till ”none” den inaktiveras meddelandet skydd. Sekretess och integritet uppnås med rätt nivå av inställningen.</li><li>**REKOMMENDATIONER:**<ul><li>När `Mode=None` -inaktiverar skydd för meddelandet</li><li>När `Mode=Sign` -tecken men inte kryptera meddelandet; ska användas när det är viktigt att dataintegritet</li><li>När `Mode=EncryptAndSign` -loggar och krypterar meddelandet</li></ul></li></ul><p>Överväg att inaktivera kryptering och signering meddelandet endast när du vill validera integriteten för information utan att bekymra dig för sekretess. Detta kan vara användbart för åtgärder eller servicekontrakt i som du måste validera den ursprungliga avsändaren men inga känsliga data skickas. När du minskar skyddsnivån var noga med att meddelandet inte innehåller personligt identifierbar information (PII).</p>|

### <a name="example"></a>Exempel
Konfigurera tjänsten och åtgärden är bara logga meddelandet visas i följande exempel. Tjänsten kontraktet exempel på `ProtectionLevel.Sign`: följande är ett exempel på hur ProtectionLevel.Sign på nivån tjänstkontrakt: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Exempel
Åtgärden kontraktet exempel på `ProtectionLevel.Sign` (för Granulär kontroll): följande är ett exempel på hur `ProtectionLevel.Sign` på OperationContract-nivå:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a id="least-account-wcf"></a>WCF: Använd ett konto med lägsta behörighet för att köra WCF-tjänst

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | .NET Framework 3 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Steg** | <ul><li>**Förklaring:** kör inte WCF-tjänster under administratörer eller Privilegierade konto. vid röjande tjänster leder den till Hög inverkan.</li><li>**REKOMMENDATIONER:** använder ett konto med lägsta behörighet som värd för WCF-tjänsten eftersom den minska angreppsytan för ditt program och minska eventuella skador om du angripna. Om kontot kräver ytterligare behörighet på infrastrukturresurser, till exempel MSMQ, händelseloggen, prestandaräknare och filsystemet, ges behörighet till dessa resurser så att WCF-tjänsten kan köras.</li></ul><p>Om din tjänst behöver åtkomst till specifika resurser för den ursprungliga anroparen, använda personifiering och delegering flöda Anroparens identitet för en underordnad auktorisering kontroll. Använd tjänstkontot lokala nätverket, vilket är en särskild inbyggt konto som har lägre privilegier i ett utvecklingsscenario med. Skapa en anpassad domän för lägsta behörighet tjänstkonto i ett produktionsscenario.</p>|

## <a id="webapi-https"></a>Tvinga all trafik till webb-API: er via HTTPS-anslutning

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC5, MVC6 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Framtvinga SSL i en Web API-styrenhet](http://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Steg** | Om ett program har en HTTPS- och en HTTP-bindning, kan klienter fortfarande använda HTTP för åtkomst till webbplatsen. Om du vill undvika detta använder du ett filter för åtgärden så att begäranden till skyddade API: er är alltid över HTTPS.|

### <a name="example"></a>Exempel 
Följande kod visar ett Web API-autentisering filter som söker efter SSL: 
```csharp
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
Lägg till det här filtret alla Web API-åtgärder som kräver SSL: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a id="redis-ssl"></a>Se till att kommunikation till Azure Redis-Cache är via SSL

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Redis Cache | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Azure Redis-SSL-stöd](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Steg** | Redis-servern stöder inte SSL direkt, men Azure Redis-Cache har. Om du ansluter till Azure Redis-Cache och klienten stöder SSL, som StackExchange.Redis, bör du använda SSL. Icke-SSL-porten inaktiveras som standard för nya Azure Redis-Cache-instanser. Se till att säkra standardinställningarna inte ändras om det inte finns ett beroende på SSL-stöd för redis-klienter. |

Observera att Redis har utformats som kan nås av betrodda klienter i betrodda miljöer. Det innebär att vanligtvis den inte är en bra idé att exponera Redis-instansen direkt till internet eller i allmänhet till en miljö där icke-betrodda klienter har direkt åtkomst till Redis TCP-port eller UNIX-socket. 

## <a id="device-field"></a>Skydda enhet till fältet Gateway-kommunikation

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Fältet för IoT-Gateway | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | För IP-baserade enheter kan du vanligtvis inkapslade kommunikationsprotokollet i en SSL/TLS-kanal för att skydda data under överföringen. Ta reda på om det finns säker versioner av protokollen som tillhandahåller säkerheten på transport eller meddelandet nivå för andra protokoll som inte stöder SSL/TLS. |

## <a id="device-cloud"></a>Skydda enhet till Molngateway kommunikation via SSL/TLS

| Namn                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Välj din kommunikationsprotokoll](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Steg** | Säker HTTP/AMQP eller MQTT protokoll med hjälp av SSL/TLS. |
