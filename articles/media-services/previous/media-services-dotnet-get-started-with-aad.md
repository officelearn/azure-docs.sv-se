---
title: Använd Azure AD-autentisering för att få åtkomst till Azure Media Services-API med .NET | Microsoft Docs
description: Det här avsnittet visar hur du använder Azure Active Directory (Azure AD)-autentisering för att få åtkomst till Azure Media Services (AMS) API med .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 15b986d4e7567be48c582e4a39b727ab110de2be
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230955"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Använda Azure AD-autentisering för att få åtkomst till Azure Media Services-API med .NET

Från och med windowsazure.mediaservices 4.0.0.4, stöder Azure Media Services autentisering baserat på Azure Active Directory (AD Azure). Det här avsnittet visar hur du använder Azure AD-autentisering för att få åtkomst till Azure Media Services-API med Microsoft .NET.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Ett Azure-konto. Mer information finns i avsnittet om [den kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Ett Media Services-konto. Mer information finns i [skapar ett Azure Media Services-konto med Azure-portalen](media-services-portal-create-account.md).
- Senast [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) paketet.
- Bekant med ämnet [åtkomst till Azure Media Services-API med översikt över Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

När du använder Azure AD-autentisering med Azure Media Services kan autentisera du på något av två sätt:

- **Användarautentisering** autentiserar en person som använder appen för att interagera med Azure Media Services-resurser. Interaktivt program bör först frågar användaren om autentiseringsuppgifter. Ett exempel är en management-konsolapp som används av behöriga användare att övervaka kodningsjobb eller liveuppspelning. 
- **Autentisering av tjänstens huvudnamn** autentiserar en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som kör daemon-tjänster, mellannivå tjänster eller schemalagda jobb, t.ex webbappar, funktionsappar, logic apps, API: er eller mikrotjänster.

>[!IMPORTANT]
>Azure Media Services stöder för närvarande en modell för Azure Access Control Service-autentisering. Access Control-auktorisering ska att bli inaktuella den 22 juni 2018. Vi rekommenderar att du migrerar till en Azure Active Directory-autentiseringsmodellen så snart som möjligt.

## <a name="get-an-azure-ad-access-token"></a>Hämta en Azure AD-token

Klientappen måste begära en Azure AD-åtkomsttoken för att ansluta till Azure Media Services-API med Azure AD-autentisering. När du använder SDK för Media Services .NET-klient kan många av information om hur du skaffa en Azure AD-åtkomsttoken omsluten och förenklad för dig i den [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) och [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs)klasser. 

Du behöver inte ange Azure AD-auktoritet, Media Services resurs-URI eller intern information om Azure AD-program. Detta är välkända värden som redan har konfigurerats av Azure AD åtkomst tokenleverantör-klassen. 

Om du inte använder Azure Media Service .NET SDK, rekommenderar vi att du använder den [Azure AD-Autentiseringsbiblioteket](../../active-directory/develop/active-directory-authentication-libraries.md). Om du vill hämta värden för parametrarna som du vill använda med Azure AD-Autentiseringsbiblioteket, se [använder Azure portal för att få åtkomst till Azure AD-autentiseringsinställningar](media-services-portal-get-started-with-aad.md).

Du har också möjlighet att ersätta standardimplementering av den **AzureAdTokenProvider** med en egen implementering.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Installera och konfigurera Azure Media Services .NET SDK

>[!NOTE] 
>Om du vill använda Azure AD-autentisering med Media Services .NET SDK måste du ha senast [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) paketet. Lägg även till en referens till den **Microsoft.IdentityModel.Clients.ActiveDirectory** sammansättningen. Om du använder en befintlig app kan innehålla den **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** sammansättningen. 

1. Skapa ett nytt C#-konsolprogram i Visual Studio.
2. Använd den [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) nuget för att installera **Azure Media Services .NET SDK**. 

    Om du vill lägga till referenser med hjälp av NuGet, gör du följande: i **Solution Explorer**, högerklicka på projektnamnet och välj sedan **hantera NuGet-paket**. Sök sedan efter **windowsazure.mediaservices** och välj **installera**.
    
    ELLER

    Kör följande kommando **Pakethanterarkonsolen** i Visual Studio.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Lägg till **med** i källkoden.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Använda autentisering med användarnamn

För att ansluta till Azure Media Service-API med alternativet användare autentisering, måste klientappen begära en Azure AD-token med hjälp av följande parametrar:  

- Azure AD tenant-slutpunkten. Klient-informationen kan hämtas från Azure-portalen. Hovra över den inloggade användaren i det övre högra hörnet.
- Media Services resurs-URI.
- Media Services (ursprunglig) program klient-ID. 
- Media Services (ursprunglig) program omdirigerings-URI. 

Värdena för dessa parametrar finns i **AzureEnvironments.AzureCloudEnvironment**. Den **AzureEnvironments.AzureCloudEnvironment** konstant är en hjälp i .NET SDK för att hämta rätt miljö varierande inställningarna för en offentlig Azure-datacenter. 

Den innehåller fördefinierade miljöinställningar för att komma åt Media Services i offentlig Datacenter endast. Du kan använda för suveräna eller government cloud-regioner, **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvironment**, eller **AzureGermanCloudEnvironment** respektive.

I följande kodexempel skapas en token:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
Om du vill börja programmera mot Media Services, måste du skapa en **CloudMediaContext** -instans som representerar serverkontext. Den **CloudMediaContext** innehåller referenser till viktiga samlingar, inklusive jobb, tillgångar, filer, åtkomstprinciper och lokaliserare. 

Du också behöva skicka den **resurs-URI för REST medietjänster** till den **CloudMediaContext** konstruktor. Om du vill hämta resurs-URI för REST medietjänster, logga in på Azure-portalen väljer du ditt Azure Media Services-konto, Välj **API-åtkomst**, och välj sedan **Anslut till Azure Media Services med användarautentisering**. 

Följande exempel skapar en **CloudMediaContext** instans:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

I följande exempel visas hur du skapar Azure AD-token och kontexten:

    namespace AzureADAuthSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);
    
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
>Om du får ett undantag där det står ”fjärrservern returnerade ett fel: (401) Ej behörig ”, se den [åtkomstkontroll](media-services-use-aad-auth-to-access-ams-api.md#access-control) avsnitt av åtkomst till Azure Media Services-API med översikt över Azure AD-autentisering.

## <a name="use-service-principal-authentication"></a>Använd autentisering av tjänstens huvudnamn
    
För att ansluta till Azure Media Services-API med tjänstens huvudnamn alternativet är mellannivå appen (webb-API eller web application) måste du begär en Azure AD-token med följande parametrar:  

- Azure AD tenant-slutpunkten. Klient-informationen kan hämtas från Azure-portalen. Hovra över den inloggade användaren i det övre högra hörnet.
- Media Services resurs-URI.
- Azure AD application värden: den **klient-ID** och **klienthemlighet**.

Värdena för den **klient-ID** och **klienthemlighet** parametrar finns i Azure-portalen. Mer information finns i [komma igång med Azure AD-autentisering med Azure portal](media-services-portal-get-started-with-aad.md).

I följande kodexempel skapas en token med hjälp av den **AzureAdTokenCredentials** konstruktor som tar **AzureAdClientSymmetricKey** som en parameter: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Du kan också ange den **AzureAdTokenCredentials** konstruktor som tar **AzureAdClientCertificate** som en parameter. 

Anvisningar om hur du skapar och konfigurerar ett certifikat i ett formulär som kan användas av Azure AD finns i [autentiserar till Azure AD i daemon-appar med certifikat - manuella konfigurationssteg](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md).

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

Om du vill börja programmera mot Media Services, måste du skapa en **CloudMediaContext** -instans som representerar serverkontext. Du också behöva skicka den **resurs-URI för REST medietjänster** till den **CloudMediaContext** konstruktor. Du kan hämta den **resurs-URI för REST medietjänster** värdet i Azure Portal.

Följande exempel skapar en **CloudMediaContext** instans:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
I följande exempel visas hur du skapar Azure AD-token och kontexten:

    namespace AzureADAuthSample
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

Kom igång med [ladda upp filer till ditt konto](media-services-dotnet-upload-files.md).
