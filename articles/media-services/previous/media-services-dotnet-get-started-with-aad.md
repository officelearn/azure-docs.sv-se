---
title: Använda Azure AD-autentisering för att komma åt Azure Media Services-API med .NET | Microsoft Docs
description: Det här avsnittet visar hur du använder Azure Active Directory (AD Azure) autentisering för att komma åt Azure Media Services (AMS) API med .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: b8f58f4010590dc40d5e8dc7ac1b634f161a807d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Använda Azure AD-autentisering för att komma åt Azure Media Services-API med .NET

Från och med windowsazure.mediaservices 4.0.0.4 stöder Azure Media Services autentisering baserat på Azure Active Directory (AD Azure). Det här avsnittet visar hur du använder Azure AD-autentisering för att komma åt Azure Media Services-API med Microsoft .NET.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto. Mer information finns i avsnittet om [den kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Ett Media Services-konto. Mer information finns i [skapa ett Azure Media Services-konto med hjälp av Azure portal](media-services-portal-create-account.md).
- Senast [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) paketet.
- Om du är bekant med ämnet [åtkomst till Azure Media Services API med AAD autentiseringsöversikt](media-services-use-aad-auth-to-access-ams-api.md). 

När du använder Azure AD-autentisering med Azure Media Services kan autentisera du på något av två sätt:

- **Användarautentisering** autentiserar en person som använder appen för att interagera med Azure Media Services-resurser. Interaktiva program bör först uppmana användaren att ange autentiseringsuppgifter. Ett exempel är en management konsolapp som används av behöriga användare att övervaka kodning jobb eller direktsänd strömning. 
- **Autentiseringen av tjänsten huvudnamn** autentiserar en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som körs daemon tjänster, mellannivå tjänster eller schemalagt jobb, t.ex webbprogram, funktionen appar, logikappar, API: er eller mikrotjänster.

>[!IMPORTANT]
>Azure Media Service stöder för närvarande en modell för autentisering av Azure Access Control Service. Dock kommer åtkomstkontroll tillstånd att bli inaktuell på 22 juni 2018. Vi rekommenderar att du migrerar till en modell för Azure Active Directory-autentisering så snart som möjligt.

## <a name="get-an-azure-ad-access-token"></a>Hämta en Azure AD-åtkomst-token

För att ansluta till Azure Media Services-API med Azure AD-autentisering, måste klientapp att begära en Azure AD-åtkomsttoken. När du använder klient-Media Services .NET SDK många av information om hur du skaffar en Azure AD-åtkomsttoken omsluten och förenklad för dig i den [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) och [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs) klasser. 

Du behöver inte ange utfärdare för Azure AD, Media Services resurs-URI eller intern programinformation för Azure AD. Detta är välkända värden som redan har konfigurerats av Azure AD åtkomst tokenleverantör-klassen. 

Om du inte använder Azure Media Service .NET SDK, rekommenderar vi att du använder den [Azure AD-Autentiseringsbiblioteket](../../active-directory/develop/active-directory-authentication-libraries.md). Värden för parametrarna som du vill använda med Azure AD-Autentiseringsbiblioteket finns [Använd Azure-portalen för att få åtkomst till Azure AD-autentiseringsinställningar](media-services-portal-get-started-with-aad.md).

Du har också möjlighet att ersätta standardimplementering av den **AzureAdTokenProvider** med din egen implementering.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Installera och konfigurera Azure Media Services .NET SDK

>[!NOTE] 
>Om du vill använda Azure AD-autentisering med Media Services .NET SDK, behöver du senast [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) paketet. Lägg även till en referens till den **Microsoft.IdentityModel.Clients.ActiveDirectory** sammansättning. Om du använder en befintlig app, inkludera den **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** sammansättning. 

1. Skapa ett nytt C#-konsolprogram i Visual Studio.
2. Använd den [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) NuGet-paketet för att installera **Azure Media Services .NET SDK**. 

    Om du vill lägga till referenser med NuGet, gör du följande: i **Solution Explorer**, högerklicka på projektnamnet och välj sedan **hantera NuGet-paket**. Sök sedan efter **windowsazure.mediaservices** och välj **installera**.
    
    ELLER

    Kör följande kommando **Pakethanterarkonsolen** i Visual Studio.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Lägg till **med** till din källkod.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Använd autentisering av användare

För att ansluta till Azure Media Service API med autentiseringsalternativet som användare behöver klientappen begära en Azure AD-token med hjälp av följande parametrar:  

- Slutpunkten för Azure AD-klient. Klient-informationen kan hämtas från Azure-portalen. Hovra över inloggade användaren i det övre högra hörnet.
- Media Services resurs-URI.
- Media Services (ursprunglig) programmet klient-ID. 
- Media Services (intern)-programmet omdirigerings-URI. 

Värdena för dessa parametrar finns i **AzureEnvironments.AzureCloudEnvironment**. Den **AzureEnvironments.AzureCloudEnvironment** konstant är en hjälp i .NET SDK för att hämta rätt miljön variabeln inställningar för en offentlig Azure-datacenter. 

Den innehåller fördefinierade inställningar för åtkomst till Media Services i de offentliga data datacenter. Du kan använda för statliga eller statlig molnområdena **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvrionment**, eller **AzureGermanCloudEnvironment** respektive.

Följande exempel skapar en token:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
Om du vill starta programmera mot Media Services måste du skapa en **CloudMediaContext** -instans som representerar serverkontext. Den **CloudMediaContext** innehåller referenser till viktiga samlingar, inklusive jobb, tillgångar, filer, principer för åtkomst och lokaliserare. 

Du måste också ange den **resurs-URI för Media Services för REST** till den **CloudMediaContext** konstruktor. För att få resurs-URI för REST medietjänster logga in på Azure portal, väljer du Azure Media Services-konto, Välj **API-åtkomst**, och välj sedan **Anslut till Azure Media Services med användarautentisering med**. 

Följande exempel skapar en **CloudMediaContext** instans:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

I följande exempel visas hur du skapar Azure AD-token och kontexten:

    namespace AADAuthSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR AAD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);
    
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
            }
    
        }
    }

>[!NOTE]
>Om du får ett undantag som säger ”fjärrservern returnerade ett fel: (401) obehörig” finns på [åtkomstkontroll](media-services-use-aad-auth-to-access-ams-api.md#access-control) avsnitt för att få åtkomst till Azure Media Services API med översikt över Azure AD-autentisering.

## <a name="use-service-principal-authentication"></a>Använd service principal autentisering
    
För att ansluta till API: et för Azure Media Services med service principal alternativet din app på mellannivå (webb-API eller webbprogram) behöver du begär en Azure AD-token med följande parametrar:  

- Slutpunkten för Azure AD-klient. Klient-informationen kan hämtas från Azure-portalen. Hovra över inloggade användaren i det övre högra hörnet.
- Media Services resurs-URI.
- Azure AD application värden: den **klient-ID** och **klienthemlighet**.

Värdena för den **klient-ID** och **klienthemlighet** parametrar finns i Azure-portalen. Mer information finns i [komma igång med Azure AD-autentisering med hjälp av Azure portal](media-services-portal-get-started-with-aad.md).

Följande exempel skapar en token med hjälp av den **AzureAdTokenCredentials** konstruktor som tar **AzureAdClientSymmetricKey** som en parameter: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Du kan också ange den **AzureAdTokenCredentials** konstruktor som tar **AzureAdClientCertificate** som en parameter. 

Instruktioner om hur du skapar och konfigurerar ett certifikat i en form som kan användas av Azure AD finns i [autentisera till Azure AD i daemon appar med certifikat - manuella konfigurationsstegen](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md).

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

Om du vill starta programmera mot Media Services måste du skapa en **CloudMediaContext** -instans som representerar serverkontext. Du måste också ange den **resurs-URI för Media Services för REST** till den **CloudMediaContext** konstruktor. Du kan hämta den **resurs-URI för REST medietjänster** värdet i Azure Portal.

Följande exempel skapar en **CloudMediaContext** instans:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
I följande exempel visas hur du skapar Azure AD-token och kontexten:

    namespace AADAuthSample
    {
    
        class Program
        {
            static void Main(string[] args)
            {
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                            new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                            AzureEnvironments.AzureCloudEnvironment);
            
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
    
                Console.ReadLine();
            }
    
        }
    }

## <a name="next-steps"></a>Nästa steg

Kom igång med [överföringen av filer till ditt konto](media-services-dotnet-upload-files.md).
