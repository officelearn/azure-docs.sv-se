---
title: Signeringsnyckel i Azure AD
description: Den här artikeln beskriver signering nyckelförnyelse Metodtips för Azure Active Directory
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2018
ms.author: celested
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82e9941a6c468a3b0ed9d1f22a2970cfa6584617
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439353"
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Signeringsnyckel i Azure Active Directory
Den här artikeln beskriver vad du behöver veta om de offentliga nycklarna som används i Azure Active Directory (Azure AD) för att logga säkerhetstoken. Det är viktigt att Observera att dessa nycklar förnya regelbundet och, i nödfall, kan distribueras omedelbart. Alla program som använder Azure AD ska kunna programmässigt hantera nyckelförnyelse processen eller upprätta en process som regelbundet manuell förnyelse. Läs vidare för att förstå hur nycklarna fungerar, hur du kan utvärdera effekten av förnyelse för ditt program och hur du uppdaterar ditt program eller upprätta en regelbunden manuell förnyelse process för att hantera nyckelförnyelse om det behövs.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Översikt över Signeringsnycklar i Azure AD
Azure AD använder kryptografi med offentliga nycklar bygger på branschstandarder för att upprätta förtroende mellan själva och de program som använder den. I praktiken kan fungerar det här på följande sätt: Azure AD använder en signeringsnyckel som består av en offentlig och privat nyckel. När en användare loggar in till ett program som använder Azure AD för autentisering, skapar en säkerhetstoken som innehåller information om användaren i Azure AD. Denna token är signerat av Azure AD med dess privata nyckel innan den skickas tillbaka till programmet. Om du vill verifiera att token är giltig och har sitt ursprung från Azure AD genom programmet måste validera token signatur med hjälp av den offentliga nyckeln som exponeras av Azure AD som finns i klientens [OpenID Connect discovery-dokumentet](https://openid.net/specs/openid-connect-discovery-1_0.html) eller SAML / WS-Fed [federationsmetadatadokumentet](azure-ad-federation-metadata.md).

Av säkerhetsskäl, kan Azure Active Directorys signering viktiga samlar regelbundet och, i nödfall, distribueras omedelbart. Alla program som kan integreras med Azure AD bör vara beredd att hantera en nyckelförnyelse händelse oavsett hur ofta kan det uppstå. Om inte, och programmet försöker använda en har upphört att gälla för att verifiera signaturen på en token, misslyckas inloggningsbegäranden.

Det finns alltid mer än en giltig nyckel i OpenID Connect discovery-dokumentet och federationsmetadatadokumentet. Programmet bör vara beredd på att använda någon av nycklarna som anges i dokumentet, eftersom en nyckel kan återställas snart, en annan kan vara har ersatts och så vidare.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Hur du avgör om ditt program kommer att påverkas och vad du gör om det.
Hur programmet hanterar nyckelförnyelse beror på olika faktorer, till exempel typ av program eller vilka identity-protokollet och biblioteket har använts. I avsnitten nedan bedöma om de vanligaste typerna av program som påverkas av nyckelförnyelse och ger vägledning om hur du uppdaterar programmet till stöd för automatisk förnyelse av eller uppdatera nyckeln manuellt.

* [Interna klientprogram som har åtkomst till resurser](#nativeclient)
* [Webbprogram / API: er som har åtkomst till resurser](#webclient)
* [Webbprogram / API: er skyddar resurser och skapats med Azure App Services](#appservices)
* [Webbprogram / API: er som skyddar resurser med hjälp av .NET OWIN OpenID Connect, WS-Fed eller WindowsAzureActiveDirectoryBearerAuthentication mellanprogram](#owin)
* [Webbprogram / API: er som skyddar resurser med hjälp av .NET Core OpenID Connect eller JwtBearerAuthentication mellanprogram](#owincore)
* [Webbprogram / API: er som skyddar resurser med hjälp av Node.js passport-azure-ad-modulen](#passport)
* [Webbprogram / API: er skyddar resurser och skapas med Visual Studio 2015 eller Visual Studio 2017](#vs2015)
* [Webbprogram skyddar resurser och skapas med Visual Studio 2013](#vs2013)
* Webb-API: er skyddar resurser och skapas med Visual Studio 2013
* [Webbprogram skyddar resurser och skapas med Visual Studio 2012](#vs2012)
* [Webbprogram skyddar resurser och skapas med Visual Studio 2010, 2008-o med hjälp av Windows Identity Foundation](#vs2010)
* [Webbprogram / API: er som skyddar resurser med hjälp av andra bibliotek eller manuellt implementera någon av protokoll som stöds](#other)

Den här vägledningen är **inte** gäller för:

* Program som har lagts till från Azure AD-Programgalleriet (inklusive anpassade) ha särskilda riktlinjer för Signeringsnycklar. [Mer information.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* Lokala program som publicerats via programproxy inte behöver bekymra dig om Signeringsnycklar.

### <a name="nativeclient"></a>Interna klientprogram som har åtkomst till resurser
Program som kommer endast åt resurser (dvs.) Microsoft Graph, KeyVault, API för Outlook och andra Microsoft-APIs) i allmänhet bara hämta en token och skickar den vidare till ägare till resursen. Med hänsyn till att de inte skyddar resurser, granska inte token och behöver därför inte att se till att den är korrekt signerad.

Interna klientprogram, oavsett om desktop eller mobile, i den här kategorin och därför påverkas inte av uppdateringen.

### <a name="webclient"></a>Webbprogram / API: er som har åtkomst till resurser
Program som kommer endast åt resurser (dvs.) Microsoft Graph, KeyVault, API för Outlook och andra Microsoft-APIs) i allmänhet bara hämta en token och skickar den vidare till ägare till resursen. Med hänsyn till att de inte skyddar resurser, granska inte token och behöver därför inte att se till att den är korrekt signerad.

Webbprogram och webb-API: er som använder flödet appspecifika (klientautentiseringsuppgifter / klientcertifikat), i den här kategorin och därför inte påverkas av uppdateringen.

### <a name="appservices"></a>Webbprogram / API: er skyddar resurser och skapats med Azure App Services
Azure App Services' autentisering / auktorisering (EasyAuth)-funktionen redan har den nödvändiga logiken för att hantera nyckelförnyelse automatiskt.

### <a name="owin"></a>Webbprogram / API: er som skyddar resurser med hjälp av .NET OWIN OpenID Connect, WS-Fed eller WindowsAzureActiveDirectoryBearerAuthentication mellanprogram
Om ditt program använder de .NET OWIN OpenID Connect, WS-Fed eller WindowsAzureActiveDirectoryBearerAuthentication mellanprogram har redan den nödvändiga logiken för att hantera nyckelförnyelse automatiskt.

Du kan bekräfta att ditt program använder någon av dessa genom att söka efter någon av följande kodfragment i ditt programs Startup.cs eller Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
     });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="owincore"></a>Webbprogram / API: er som skyddar resurser med hjälp av .NET Core OpenID Connect eller JwtBearerAuthentication mellanprogram
Om ditt program använder .NET Core OWIN OpenID Connect eller JwtBearerAuthentication mellanprogram, har den redan den nödvändiga logiken för att hantera nyckelförnyelse automatiskt.

Du kan bekräfta att ditt program använder någon av dessa genom att söka efter någon av följande kodfragment i ditt programs Startup.cs eller Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="passport"></a>Webbprogram / API: er som skyddar resurser med hjälp av Node.js passport-azure-ad-modulen
Om ditt program använder Node.js passport-ad-modulen, har den redan den nödvändiga logiken för att hantera nyckelförnyelse automatiskt.

Du kan bekräfta att ditt program passport-ad genom att söka efter följande kodfragment i ditt programs app.js

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Webbprogram / API: er skyddar resurser och skapas med Visual Studio 2015 eller Visual Studio 2017
Om ditt program har skapats med en mall för programmet i Visual Studio 2015 eller Visual Studio 2017 och du har valt **arbets-och Skolkonton** från den **ändra autentisering** menyn den redan den nödvändiga logiken för att hantera nyckelförnyelse automatiskt. Den här logiken inbäddad i OWIN OpenID Connect-middleware, hämtar och cachelagrar nycklarna från OpenID Connect discovery-dokumentet och uppdaterar dem regelbundet.

Lägg till autentisering till din lösning manuellt och kanske programmet inte har logiken som behövs nyckelförnyelse. Du kommer att behöva skriva den själv eller följer du stegen i [webbprogram / API: er med andra bibliotek eller manuellt implementera någon av protokoll som stöds](#other).

### <a name="vs2013"></a>Webbprogram skyddar resurser och skapas med Visual Studio 2013
Om ditt program har skapats med en mall för programmet i Visual Studio 2013 och du har valt **Organisationskonton** från den **ändra autentisering** menyn den redan har den nödvändiga logiken att hantera nyckelförnyelse automatiskt. Den här logiken lagrar Unik identifierare för din organisation och signering nyckelinformationen i två databastabeller som är kopplade till projektet. Du hittar anslutningssträngen för databasen i projektets Web.config-filen.

Lägg till autentisering till din lösning manuellt och kanske programmet inte har logiken som behövs nyckelförnyelse. Du behöver skriva den själv eller följer du stegen i [webbprogram / API: er med hjälp av andra bibliotek eller manuellt använda några av protokoll som stöds.](#other).

Följande steg hjälper dig att kontrollera att logiken som fungerar i ditt program.

1. Öppna lösningen i Visual Studio 2013, och klicka sedan på den **Server Explorer** fliken i det högra fönstret.
2. Expandera **dataanslutningar**, **DefaultConnection**, och sedan **tabeller**. Leta upp den **IssuingAuthorityKeys** tabellen, högerklicka på den och klicka sedan på **visa tabelldata**.
3. I den **IssuingAuthorityKeys** tabellen, kommer det finnas minst en rad, vilket motsvarar tumavtrycksvärde för nyckeln. Ta bort alla rader i tabellen.
4. Högerklicka på den **klienter** tabellen och klicka sedan på **visa tabelldata**.
5. I den **klienter** tabellen, kommer det finnas minst en rad som motsvarar en unik katalog klient-ID. Ta bort alla rader i tabellen. Om du inte tar bort rader i både den **klienter** tabell och **IssuingAuthorityKeys** tabell, du får ett fel vid körning.
6. Skapa och kör programmet. När du har loggat in på ditt konto kan stoppa du programmet.
7. Gå tillbaka till den **Server Explorer** och titta på värdena i den **IssuingAuthorityKeys** och **klienter** tabell. Lägg märke till att de har varit automatiskt fyllas i på nytt med lämplig information från federationsmetadatadokumentet.

### <a name="vs2013"></a>Webb-API: er skyddar resurser och skapas med Visual Studio 2013
Om du har skapat ett webb-API-program i Visual Studio 2013 med hjälp av webb-API-mallen och sedan valt **Organisationskonton** från den **ändra autentisering** menyn du redan har nödvändigt logik i ditt program.

Om du har manuellt konfigurerade autentisering, följer du anvisningarna nedan för att lära dig hur du konfigurerar ditt webb-API för att automatiskt uppdatera dess viktig information.

Följande kodfragment visar hur du kan hämta de senaste nycklarna från federationsmetadatadokumentet och sedan använda den [JWT-Token Handler](https://msdn.microsoft.com/library/dn205065.aspx) att validera token. Kodfragmentet förutsätter att du ska använda egna cachelagringsmekanism för att spara nyckeln för att validera framtida token från Azure AD, oavsett om det är i en databas, konfigurationsfilen eller någon annanstans.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>Webbprogram skyddar resurser och skapas med Visual Studio 2012
Om ditt program har skapats i Visual Studio 2012 använt du förmodligen identitet och åtkomst verktyget konfigurerar programmet. Det är troligt att du använder den [verifierar Utfärdarens namn register (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). VINR ansvarar för att underhålla information om betrodda identitetsleverantörer (Azure AD) och alla nycklarna som används för att validera token som utfärdas av dem. VINR också gör det enkelt att automatiskt uppdatera den viktiga information som lagras i en Web.config-fil genom att ladda ned den senaste federationsmetadatadokumentet som är associerade med din katalog kontrollerar om konfigurationen är inaktuell med senaste dokumentet, och Uppdatera programmet för att använda den nya nyckeln som behövs.

Om du har skapat ditt program med någon av kodexempel eller genomgången dokumentationen från Microsoft ingår redan nyckelförnyelse logiken i ditt projekt. Du ser att det redan finns koden nedan i projektet. Om programmet inte redan har den här logiken följer du stegen nedan för att lägga till den och kontrollera att den fungerar korrekt.

1. I **Solution Explorer**, lägga till en referens till den **System.IdentityModel** sammansättningen för lämpligt projekt.
2. Öppna den **Global.asax.cs** filen och Lägg till följande med hjälp av direktiv:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Lägg till följande metod för att den **Global.asax.cs** fil:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Anropa den **RefreshValidationSettings()** -metod i den **Application_Start()** -metod i **Global.asax.cs** enligt:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

När du har följt de här stegen, uppdateras programmets Web.config med den senaste informationen från federationsmetadatadokumentet, inklusive de senaste nycklarna. Den här uppdateringen sker varje gång din programpool återanvänds i IIS. IIS är som standard att återanvända program var 29: e timme.

Följ stegen nedan för att kontrollera att logiken som nyckelförnyelse fungerar.

1. När du har kontrollerat att ditt program använder koden ovan, öppna den **Web.config** filen och navigera till den  **\<issuerNameRegistry >** block, mer specifikt söker den efter några få rader:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. I den  **\<Lägg till tumavtrycket = ”” >** ändra värdet för tumavtryck genom att ersätta alla tecken med ett annat namn. Spara den **Web.config** fil.
3. Skapa programmet och sedan köra den. Om du kan slutföra inloggningsprocessen, uppdateras har nyckeln genom att hämta nödvändig information från din katalogs federationsmetadatadokumentet i ditt program. Om du har problem med inloggning, se till att ändringarna i ditt program är korrekta genom att läsa den [att lägga till inloggning till dina webb-program med hjälp av Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) artikeln, eller hämta och granska följande kodexempel: [Molnprogram med flera innehavare för Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="vs2010"></a>Webbprogram skyddar resurser och skapas med Visual Studio 2008 eller 2010 och Windows Identity Foundation (WIF) v1.0 för .NET 3.5
Om du har byggt ett program på WIF v1.0 finns inga angivna mekanism för att automatiskt uppdatera konfigurationen för ditt program om du vill använda en ny nyckel.

* *Enklast* Använd FedUtil-verktyg som ingår i WIF SDK, som kan hämta det senaste Metadatadokumentet och uppdatera din konfiguration.
* Uppdatera appen till .NET 4.5, som innehåller den senaste versionen av WIF finns i System-namnområdet. Du kan sedan använda den [verifierar Utfärdarens namn register (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) att utföra automatiska uppdateringar av programmets konfiguration.
* Utföra en manuell förnyelse enligt anvisningarna i slutet av det här vägledningsdokumentet.

Anvisningar för att använda FedUtil för att uppdatera din konfiguration:

1. Kontrollera att du har WIF v1.0 SDK är installerat på utvecklingsdatorn för Visual Studio 2008 eller 2010. Du kan [ladda ned den här](https://www.microsoft.com/en-us/download/details.aspx?id=4451) om du ännu inte har installerat den.
2. Öppna i Visual Studio-lösningen, och sedan högerklickar du på tillämpliga projektet och välj **uppdatera federationsmetadata**. Om det här alternativet inte är tillgänglig har FedUtil och/eller WIF v1.0 SDK inte installerats.
3. Välj prompten **uppdatering** att börja uppdatera din federationsmetadata. Om du har åtkomst till servermiljö där programmet körs kan du också använda Fedutil's [automatisk metadata update scheduler](https://msdn.microsoft.com/library/ee517272.aspx).
4. Klicka på **Slutför** att slutföra uppdateringsprocessen.

### <a name="other"></a>Webbprogram / API: er som skyddar resurser med hjälp av andra bibliotek eller manuellt implementera någon av protokoll som stöds
Om du använder några andra bibliotek eller manuellt implementerats någon av protokoll som stöds kan behöver du granska i biblioteket eller implementeringen så att nyckeln hämtas från OpenID Connect discovery-dokumentet eller federationsmetadata dokumentet. Ett sätt att kontrollera det här är att göra en sökning i din kod eller kod i biblioteket för något anrop ut till OpenID discovery-dokumentet eller federationsmetadatadokumentet.

Om nyckeln lagras någonstans eller hårdkodade i ditt program kan du manuellt hämta nyckeln och uppdatera den i enlighet med detta genom att utföra en manuell förnyelse enligt anvisningarna i slutet av det här vägledningsdokumentet. **Det rekommenderas starkt att du förbättra dina program som stöder automatisk förnyelse** med någon av metoderna disposition i den här artikeln för att undvika framtida avbrott och kostnader om Azure AD ökar dess förnya takt eller har en nödsituation out-of-band-förnyelse.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Testa ditt program för att avgöra om det kommer att påverkas
Du kan verifiera om programmet stöder automatisk nyckelförnyelse genom att hämta skripten och följa anvisningarna i [den här GitHub-lagringsplatsen.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Hur du utför en manuell förnyelse om programmet inte stöder automatisk förnyelse
Om ditt program har **inte** stöd för automatisk förnyelse, måste du upprätta en process som regelbundet övervakar Azure AD signering nycklar och utför en manuell förnyelse därefter. [Den här GitHub-lagringsplatsen](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) innehåller skript och instruktioner om hur du gör detta.

