---
title: Kommunikationssäkerhet för Microsoft Threat Modeling Tool
titleSuffix: Azure
description: mildrande åtgärder för hot som exponeras i hotmodelleringsverktyget
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 39ee0fa2dc973cd6c20756cae2024af79d1375dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294152"
---
# <a name="security-frame-communication-security--mitigations"></a>Säkerhetsram: Kommunikationssäkerhet | Mitigations 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Azure Event Hub** | <ul><li>[Säker kommunikation till eventhubben med SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Kontrollera behörigheten för tjänstkonto och kontrollera att de anpassade tjänsterna eller ASP.NET sidor respekterar CRM:s säkerhet](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Använda datahanteringsgateway när du ansluter lokalt SQL Server till Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Identitetsserver** | <ul><li>[Kontrollera att all trafik till Identity Server är över HTTPS-anslutning](#identity-https)</li></ul> |
| **Webbprogram** | <ul><li>[Verifiera X.509-certifikat som används för att autentisera SSL-, TLS- och DTLS-anslutningar](#x509-ssltls)</li><li>[Konfigurera SSL-certifikat för anpassad domän i Azure App Service](#ssl-appservice)</li><li>[Tvinga all trafik till Azure App Service via HTTPS-anslutning](#appservice-https)</li><li>[Aktivera HTTP Strikt transportsäkerhet (HSTS)](#http-hsts)</li></ul> |
| **Databas** | <ul><li>[Kontrollera kryptering av SQL-serveranslutning och certifikatvalidering](#sqlserver-validation)</li><li>[Tvinga krypterad kommunikation till SQL-server](#encrypted-sqlserver)</li></ul> |
| **Azure-lagring** | <ul><li>[Se till att kommunikationen till Azure Storage är över HTTPS](#comm-storage)</li><li>[Validera MD5-hash efter hämtning av blob om HTTPS inte kan aktiveras](#md5-https)</li><li>[Använd SMB 3.0-kompatibel klient för att säkerställa kryptering med data under överföring till Azure File Shares](#smb-shares)</li></ul> |
| **Mobil klient** | <ul><li>[Implementera certifikatnålning](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Aktivera HTTPS - Secure Transport kanal](#https-transport)</li><li>[WCF: Ange säkerhetsskyddsnivå för meddelanden till EncryptAndSign](#message-protection)</li><li>[WCF: Använd ett konto med minst privilegierad behörighet för att köra din WCF-tjänst](#least-account-wcf)</li></ul> |
| **Webb-API** | <ul><li>[Tvinga all trafik till webb-API:er via HTTPS-anslutning](#webapi-https)</li></ul> |
| **Azure Cache for Redis** | <ul><li>[Se till att kommunikationen till Azure Cache for Redis är över SSL](#redis-ssl)</li></ul> |
| **IoT-fältgateway** | <ul><li>[Säker kommunikation mellan enhet och fältgateway](#device-field)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Säker kommunikation mellan enheter och molngateway med SSL/TLS](#device-cloud)</li></ul> |

## <a name="secure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>Säker kommunikation till eventhubben med SSL/TLS

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Event Hub | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Översikt över autentisering och säkerhetsmodell för eventhubbar](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Steg** | Säkra AMQP- eller HTTP-anslutningar till Event Hub med SSL/TLS |

## <a name="check-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>Kontrollera behörigheten för tjänstkonto och kontrollera att de anpassade tjänsterna eller ASP.NET sidor respekterar CRM:s säkerhet

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Kontrollera behörigheten för tjänstkonto och kontrollera att de anpassade tjänsterna eller ASP.NET sidor respekterar CRM:s säkerhet |

## <a name="use-data-management-gateway-while-connecting-on-premises-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>Använda datahanteringsgateway när du ansluter lokalt SQL Server till Azure Data Factory

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Data Factory | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Länkade tjänsttyper - Azure och lokalt |
| **Referenser**              |[Flytta data mellan lokal och Azure Data Factory](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway), [Datahanteringsgateway](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **Steg** | <p>DMG-verktyget (Data Management Gateway) krävs för att ansluta till datakällor som är skyddade bakom corpnet eller en brandvägg.</p><ol><li>Att låsa maskinen isolerar DMG-verktyget och förhindrar att program inte fungerar skadliga eller snokar på datakällmaskinen. (T.ex. senaste uppdateringarna måste installeras, aktivera minsta nödvändiga portar, kontrollerad konton etablering, granskning aktiverad, diskkryptering aktiverad etc.)</li><li>Data gateway-tangenten måste roteras med täta intervall eller när DMG-tjänstkontots lösenord förnyas</li><li>Datatransitering via länktjänsten måste krypteras</li></ol> |

## <a name="ensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Kontrollera att all trafik till Identity Server är över HTTPS-anslutning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitetsserver | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [IdentityServer3 - Nycklar, signaturer och kryptografi](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 - Distribution](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Steg** | Som standard kräver IdentityServer att alla inkommande anslutningar kommer över HTTPS. Det är absolut obligatoriskt att kommunikation med IdentityServer endast sker via säkra transporter. Det finns vissa distributionsscenarier som SSL-avlastning där det här kravet kan mjukas upp. Mer information finns på distributionssidan för Identity Server i referenserna. |

## <a name="verify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>Verifiera X.509-certifikat som används för att autentisera SSL-, TLS- och DTLS-anslutningar

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Program som använder SSL, TLS eller DTLS måste verifiera X.509-certifikaten för de enheter som de ansluter till. Detta omfattar kontroll av intyg för</p><ul><li>Domännamn</li><li>Giltighetsdatum (både start- och förfallodatum)</li><li>Status för återkallad utkall</li><li>Användning (till exempel serverautentisering för servrar, klientautentisering för klienter)</li><li>Förtroendekedja. Certifikat måste kedjas till en rotcertifikatutfärdare som är betrodd av plattformen eller som uttryckligen konfigurerats av administratören</li><li>Nyckellängden för certifikatets offentliga nyckel måste vara >2048 bitar</li><li>Hash-algoritmen måste vara SHA256 och högre |

## <a name="configure-ssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>Konfigurera SSL-certifikat för anpassad domän i Azure App Service

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | EnvironmentType - Azure |
| **Referenser**              | [Aktivera HTTPS för en app i Azure App Service](../../app-service/configure-ssl-bindings.md) |
| **Steg** | Som standard aktiverar Azure redan HTTPS för varje app med ett jokerteckencertifikat för domänen *.azurewebsites.net. Men precis som alla jokerteckendomäner är det inte lika säkert som att använda en anpassad domän med eget certifikat [Refer](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Vi rekommenderar att SSL aktiveras för den anpassade domän som den distribuerade appen kommer att nås via|

## <a name="force-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>Tvinga all trafik till Azure App Service via HTTPS-anslutning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | EnvironmentType - Azure |
| **Referenser**              | [Framtvinga HTTPS på Azure App Service](../../app-service/configure-ssl-bindings.md#enforce-https) |
| **Steg** | <p>Även om Azure redan aktiverar HTTPS för Azure-apptjänster med ett jokerteckencertifikat för domänen *.azurewebsites.net, framtvingar det inte HTTPS. Besökare kan fortfarande komma åt appen med HTTP, vilket kan äventyra appens säkerhet och därför måste HTTPS tillämpas explicit. ASP.NET MVC-program ska använda [filtret RequireHttps](https://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) som tvingar en osäker HTTP-begäran att skickas igen via HTTPS.</p><p>Alternativt kan URL-omskrivningsmodulen, som ingår i Azure App Service, användas för att framtvinga HTTPS. URL Skriv om modul gör det möjligt för utvecklare att definiera regler som tillämpas på inkommande begäranden innan begäranden lämnas till ditt program. URL Skriv om regler definieras i en web.config-fil som lagras i programmets rot</p>|

### <a name="example"></a>Exempel
Följande exempel innehåller en grundläggande URL-skrivregel som tvingar all inkommande trafik att använda HTTPS
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
Den här regeln fungerar genom att returnera en HTTP-statuskod på 301 (permanent omdirigering) när användaren begär en sida med HTTP. 301 omdirigerar begäran till samma URL som den begärda besökaren, men ersätter HTTP-delen av begäran med HTTPS. Skulle till `HTTP://contoso.com` exempel omdirigeras `HTTPS://contoso.com`till . 

## <a name="enable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>Aktivera HTTP Strikt transportsäkerhet (HSTS)

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [OWASP HTTP Strikt transportsäkerhet lathund](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| **Steg** | <p>HTTP Strict Transport Security (HSTS) är en opt-in säkerhetsförbättring som anges av ett webbprogram med hjälp av ett särskilt svarshuvud. När en webbläsare som stöds tar emot det här huvudet förhindrar webbläsaren att all kommunikation skickas via HTTP till den angivna domänen och skickar i stället all kommunikation via HTTPS. Det förhindrar också HTTPS klicka igenom uppmaningar på webbläsare.</p><p>För att implementera HSTS måste följande svarshuvud konfigureras för en webbplats globalt, antingen i kod eller i config. Strikt-Transport-Säkerhet: max-ålder = 300; includeSubDomains HSTS tar itu med följande hot:</p><ul><li>Användarbokmärken eller manuellt `https://example.com` typer och är föremål för en man-in-the-middle-angripare: HSTS omdirigerar automatiskt HTTP-begäranden till HTTPS för måldomänen</li><li>Webbprogram som är avsett att vara rent HTTPS innehåller oavsiktligt HTTP-länkar eller betjänar innehåll via HTTP: HSTS omdirigerar automatiskt HTTP-begäranden till HTTPS för måldomänen</li><li>En man-in-the-middle angripare försöker avlyssna trafik från en offer användare med hjälp av ett ogiltigt certifikat och hoppas att användaren kommer att acceptera det dåliga certifikatet: HSTS tillåter inte en användare att åsidosätta ogiltigt certifikat meddelande</li></ul>|

## <a name="ensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>Kontrollera kryptering av SQL-serveranslutning och certifikatvalidering

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | SQL Azure  |
| **Attribut**              | SQL-version - V12 |
| **Referenser**              | [Metodtips för att skriva säkra anslutningssträngar för SQL Database](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Steg** | <p>All kommunikation mellan SQL Database och ett klientprogram krypteras alltid med SECURE Sockets Layer (SSL). SQL Database stöder inte okrypterade anslutningar. Om du vill validera certifikat med programkod eller programverktyg begär du uttryckligen en krypterad anslutning och litar inte på servercertifikaten. Om programkoden eller dina programverktyg inte begär en krypterad anslutning får de fortfarande krypterade anslutningar</p><p>De får dock inte validera servercertifikaten och därmed vara mottagliga för "man in the middle"-attacker. Om du vill validera certifikat med ADO.NET `Encrypt=True` `TrustServerCertificate=False` programkod anger du och i databasanslutningssträngen. Om du vill validera certifikat via SQL Server Management Studio öppnar du dialogrutan Anslut till server. Klicka på Kryptera anslutning på fliken Anslutningsegenskaper</p>|

## <a name="force-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>Tvinga krypterad kommunikation till SQL-server

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | OnPrem (onprem) |
| **Attribut**              | SQL-version - MsSQL2016, SQL-version - MsSQL2012, SQL-version - MsSQL2014 |
| **Referenser**              | [Aktivera krypterade anslutningar till databasmotorn](https://msdn.microsoft.com/library/ms191192)  |
| **Steg** | Om du aktiverar SSL-kryptering ökar säkerheten för data som överförs över nätverk mellan instanser av SQL Server och program. |

## <a name="ensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Se till att kommunikationen till Azure Storage är över HTTPS

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Kryptering på Azure Storage Transport-Nivå – med HTTPS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Steg** | För att säkerställa säkerheten för Azure Storage-data under överföring, använd alltid HTTPS-protokollet när du anropar REST-API:erna eller åtkomst till objekt i lagring. Signaturer för delad åtkomst, som kan användas för att delegera åtkomst till Azure Storage-objekt, innehåller också ett alternativ för att ange att endast HTTPS-protokollet kan användas när du använder signaturer för delad åtkomst, så att alla som skickar ut länkar med SAS-token ska använda rätt protokoll.|

## <a name="validate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>Validera MD5-hash efter hämtning av blob om HTTPS inte kan aktiveras

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | StorageType - Blob |
| **Referenser**              | [Översikt över Windows Azure Blob MD5](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Steg** | <p>Windows Azure Blob-tjänsten tillhandahåller mekanismer för att säkerställa dataintegritet både vid program- och transportlager. Om du av någon anledning behöver använda HTTP i stället för HTTPS och du arbetar med blockblobar kan du använda MD5-kontroll för att kontrollera integriteten hos de blobbar som överförs</p><p>Detta kommer att bidra till skydd mot fel i nätverks-/transportlager, men inte nödvändigtvis med mellanliggande attacker. Om du kan använda HTTPS, som ger säkerhet på transportnivå, är det överflödigt och onödigt att använda MD5-kontroll.</p>|

## <a name="use-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>Använd SMB 3.0-kompatibel klient för att säkerställa kryptering med data under överföring till Azure File-resurser

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Mobil klient | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | StorageType - Fil |
| **Referenser**              | [Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [Azure File Storage SMB-stöd för Windows-klienter](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Steg** | Azure File Storage stöder HTTPS när DU använder REST API, men används oftare som en SMB-filresurs som är kopplad till en virtuell dator. SMB 2.1 stöder inte kryptering, så anslutningar tillåts endast inom samma region i Azure. SMB 3.0 stöder dock kryptering och kan användas med Windows Server 2012 R2, Windows 8, Windows 8.1 och Windows 10, vilket ger åtkomst mellan regioner och till och med åtkomst på skrivbordet. |

## <a name="implement-certificate-pinning"></a><a id="cert-pinning"></a>Implementera certifikatnålning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänt, Windows Phone |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Certifikat och offentlig nyckel-pinning](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| **Steg** | <p>Certificate pinning försvarar mot Man-In-The-Middle (MITM) attacker. Pinning är processen att associera en värd med deras förväntade X509-certifikat eller offentlig nyckel. När ett certifikat eller en offentlig nyckel är känd eller sedd för en värd associeras certifikatet eller den offentliga nyckeln eller "fäst" till värden. </p><p>Således, när en motståndare försöker göra SSL MITM-attack, under SSL-handskakning nyckeln från angriparens server kommer att skilja sig från den fästa certifikatets nyckel, och begäran kommer att ignoreras, vilket förhindrar MITM-certifikat nålning kan uppnås genom att genomföra `ServerCertificateValidationCallback` ServicePointManagers delegat.</p>|

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

## <a name="enable-https---secure-transport-channel"></a><a id="https-transport"></a>Aktivera HTTPS - Secure Transport kanal

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | NET Ram 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Steg** | Programkonfigurationen bör säkerställa att HTTPS används för all åtkomst till känslig information.<ul><li>**FÖRKLARING:** Om ett program hanterar känslig information och inte använder kryptering på meddelandenivå bör det endast tillåtas att kommunicera via en krypterad transportkanal.</li><li>**REKOMMENDATIONER:** Kontrollera att HTTP-transport är inaktiverat och aktivera HTTPS-transport i stället. Ersätt till exempel `<httpTransport/>` `<httpsTransport/>` med taggen. Förlita dig inte på en nätverkskonfiguration (brandvägg) för att garantera att programmet endast kan nås via en säker kanal. Ur filosofisk synvinkel bör ansökan inte vara beroende av nätverket för dess säkerhet.</li></ul><p>Ur praktisk synvinkel följer de personer som ansvarar för att säkra nätverket inte alltid säkerhetskraven för programmet när de utvecklas.</p>|

## <a name="wcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: Ange säkerhetsskyddsnivå för meddelanden till EncryptAndSign

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | .NET-ramverk 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Steg** | <ul><li>**FÖRKLARING:** När skyddsnivå är inställd på "ingen" kommer det att inaktivera meddelandeskydd. Konfidentialitet och integritet uppnås med lämplig inställningsnivå.</li><li>**Rekommendationer:**<ul><li>när `Mode=None` - Inaktiverar meddelandeskydd</li><li>när `Mode=Sign` - Tecken men inte kryptera meddelandet; bör användas när dataintegritet är viktigt</li><li>när `Mode=EncryptAndSign` - Signerar och krypterar meddelandet</li></ul></li></ul><p>Överväg att stänga av kryptering och bara signera ditt meddelande när du bara behöver validera integriteten för informationen utan frågor om sekretess. Detta kan vara användbart för åtgärder eller servicekontrakt där du måste validera den ursprungliga avsändaren men inga känsliga data överförs. När du minskar skyddsnivån bör du vara försiktig så att meddelandet inte innehåller några personuppgifter.</p>|

### <a name="example"></a>Exempel
Konfigurera tjänsten och åtgärden för att bara signera meddelandet visas i följande exempel. Exempel på `ProtectionLevel.Sign`servicekontrakt: Följande är ett exempel på hur du använder ProtectionLevel.Sign på servicekontraktsnivå: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Exempel
Exempel på `ProtectionLevel.Sign` operationskontrakt (för detaljerad kontroll): Följande `ProtectionLevel.Sign` är ett exempel på användning på OperationContract-nivå:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="wcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: Använd ett konto med minst privilegierad behörighet för att köra din WCF-tjänst

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | .NET-ramverk 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Steg** | <ul><li>**FÖRKLARING:** Kör inte WCF-tjänster under administratörskontot eller kontot med hög behörighet. om tjänster kompromissar kommer det att leda till stor inverkan.</li><li>**REKOMMENDATIONER:** Använd ett konto med minst privilegierad behörighet för att vara värd för WCF-tjänsten eftersom det minskar programmets angreppsyta och minskar de potentiella skadorna om du blir attackerad. Om tjänstkontot kräver ytterligare åtkomsträttigheter för infrastrukturresurser som MSMQ, händelseloggen, prestandaräknarna och filsystemet, bör lämpliga behörigheter ges till dessa resurser så att WCF-tjänsten kan köras.</li></ul><p>Om din tjänst behöver komma åt specifika resurser för den ursprungliga anroparen använder du personifiering och delegering för att flöda uppringarens identitet för en nedströmsauktoriseringskontroll. I ett utvecklingsscenario använder du det lokala nätverkstjänstkontot, som är ett särskilt inbyggt konto som har nedsatt behörighet. Skapa ett konto med minst privilegierad anpassad domäntjänst i ett produktionsscenario.</p>|

## <a name="force-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>Tvinga all trafik till webb-API:er via HTTPS-anslutning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Tvinga SSL i en webb-API-styrenhet](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Steg** | Om ett program har både en HTTPS- och http-bindning kan klienter fortfarande använda HTTP för att komma åt webbplatsen. För att förhindra detta, använd ett åtgärdsfilter för att säkerställa att begäranden till skyddade API:er alltid är över HTTPS.|

### <a name="example"></a>Exempel 
Följande kod visar ett webb-API-autentiseringsfilter som söker efter SSL: 
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
 
## <a name="ensure-that-communication-to-azure-cache-for-redis-is-over-ssl"></a><a id="redis-ssl"></a>Se till att kommunikationen till Azure Cache for Redis är över SSL

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Cache for Redis | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Support för Azure Redis SSL](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Steg** | Redis-servern stöder inte SSL direkt, men Azure Cache för Redis gör det. Om du ansluter till Azure Cache för Redis och din klient stöder SSL, till exempel StackExchange.Redis, bör du använda SSL. Som standard är icke-SSL-port inaktiverad för nya Azure-cache för Redis-instanser. Kontrollera att de säkra standardinställningarna inte ändras om det inte finns ett beroende av SSL-stöd för redis-klienter. |

Observera att Redis är utformat för att nås av betrodda klienter i betrodda miljöer. Detta innebär att det vanligtvis inte är en bra idé att exponera Redis-instansen direkt till internet eller, i allmänhet, till en miljö där ej betrodda klienter direkt kan komma åt Redis TCP-port eller UNIX-socket. 

## <a name="secure-device-to-field-gateway-communication"></a><a id="device-field"></a>Säker kommunikation mellan enhet och fältgateway

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-fältgateway | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | För IP-baserade enheter kan kommunikationsprotokollet vanligtvis kapslas in i en SSL/TLS-kanal för att skydda data under överföring. För andra protokoll som inte stöder SSL/TLS undersöka om det finns säkra versioner av protokollet som ger säkerhet vid transport- eller meddelandelager. |

## <a name="secure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>Säker kommunikation mellan enheter och molngateway med SSL/TLS

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Välj ditt kommunikationsprotokoll](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Steg** | Säkra HTTP/AMQP- eller MQTT-protokoll med SSL/TLS. |
