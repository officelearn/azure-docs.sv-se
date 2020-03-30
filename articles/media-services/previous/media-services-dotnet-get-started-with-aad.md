---
title: Använda Azure AD-autentisering för att komma åt Azure Media Services API med .NET | Microsoft-dokument
description: Det här avsnittet visar hur du använder Azure Active Directory (Azure AD) autentisering för att komma åt Azure Media Services (AMS) API med .NET.
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
ms.openlocfilehash: b53fca292630ef988ee1357ea50adc4d7b7e9be5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162887"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Använda Azure AD-autentisering för att komma åt Azure Media Services API med .NET

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Från och med windowsazure.mediaservices 4.0.0.4 stöder Azure Media Services autentisering baserat på Azure Active Directory (Azure AD). Det här avsnittet visar hur du använder Azure AD-autentisering för att komma åt Azure Media Services API med Microsoft .NET.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto. Mer information finns i [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/)av Azure . 
- Ett Media Services-konto. Mer information finns i [Skapa ett Azure Media Services-konto med Azure-portalen](media-services-portal-create-account.md).
- Det senaste [NuGet-paketet.](https://www.nuget.org/packages/windowsazure.mediaservices)
- Förtrogenhet med ämnet [Åtkomst till Azure Media Services API med Azure AD-autentiseringsöversikt](media-services-use-aad-auth-to-access-ams-api.md). 

När du använder Azure AD-autentisering med Azure Media Services kan du autentisera på ett av två sätt:

- **Användarautentisering** autentiserar en person som använder appen för att interagera med Azure Media Services-resurser. Det interaktiva programmet bör först uppmana användaren om autentiseringsuppgifter. Ett exempel är en hanteringskonsolapp som används av behöriga användare för att övervaka kodningsjobb eller livestreaming. 
- **Tjänstens huvudautentisering** autentiserar en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som kör daemontjänster, mellannivåtjänster eller schemalagda jobb, till exempel webbappar, funktionsappar, logikappar, API:er eller mikrotjänster.

>[!IMPORTANT]
>Azure Media Service stöder för närvarande en autentiseringsmodell för Azure Access Control Service. Åtkomstkontrollsauktorisering kommer dock att vara inaktuell den 22 juni 2018. Vi rekommenderar att du migrerar till en Azure Active Directory-autentiseringsmodell så snart som möjligt.

## <a name="get-an-azure-ad-access-token"></a>Hämta en Azure AD-åtkomsttoken

Om du vill ansluta till Azure Media Services API med Azure AD-autentisering måste klientappen begära en Azure AD-åtkomsttoken. När du använder Media Services .NET-klienten SDK radbryts och förenklas många av detaljerna om hur du skaffar en Azure AD-åtkomsttoken för dig i klasserna [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) och [AzureAdTokenCredentials.](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs) 

Du behöver till exempel inte ange Azure AD-behörighet, Media Services-resurs-URI eller information om azure AD-program. Det här är välkända värden som redan har konfigurerats av azure AD-åtkomsttokenproviderklassen. 

Om du inte använder Azure Media Service .NET SDK rekommenderar vi att du använder [Azure AD Authentication Library](../../active-directory/azuread-dev/active-directory-authentication-libraries.md). Information om hur du hämtar värden för de parametrar som du behöver använda med Azure AD Authentication Library finns i [Använda Azure-portalen för att komma åt Azure AD-autentiseringsinställningar](media-services-portal-get-started-with-aad.md).

Du har också möjlighet att ersätta standardimplementeringen av **AzureAdTokenProvider** med din egen implementering.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Installera och konfigurera Azure Media Services .NET SDK

>[!NOTE] 
>Om du vill använda Azure AD-autentisering med Media Services .NET SDK måste du ha det senaste [NuGet-paketet.](https://www.nuget.org/packages/windowsazure.mediaservices) Lägg också till en referens till **sammansättningen Microsoft.IdentityModel.Clients.ActiveDirectory.** Om du använder en befintlig app kan du inkludera sammansättningen **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll.** 

1. Skapa ett nytt C#-konsolprogram i Visual Studio.
2. Använd [paketet windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) NuGet för att installera **Azure Media Services .NET SDK**. 

    Så här lägger du till referenser med NuGet: högerklicka på projektnamnet i **Solution Explorer**och välj sedan **Hantera NuGet-paket**. Sök sedan efter **windowsazure.mediaservices** och välj **Installera**.
    
    ELLER

    Kör följande kommando i **Package Manager Console** i Visual Studio.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Lägg till **med hjälp av** källkoden.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Använda användarautentisering

Om du vill ansluta till Azure Media Service API med alternativet användarautentisering måste klientappen begära en Azure AD-token med hjälp av följande parametrar:  

- Slutpunkten för Azure AD-klient. Klientinformationen kan hämtas från Azure-portalen. Hovra över den inloggade användaren i det övre högra hörnet.
- Resurs-URI för Media Services.
- Media Services (inbyggt) programklient-ID. 
- Media Services (native) program omdirigera URI. 

Värdena för dessa parametrar finns i **AzureEnvironments.AzureCloudEnvironment**. **AzureEnvironments.AzureCloudMiljö-konstanten** är en hjälpare i .NET SDK för att få rätt miljövariabelinställningar för ett offentligt Azure-datacenter. 

Den innehåller fördefinierade miljöinställningar för åtkomst till Media Services endast i de offentliga datacenter. För suveräna eller myndighetsmolnregioner kan du använda **AzureChinaCloudEnvironment,** **AzureUsGovernmentEnvironment**eller **AzureGermanCloudEnvironment** respektive.

I följande kodexempel skapas en token:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
Om du vill börja programmera mot Media Services måste du skapa en **CloudMediaContext-instans** som representerar serverkontexten. **CloudMediaContext** innehåller referenser till viktiga samlingar, inklusive jobb, tillgångar, filer, åtkomstprinciper och positionerare. 

Du måste också skicka **resurs-URI för Media REST Services** till **CloudMediaContext-konstruktorn.** Om du vill hämta resurs-URI för Media REST Services loggar du in på Azure-portalen, väljer ditt Azure Media Services-konto, väljer **API-åtkomst**och väljer sedan **Anslut till Azure Media Services med användarautentisering**. 

I följande kodexempel skapas en **CloudMediaContext-instans:**

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
>Om du får ett undantag med det står "Fjärrservern returnerade ett fel: (401) Obehörigt", se avsnittet [Åtkomstkontroll](media-services-use-aad-auth-to-access-ams-api.md#access-control) i Åtkomst till Azure Media Services API med Azure AD-autentiseringsöversikt.

## <a name="use-service-principal-authentication"></a>Använd autentisering av tjänstens huvudnamn
    
Om du vill ansluta till Azure Media Services API med alternativet tjänstens huvudnamn måste din middle-tier-app (webb-API eller webbprogram) begära en Azure AD-token med följande parametrar:  

- Slutpunkten för Azure AD-klient. Klientinformationen kan hämtas från Azure-portalen. Hovra över den inloggade användaren i det övre högra hörnet.
- Resurs-URI för Media Services.
- Azure AD-programvärden: **klient-ID** och **klienthemlighet**.

Värdena för **klient-ID** och **klienthemliga** parametrar finns i Azure-portalen. Mer information finns i [Komma igång med Azure AD-autentisering med Azure-portalen](media-services-portal-get-started-with-aad.md).

I följande kodexempel skapas en token med hjälp av **AzureAdTokenCredentials-konstruktorn** som tar **AzureAdClientSymmetricKey** som parameter: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Du kan också ange **AzureAdTokenCredentials-konstruktorn** som tar **AzureAdClientCertificate** som parameter. 

Instruktioner om hur du skapar och konfigurerar ett certifikat i ett formulär som kan användas av Azure AD finns [i Autentisering till Azure AD i daemonappar med certifikat - manuella konfigurationssteg](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md).

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

Om du vill börja programmera mot Media Services måste du skapa en **CloudMediaContext-instans** som representerar serverkontexten. Du måste också skicka **resurs-URI för Media REST Services** till **CloudMediaContext-konstruktorn.** Du kan också hämta **resurs-URI för Media REST Services-värdet** från Azure-portalen.

I följande kodexempel skapas en **CloudMediaContext-instans:**

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

Kom igång med [att ladda upp filer till ditt konto](media-services-dotnet-upload-files.md).
