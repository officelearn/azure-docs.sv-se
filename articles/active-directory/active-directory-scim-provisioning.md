---
title: "Med hjälp av System för Identitetshantering domäner automatiskt etablera användare och grupper från Azure Active Directory till program | Microsoft Docs"
description: "Azure Active Directory kan automatiskt etablera användare och grupper till några program eller identitet butik som är fronted av en webbtjänst med det gränssnitt som definierats i specifikationen av SCIM-protokollet"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: mtillman
editor: 
ms.assetid: 4d86f3dc-e2d3-4bde-81a3-4a0e092551c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.custom: aaddev;it-pro
ms.openlocfilehash: 82649b0da67882a0088876798b6f0d79e46051a7
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="using-system-for-cross-domain-identity-management-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Med hjälp av System för Identitetshantering i domänerna att automatiskt etablera användare och grupper från Azure Active Directory till program

## <a name="overview"></a>Översikt
Azure Active Directory (AD Azure) automatiskt kan etablera användare och grupper till några program eller identitet store som fronted av en webbtjänst med gränssnittet definieras i den [System för domäner Identity Management (SCIM) 2.0 protokollspecifikation](https://tools.ietf.org/html/draft-ietf-scim-api-19). Azure Active Directory kan skicka begäranden om att skapa, ändra eller ta bort tilldelade användare och grupper till webbtjänsten. Webbtjänsten kan översätta dessa begäranden till åtgärder i Identitetslagret mål. 

![][0]
*Bild 1: Etablering från Azure Active Directory till en butik identitet via en webbtjänst*

Den här funktionen kan användas tillsammans med funktioner som ”ta med din egen app” i Azure AD för att aktivera enkel inloggning och automatisk användaretablering för program som tillhandahåller eller fronted av en SCIM-webbtjänst.

Det finns två användningsfall för att använda SCIM i Azure Active Directory:

* **Etablering av användare och grupper till program som stöder SCIM** program som stöder SCIM 2.0 och använder OAuth ägar-token för autentisering fungerar med Azure AD utan konfiguration.
* **Skapa din egen lösning för etablering för program som stöder andra API-baserad etablering** för icke-SCIM program kan du skapa en slutpunkt för SCIM att översätta mellan SCIM för Azure AD-slutpunkten och API: er har stöd för programmet för användaretablering. Vi ger Common Language Infrastructure (CLI) bibliotek och kodexempel som visar hur du gör ger en SCIM slutpunkt och översätta SCIM meddelanden för att hjälpa dig att utveckla en SCIM slutpunkt.  

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Etablering av användare och grupper till program som stöder SCIM
Azure AD kan konfigureras för att automatiskt etablera tilldelade användare och grupper till program som implementerar en [System för domäner Identity Management 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) webbtjänsten och acceptera OAuth ägar-token för autentisering. Inom SCIM 2.0-specifikationen måste program uppfylla följande villkor:

* Har stöd för att skapa användare och/eller grupper, enligt avsnittet 3.3 av SCIM-protokollet.  
* Har stöd för ändring av användare och/eller grupper med patch-förfrågningar enligt avsnittet 3.5.2 av SCIM-protokollet.  
* Har stöd för hämtning av en känd resurs enligt avsnittet 3.4.1 av SCIM-protokollet.  
* Har stöd för frågor till användare och/eller grupper, enligt avsnittet 3.4.2 av SCIM-protokollet.  Som standard tillfrågas användare av externalId och grupper är efterfrågas av displayName.  
* Har stöd för frågor till användaren genom ID och manager enligt punkt 3.4.2 av SCIM-protokollet.  
* Har stöd för frågor till grupper efter ID och av medlem enligt punkt 3.4.2 av SCIM-protokollet.  
* Accepterar OAuth ägar-token för auktorisering enligt avsnittet 2.1 av SCIM-protokollet.

Kontrollera med leverantören av program eller program leverantörens dokumentation för rapporter för kompatibilitet med dessa krav.

### <a name="getting-started"></a>Komma igång
Program som stöder SCIM-profilen som beskrivs i den här artikeln kan anslutas till Azure Active Directory med funktionen ”icke-galleriet program” i Azure AD application gallery. När du är ansluten, körs Azure AD en synkroniseringsprocess var tjugonde minut där den frågar programmets SCIM slutpunkt för tilldelade användare och grupper och skapar eller ändrar dem enligt tilldelning av information.

**Om du vill ansluta ett program stöder som SCIM:**

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Bläddra till ** Azure Active Directory > företagsprogram och välj **nytt program > alla > icke-galleriet programmet**.
3. Ange ett namn för ditt program och klicka på **Lägg till** ikon för att skapa ett app-objekt.
    
  ![][1]
  *Bild 2: Azure AD application gallery*
    
4. I skärmbilden som visas väljer du den **etablering** fliken i den vänstra kolumnen.
5. I den **etablering läge** väljer du **automatisk**.
    
  ![][2]
  *Bild 3: Konfigurera etablering i Azure-portalen*
    
6. I den **klient URL** , ange Webbadressen till programmets SCIM slutpunkt. Exempel: https://api.contoso.com/scim/v2/
7. Om slutpunkten SCIM kräver en OAuth ägar-token från en utfärdare än Azure AD, kopiera nödvändiga OAuth ägar-token till den valfria **hemlighet Token** fältet. Om det här fältet är tomt, med en OAuth ägar-token som utfärdas från Azure AD med varje begäran med Azure AD. Appar som använder Azure AD som en identitetsleverantör kan verifiera den här Azure AD-utfärdade token.
8. Klicka på den **Testanslutningen** så försöker ansluta till slutpunkten SCIM för Azure Active Directory. Om försöker misslyckas, visas information om felet.  
9. Om försök att ansluta till program-lyckad klickar **spara** spara administratörsautentiseringsuppgifter.
10. I den **mappningar** avsnittet finns det två valbar uppsättningar attributmappning: en för användarobjekt och en för gruppobjekt. Välj var och en att granska de attribut som synkroniseras från Azure Active Directory till din app. De attribut som valts som **matchande** egenskaper som används för att matcha de användare och grupper i din app för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

    >[!NOTE]
    >Alternativt kan du inaktivera synkronisering av gruppobjekt genom att inaktivera ”grupper”-mappning. 

11. Under **inställningar**, **omfång** fältet definierar vilka användare som är och eller grupper som ska synkroniseras. Att välja ”Sync endast har tilldelats användare och grupper” (rekommenderas) bara synkronisera användare och grupper som tilldelats i den **användare och grupper** fliken.
12. När konfigurationen är klar kan du ändra den **Status för etablering** till **på**.
13. Klicka på **spara** att starta Azure AD etableras. 
14. Om du synkroniserar endast har tilldelats användare och grupper (rekommenderas), måste du markera den **användare och grupper** fliken och tilldela användare och/eller grupper som du vill synkronisera.

När den inledande synkroniseringen har startat, kan du använda den **granskningsloggar** att övervaka förloppet som visar alla åtgärder som utförs av tjänsten etablering på din app. Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

>[!NOTE]
>Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var tjugonde minut så länge som tjänsten körs. 


## <a name="building-your-own-provisioning-solution-for-any-application"></a>Skapa din egen lösning för etablering för alla program
Du kan aktivera enkel inloggning och automatisk användaretablering för nästan alla program som innehåller en REST- eller SOAP användaretablering API genom att skapa en SCIM-webbtjänst som samverkar med Azure Active Directory.

Här är hur det fungerar:

1. Azure AD innehåller ett vanligt språk infrastrukturbibliotek med namnet [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Systemintegrerare och utvecklare kan använda det här biblioteket för att skapa och distribuera en SCIM-baserade webbtjänstslutpunkt kan ansluta Azure AD till Identitetslagret för alla program.
2. Mappningar implementeras i webbtjänsten för att mappa det standardiserade användarschemat till användarschemat och protokoll som krävs för programmet.
3. Slutpunkts-URL är registrerad i Azure AD som en del av ett anpassat program i programgalleriet.
4. Användare och grupper är tilldelade till det här programmet i Azure AD. Vid tilldelning av placeras de i en kö som ska synkroniseras till målprogrammet. Hantera kön synkroniseringsprocessen körs var tjugonde minut.

### <a name="code-samples"></a>Kodexempel
Att göra den här processen, en uppsättning [kodexempel](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) tillhandahålls som skapar en slutpunkt för webbtjänsten SCIM och visa Automatisk etablering. Ett exempel är av en provider som underhåller en fil med rader av kommaavgränsade värden som representerar användare och grupper.  Den andra är av en provider som fungerar på tjänsten Amazon Web Services identitets- och åtkomsthantering.  

**Förutsättningar**

* Visual Studio 2013 eller senare
* [Azure SDK för .NET](https://azure.microsoft.com/downloads/)
* Windows-datorn som stöder ASP.NET framework 4.5 som ska användas som SCIM slutpunkt. Den här datorn måste vara tillgänglig från molnet
* [En Azure-prenumeration med en utvärderingsversion eller licensierad version av Azure AD Premium](https://azure.microsoft.com/services/active-directory/)
* Amazon AWS-exemplet kräver bibliotek från den [AWS Toolkit för Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html). Mer information finns i README-filen som ingår i exemplet.

### <a name="getting-started"></a>Komma igång
Det enklaste sättet att implementera en SCIM-slutpunkt som kan acceptera etableringsbegäranden från Azure AD är att skapa och distribuera kodexempel som matar ut etablerade användare till en fil med kommaavgränsade värden (CSV).

**Skapa en exempel SCIM slutpunkt:**

1. Hämta exempel kodpaketet på [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2. Packa upp paketet och placera den på din Windows-dator på en plats, till exempel C:\AzureAD-BYOA-Provisioning-Samples\.
3. Starta FileProvisioningAgent lösningen i Visual Studio i den här mappen.
4. Välj **Verktyg > Library Package Manager > Package Manager-konsolen**, och kör följande kommandon att lösa lösning referenser FileProvisioningAgent projektet:
  ```` 
   Install-Package Microsoft.SystemForCrossDomainIdentityManagement
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   Install-Package Microsoft.Owin.Diagnostics
   Install-Package Microsoft.Owin.Host.SystemWeb
  ````
5. Skapa FileProvisioningAgent-projektet.
6. Starta Kommandotolken programmet i Windows (som administratör) och använda den **cd** kommando för att ändra katalogen till din **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug** mapp.
7. Kör du följande kommando och ersätter < ip-adress > med IP-adress eller domännamn namnet på Windows-dator:
  ````   
   FileAgnt.exe http://<ip-address>:9000 TargetFile.csv
  ````
8. I under **Windows-inställningar > nätverk och Internet-inställningar**, Välj den **Windows-brandväggen > Avancerade inställningar**, och skapa en **inkommande regel** som tillåter inkommande åtkomst till port 9000.
9. Om Windows-dator bakom en router kan måste routern konfigureras för att utföra Network Access Translation mellan dess port 9000 som exponeras för internet och port 9000 på Windows-datorn. Detta krävs för Azure AD för att kunna komma åt den här slutpunkten i molnet.

**För att registrera slutpunkten exempel SCIM i Azure AD:**

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Bläddra till ** Azure Active Directory > företagsprogram och välj **nytt program > alla > icke-galleriet programmet**.
3. Ange ett namn för ditt program och klicka på **Lägg till** ikon för att skapa ett app-objekt. Det programobjekt som skapas ska representera mål appen du skulle etablering till och implementera enkel inloggning för och inte bara SCIM slutpunkt.
4. I skärmbilden som visas väljer du den **etablering** fliken i den vänstra kolumnen.
5. I den **etablering läge** väljer du **automatisk**.
    
  ![][2]
  *Bild 4: Konfigurera etablering i Azure-portalen*
    
6. I den **klient URL** anger internet-exponerade URL och port för slutpunkten SCIM. Det är något som http://testmachine.contoso.com:9000 eller http://<ip-address>:9000/, där < ip-adress > är internet exponeras IP adress.  
7. Om slutpunkten SCIM kräver en OAuth ägar-token från en utfärdare än Azure AD, kopiera nödvändiga OAuth ägar-token till den valfria **hemlighet Token** fältet. Om det här fältet är tomt, innehåller en OAuth ägar-token som utfärdas från Azure AD med varje begäran Azure AD. Appar som använder Azure AD som en identitetsleverantör kan verifiera den här Azure AD-utfärdade token.
8. Klicka på den **Testanslutningen** så försöker ansluta till slutpunkten SCIM för Azure Active Directory. Om försöker misslyckas, visas information om felet.  
9. Om försök att ansluta till program-lyckad klickar **spara** spara administratörsautentiseringsuppgifter.
10. I den **mappningar** avsnittet finns det två valbar uppsättningar attributmappning: en för användarobjekt och en för gruppobjekt. Välj var och en att granska de attribut som synkroniseras från Azure Active Directory till din app. De attribut som valts som **matchande** egenskaper som används för att matcha de användare och grupper i din app för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.
11. Under **inställningar**, **omfång** fältet definierar vilka användare som är och eller grupper som ska synkroniseras. Att välja ”Sync endast har tilldelats användare och grupper” (rekommenderas) bara synkronisera användare och grupper som tilldelats i den **användare och grupper** fliken.
12. När konfigurationen är klar kan du ändra den **Status för etablering** till **på**.
13. Klicka på **spara** att starta Azure AD etableras. 
14. Om du synkroniserar endast har tilldelats användare och grupper (rekommenderas), måste du markera den **användare och grupper** fliken och tilldela användare och/eller grupper som du vill synkronisera.

När den inledande synkroniseringen har startat, kan du använda den **granskningsloggar** att övervaka förloppet som visar alla åtgärder som utförs av tjänsten etablering på din app. Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

Det sista steget vid verifiering av exemplet är att öppna filen TargetFile.csv i mappen \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug på din Windows-dator. När etableringen har körts, visas den här filen detaljer om allt tilldelade och etablerad användare och grupper.

### <a name="development-libraries"></a>Utvecklingsbibliotek
För att utveckla egna webbtjänst som uppfyller specifikationerna SCIM uppbyggd följande bibliotek som tillhandahålls av Microsoft för att påskynda utvecklingsprocessen: 

1. Common Language Infrastructure (CLI) bibliotek erbjuds för användning med språk som är baserat på infrastrukturen, till exempel C#. En av dessa bibliotek [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), deklarerar ett gränssnitt, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, som visas i följande bild: en utvecklare som använder biblioteken skulle implementera gränssnittet med en klass som kan refereras till, Allmänt, som en provider. Biblioteken kan utvecklare att distribuera en webbtjänst som överensstämmer med SCIM-specifikationen. Webbtjänsten kan finnas antingen i Internet Information Services eller alla körbara vanlig infrastruktur för språk-sammansättningen. Begäran översätts till anrop till leverantörens metoder som skulle vara programmerad av utvecklaren att använda vissa Identitetslagret.
  
  ![][3]
  
2. [Express route-hanterare](http://expressjs.com/guide/routing.html) tillgängliga för parsning av node.js begärandeobjekt som motsvarar anrop (enligt specifikationen SCIM), görs till en node.js-webbtjänst.   

### <a name="building-a-custom-scim-endpoint"></a>Skapa en anpassad SCIM slutpunkt
Med hjälp av CLI-bibliotek, kan utvecklare som använder dessa bibliotek värd sina tjänster i alla körbara vanlig infrastruktur för språk-sammansättningen eller i Internet Information Services. Här är exempelkod som värd för en tjänst i en körbar sammansättning på adressen http://localhost:9000: 

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

Den här tjänsten måste ha en HTTP-adress och servern certifikat för serverautentisering som rotcertifikatutfärdaren är något av följande: 

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

Värdet som angetts för argumentet certhash är här, tumavtrycket för certifikatet, men värdet som angetts för argumentet appid är en godtycklig globalt unik identifierare.  

Som värd för tjänsten inom Internet Information Services, skulle en utvecklare skapa en CLA kod bibliotekssammansättning med en klass som heter Start i standardnamnområdet för sammansättningen.  Här är ett exempel på sådan klass: 

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
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Hantering av slutpunkten autentisering
Begäranden från Azure Active Directory innehåller en OAuth 2.0-ägartoken.   Alla tjänster som tar emot begäran ska autentisera utfärdaren som Azure Active Directory för den förväntade Azure Active Directory-klienten för åtkomst till Azure Active Directory Graph-webbtjänsten.  I denna token identifieras utfärdaren av ett iss-anspråk som ”iss”: ”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”.  I det här exemplet basadressen för anspråksvärdet, https://sts.windows.net, identifierar Azure Active Directory som utfärdaren medan relativ adress segment, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, är en unik identifierare för Azure Active Directory-klient som token har utfärdats.  Om token har utfärdats för åtkomst till Azure Active Directory Graph-webbtjänsten ska identifierare för tjänsten, 00000002-0000-0000-c000-000000000000, i värdet för token eller anspråk.  

Utvecklare som använder biblioteken CLA som tillhandahålls av Microsoft för att skapa en SCIM-tjänst kan autentisera begäranden från Azure Active Directory med Microsoft.Owin.Security.ActiveDirectory paketet genom att följa dessa steg: 

1. I en provider, implementera egenskapen Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior genom att låta den returnera en metod som ska anropas när tjänsten startas: 

  ````
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
  ````

2. Lägg till följande kod i metoden att alla förfrågningar till någon av Tjänsteslutpunkter autentiserad som försetts med en token som utfärdas av Azure Active Directory för en angiven klient för åtkomst till Azure AD Graph-webbtjänsten: 

  ````
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
  ````


## <a name="user-and-group-schema"></a>Användar- och schema
Azure Active Directory kan etablera två typer av resurser till SCIM webbtjänster.  Dessa typer av resurser är användare och grupper.  

Resurser för användare identifieras av schema-ID, urn: ietf:params:scim:schemas:extension:enterprise:2.0:User som ingår i den här protokollspecifikation: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  Standardmappningen attribut för användare i Azure Active Directory attributen för urn: ietf:params:scim:schemas:extension:enterprise:2.0:User resurser finns i tabell 1 nedan.  

Gruppera resurser identifieras av schema-ID, http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Tabell 2 nedan visar standardmappningen attribut för resursgrupper i Azure Active Directory attributen http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabell 1: Standard användaren attributmappning
| Azure Active Directory-användare | urn: ietf:params:scim:schemas:extension:enterprise:2.0:User |
| --- | --- |
| IsSoftDeleted |aktiv |
| Visningsnamn |Visningsnamn |
| Fax TelephoneNumber |phoneNumbers typ eq ”fax” .value |
| givenName |name.givenName |
| Befattning |Rubrik |
| E-post |e-postmeddelanden typen eq ”arbete” .value |
| mailNickname |externalId |
| Manager |Manager |
| mobila |phoneNumbers typ eq ”mobil” .value |
| objekt-ID |id |
| Postnummer |adresser typen eq ”arbete” .postalCode |
| Proxy-adresser |e-postmeddelanden [Ange eq ”andra”]. Värdet |
| fysisk-leverans – OfficeName |[Ange eq ”andra”] adresser. Formaterad |
| StreetAddress |adresser typen eq ”arbete” .streetAddress |
| Efternamn |name.familyName |
| Telefonnummer |phoneNumbers typ eq ”arbete” .value |
| användaren huvudkontot |Användarnamn |

### <a name="table-2-default-group-attribute-mapping"></a>Tabell 2: Standard grupp attributmappning
| Azure Active Directory-grupp | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| --- | --- |
| Visningsnamn |externalId |
| E-post |e-postmeddelanden typen eq ”arbete” .value |
| mailNickname |Visningsnamn |
| medlemmar |medlemmar |
| objekt-ID |id |
| proxyAddresses |e-postmeddelanden [Ange eq ”andra”]. Värdet |

## <a name="user-provisioning-and-de-provisioning"></a>Användaretablering och avetablering
Följande bild visar meddelanden att Azure Active Directory skickar till en tjänst för SCIM att hantera livscykeln för en användare i en annan identitet store. Diagrammet visar även hur en SCIM tjänst implementeras med hjälp av CLI-bibliotek från Microsoft för skapande av dessa tjänster översätta dessa begäranden till anrop av metoderna av en provider.  

![][4]
*Bild 5: Användaretablering och avetablering sekvens*

1. Azure Active Directory frågar tjänsten för en användare med ett attributvärde för externalId matchar mailNickname attributvärdet för en användare i Azure AD. Frågan uttrycks som en begäran om Hypertext Transfer Protocol (HTTP) som det här exemplet där jyoung är ett exempel på en mailNickname för en användare i Azure Active Directory: 
  ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
  ````
  Om tjänsten har skapats med vanlig infrastruktur för språk-bibliotek som tillhandahålls av Microsoft för att implementera SCIM tjänster, översätts begäran till ett anrop till metoden fråga i den tjänstleverantören.  Här är signaturen för metoden: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
  ````
  Här är definitionen av gränssnittet Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 
  ````
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
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
  ````
  I följande exempel i en fråga för en användare med ett angivet värde för attributet externalId är värden argument som skickas till metoden frågan: 
  * parametrar. AlternateFilters.Count: 1
  * parametrar. AlternateFilters.ElementAt(0). AttributePath: ”externalId”
  * parametrar. AlternateFilters.ElementAt(0). Jämförelseoperator: ComparisonOperator.Equals
  * parametrar. AlternateFilter.ElementAt(0). ComparisonValue: ”jyoung”
  * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin. Begärande-ID ”] 

2. Om svaret på en fråga till webbtjänsten för en användare med ett externalId-attributvärde som matchar mailNickname attributvärdet för en användare inte returnerar några användare, begäranden Azure Active Directory för att etablera tjänsten en användare som motsvarar det i Azure Active Directory.  Här är ett exempel på en sådan begäran: 
  ````
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
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
  ````
  Vanlig infrastruktur för språk-bibliotek som tillhandahålls av Microsoft för att implementera SCIM tjänster kan översätta begäran i ett anrop till metoden skapa av tjänstens providern.  Create-metoden har den här signaturen: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
  ````
  Värdet för argumentet resurs är en instans av Microsoft.SystemForCrossDomainIdentityManagement i en begäran att etablera en användare. Core2EnterpriseUser klass som definierats i Microsoft.SystemForCrossDomainIdentityManagement.Schemas-biblioteket.  Om begäran om att etablera användaren lyckas, förväntas implementeringen av metoden för att returnera en instans av Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser klass med värdet för egenskapen ID angetts till den unika identifieraren för den nyetablerade användaren.  

3. Om du vill uppdatera en användare finns i en identity-butik fronted genom en SCIM fortsätter Azure Active Directory genom att begära det aktuella tillståndet för den användaren från tjänsten med en begäran som: 
  ````
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  I en tjänst som skapats med vanlig infrastruktur för språk-bibliotek som tillhandahålls av Microsoft för att implementera SCIM tjänster översätts begäran till ett anrop till metoden hämta i service provider.  Här är signaturen för hämta-metoden: 
  ````
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
  ````
  I en begäran om att hämta aktuell status för en användare exempelvis är värdena för egenskaperna för objektet som tillhandahålls som värde för argumentet parametrar följande: 
  
  * ID: ”54D382A4-2050-4C03-94D1-E769F1D15682”
  * SchemaIdentifier: ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”

4. Om ett referensattribut är uppdateras Azure Active Directory frågar tjänsten för att fastställa huruvida det aktuella värdet för referensattributet i Identitetslagret fronted av tjänsten redan överensstämmer med värdet för attributet i Azure Active Directory. För användare är det enda attributet som efterfrågas det aktuella värdet på det här sättet manager-attribut. Här är ett exempel på en begäran om att avgöra om attributet manager för en viss användare-objektet har ett visst värde: 
  ````
    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...
  ````
  Värdet för Frågeparametern attribut-id, innebär att om ett användarobjekt finns som uppfyller de uttryck som värde för Filterparametern-frågan och sedan tjänsten förväntas att svara med en urn: ietf:params:scim:schemas:core:2.0:User eller urn: ietf:params:scim:schemas:extension:enterprise:2.0:User resurs, inklusive endast värdet för den resurs-id-attribut.  Värdet för den **id** attributet är känt som begäranden. Den ingår i värdet för Frågeparametern filter; Syftet med ber om den är faktiskt att begära en minimal representation av en resurs som uppfyller filteruttrycket som en indikation på om det finns sådana objekt.   

  Om tjänsten har skapats med vanlig infrastruktur för språk-bibliotek som tillhandahålls av Microsoft för att implementera SCIM tjänster, översätts begäran till ett anrop till metoden fråga i den tjänstleverantören. Värdet för egenskaperna för objektet som tillhandahålls som värde för argumentet parametrar är följande: 
  
  * parametrar. AlternateFilters.Count: 2
  * parametrar. AlternateFilters.ElementAt(x). AttributePath: ”id”
  * parametrar. AlternateFilters.ElementAt(x). Jämförelseoperator: ComparisonOperator.Equals
  * parametrar. AlternateFilter.ElementAt(x). ComparisonValue: ”54D382A4-2050-4C03-94D1-E769F1D15682”
  * parametrar. AlternateFilters.ElementAt(y). AttributePath: ”manager”
  * parametrar. AlternateFilters.ElementAt(y). Jämförelseoperator: ComparisonOperator.Equals
  * parametrar. AlternateFilter.ElementAt(y). ComparisonValue: ”2819c223-7f76-453a-919d-413861904646”
  * parametrar. RequestedAttributePaths.ElementAt(0): ”id”
  * parametrar. SchemaIdentifier: ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”

  Här kan värdet för indexet x kan vara 0 och värdet för y indexet kan vara 1, eller värdet för x kan vara 1 och värdet för y kan vara 0, beroende på ordningen på uttryck för filter Frågeparametern.   

5. Här är ett exempel på en begäran från Azure Active Directory till en SCIM-tjänsten för att uppdatera en användare: 
  ````
    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
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
  ````
  Infrastruktur för Microsoft Common Language-bibliotek för att implementera SCIM tjänster kan översätta begäran i ett anrop till metoden Update på tjänstens provider. Här är signaturen för metoden Update: 
  ````
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
  ````
    I en begäran om att uppdatera en användare exempelvis har det angivna objektet som värde för argumentet korrigering egenskapsvärdena: 
  
  * ResourceIdentifier.Identifier: ”54D382A4-2050-4C03-94D1-E769F1D15682”
  * ResourceIdentifier.SchemaIdentifier: ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”
  * (PatchRequest som PatchRequest2). Operations.Count: 1
  * (PatchRequest som PatchRequest2). Operations.ElementAt(0). OperationName: OperationName.Add
  * (PatchRequest som PatchRequest2). Operations.ElementAt(0). Path.AttributePath: ”manager”
  * (PatchRequest som PatchRequest2). Operations.ElementAt(0). Value.Count: 1
  * (PatchRequest som PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referens: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
  * (PatchRequest som PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Värde: 2819c223-7f76-453a-919d-413861904646

6. Om du vill avetablera en användare från en Identitetslagret fronted av SCIM-tjänsten, skickar Azure AD en begäran som: 
  ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  Om tjänsten har skapats med vanlig infrastruktur för språk-bibliotek som tillhandahålls av Microsoft för att implementera SCIM tjänster, översätts begäran till ett anrop till metoden Delete av tjänstens providern.   Den här metoden har den här signaturen: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
  ````
  Objektet som tillhandahålls som värde för argumentet resourceIdentifier har egenskapsvärdena i exemplet med en begäran om att avetablera en användare: 
  
  * ResourceIdentifier.Identifier: ”54D382A4-2050-4C03-94D1-E769F1D15682”
  * ResourceIdentifier.SchemaIdentifier: ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”

## <a name="group-provisioning-and-de-provisioning"></a>Gruppen etablering och avetablering
Följande bild visar meddelanden att Azure AcD skickar till en tjänst för SCIM att hantera livscykeln för en grupp i en annan identitet store.  Dessa meddelanden skiljer sig från de meddelanden som hör till användare på tre sätt: 

* Schemat för en gruppresurs identifieras som http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  
* Begäranden att hämta grupper kräva att attributet medlemmar är som ska undantas från alla resurser som finns i svaret på begäran.  
* Förfrågningar att avgöra om ett referensattribut har ett visst värde är förfrågningar om attributet medlemmar.  

![][5]
*Bild 6: Grupp etablering och avetablering sekvens*

## <a name="related-articles"></a>Relaterade artiklar
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
* [Automatisera användaren etablering/avetablering för SaaS-appar](active-directory-saas-app-provisioning.md)
* [Anpassa attributmappning för Användaretablering](active-directory-saas-customizing-attribute-mappings.md)
* [Skriva uttryck för attributmappning](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Omfångsfilter för Användaretablering](active-directory-saas-scoping-filters.md)
* [Kontot etablering meddelanden](active-directory-saas-account-provisioning-notifications.md)
* [Lista över självstudier om hur du integrerar SaaS-appar](active-directory-saas-tutorial-list.md)

<!--Image references-->
[0]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[1]: ./media/active-directory-scim-provisioning/scim-figure-2a.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2b.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
