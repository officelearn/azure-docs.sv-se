---
title: Signering av nyckelöverrullning i Azure AD
description: I den här artikeln beskrivs metodtips för signeringsnyckelvält för Azure Active Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: f3585cfa7ea6f0d8afc61e899f9641d415a2e354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161196"
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Signering av nyckelöverrullning i Azure Active Directory
I den här artikeln beskrivs vad du behöver veta om de offentliga nycklar som används i Azure Active Directory (Azure AD) för att signera säkerhetstoken. Det är viktigt att notera att dessa nycklar rulla över regelbundet och, i en nödsituation, kan rullas över omedelbart. Alla program som använder Azure AD ska kunna hantera nyckelförtrollningsprocessen programmatiskt eller upprätta en periodisk manuell överrullningsprocess. Fortsätt läsa för att förstå hur nycklarna fungerar, hur du bedömer effekten av överrullningen till ditt program och hur du uppdaterar ditt program eller upprättar en periodisk manuell överrullningsprocess för att hantera nyckelvälpning om det behövs.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Översikt över signeringsnycklar i Azure AD
Azure AD använder kryptering med offentliga nycklar som bygger på branschstandarder för att skapa förtroende mellan sig själv och de program som använder den. I praktiska termer fungerar detta på följande sätt: Azure AD använder en signeringsnyckel som består av ett offentligt och privat nyckelpar. När en användare loggar in på ett program som använder Azure AD för autentisering skapar Azure AD en säkerhetstoken som innehåller information om användaren. Den här token signeras av Azure AD med hjälp av sin privata nyckel innan den skickas tillbaka till programmet. För att verifiera att token är giltig och kommer från Azure AD måste programmet validera tokens signatur med hjälp av den offentliga nyckeln som exponeras av Azure AD och som finns i klientens [OpenID Connect-identifieringsdokument](https://openid.net/specs/openid-connect-discovery-1_0.html) eller SAML/WS-Fed-federationmetadatadokument . [federation metadata document](../azuread-dev/azure-ad-federation-metadata.md)

Av säkerhetsskäl rullar Azure AD:s signeringsnyckel regelbundet och, i händelse av en nödsituation, kan rullas över omedelbart. Alla program som integreras med Azure AD bör vara beredda att hantera en nyckelförstrollningshändelse oavsett hur ofta det kan inträffa. Om den inte gör det och ditt program försöker använda en utgången nyckel för att verifiera signaturen på en token, misslyckas inloggningsbegäran.

Det finns alltid mer än en giltig nyckel tillgänglig i OpenID Connect-identifieringsdokumentet och federationsmetadatadokumentet. Din ansökan bör vara beredd att använda någon av de nycklar som anges i dokumentet, eftersom en nyckel kan rullas snart, kan en annan vara dess ersättning, och så vidare.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Hur du bedömer om din ansökan kommer att påverkas och vad du ska göra åt det
Hur programmet hanterar nyckelöverrullning beror på variabler som typen av program eller vilket identitetsprotokoll och bibliotek som användes. I avsnitten nedan utvärderas om de vanligaste typerna av program påverkas av nyckelförtrollningen och ger vägledning om hur du uppdaterar programmet för att stödja automatisk överrullning eller manuellt uppdatera nyckeln.

* [Inbyggda klientprogram som använder resurser](#nativeclient)
* [Webbprogram /API:er som använder resurser](#webclient)
* [Webbprogram/API:er som skyddar resurser och skapas med Azure App Services](#appservices)
* [Webbprogram/API:er som skyddar resurser med hjälp av .NET OWIN OpenID Connect, WS-Fed eller WindowsAzureActiveDirectoryBearerAuthentication middleware](#owin)
* [Webbprogram / API:er som skyddar resurser med hjälp av .NET Core OpenID Connect eller JwtBearerAuthentication middleware](#owincore)
* [Webbprogram / API:er som skyddar resurser med node.js passport-azure-ad-modul](#passport)
* [Webbprogram/API:er som skyddar resurser och skapas med Visual Studio 2015 eller senare](#vs2015)
* [Webbprogram som skyddar resurser och skapas med Visual Studio 2013](#vs2013)
* Webb-API:er som skyddar resurser och skapas med Visual Studio 2013
* [Webbprogram som skyddar resurser och skapas med Visual Studio 2012](#vs2012)
* [Webbprogram som skyddar resurser och skapas med Visual Studio 2010, 2008 o med Windows Identity Foundation](#vs2010)
* [Webbprogram/API:er som skyddar resurser med andra bibliotek eller implementerar något av de protokoll som stöds](#other)

Denna vägledning är **inte** tillämplig för:

* Program som läggs till från Azure AD Application Gallery (inklusive Anpassad) har separat vägledning när det gäller signeringsnycklar. [Mer information.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* Lokala program som publiceras via programproxy behöver inte oroa sig för signeringsnycklar.

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>Inbyggda klientprogram som använder resurser
Program som bara har åtkomst till resurser (dvs. Microsoft Graph, KeyVault, Outlook API och andra Microsoft API:er) hämtar i allmänhet bara en token och skickar den vidare till resursägaren. Med tanke på att de inte skyddar några resurser inspekterar de inte token och behöver därför inte se till att den är korrekt signerad.

Inbyggda klientprogram, oavsett om de är stationära eller mobila, hör till denna kategori och påverkas därför inte av överrullningen.

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>Webbprogram /API:er som använder resurser
Program som bara har åtkomst till resurser (dvs. Microsoft Graph, KeyVault, Outlook API och andra Microsoft API:er) hämtar i allmänhet bara en token och skickar den vidare till resursägaren. Med tanke på att de inte skyddar några resurser inspekterar de inte token och behöver därför inte se till att den är korrekt signerad.

Webbprogram och webb-API:er som använder flödet endast för appar (klientautentiseringsuppgifter/klientcertifikat), hör till den här kategorin och påverkas därför inte av överrullningen.

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>Webbprogram/API:er som skyddar resurser och skapas med Azure App Services
Azure App Services autentisering / auktorisering (EasyAuth) funktionalitet har redan den nödvändiga logiken för att hantera nyckelöverrullning automatiskt.

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>Webbprogram/API:er som skyddar resurser med hjälp av .NET OWIN OpenID Connect, WS-Fed eller WindowsAzureActiveDirectoryBearerAuthentication middleware
Om ditt program använder .NET OWIN OpenID Connect, WS-Fed eller WindowsAzureActiveDirectoryBearerAuthentication middleware, har det redan den logik som krävs för att hantera nyckelöverrullning automatiskt.

Du kan bekräfta att ditt program använder något av detta genom att leta efter något av följande utdrag i programmets Startup.cs eller Startup.Auth.cs

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

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>Webbprogram / API:er som skyddar resurser med hjälp av .NET Core OpenID Connect eller JwtBearerAuthentication middleware
Om ditt program använder .NET Core OWIN OpenID Connect eller JwtBearerAuthentication middleware, har det redan den logik som krävs för att hantera nyckelöverrullning automatiskt.

Du kan bekräfta att ditt program använder något av detta genom att leta efter något av följande utdrag i programmets Startup.cs eller Startup.Auth.cs

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

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>Webbprogram / API:er som skyddar resurser med node.js passport-azure-ad-modul
Om ditt program använder nod.js pass-ad-modulen har den redan den logik som krävs för att hantera nyckelöverrullning automatiskt.

Du kan bekräfta att din ansökan passport-ad genom att söka efter följande utdrag i programmets app.js

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>Webbprogram/API:er som skyddar resurser och skapas med Visual Studio 2015 eller senare
Om ditt program har skapats med hjälp av en webbprogrammall i Visual Studio 2015 eller senare och du valde **Arbets- eller skolkonton** på menyn **Ändra autentisering** har det redan den logik som krävs för att hantera nyckelöverrullning automatiskt. Den här logiken, inbäddad i OWIN OpenID Connect mellanprogram, hämtar och cachelagrar nycklarna från OpenID Connect-identifieringsdokumentet och uppdaterar dem regelbundet.

Om du har lagt till autentisering i lösningen manuellt kanske programmet inte har den nödvändiga logiken för nyckelfördämning. Du måste skriva det själv, eller följa stegen i [webbprogram / API: er med hjälp av andra bibliotek eller manuellt genomföra någon av de protokoll som stöds](#other).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Webbprogram som skyddar resurser och skapas med Visual Studio 2013
Om ditt program skapades med hjälp av en webbprogrammall i Visual Studio 2013 och du valde **organisationskonton** på menyn **Ändra autentisering** har det redan den logik som krävs för att hantera nyckelöverrullning automatiskt. Den här logiken lagrar organisationens unika identifierare och signeringsnyckelinformationen i två databastabeller som är associerade med projektet. Du hittar anslutningssträngen för databasen i projektets web.config-fil.

Om du har lagt till autentisering i lösningen manuellt kanske programmet inte har den nödvändiga logiken för nyckelfördämning. Du måste skriva det själv, eller följa stegen i [webbprogram / API: er med hjälp av andra bibliotek eller manuellt genomföra någon av de protokoll som stöds.](#other).

Följande steg hjälper dig att kontrollera att logiken fungerar som den ska i programmet.

1. Öppna lösningen i Visual Studio 2013 och klicka sedan på fliken **Serverutforskaren** i det högra fönstret.
2. Expandera **dataanslutningar,** **DefaultConnection**och sedan **tabeller**. Leta upp tabellen **IssuAuthorityKeys,** högerklicka på den och klicka sedan på **Visa tabelldata**.
3. I tabellen **IssuauthorityKeys** finns det minst en rad, vilket motsvarar tumavtrycket för nyckeln. Ta bort alla rader i tabellen.
4. Högerklicka på tabellen **Klienter** och klicka sedan på **Visa tabelldata**.
5. I tabellen **Klienter** finns det minst en rad, vilket motsvarar en unik katalogklientidentifierare. Ta bort alla rader i tabellen. Om du inte tar bort raderna i tabellen **Klienter** och tabellen **IssuauthorityKeys** får du ett felmeddelande vid körning.
6. Skapa och kör programmet. När du har loggat in på ditt konto kan du stoppa programmet.
7. Gå tillbaka till **Server Explorer** och titta på värdena i tabellen **IssuingAuthorityKeys** och **Tenants.** Du kommer att märka att de automatiskt har återbefolkats med lämplig information från federationens metadatadokument.

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Webb-API:er som skyddar resurser och skapas med Visual Studio 2013
Om du har skapat ett webb-API-program i Visual Studio 2013 med webb-API-mallen och sedan valde **organisationskonton** på menyn **Ändra autentisering** har du redan den nödvändiga logiken i programmet.

Om du konfigurerat autentisering manuellt följer du instruktionerna nedan för att lära dig hur du konfigurerar webb-API:et för att automatiskt uppdatera dess nyckelinformation.

Följande kodavsnitt visar hur du hämtar de senaste nycklarna från federationsmetadatadokumentet och använder sedan [JWT Token Handler](https://msdn.microsoft.com/library/dn205065.aspx) för att validera token. Kodavsnittet förutsätter att du använder din egen cachelagringsmekanism för att spara nyckeln för att validera framtida token från Azure AD, oavsett om det är i en databas, konfigurationsfil eller någon annanstans.

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

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2012"></a><a name="vs2012"></a>Webbprogram som skyddar resurser och skapas med Visual Studio 2012
Om ditt program byggdes i Visual Studio 2012 använde du förmodligen identity and access-verktyget för att konfigurera ditt program. Det är också troligt att du använder [det validerande utfärdarens namnregister (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). VINR ansvarar för att underhålla information om betrodda identitetsleverantörer (Azure AD) och de nycklar som används för att validera token som utfärdats av dem. VINR gör det också enkelt att automatiskt uppdatera den nyckelinformation som lagras i en Web.config-fil genom att hämta det senaste federationsmetadatadokumentet som är associerat med katalogen, kontrollera om konfigurationen är inaktuell med det senaste dokumentet och uppdatera programmet för att använda den nya nyckeln om det behövs.

Om du har skapat programmet med något av kodexemplen eller genomgången dokumentation som tillhandahålls av Microsoft, finns redan nyckelöverrullningslogiken i projektet. Du kommer att märka att koden nedan redan finns i ditt projekt. Om ditt program inte redan har den här logiken följer du stegen nedan för att lägga till det och för att kontrollera att det fungerar korrekt.

1. Lägg till en referens till **System.IdentityModel-sammansättningen** för rätt projekt i **Solution Explorer.**
2. Öppna **Global.asax.cs** filen och lägg till följande med hjälp av direktiv:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Lägg till följande **Global.asax.cs** metod i Global.asax.cs-filen:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Anropa metoden **RefreshValidationSettings()** i metoden **Application_Start()** i **Global.asax.cs** som visas:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

När du har följt dessa steg uppdateras programmets Web.config med den senaste informationen från federationsmetadatadokumentet, inklusive de senaste nycklarna. Den här uppdateringen sker varje gång programpoolen återvinns i IIS. som standard är IIS inställt på att återvinna program var 29:e timme.

Följ stegen nedan för att kontrollera att logiken för nyckelfördrollning fungerar.

1. När du har kontrollerat att programmet använder koden ovan öppnar du filen **Web.config** och navigerar till ** \<numretrNameRegistry>** block, särskilt efter följande rader:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. Ändra tumavtrycksvärdet ** \<i inställningen Lägg till tumavtryck="">** genom att ersätta ett tecken med ett annat tecken. Spara filen **Web.config**.
3. Skapa programmet och kör det sedan. Om du kan slutföra inloggningsprocessen uppdaterar programmet nyckeln genom att hämta den information som krävs från katalogens federationsmetadatadokument. Om du har problem med att logga in, se till att ändringarna i ditt program är korrekta genom att läsa [Lägga till inloggning i webbprogrammet Med hjälp av Azure AD-artikel,](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) eller hämta och inspektera följande kodexempel: Cloud Application för flera [innehavare för Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2008-or-2010-and-windows-identity-foundation-wif-v10-for-net-35"></a><a name="vs2010"></a>Webbprogram som skyddar resurser och skapas med Visual Studio 2008 eller 2010 och WiF (Windows Identity Foundation) v1.0 för .NET 3.5
Om du har skapat ett program på WIF v1.0 finns det ingen mekanism för att automatiskt uppdatera programmets konfiguration för att använda en ny nyckel.

* *Enklaste sättet* Använd FedUtil-verktygen som ingår i WIF SDK, som kan hämta det senaste metadatadokumentet och uppdatera konfigurationen.
* Uppdatera programmet till .NET 4.5, som innehåller den senaste versionen av WIF som finns i systemnamnområdet. Du kan sedan använda [VINR (Validating Issuer Name Registry)](https://msdn.microsoft.com/library/dn205067.aspx) för att utföra automatiska uppdateringar av programmets konfiguration.
* Utför en manuell överrullning enligt instruktionerna i slutet av det här vägledningsdokumentet.

Instruktioner för att använda FedUtil för att uppdatera din konfiguration:

1. Kontrollera att WIF v1.0 SDK är installerat på din utvecklingsmaskin för Visual Studio 2008 eller 2010. Du kan [ladda ner den härifrån](https://www.microsoft.com/en-us/download/details.aspx?id=4451) om du ännu inte har installerat den.
2. Öppna lösningen i Visual Studio och högerklicka sedan på gällande projekt och välj **Uppdatera federationsmetadata**. Om det här alternativet inte är tillgängligt har FedUtil och/eller WIF v1.0 SDK inte installerats.
3. Välj **Uppdatera** för att börja uppdatera federationsmetadata i prompten. Om du har tillgång till servermiljön där programmet finns kan du eventuellt använda FedUtils [automatiska schema för metadatauppdatering](https://msdn.microsoft.com/library/ee517272.aspx).
4. Klicka på **Slutför** om du vill slutföra uppdateringsprocessen.

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>Webbprogram/API:er som skyddar resurser med andra bibliotek eller implementerar något av de protokoll som stöds
Om du använder något annat bibliotek eller har implementerat något av de protokoll som stöds manuellt måste du granska biblioteket eller implementeringen för att säkerställa att nyckeln hämtas från antingen OpenID Connect-identifieringsdokumentet eller federationsmetadata Dokument. Ett sätt att söka efter detta är att göra en sökning i koden eller bibliotekets kod för alla anrop till antingen OpenID-identifieringsdokumentet eller federationsmetadatadokumentet.

Om nyckeln lagras någonstans eller hårdkodas i ditt program kan du hämta nyckeln manuellt och uppdatera den därefter genom att utföra en manuell överrullning enligt instruktionerna i slutet av det här vägledningsdokumentet. **Det rekommenderas starkt att du förbättrar ditt program för att stödja automatisk överrullning** med någon av de metoder som beskrivs i den här artikeln för att undvika framtida störningar och omkostnader om Azure AD ökar dess överrullningskadens eller har en nödutrullning utanför bandet.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Så här testar du programmet för att avgöra om det kommer att påverkas
Du kan verifiera om programmet stöder automatisk nyckelöverrullning genom att hämta skripten och följa instruktionerna i [den här GitHub-databasen.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Så här utför du en manuell överrullning om programmet inte stöder automatisk överrullning
Om ditt program **inte** stöder automatisk överrullning måste du upprätta en process som regelbundet övervakar Azure AD:s signeringsnycklar och utför en manuell överrullning i enlighet med detta. [Den här GitHub-databasen](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) innehåller skript och instruktioner om hur du gör detta.

