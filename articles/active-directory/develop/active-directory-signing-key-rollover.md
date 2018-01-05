---
title: "Signering nyckelförnyelse i Azure AD"
description: "Den här artikeln beskrivs signering nyckelförnyelse bästa praxis för Azure Active Directory"
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2016
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 5396baa57fe0b49809d9fe06eb2b2feda2ed9ba8
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Signering nyckelförnyelse i Azure Active Directory
Den här artikeln beskrivs vad du behöver känna till om de offentliga nycklarna som används i Azure Active Directory (Azure AD) för att signera säkerhetstoken. Det är viktigt att Observera att dessa nycklar förnyelse regelbundet och, i nödfall, kan återställas omedelbart. Alla program som använder Azure AD ska kunna hantera nyckelförnyelse processen eller upprätta en process som regelbundet manuell förnyelse programmässigt. Fortsätt läsa för att förstå hur nycklarna fungerar, hur du utvärdera effekten av förnyelsen ditt program och att uppdatera ditt program eller upprätta en periodiska manuell förnyelse process för att hantera nyckelförnyelse om det behövs.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Översikt över Signeringsnycklar i Azure AD
Azure AD använder offentliga nycklar som bygger på branschstandard för att upprätta förtroende mellan sig själv och de program som använder den. Rent praktiskt detta fungerar på följande sätt: Azure AD använder en signeringsnyckel som består av en offentlig och privat nyckel. När en användare loggar in på ett program som använder Azure AD för autentisering, skapar en säkerhetstoken som innehåller information om användaren i Azure AD. Denna token är signerat av Azure AD med hjälp av den privata nyckeln innan den skickas tillbaka till programmet. Verifiera att token är giltig och har sitt ursprung från Azure AD, måste programmet Validera token signatur med hjälp av den offentliga nyckeln som exponeras av Azure AD som ingår i klientens [OpenID Connect discovery-dokumentet](http://openid.net/specs/openid-connect-discovery-1_0.html) eller SAML / WS-Fed [federation Metadatadokumentet](active-directory-federation-metadata.md).

Av säkerhetsskäl kan Azure AD-signering nyckel samlar regelbundet och, i nödfall, återställas omedelbart. Alla program som kan integreras med Azure AD bör vara beredd att hantera en nyckelförnyelse händelse oavsett hur ofta kan det uppstå. Om det inte, och programmet försöker använda en har upphört att gälla för att verifiera signaturen på ett token, misslyckas inloggningen begäran.

Det finns alltid mer än en giltig nyckel i discovery-dokumentet OpenID Connect och federation Metadatadokumentet. Ditt program bör vara beredd på att använda någon av nycklar som har angetts i dokumentet, eftersom en nyckel kan återställas snart, en annan kan dess ersättare, och så vidare.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Hur du utvärdera om ditt program kommer att påverkas och vad du gör om den
Hur programmet hanterar nyckelförnyelse beror på olika faktorer, till exempel typ av program eller vilka identity-protokollet och biblioteket har använts. I avsnitten nedan bedöma om de vanligaste typerna av program som påverkas av nyckelförnyelse och ge vägledning om hur du uppdaterar programmet för att stödja automatisk förnyelse eller manuellt uppdatera nyckeln.

* [Intern klientprogram att komma åt resurser](#nativeclient)
* [Webbprogram / API: er som har åtkomst till resurser](#webclient)
* [Webbprogram / API: er skydda resurser och skapats med Azure App Service](#appservices)
* [Webbprogram / skydda resurser med hjälp av .NET OWIN OpenID Connect, WS-Fed eller WindowsAzureActiveDirectoryBearerAuthentication mellanprogram-API: er](#owin)
* [Webbprogram / skydda resurser med hjälp av .NET Core OpenID Connect eller JwtBearerAuthentication mellanprogram-API: er](#owincore)
* [Webbprogram / skydda resurser med hjälp av passport-azure-ad-modulen för Node.js-API: er](#passport)
* [Webbprogram / API: er skydda resurser och skapats med Visual Studio 2015 eller Visual Studio 2017](#vs2015)
* [Webbprogram skydda resurser och skapas med Visual Studio 2013](#vs2013)
* [Webb-API: er skydda resurser och skapas med Visual Studio 2013](#vs2013_webapi)
* [Webbprogram skydda resurser och skapas med Visual Studio 2012](#vs2012)
* [Webbprogram skydda resurser och skapas med Visual Studio 2010, 2008-o med hjälp av Windows Identity Foundation](#vs2010)
* [Webbprogram / API: er som skyddar resurser med andra bibliotek eller manuellt använda några av protokoll som stöds](#other)

Den här vägledningen är **inte** gäller för:

* Program som har lagts till från Azure AD Application Gallery (inklusive anpassade) har särskilda riktlinjer med avseende på Signeringsnycklar. [Mer information.](../active-directory-sso-certs.md)
* Lokalt program som publicerats via programproxy inte behöver bry dig om Signeringsnycklar.

### <a name="nativeclient"></a>Intern klientprogram att komma åt resurser
Program som kommer endast åt resurser (dvs Microsoft Graph, KeyVault, API för Outlook och andra Microsoft-APIs) vanligtvis bara hämta en token och skickar den längs till resursägare. Med tanke på att de inte skyddar alla resurser kan kontrollera inte token och därför behöver inte se till att den är korrekt signerad.

Native client-program, om desktop eller mobile, tillhör den här kategorin och därför påverkas inte av förnyelsen.

### <a name="webclient"></a>Webbprogram / API: er som har åtkomst till resurser
Program som kommer endast åt resurser (dvs Microsoft Graph, KeyVault, API för Outlook och andra Microsoft-APIs) vanligtvis bara hämta en token och skickar den längs till resursägare. Med tanke på att de inte skyddar alla resurser kan kontrollera inte token och därför behöver inte se till att den är korrekt signerad.

Webbprogram och webb-API: er som använder endast app-flöde (klientens autentiseringsuppgifter / klientcertifikat), tillhör den här kategorin och därför inte påverkas av förnyelsen.

### <a name="appservices"></a>Webbprogram / API: er skydda resurser och skapats med Azure App Service
Azure Apptjänst autentisering / auktorisering (EasyAuth)-funktioner redan har den nödvändiga logiken för att hantera nyckelförnyelse automatiskt.

### <a name="owin"></a>Webbprogram / skydda resurser med hjälp av .NET OWIN OpenID Connect, WS-Fed eller WindowsAzureActiveDirectoryBearerAuthentication mellanprogram-API: er
Om ditt program använder de .NET OWIN OpenID Connect, WS-Fed eller WindowsAzureActiveDirectoryBearerAuthentication mellanprogram har redan den nödvändiga logiken för att hantera nyckelförnyelse automatiskt.

Du kan bekräfta att programmet använder någon av dessa genom att söka efter någon av följande kodavsnitt i ditt program Startup.cs eller Startup.Auth.cs

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

### <a name="owincore"></a>Webbprogram / skydda resurser med hjälp av .NET Core OpenID Connect eller JwtBearerAuthentication mellanprogram-API: er
Om ditt program använder .NET Core OWIN OpenID Connect eller JwtBearerAuthentication mellanprogram, har redan den nödvändiga logiken för att hantera nyckelförnyelse automatiskt.

Du kan bekräfta att programmet använder någon av dessa genom att söka efter någon av följande kodavsnitt i ditt program Startup.cs eller Startup.Auth.cs

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

### <a name="passport"></a>Webbprogram / skydda resurser med hjälp av passport-azure-ad-modulen för Node.js-API: er
Om ditt program använder Node.js passport-ad-modulen har redan den nödvändiga logiken för att hantera nyckelförnyelse automatiskt.

Du kan bekräfta att din ansökan passport-ad genom att söka efter följande kodavsnitt i ditt program app.js

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Webbprogram / API: er skydda resurser och skapats med Visual Studio 2015 eller Visual Studio 2017
Om programmet har skapats med en mall för program i Visual Studio 2015 eller Visual Studio 2017 och du har valt **arbets-och Skolkonton** från den **ändra autentisering** menyn den redan har den nödvändiga logiken för att hantera nyckelförnyelse automatiskt. Den här logiken inbäddat i OWIN OpenID Connect-mellanprogram hämtar och cachelagrar nycklarna från OpenID Connect discovery-dokumentet och uppdaterar regelbundet hur dem.

Om autentisering manuellt läggas till i din lösning kanske inte nödvändiga nyckelförnyelse logiken i ditt program. Du behöver skriva den själv eller följer du stegen i [webbprogram / API: er med hjälp av andra bibliotek eller manuellt använda några av protokoll som stöds](#other).

### <a name="vs2013"></a>Webbprogram skydda resurser och skapas med Visual Studio 2013
Om programmet har skapats med en mall för program i Visual Studio 2013 och du har valt **Organisationskonton** från den **ändra autentisering** menyn den redan har den nödvändiga logiken för att hantera nyckelförnyelse automatiskt. Den här logiken lagrar Unik identifierare för din organisation och signering nyckelinformation i två databastabeller som är kopplade till projektet. Du hittar anslutningssträngen för databasen i projektets Web.config-fil.

Om autentisering manuellt läggas till i din lösning kanske inte nödvändiga nyckelförnyelse logiken i ditt program. Du behöver skriva den själv eller följer du stegen i [webbprogram / API: er med hjälp av andra bibliotek eller manuellt använda några av protokoll som stöds.](#other).

Följande steg hjälper dig att kontrollera att logiken som fungerar i ditt program.

1. Öppna lösningen i Visual Studio 2013 och klicka sedan på den **Server Explorer** fliken i det högra fönstret.
2. Expandera **dataanslutningar**, **DefaultConnection**, och sedan **tabeller**. Leta upp den **IssuingAuthorityKeys** tabell, högerklicka på den och klicka sedan på **visa tabelldata**.
3. I den **IssuingAuthorityKeys** tabell, ska det finnas minst en rad som motsvarar tumavtrycket värdet för nyckeln. Ta bort alla rader i tabellen.
4. Högerklicka på den **klienter** tabell och klicka sedan på **visa tabelldata**.
5. I den **hyresgäster** tabell, kommer det att minst en rad som motsvarar en unik katalog klient identifierare. Ta bort alla rader i tabellen. Om du inte tar bort raderna i både den **klienter** tabell och **IssuingAuthorityKeys** tabell, du får ett fel vid körning.
6. Skapa och köra programmet. Du kan stoppa programmet när du har loggat in till ditt konto.
7. Gå tillbaka till den **Server Explorer** och titta på värdena i den **IssuingAuthorityKeys** och **hyresgäster** tabell. Lägg märke till att de har varit automatiskt fylls med lämplig information från federation Metadatadokumentet.

### <a name="vs2013"></a>Webb-API: er skydda resurser och skapas med Visual Studio 2013
Om du har skapat ett webb-API-program i Visual Studio 2013 med hjälp av Web API-mall och sedan markerade **Organisationskonton** från den **ändra autentisering** menyn du redan har den nödvändiga logiken i ditt program.

Om du har manuellt konfigurerade autentisering, följer du anvisningarna nedan för att lära dig hur du konfigurerar dina webb-API för att automatiskt uppdatera dess viktig information.

Följande kodavsnitt visar hur du hämta de senaste nycklarna från federation metadata dokument och sedan använda den [JWT-Token hanteraren](https://msdn.microsoft.com/library/dn205065.aspx) att validera token. Kodfragmentet förutsätter att du ska använda egna cachelagringsmekanism för för att spara nyckeln för att validera framtida token från Azure AD, oavsett om den är i en databas, konfigurationsfilen eller någon annanstans.

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

### <a name="vs2012"></a>Webbprogram skydda resurser och skapas med Visual Studio 2012
Om programmet har skapats i Visual Studio 2012, används du förmodligen identitet och åtkomst för att konfigurera ditt program. Det är också troligt att du använder den [verifierar Utfärdarens namn registret (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). VINR ansvarar för att underhålla information om betrodda identitetsleverantörer (Azure AD) och nycklarna som används för att validera token som utfärdats av dem. VINR gör det också enkelt att automatiskt uppdatera viktig information lagras i en Web.config-fil genom att hämta den senaste federation metadata dokument som är kopplade till din katalog kontrollerar om konfigurationen är inaktuell med senaste dokumentet och uppdatera programmet så att den nya nyckeln som behövs.

Om du har skapat ditt program med hjälp av kodexempel eller genomgången dokumentationen från Microsoft ingår redan nyckelförnyelse logiken i projektet. Du kommer att märka att det redan finns koden nedan i projektet. Om programmet inte redan har denna logik följer du stegen nedan för att lägga till den och kontrollera att den fungerar korrekt.

1. I **Solution Explorer**, lägga till en referens till den **System.IdentityModel** sammansättningen för rätt projekt.
2. Öppna den **Global.asax.cs** och Lägg till följande med hjälp av direktiven:
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
4. Anropa den **RefreshValidationSettings()** metod i den **Application_Start()** metod i **Global.asax.cs** som visas:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

När du har följt de här stegen kan uppdateras programmets Web.config med den senaste informationen från federation metadata dokument, inklusive de senaste nycklarna. Den här uppdateringen sker varje gång en programpool återanvänds i IIS. IIS är som standard att återanvända program var 29: e timme.

Följ stegen nedan för att kontrollera att nyckelförnyelse logiken fungerar.

1. När du har kontrollerat att programmet använder koden ovan, öppna den **Web.config** fil och navigera till den  **<issuerNameRegistry>**  block, särskilt söker efter följande några rader:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. I den  **<add thumbprint=””>**  inställningen genom att ändra värdet tumavtryck genom att ersätta ett tecken med ett annat namn. Spara den **Web.config** fil.
3. Skapa programmet och sedan köra den. Om du kan slutföra processen inloggning, uppdateras har nyckeln genom att hämta nödvändig information från din katalog federation metadata dokument i ditt program. Om du har problem att logga in, kontrollera ändringarna i ditt program är korrekta genom att läsa den [att lägga till inloggning till din webbserver program med hjälp av Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) artikel, eller hämta och kontrollera följande kodexempel: [ Flera innehavare molnet program för Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="vs2010"></a>Webbprogram skydda resurser och skapas med Visual Studio 2008 eller 2010 och Windows Identity Foundation (WIF) version 1.0 för .NET 3.5
Om du har skapat ett program på WIF v1.0 finns inga angivna mekanism för att automatiskt uppdatera ditt programs konfiguration om du vill använda en ny nyckel.

* *Enklast* använder FedUtil-verktygsuppsättning som ingår i WIF SDK, som kan hämta den senaste Metadatadokumentet och uppdatera din konfiguration.
* Uppdatera ditt program till .NET 4.5, som innehåller den senaste versionen av WIF finns i System-namnområdet. Du kan sedan använda den [verifierar Utfärdarens namn registret (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) att utföra automatiska uppdateringar av programmets konfiguration.
* Utföra en manuell förnyelse enligt anvisningarna i slutet av dokumentet vägledning.

Instruktioner för att använda FedUtil för att uppdatera konfigurationen:

1. Kontrollera att du har WIF v1.0 SDK är installerat på utvecklingsdatorn för Visual Studio 2008 eller 2010. Du kan [ladda ned det från den här](https://www.microsoft.com/en-us/download/details.aspx?id=4451) om du ännu inte har installerat den.
2. Öppna en lösning i Visual Studio och högerklicka på tillämpliga projektet och välj **uppdatera federationsmetadata**. Om det här alternativet inte är tillgängligt har FedUtil och/eller WIF v1.0 SDK inte installerats.
3. Välj prompten **uppdatering** ska börja uppdatera din federationsmetadata. Om du har åtkomst till servermiljö där programmet körs kan du också använda Fedutil's [uppdateringsschema för automatisk metadata](https://msdn.microsoft.com/library/ee517272.aspx).
4. Klicka på **Slutför** att slutföra uppdateringsprocessen.

### <a name="other"></a>Webbprogram / API: er som skyddar resurser med andra bibliotek eller manuellt använda några av protokoll som stöds
Om du använder vissa andra bibliotek eller manuellt implementerats någon av protokoll som stöds, behöver du granska biblioteket eller implementeringen så att nyckeln hämtas från OpenID Connect discovery-dokumentet eller federation Metadatadokumentet. Ett sätt att kontrollera om det är att göra en sökning i koden eller bibliotekets kod efter samtal ut till OpenID discovery-dokumentet eller federation Metadatadokumentet.

Om de nycklar lagras någonstans eller hårdkodad i ditt program kan du manuellt hämta nyckeln och uppdatera den i enlighet med detta genom att utföra en manuell förnyelse enligt anvisningarna i slutet av dokumentet vägledning. **Det rekommenderas starkt att du förbättra programmet att stödja automatisk förnyelse** med någon av metoderna disposition i den här artikeln för att undvika framtida avbrott och omkostnader om Azure AD ökar dess förnyade takt eller har en nödsituation out-of-band-förnyelse.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Hur du testar programmet att fastställa om det kommer att påverkas
Du kan kontrollera om ditt program stöder automatisk nyckelförnyelse genom att hämta skripten och följa anvisningarna i [GitHub-lagringsplatsen.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Hur du utför en manuell förnyelse om programmet inte stöder automatisk förnyelse
Om programmet inte **inte** stöd för automatisk förnyelse, måste du upprätta en process som regelbundet övervakar Azure AD signering nycklar och utför en manuell förnyelse därefter. [Den här GitHub-lagringsplatsen](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) innehåller skript och instruktioner om hur du gör detta.

