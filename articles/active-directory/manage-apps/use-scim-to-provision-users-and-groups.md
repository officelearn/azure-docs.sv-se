---
title: Automatisera etablering av appar med SCIM i Azure Active Directory | Microsoft Docs
description: Azure Active Directory kan automatiskt etablera användare och grupper till valfritt program eller en identitet Arkiv som är fronted av en webbtjänst med gränssnittet som definierats i protokollspecifikation SCIM
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: celested
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 946a70a1b3fe2ddcaf8ec58b9ebc297f1d8894fd
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178863"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Med hjälp av System för domänerna Identity Management (SCIM) att automatiskt etablera användare och grupper från Azure Active Directory till program

## <a name="overview"></a>Översikt
Azure Active Directory (Azure AD) automatiskt kan etablera användare och grupper till alla program eller en identitet store som fronted av en webbtjänst med gränssnittet som definieras i den [System för domänerna Identity Management (SCIM) 2.0-protokollet specifikationen](https://tools.ietf.org/html/draft-ietf-scim-api-19). Azure Active Directory kan skicka begäranden om att skapa, ändra eller ta bort tilldelade användare och grupper till webbtjänsten. Webbtjänsten kan sedan omvandla dessa begäranden till åtgärder på mål-identitetsarkiv. 

>[!IMPORTANT]
>Beteendet för Azure AD SCIM-implementering uppdaterades senast 18 December 2018. Information om vad som ändrats finns [SCIM 2.0-protokollet kompatibiliteten för Azure AD-användare etableringstjänsten](application-provisioning-config-problem-scim-compatibility.md).

![][0]
*Bild 1: Från Azure Active Directory-etablering till en identitetsarkiv via en webbtjänst*

Den här funktionen kan användas tillsammans med funktionen ”Ta med din egen app” i Azure AD. Den här funktionen möjliggör enkel inloggning och automatisk användaretablering för program som fronted av en SCIM-webbtjänst.

Det finns två användningsfall för att använda SCIM i Azure Active Directory:

* **Etablering av användare och grupper till program som stöder SCIM** -program som stöder SCIM 2.0 och använder OAuth ägar-token för autentisering fungerar med Azure AD utan konfiguration.
  
* **Skapa din egen etablering lösning för program som stöder andra API-baserad tilldelning** -för icke-SCIM-program, kan du skapa en SCIM-slutpunkt för att översätta mellan Azure AD SCIM-slutpunkten och alla API: er som stöds av programmet för etableringen av användare. Som hjälper dig att utveckla en SCIM-slutpunkt, finns det vanliga språk infrastruktur (CLI) bibliotek tillsammans med kodexempel som visar hur du anger en SCIM-slutpunkt och översätta SCIM meddelanden.  

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Etablering av användare och grupper till program som stöder SCIM
Azure AD kan konfigureras att automatiskt etablera tilldelade användare och grupper till program som implementerar en [System för Identitetshantering för domänerna 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) webbtjänsten och godkänna OAuth-ägar-token för autentisering. Inom SCIM 2.0-specifikationen måste program uppfylla följande krav:

* Stöder skapandet av användare och/eller grupper, enligt avsnittet 3.3 av SCIM-protokollet.  
* Har stöd för ändra användare och/eller grupper med patch-förfrågningar enligt avsnittet 3.5.2 av SCIM-protokollet.  
* Har stöd för hämtning av en känd resurs enligt avsnittet 3.4.1 av SCIM-protokollet.  
* Har stöd för frågor till användare och/eller grupper, enligt avsnittet 3.4.2 av SCIM-protokollet.  Som standard av externalId tillfrågas användare och grupper tillfrågas av displayName.  
* Har stöd för fråga användaren efter ID och efter manager enligt avsnittet 3.4.2 av SCIM-protokollet.  
* Har stöd för frågor till grupper efter ID och av medlem enligt avsnittet 3.4.2 av SCIM-protokollet.  
* Accepterar OAuth-ägartoken för auktorisering enligt avsnittet 2.1 av SCIM-protokollet.

Kontrollera med din leverantör av program eller ditt program leverantörens dokumentation för instruktioner för kompatibilitet med dessa krav.

### <a name="getting-started"></a>Komma igång
Program som stöder SCIM-profilen som beskrivs i den här artikeln kan anslutas till Azure Active Directory med hjälp av funktionen ”icke-galleriprogram” i Azure AD-programgalleriet. När du är ansluten, kör Azure AD en synkroniseringsprocess varje 40 minuter där den frågar programmets SCIM-slutpunkten för tilldelade användare och grupper, och skapar eller ändrar dem enligt tilldelningsinformation.

**Ansluta ett program som stöder SCIM:**

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Bläddra till **Azure Active Directory > företagsprogram**, och välj **nytt program > alla > icke-galleriprogram**.
3. Ange ett namn för ditt program och klicka på **Lägg till** ikon för att skapa ett app-objekt.
    
   ![][1]
   *Bild 2: Azure AD-programgalleriet*
    
4. I skärmbilden som visas väljer du den **etablering** fliken i den vänstra kolumnen.
5. I den **etablering läge** menyn och välj **automatisk**.
    
   ![][2]
   *Bild 3: Konfigurera etablering i Azure portal*
    
6. I den **klient-URL** fältet, anger du URL till slutpunkten för programmets SCIM. Exempel: https://api.contoso.com/scim/v2/
7. Om SCIM-slutpunkten kräver en OAuth-ägartoken från en utfärdare än Azure AD kan sedan kopiera den obligatoriska OAuth-ägartoken till det valfria **hemlighet Token** fält. Om fältet lämnas tomt, innehåller en OAuth-ägartoken som har utfärdats från Azure AD med varje begäran med Azure AD. Appar som använder Azure AD som identitetsprovider kan verifiera den här Azure AD-utfärdade token.
8. Klicka på den **Testanslutningen** så försöker ansluta till SCIM-slutpunkten för Azure Active Directory. Om försöker misslyckas, visas information om felet.  

    >[!NOTE]
    >**Testa anslutning** frågar SCIM-slutpunkten för en användare som inte finns med en slumpmässig GUID som egenskapen matchande som valts i Azure AD-konfigurationen. Förväntade rätt svar är HTTP 200 OK med ett tomt SCIM ListResponse-meddelande. 

9. Om försöker att ansluta till program-lyckas klickar **spara** att spara autentiseringsuppgifter som administratör.
10. I den **mappningar** avsnittet finns det två valbar uppsättningar attributmappningar: en för användarobjekt och en för gruppobjekt. Välj var och en att granska de attribut som synkroniseras från Azure Active Directory till din app. Attribut som har markerats som **matchande** egenskaper som används för att matcha de användare och grupper i din app för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

    >[!NOTE]
    >Du kan också inaktivera synkronisering av gruppobjekt genom att inaktivera ”grupper”-mappning. 

11. Under **inställningar**, **omfång** fältet definierar vilka användare och grupper synkroniseras. Att välja ”synkronisera enbart tilldelade användare och grupper” (rekommenderas) det kommer endast att synkronisera användare och grupper som tilldelats i den **användare och grupper** fliken.
12. När konfigurationen är klar kan du ändra den **Etableringsstatus** till **på**.
13. Klicka på **spara** att starta den Azure AD-etableringstjänsten. 
14. Om synkronisera enbart tilldelade användare och grupper (rekommenderas), måste du markera den **användare och grupper** fliken och tilldela användare och/eller grupper som du vill synkronisera.

När den inledande synkroniseringen har startats kan du använda den **granskningsloggar** flik för att övervaka förloppet, som visar alla åtgärder som utförs av etableringstjänsten i din app. Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](check-status-user-account-provisioning.md).

> [!NOTE]
> Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. 


## <a name="building-your-own-provisioning-solution-for-any-application"></a>Skapa din egen etablering lösning för alla program
Genom att skapa en SCIM-webbtjänst som har kontakt med Azure Active Directory kan aktivera du enkel inloggning och automatisk användaretablering för nästan alla program som innehåller en REST- eller SOAP användaretablering API.

Så fungerar här det:

1. Azure AD tillhandahåller ett gemensamt språk infrastrukturbibliotek med namnet [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Systemintegrerare och utvecklare kan använda det här biblioteket för att skapa och distribuera en SCIM-baserade slutpunkt för webbtjänsten kan ansluta Azure AD till identitetsarkiv för alla program.
2. Mappningar implementeras i webbtjänsten för att mappa det standardiserade användarschemat till användarschemat och protokoll som krävs av programmet.
3. Slutpunkts-URL är registrerad i Azure AD som en del av ett anpassat program i programgalleriet.
4. Användare och grupper har tilldelats det här programmet i Azure AD. Vid tilldelning placeras de i en kö som ska synkroniseras till målprogrammet. Hantering av kön synkroniseringsprocessen körs varje 40 minuter.

### <a name="code-samples"></a>Kodexempel
Att göra den här processen enklare, [kodexempel](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) tillhandahålls som skapar en slutpunkt för webbtjänsten SCIM och visa Automatisk etablering. Ett exempel är av en provider som underhåller en fil med rader med kommaavgränsade värden som representerar användare och grupper.  Den andra är av en provider som körs på Amazon Web Services Identity and Access Management-tjänsten.  

**Förutsättningar**

* Visual Studio 2013 eller senare
* [Azure SDK för .NET](https://azure.microsoft.com/downloads/)
* Windows dator som har stöd för ASP.NET framework 4.5 som ska användas som SCIM-slutpunkt. Den här datorn måste kunna nås från molnet
* [En Azure-prenumeration med en utvärderingsversion eller licensierad version av Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Komma igång
Det enklaste sättet att implementera en SCIM-slutpunkt som kan ta emot etableringsbegäranden från Azure AD är att skapa och distribuera kodexemplet som visar de etablerade användarna till en fil med kommaavgränsade värden (CSV).

#### <a name="to-create-a-sample-scim-endpoint"></a>Skapa en exempel SCIM-slutpunkt

1. Ladda ned koden exempelpaketet på [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2. Packa upp paketet och placera den på din Windows-dator på en plats, till exempel C:\AzureAD-BYOA-Provisioning-Samples\.
3. Starta FileProvisioning\Host\FileProvisioningService.csproj-projekt i Visual Studio i den här mappen.
4. Välj **Verktyg > NuGet Package Manager > Package Manager Console**, och kör följande kommandon för att FileProvisioningService projektet ska matcha referenserna lösningen:

   ```
    Update-Package -Reinstall
   ```

5. Skapa FileProvisioningService-projektet.
6. Starta Kommandotolken programmet i Windows (som administratör) och använda den **cd** kommando för att ändra katalogen till din **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**mapp.
7. Kör du följande kommando och ersätter < ip-adress > med IP-adress eller domännamn namnet på den Windows-datorn:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

8. I Windows under **Windows-inställningar > nätverk och Internet-inställningar**väljer den **Windows-brandväggen > Avancerade inställningar**, och skapa en **inkommande regel** som tillåter ingående åtkomst till port 9000.
9. Om Windows-dator bakom en router kan måste routern konfigureras för att utföra Network Access Translation mellan dess port 9000 som exponeras för internet och port 9000 på Windows-datorn. Den här konfigurationen krävs för Azure AD för att kunna komma åt den här slutpunkten i molnet.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Registrerar exempel SCIM slutpunkten i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Bläddra till **Azure Active Directory > företagsprogram**, och välj **nytt program > alla > icke-galleriprogram**.
3. Ange ett namn för ditt program och klicka på **Lägg till** ikon för att skapa ett app-objekt. Det programobjekt som skapas är avsedd att representera målappen du skulle etablering för och implementera enkel inloggning för och inte bara SCIM-slutpunkten.
4. I skärmbilden som visas väljer du den **etablering** fliken i den vänstra kolumnen.
5. I den **etablering läge** menyn och välj **automatisk**.
    
   ![][2]
   *Bild 4: Konfigurera etablering i Azure portal*
    
6. I den **klient-URL** fältet, anger du URL: en och en port för din SCIM-slutpunkt som visas på internet. Transaktionen är något som liknar http://testmachine.contoso.com:9000 eller http://\<ip-adress >: 9000 / där \<ip-adress > är internet exponerade IP adress.  
7. Om SCIM-slutpunkten kräver en OAuth-ägartoken från en utfärdare än Azure AD kan sedan kopiera den obligatoriska OAuth-ägartoken till det valfria **hemlighet Token** fält. Om fältet lämnas tomt används att Azure AD inkludera en OAuth-ägartoken som har utfärdats från Azure AD med varje begäran. Appar som använder Azure AD som identitetsprovider kan verifiera den här Azure AD-utfärdade token.
8. Klicka på den **Testanslutningen** så försöker ansluta till SCIM-slutpunkten för Azure Active Directory. Om försöker misslyckas, visas information om felet.  

    >[!NOTE]
    >**Testa anslutning** frågar SCIM-slutpunkten för en användare som inte finns med en slumpmässig GUID som egenskapen matchande som valts i Azure AD-konfigurationen. Förväntade rätt svar är HTTP 200 OK med ett tomt SCIM ListResponse-meddelande

9. Om försöker att ansluta till program-lyckas klickar **spara** att spara autentiseringsuppgifter som administratör.
10. I den **mappningar** avsnittet finns det två valbar uppsättningar attributmappningar: en för användarobjekt och en för gruppobjekt. Välj var och en att granska de attribut som synkroniseras från Azure Active Directory till din app. Attribut som har markerats som **matchande** egenskaper som används för att matcha de användare och grupper i din app för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.
11. Under **inställningar**, **omfång** fältet definierar vilka användare som är och eller grupper är synkroniserade. Att välja ”synkronisera enbart tilldelade användare och grupper” (rekommenderas) det kommer endast att synkronisera användare och grupper som tilldelats i den **användare och grupper** fliken.
12. När konfigurationen är klar kan du ändra den **Etableringsstatus** till **på**.
13. Klicka på **spara** att starta den Azure AD-etableringstjänsten. 
14. Om synkronisera enbart tilldelade användare och grupper (rekommenderas), måste du markera den **användare och grupper** fliken och tilldela användare och/eller grupper som du vill synkronisera.

När den inledande synkroniseringen har startats kan du använda den **granskningsloggar** flik för att övervaka förloppet, som visar alla åtgärder som utförs av etableringstjänsten i din app. Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](check-status-user-account-provisioning.md).

Det sista steget i att verifiera exemplet är att öppna filen TargetFile.csv i mappen \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug på din Windows-dator. När etableringen har körts, innehåller den här filen information om alla tilldelade och etablerat användare och grupper.

### <a name="development-libraries"></a>Utvecklingsbibliotek
För att utveckla dina egna webbtjänst som överensstämmer med SCIM-specifikationen först du med att bekanta dig med följande bibliotek som tillhandahålls av Microsoft för att hjälpa dig att påskynda utvecklingen: 

1. Vanliga språk infrastruktur (CLI) bibliotek erbjuds för användning med språk baserat på den infrastrukturen, till exempel C#. En av dessa bibliotek [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), deklarerar ett gränssnitt, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, som visas i följande bild:  En utvecklare som använder biblioteken skulle implementera gränssnittet med en klass som kan hänvisas till, med det allmänna skyddet, som en provider. Bibliotek som gör att utvecklare kan distribuera en webbtjänst som överensstämmer med SCIM-specifikationen. Webbtjänsten kan antingen finnas i Internet Information Services eller valfri körbara vanliga språk infrastruktur-sammansättningen. Begäran översätts till anrop till leverantörens metoder som skulle programmeras av utvecklaren att fungera på vissa identitetsarkiv.
  
   ![][3]
  
2. [Express route-hanterare](https://expressjs.com/guide/routing.html) tillgängliga för parsning av node.js begäran objekt som representerar anrop (enligt specifikationen SCIM), görs till en node.js-webbtjänst.   

### <a name="building-a-custom-scim-endpoint"></a>Att skapa en anpassad SCIM-slutpunkt
Med hjälp av CLI-bibliotek, kan utvecklare som använder dessa bibliotek ha sina tjänster i alla körbara vanliga språk infrastruktur-sammansättningen eller i Internet Information Services. Här är exempelkod som värd för en tjänst i en körbar sammansättning på adressen http://localhost:9000: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Den här tjänsten måste ha en HTTP-adress och server certifikat för serverautentisering som rotcertifikatutfärdaren är en av följande namn: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Ett certifikat för serverautentisering kan bindas till en port på en Windows-värd med hjälp av verktyget network shell: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Här är värdet som angetts för argumentet certhash tumavtrycket för certifikatet, medan värdet som angetts för argumentet appid är ett valfritt globalt unikt ID.  

Ska vara värd för tjänsten i Internet Information Services, skulle en utvecklare skapa en CLA kod bibliotekssammansättning med en klass med namnet Start i standardnamnområdet av sammansättningen.  Här är ett exempel på sådana en klass: 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Hantering av slutpunktsautentisering
Begäranden från Azure Active Directory omfattar en OAuth 2.0-ägartoken.   Alla tjänster som tar emot begäran ska autentisera utfärdaren som Azure Active Directory för den förväntade Azure Active Directory-klienten för åtkomst till Azure Active Directory Graph-webbtjänsten.  I token utfärdaren identifieras med ett iss-anspråk som ”iss” ”:https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”.  I det här exemplet basadressen för anspråksvärdet, https://sts.windows.net, identifierar Azure Active Directory som utfärdaren, medan relativt adressen segment, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, är en unik identifierare för Azure Active Directory-klient på räkning av vilka token utfärdats.  Om token har utfärdats för att komma åt Azure Active Directory Graph-webbtjänsten ska identifierare för tjänsten, 00000002-0000-0000-c000-000000000000, i värdet för den token aud anspråk.  

Utvecklare som använder CLA-bibliotek som tillhandahålls av Microsoft för att skapa en SCIM-tjänst kan autentisera begäranden från Azure Active Directory med hjälp av Microsoft.Owin.Security.ActiveDirectory-paketet genom att följa dessa steg: 

1. I en provider, implementerar du egenskapen Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior genom att låta den returnera en metod som anropas när tjänsten startas: 

   ```
     public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
     {
       get
       {
         return this.OnServiceStartup;
       }
     }

     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
       System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
     {
     }
   ```

2. Lägg till följande kod i metoden har varje begäran till någon av tjänstslutpunkter autentiserad som försetts med en token utfärdad av Azure Active Directory för en angiven klient för åtkomst till Azure AD Graph-webbtjänsten: 

   ```
     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
       System.Web.Http.HttpConfiguration HttpConfiguration configuration)
     {
       // IFilter is defined in System.Web.Http.dll.  
       System.Web.Http.Filters.IFilter authorizationFilter = 
         new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

       // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
       // System.IdentityModel.Token.Jwt.dll.
       SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
         new TokenValidationParameters()
         {
           ValidAudience = "00000002-0000-0000-c000-000000000000"
         };

       // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
       // Microsoft.Owin.Security.ActiveDirectory.dll
       Microsoft.Owin.Security.ActiveDirectory.
       WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
         new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
         TokenValidationParameters = tokenValidationParameters,
         Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                       // identifier for this one.  
       };

       applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
     }
   ```


## <a name="user-and-group-schema"></a>Schema för användare och grupper
Azure Active Directory kan etablera två typer av resurser till SCIM-webbtjänster.  Dessa typer av resurser är användare och grupper.  

Användarresurser identifieras av schemaidentifierare ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”, som ingår i den här protokollspecifikation: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  Standardmappningen attribut för användare i Azure Active Directory attributen för ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User” resurser finns i tabell 1, nedan.  

Gruppera resurser identifieras av schema-ID http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Tabell 2 nedan visar standardmappningen attribut för grupper i Azure Active Directory attributen för http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group resurser.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabell 1: Attributmappning för standard-användare

| Azure Active Directory-användare | ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User” |
| --- | --- |
| IsSoftDeleted |aktiv |
| displayName |displayName |
| Fax TelephoneNumber |phoneNumbers [typ eq ”fax”] .value |
| givenName |name.givenName |
| jobTitle |rubrik |
| e-post |e-postmeddelanden [typ eq ”arbete pågår”] .value |
| mailNickname |externalId |
| ansvarig |ansvarig |
| mobila |phoneNumbers [typ eq ”mobil”] .value |
| objekt-ID |ID |
| Postnummer |adresser typ eq ”arbete pågår” .postalCode |
| Proxy-adresser |e-postmeddelande [Ange eq ”annan”]. Värde |
| fysiska-leverans – OfficeName |adresser [Ange eq ”annan”]. Formaterad |
| streetAddress |adresser typ eq ”arbete pågår” .streetAddress |
| Efternamn |name.familyName |
| Telefonnummer |phoneNumbers [typ eq ”arbete pågår”] .value |
| användaren huvudkontot |Användarnamn |

### <a name="table-2-default-group-attribute-mapping"></a>Tabell 2: Attributmappning för standard-grupp

| Azure Active Directory-grupp | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| --- | --- |
| displayName |externalId |
| e-post |e-postmeddelanden [typ eq ”arbete pågår”] .value |
| mailNickname |displayName |
| medlemmar |medlemmar |
| objekt-ID |ID |
| proxyAddresses |e-postmeddelande [Ange eq ”annan”]. Värde |

## <a name="user-provisioning-and-de-provisioning"></a>Användaretablering och avetablering
Följande bild visar meddelandena som Azure Active Directory skickar till SCIM-tjänsten att hantera livscykeln för en användare i en annan identitet store. Diagrammet visar också hur en SCIM-tjänst som implementeras med hjälp av CLI-bibliotek som tillhandahålls av Microsoft för att skapa sådana tjänster översätta dessa begäranden till anrop av metoderna av en provider.  

![][4]
*Bild 5: Användaretablering och avetablering sekvens*

1. Azure Active Directory frågar tjänsten för en användare med ett externalId-attributvärde som matchar mailNickname-attributvärdet för en användare i Azure AD. Frågan uttrycks som en Hypertext Transfer Protocol (HTTP)-begäran, till exempel det här exemplet där jyoung är ett exempel på en mailNickname för en användare i Azure Active Directory: 

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   Om tjänsten har skapats med Common Language infrastruktur-bibliotek som tillhandahålls av Microsoft för att implementera SCIM-tjänster, översätts förfrågan till ett anrop till metoden fråga i den tjänstleverantör.  Här är signaturen för metoden: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   Här är definitionen av gränssnittet Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 

   ```
     public interface IQueryParameters: 
       Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
         System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
         { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
       system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
       { get; }
       System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
       { get; }
       string SchemaIdentifier 
       { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
     {
         Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
           { get; set; }
         string AttributePath 
           { get; } 
         Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
           { get; }
         string ComparisonValue 
           { get; }
     }

     public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
     {
         Equals
     }
   ```

   I följande exempel av en fråga för en användare med ett angivet värde för attributet externalId och är värden för argumenten som skickas till Query-metoden: 
   * parameters.AlternateFilters.Count: 1
   * parametrar. AlternateFilters.ElementAt(0). AttributePath: ”externalId”
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
   * parametrar. AlternateFilter.ElementAt(0). ComparisonValue: ”jyoung”
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. Om svaret på en fråga till webbtjänsten för en användare med ett externalId-attributvärde som överensstämmer med mailNickname-attributvärdet för en användare inte returnerar några användare sedan Azure Active Directory-begäranden som tjänsten etablerar en användare som motsvarar det i Azure Active Directory.  Här är ett exempel på en sådan begäran: 

   ```
     POST https://.../scim/Users HTTP/1.1
     Authorization: Bearer ...
     Content-type: application/scim+json
     {
       "schemas":
       [
         "urn:ietf:params:scim:schemas:core:2.0:User",
         "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
       "externalId":"jyoung",
       "userName":"jyoung",
       "active":true,
       "addresses":null,
       "displayName":"Joy Young",
       "emails": [
         {
           "type":"work",
           "value":"jyoung@Contoso.com",
           "primary":true}],
       "meta": {
         "resourceType":"User"},
        "name":{
         "familyName":"Young",
         "givenName":"Joy"},
       "phoneNumbers":null,
       "preferredLanguage":null,
       "title":null,
       "department":null,
       "manager":null}
   ```

   Vanliga språk infrastruktur-bibliotek som tillhandahålls av Microsoft för att implementera SCIM tjänster skulle omvandla begäran till ett anrop till metoden skapa av tjänstens-providern.  Skapa-metoden har den här signaturen: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create (
       Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
       string correlationIdentifier);
   ```

   Värdet för argumentet resurs är en instans av Microsoft.SystemForCrossDomainIdentityManagement i en begäran att etablera en användare. Core2EnterpriseUser klassen, som definieras i Microsoft.SystemForCrossDomainIdentityManagement.Schemas-biblioteket.  Om begäran att etablera användaren lyckas, förväntas implementeringen av metoden för att returnera en instans av Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser klass med värdet för egenskapen ID angetts till den unika identifieraren för den nyligen etablerade användaren.  

3. Om du vill uppdatera en användare som är kända i en identitetsarkiv fronted genom en SCIM fortsätter Azure Active Directory genom att begära det aktuella tillståndet för den användaren från tjänsten med en begäran som: 

   ```
     GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   I en tjänst som skapats med Common Language infrastruktur-bibliotek som tillhandahålls av Microsoft för att implementera SCIM-tjänster, översätts förfrågan till ett anrop till metoden hämta av tjänstens-providern.  Här är signaturen för hämta-metoden: 

    ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
     // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
        Retrieve(
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
            parameters, 
            string correlationIdentifier);
 
     public interface 
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
         IRetrievalParameters
         {
           Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
             ResourceIdentifier 
               { get; }
     }
     public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
     {
         string Identifier 
           { get; set; }
         string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
           { get; set; }
     }
   ```

   I exemplet med en begäran att hämta det aktuella tillståndet för en användare, är värdena för egenskaperna för objektet som tillhandahålls som värde för argumentet parametrar följande: 
  
   * Identifierare: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”

4. Om ett referensattribut är uppdateras Azure Active Directory frågar tjänsten för att avgöra huruvida det aktuella värdet för referensattributet i Identitetslagret fronted av tjänsten redan matchar värdet för attributet i Azure Active Katalogen. För användare är det enda attributet som efterfrågas det aktuella värdet på det här sättet manager-attribut. Här är ett exempel på en begäran för att avgöra om attributet manager för en viss användare-objektet har ett visst värde: 

   ```
     GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq  2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
     Authorization: Bearer ...
   ```

   Anger värdet för Frågeparametern attribut, ”ID”, som om ett användarobjekt finns som uppfyller de uttryck som värde för Frågeparametern filtret och tjänsten förväntas svara med en ”urn: ietf:params:scim:schemas:core:2.0: Användare ”eller” urn: ietf:params:scim:schemas:extension:enterprise:2.0:User ”resursen, inklusive endast värdet för den resursen” ID ”-attributet.  Värdet för den **ID** attributet är känd för begäranden. Den ingår i värdet för Frågeparametern filter; Syftet med ber om det är faktiskt att begära en minimal representation av en resurs som uppfyller filteruttryck som en indikation på om huruvida det finns ett sådant objekt.   

   Om tjänsten har skapats med Common Language infrastruktur-bibliotek som tillhandahålls av Microsoft för att implementera SCIM-tjänster, översätts förfrågan till ett anrop till metoden fråga i den tjänstleverantör. Värdet för egenskaperna för objektet som tillhandahålls som värde för argumentet parametrar är följande: 
  
   * parameters.AlternateFilters.Count: 2
   * parameters.AlternateFilters.ElementAt(x).AttributePath: ”ID”
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue:  "54D382A4-2050-4C03-94D1-E769F1D15682"
   * parametrar. AlternateFilters.ElementAt(y). AttributePath: ”manager”
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(y).ComparisonValue:  "2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0): ”ID”
   * parametrar. SchemaIdentifier: ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”

   Här är värdet för indexet x kan vara 0 och värdet för y index kan vara 1, eller värdet för x kan vara 1 och värdet för y kan vara 0, beroende på ordningen på uttryck av filter-frågeparameter.   

5. Här är ett exempel på en begäran från Azure Active Directory till en SCIM-tjänsten för att uppdatera en användare: 

   ```
     PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
     Content-type: application/scim+json
     {
       "schemas": 
       [
         "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
       "Operations":
       [
         {
           "op":"Add",
           "path":"manager",
           "value":
             [
               {
                 "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                 "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
   ```

   Microsoft Common Language infrastruktur-bibliotek för att implementera SCIM tjänster skulle omvandla begäran till ett anrop till metoden Update av tjänstens-providern. Här är signaturen för metoden Update: 

   ```
     // System.Threading.Tasks.Tasks and 
     // System.Collections.Generic.IReadOnlyCollection<T>
     // are defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
     // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

     System.Threading.Tasks.Task Update(
       Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
       string correlationIdentifier);

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
     {
     Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
       PatchRequest 
         { get; set; }
     Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
       ResourceIdentifier 
         { get; set; }        
     }

     public class PatchRequest2: 
       Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
     {
     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
         Operations
         { get;}

     public void AddOperation(
       Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
     }

     public class PatchOperation
     {
     public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
       Name
       { get; set; }

     public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
       Path
       { get; set; }

     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
       { get; }

     public void AddValue(
       Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
     }

     public enum OperationName
     {
       Add,
       Remove,
       Replace
     }

     public interface IPath
     {
       string AttributePath { get; }
       System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
       Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
     }

     public class OperationValue
     {
       public string Reference
       { get; set; }

       public string Value
       { get; set; }
     }
   ```

    I exemplet med en begäran om att uppdatera en användare, har det angivna objektet som värde för argumentet patch egenskapsvärdena: 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”
   * (PatchRequest som PatchRequest2). Operations.Count: 1
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
   * (PatchRequest som PatchRequest2). Operations.ElementAt(0). Path.AttributePath: ”manager”
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
   * (PatchRequest som PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referens: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

6. Om du vill avetablera en användare från en identity-butik fronted av en SCIM-tjänst, skickar Azure AD en begäran som: 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   Om tjänsten har skapats med Common Language infrastruktur-bibliotek som tillhandahålls av Microsoft för att implementera SCIM-tjänster, översätts förfrågan till ett anrop till metoden Delete av tjänstens-providern.   Metoden har den här signaturen: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   Objektet som tillhandahålls som värde för argumentet resourceIdentifier har de här egenskaperna i exemplet med en begäran om att avetablera en användare: 

   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”

## <a name="group-provisioning-and-de-provisioning"></a>Gruppetablering och avetablering
Följande bild visar meddelandena som Azure AcD skickar till SCIM-tjänsten att hantera livscykeln för en grupp i en annan identitet store.  Meddelandena som skiljer sig från de meddelanden som hör till användare på tre sätt: 

* Schemat för en gruppresurs identifieras som `http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group`.  
* Begäranden att hämta grupper förbehålla att attributet medlemmar är som ska undantas från en resurs i svaret på begäran.  
* Förfrågningar att avgöra om ett referensattribut har ett visst värde är begäranden om attributet medlemmar.  

![][5]
*Bild 6: Gruppetablering och avetablering sekvens*

## <a name="related-articles"></a>Relaterade artiklar
* [Automatisera användaren etablering/avetablering för SaaS-appar](user-provisioning.md)
* [Anpassa attributmappningar för etableringen av användare](customize-application-attributes.md)
* [Skriva uttryck för attributmappningar](functions-for-customizing-application-data.md)
* [Omfångsfilter för etableringen av användare](define-conditional-rules-for-provisioning-user-accounts.md)
* [Kontoetableringsmeddelanden](user-provisioning.md)
* [Lista över guider om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
