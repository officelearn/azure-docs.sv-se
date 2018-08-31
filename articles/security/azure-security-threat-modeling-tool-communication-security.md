---
title: Kommunikation säkerhet – Microsoft Hotmodelleringsverktyg – Azure | Microsoft Docs
description: åtgärder för hot som exponeras i Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 7e8afc02c738a2bba445b1d84b7cb899dfbb93a0
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301562"
---
# <a name="security-frame-communication-security--mitigations"></a>Security ram: KOMMUNIKATIONSSÄKERHET | Åtgärder 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Azure-Händelsehubb** | <ul><li>[Säker kommunikation till Event Hub med hjälp av SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Kontrollera behörighet för tjänsten och kontrollera att anpassade Services eller ASP.NET-sidor respekterar CRM-säkerhet](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Använd Data management gateway vid anslutning på lokal SQL Server till Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Identitetsserver** | <ul><li>[Se till att all trafik till Identity Server är över HTTPS-anslutning](#identity-https)</li></ul> |
| **Webbprogram** | <ul><li>[Verifiera X.509-certifikat som används för att autentisera anslutningar för SSL, TLS och DTLS](#x509-ssltls)</li><li>[Konfigurera SSL-certifikat för anpassad domän i Azure App Service](#ssl-appservice)</li><li>[Tvinga all trafik till Azure App Service via HTTPS-anslutning](#appservice-https)</li><li>[Aktivera HTTP strikt Transport Security (HSTS)](#http-hsts)</li></ul> |
| **Databas** | <ul><li>[Se till att SQL server-kryptering och certifikatet anslutningsverifiering](#sqlserver-validation)</li><li>[Framtvinga krypterad kommunikation till SQLServer](#encrypted-sqlserver)</li></ul> |
| **Azure Storage** | <ul><li>[Se till att kommunikation till Azure Storage är över HTTPS](#comm-storage)</li><li>[Validera MD5-hash när du hämtat blob om det inte går att aktivera HTTPS](#md5-https)</li><li>[Använd kompatibla SMB 3.0-klienten för att säkerställa under överföring datakryptering för Azure-filresurser](#smb-shares)</li></ul> |
| **Mobila klienten** | <ul><li>[Implementera fästa certifikat](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Aktivera HTTPS – säker transportkanal](#https-transport)</li><li>[WCF: Meddelandesäkerhet Set skyddsnivå till EncryptAndSign](#message-protection)</li><li>[WCF: Använd ett konto med lägsta behörighet för att köra WCF-tjänst](#least-account-wcf)</li></ul> |
| **Webb-API** | <ul><li>[Tvinga all trafik till webb-API: er via HTTPS-anslutning](#webapi-https)</li></ul> |
| **Azure Redis Cache** | <ul><li>[Se till att kommunikation till Azure Redis Cache är via SSL](#redis-ssl)</li></ul> |
| **IoT-fält-Gateway** | <ul><li>[Skydda enhet för fält-Gateway-kommunikation](#device-field)</li></ul> |
| **IoT-molnet Gateway** | <ul><li>[Skydda enhet till Molngatewayen kommunikation via SSL/TLS](#device-cloud)</li></ul> |

## <a id="comm-ssltls"></a>Säker kommunikation till Event Hub med hjälp av SSL/TLS

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure händelsehubb | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Autentisering och säkerhet modellen översikt över Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Steg** | Skydda AMQP- eller HTTP-anslutningar till Händelsehubb med hjälp av SSL/TLS |

## <a id="priv-aspnet"></a>Kontrollera behörighet för tjänsten och kontrollera att anpassade Services eller ASP.NET-sidor respekterar CRM-säkerhet

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Kontrollera behörighet för tjänsten och kontrollera att anpassade Services eller ASP.NET-sidor respekterar CRM-säkerhet |

## <a id="sqlserver-factory"></a>Använd Data management gateway vid anslutning på lokal SQL Server till Azure Data Factory

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Data Factory | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Länkade tjänsttyper – Azure och lokalt |
| **Referenser**              |[ Flytta data mellan lokal och Azure Data Factory](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway), [gateway för datahantering](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **Steg** | <p>Verktyget Data Management Gateway (DMG) krävs för att ansluta till datakällor som skyddas bakom corpnet eller brandvägg.</p><ol><li>Låsa datorn isolerar DMG-verktyget och förhindrar att felaktiga program från att skadas eller snooping på källdatorn data. (T.ex.) senaste uppdateringarna måste installeras, aktivera minsta nödvändiga portar kontrollerad konton etablering, granskning aktiverat diskkryptering aktiverat osv.)</li><li>Data Gateway-nyckeln måste roteras regelbundet eller när du förnyar DMG tjänstkontolösenord</li><li>Data eltransit via länken-tjänsten måste vara krypterat</li></ol> |

## <a id="identity-https"></a>Se till att all trafik till Identity Server är över HTTPS-anslutning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitetsserver | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [IdentityServer3 - nycklar, signaturer och kryptografi](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 - distribution](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Steg** | Som standard måste IdentityServer alla inkommande anslutningar för att komma över HTTPS. Det är absolut obligatoriskt att kommunikation med IdentityServer görs över säker transporter. Det finns vissa scenarier som SSL-avlastning där det här kravet minskas. Se sidan Identity Server-distribution i referenser till mer information. |

## <a id="x509-ssltls"></a>Verifiera X.509-certifikat som används för att autentisera anslutningar för SSL, TLS och DTLS

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Program som använder SSL, TLS och DTLS måste fullständigt Kontrollera X.509-certifikat för de entiteter som de ansluter till. Detta inkluderar verifiering av certifikat för:</p><ul><li>Domännamn</li><li>Giltighetsdatum (både början och förfallodatum datum)</li><li>Återkallningsstatus</li><li>Användning (till exempel Server-autentisering för servrar, klientautentisering för klienter)</li><li>Lita på kedjan. Certifikat måste kedja till en rotcertifikatutfärdare (CA) som är betrodd av plattformen eller uttryckligen har konfigurerats av administratören</li><li>Nyckellängd på certifikatets offentliga nyckel måste vara > 2 048 bitar</li><li>Hash-algoritm måste vara SHA256 och senare |

## <a id="ssl-appservice"></a>Konfigurera SSL-certifikat för anpassad domän i Azure App Service

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | EnvironmentType - Azure |
| **Referenser**              | [Aktivera HTTPS för en app i Azure App Service](../app-service/app-service-web-tutorial-custom-ssl.md) |
| **Steg** | Som standard Azure redan aktiverar HTTPS för varje app med ett jokerteckencertifikat för den *. azurewebsites.net-domänen. Som alla Jokerteckendomäner, det är dock inte lika säkra som använder en anpassad domän med egna certifikat [finns](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Vi rekommenderar att aktivera SSL för den anpassade domänen som den distribuerade appen som kan nås via|

## <a id="appservice-https"></a>Tvinga all trafik till Azure App Service via HTTPS-anslutning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | EnvironmentType - Azure |
| **Referenser**              | [Använda HTTPS på Azure App Service](../app-service/app-service-web-tutorial-custom-ssl.md#enforce-https) |
| **Steg** | <p>Även om Azure kan redan HTTPS för Azure app services med ett jokerteckencertifikat för domänen *. azurewebsites.net, det behöver inte använda HTTPS. Besökare kan fortfarande komma åt appen med HTTP, vilket kan äventyra säkerheten för appens och kan därför HTTPS måste tillämpas uttryckligen. ASP.NET MVC-program ska använda den [RequireHttps filter](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) som tvingar en osäker HTTP-förfrågan skickas igen via HTTPS.</p><p>Modulen Webbadressomskrivning som ingår i Azure App Service kan också användas för att använda HTTPS. Modulen för omarbetning för URL: en kan utvecklare definiera regler som tillämpas på inkommande begäranden innan begäranden ska lämnas i ditt program. URL-Omskrivningsregler regler har definierats i en web.config-fil som lagras i roten av programmet</p>|

### <a name="example"></a>Exempel
I följande exempel innehåller en grundläggande URL-Omskrivningsregler regel som tvingar all inkommande trafik ska använda HTTPS
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
Den här regeln fungerar genom att returnera ett HTTP-statuskoden 301 (permanent omdirigering) när användaren begär en sida med HTTP. 301 omdirigerar begäran till samma Webbadress som den besökare som begärts, men ersätter den HTTP-delen av begäran med HTTPS. Till exempel HTTP://contoso.com omdirigeras till HTTPS://contoso.com. 

## <a id="http-hsts"></a>Aktivera HTTP strikt Transport Security (HSTS)

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [OWASP HTTP strikt transportsäkerhet Lathund](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| **Steg** | <p>HTTP strikt Transport Security (HSTS) är en valbar säkerhetsförbättring som anges av ett webbprogram med hjälp av en särskild svarshuvudet. När en webbläsare som stöds får den här rubriken som webbläsaren förhindrar all kommunikation som skickas via HTTP till den angivna domänen och i stället skickar all kommunikation via HTTPS. Det förhindrar även att HTTPS klicka dig igenom anvisningarna i webbläsare.</p><p>Om du vill implementera HSTS har följande svarshuvudet konfigureras för en webbplats globalt, antingen i kod eller i konfigurationen. Strikt Transport Security: max-age = 300; includeSubDomains HSTS löser följande hot:</p><ul><li>Användaren bokmärken eller manuellt typer http://example.com och lyder under en man-in-the-middle-angripare: HSTS automatiskt omdirigerar HTTP-begäranden till HTTPS för måldomänen</li><li>Webbprogram som är avsedd att vara helt och hållet HTTPS oavsiktligt innehåller HTTP länkar eller tillhandahåller innehåll över HTTP: HSTS automatiskt omdirigerar HTTP-begäranden till HTTPS för måldomänen</li><li>En man-in-the-middle-angripare försöker komma åt trafik från en victim-användare som använder ett ogiltigt certifikat och hopes användaren accepterar ogiltigt certifikat: HSTS tillåter inte en användare åsidosätta meddelandet ogiltigt certifikat</li></ul>|

## <a id="sqlserver-validation"></a>Se till att SQL server-kryptering och certifikatet anslutningsverifiering

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | SQL Azure  |
| **Attribut**              | SQL Version - V12 |
| **Referenser**              | [Metodtips för att skriva säkra anslutningssträngar för SQL-databas](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Steg** | <p>All kommunikation mellan SQL-databas och ett klientprogram krypteras med hjälp av Secure Sockets Layer (SSL) hela tiden. SQL Database stöder inte okrypterad anslutningar. För att verifiera certifikat med programkod eller verktyg kan uttryckligen begära en krypterad anslutning och lita inte på servercertifikat. Om din programkod eller verktyg inte begär en krypterad anslutning, får de fortfarande krypterade anslutningar</p><p>Men de kan inte verifiera servercertifikatet och därför kommer vara sårbar för ”man in the middle”-attacker. För att verifiera certifikat med ADO.NET programkoden kan ange `Encrypt=True` och `TrustServerCertificate=False` i anslutningssträngen för databasen. Öppna i dialogrutan Anslut till servern för att verifiera certifikat via SQL Server Management Studio. Klicka på kryptera anslutningen på fliken Egenskaper för anslutning</p>|

## <a id="encrypted-sqlserver"></a>Framtvinga krypterad kommunikation till SQLServer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | OnPrem |
| **Attribut**              | SQL-Version - MsSQL2016, SQL-Version - MsSQL2012, SQL-Version - MsSQL2014 |
| **Referenser**              | [Aktivera krypterade anslutningar till databasmotorn](https://msdn.microsoft.com/library/ms191192)  |
| **Steg** | Om du aktiverar SSL-kryptering ökar du säkerheten för data som överförs över nätverk mellan instanser av SQL Server och program. |

## <a id="comm-storage"></a>Se till att kommunikation till Azure Storage är över HTTPS

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Azure Storage på transportnivå kryptering – med hjälp av HTTPS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Steg** | För att säkerställa säkerheten för Azure Storage data under överföring, Använd alltid HTTPS-protokollet anropa REST-API: er eller åtkomst till objekt i lagring. Dessutom innehåller signaturer för delad åtkomst, som kan användas för att delegera åtkomst till Azure Storage-objekt, ett alternativ för att ange endast HTTPS-protokollet kan användas när du använder signaturer för delad åtkomst, se till att vem som helst skicka länkar med SAS-token kommer att använda rätt protokoll.|

## <a id="md5-https"></a>Validera MD5-hash när du hämtat blob om det inte går att aktivera HTTPS

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | StorageType - Blob |
| **Referenser**              | [Översikt över Windows Azure-Blob-MD5](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Steg** | <p>Windows Azure Blob service tillhandahåller mekanismer för att garantera dataintegriteten både på program- och transport lager. Om av någon anledning måste du använda HTTP i stället för HTTPS och du arbetar med blockblobar, kan du använda MD5 kontrollerar för att kontrollera integriteten för de blobar som överförs</p><p>På så sätt med skydd mot fel för nätverk/transport layer, men inte nödvändigtvis med mellanliggande attacker. Om du kan använda HTTPS, som ger transportsäkerhet, är sedan med hjälp av MD5 kontrollerar redundant och onödiga.</p>|

## <a id="smb-shares"></a>Använd kompatibla SMB 3.0-klienten för att säkerställa under överföring datakryptering för Azure-filresurser

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobila klienten | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | StorageType - fil |
| **Referenser**              | [Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [Azure File Storage SMB-stöd för Windows-klienter](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Steg** | Azure File Storage stöder HTTPS när du använder REST-API, men är mer ofta används som en SMB-filresurs kopplade till en virtuell dator. SMB 2.1 stöder inte kryptering, så anslutningar är bara tillåtna inom samma region i Azure. Dock SMB 3.0 stöder kryptering och kan användas med Windows Server 2012 R2, Windows 8, Windows 8.1 och Windows 10, vilket gör att interregionala åtkomst till och med åtkomst på skrivbordet. |

## <a id="cert-pinning"></a>Implementera fästa certifikat

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk, Windows Phone |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Certifikat och offentlig nyckel fästa](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| **Steg** | <p>Certifikatet fästa skyddar mot Man-In-The-Middle attacker. Fästa är processen att associera en värd med deras förväntade X509 certifikat eller offentlig nyckel. När ett certifikat eller en offentlig nyckel är känd eller visas för en värd, certifikatet eller den offentliga nyckeln att associerade eller Fäst till värden. </p><p>Därför när en angripare försöker göra SSL MITM-attacker, under SSL-handskakningen nyckeln från angriparens server kommer skilja sig från fästa certifikatets nyckel och begäran ignoreras, vilket gör MITM certifikat fästa kan uppnås genom Implementera Servicepointmanager's `ServerCertificateValidationCallback` delegera.</p>|

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

## <a id="https-transport"></a>Aktivera HTTPS – säker transportkanal

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | NET Framework 3 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning Nordirland](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Steg** | Programkonfigurationen bör se till att HTTPS används för all åtkomst till känslig information.<ul><li>**Förklaring:** om ett program som hanterar känslig information och inte använder kryptering på enhetsnivå meddelande så bör endast tillåtas att kommunicera via en krypterad transportkanal.</li><li>**REKOMMENDATIONER:** se till att HTTP-transport är inaktiverat och aktivera HTTPS-transport i stället. Ersätt till exempel den `<httpTransport/>` med `<httpsTransport/>` tagg. Förlita dig inte på en nätverkskonfiguration (brandvägg) för att garantera att programmet bara kan nås via en säker kanal. Programmet bör inte bero på nätverket för säkerheten från filosofisk synvinkel.</li></ul><p>Ur en praktisk synvinkel spåra vilka personer som ansvarar för att skydda nätverket alltid inte säkerhetskraven för programmet när de uppstår.</p>|

## <a id="message-protection"></a>WCF: Meddelandesäkerhet Set skyddsnivå till EncryptAndSign

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | .NET framework 3 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Steg** | <ul><li>**Förklaring:** när Protection nivå är inställt på ”Ingen” det inaktiverar skydd för meddelandet. Sekretess och integritet uppnås med lämplig nivå av inställningen.</li><li>**REKOMMENDATIONER:**<ul><li>När `Mode=None` -inaktiverar skydd för meddelande</li><li>När `Mode=Sign` -loggar, men krypterar inte meddelandet; bör användas när det är viktigt att dataintegritet</li><li>När `Mode=EncryptAndSign` -loggar och krypterar meddelandet</li></ul></li></ul><p>Överväg att stänga av kryptering och signering ditt meddelande endast när du vill validera integriteten för information utan att bekymra dig om sekretessen. Detta kan vara användbart för åtgärder eller servicekontrakt i som du behöver att verifiera den ursprungliga avsändaren, men inga känsliga data skickas. När du minskar skyddsnivån noga med att meddelandet inte innehåller personligt identifierbar information (PII).</p>|

### <a name="example"></a>Exempel
Konfigurera tjänsten och åtgärden att endast logga meddelandet visas i följande exempel. Exempel på kontraktet av `ProtectionLevel.Sign`: följande är ett exempel på hur du använder ProtectionLevel.Sign på nivån tjänstkontrakt: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Exempel
Åtgärden kontrakt exempel på `ProtectionLevel.Sign` (för detaljerad kontroll): följande är ett exempel på hur du använder `ProtectionLevel.Sign` på Webget-nivå:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a id="least-account-wcf"></a>WCF: Använd ett konto med lägsta behörighet för att köra WCF-tjänst

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | .NET framework 3 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Steg** | <ul><li>**Förklaring:** kör inte WCF-tjänster under administratör eller Privilegierade konto. vid tjänster kompromettering resulterar det i hög påverkan.</li><li>**REKOMMENDATIONER:** använder ett konto med lägsta behörighet som värd för WCF-tjänsten eftersom det minska angreppsytan för ditt program och minska eventuella skador om du angripna. Om tjänstkontot kräver ytterligare behörighet på infrastrukturresurser, till exempel MSMQ, händelseloggen, prestandaräknare och filsystemet, ges behörighet till de här resurserna så att WCF-tjänst kan köras.</li></ul><p>Om din tjänst behöver åtkomst till specifika resurser åt ursprungliga anroparen, använda personifiering och delegering flöda Anroparens identitet för en underordnad auktorisering-kontroll. I ett utvecklingsscenario, använder du tjänstkontot lokalt nätverk, vilket är ett särskilt inbyggt konto som har lägre privilegier. Skapa ett tjänstkonto för anpassad domän för lägsta behörighet i ett produktionsscenario för.</p>|

## <a id="webapi-https"></a>Tvinga all trafik till webb-API: er via HTTPS-anslutning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC5 MVC6 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Tvingande SSL i en webb-API-kontroll](http://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Steg** | Om ett program har en HTTPS- och en HTTP-bindning, kan klienter fortfarande använda HTTP på webbplatsen. Om du vill förhindra detta använder du ett filter för åtgärden så att förfrågningar till skyddade API: er är alltid över HTTPS.|

### <a name="example"></a>Exempel 
Följande kod visar ett webb-API-autentisering-filter som söker efter SSL: 
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
Lägg till det här filtret i alla webb-API-åtgärder som kräver SSL: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a id="redis-ssl"></a>Se till att kommunikation till Azure Redis Cache är via SSL

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Redis Cache | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Azure Redis SSL-support](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Steg** | Redis-servern stöder inte SSL direkt, men Azure Redis Cache har. Om du ansluter till Azure Redis Cache och klienten stöder SSL, som StackExchange.Redis, bör du använda SSL. Som standard är icke-SSL-porten inaktiverad för nya Azure Redis Cache-instanser. Se till att säkra standardinställningarna inte ändras om det inte finns ett beroende på SSL-stöd för redis-klienter. |

Observera att Redis har utformats för att användas av betrodda klienter i betrodda miljöer. Det innebär att vanligtvis det inte är en bra idé att exponera Redis-instansen direkt till internet eller i allmänhet till en miljö där ej betrodda klienter har direkt åtkomst till Redis-TCP-port eller UNIX-socket. 

## <a id="device-field"></a>Skydda enhet för fält-Gateway-kommunikation

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-fält-Gateway | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | För IP-baserade enheter, kan du vanligtvis inkapslade kommunikationsprotokollet i en SSL/TLS-kanal för att skydda data under överföring. Undersök om det finns säkert versioner av protokollet som tillhandahåller säkerheten på transport eller meddelandet nivå för andra protokoll som inte har stöd för SSL/TLS. |

## <a id="device-cloud"></a>Skydda enhet till Molngatewayen kommunikation via SSL/TLS

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-molnet Gateway | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Välj din kommunikationsprotokoll](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Steg** | Skydda HTTP/AMQP och MQTT-protokoll använder SSL/TLS. |
