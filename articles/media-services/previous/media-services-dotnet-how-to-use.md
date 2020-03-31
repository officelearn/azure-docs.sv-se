---
title: Konfigurera dator för utveckling av mediatjänster med .NET
description: Läs mer om förutsättningarna för Media Services med Media Services SDK för .NET. Lär dig också hur du skapar en Visual Studio-app.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 51fffbd170daecfec6fcea95caa0526e6d881407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "64724118"
---
# <a name="media-services-development-with-net"></a>Utveckling av mediatjänster med .NET 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

I den här artikeln beskrivs hur du börjar utveckla Media Services-program med .NET.

**Med Azure Media Services .NET SDK-biblioteket** kan du programmera mot Media Services med .NET. För att göra det ännu enklare att utveckla med .NET tillhandahålls **Azure Media Services .NET SDK Extensions-biblioteket.** Det här biblioteket innehåller en uppsättning tilläggsmetoder och hjälpfunktioner som förenklar .NET-koden. Båda biblioteken är tillgängliga via **NuGet** och **GitHub**.

## <a name="prerequisites"></a>Krav
* Ett Media Services-konto i en ny eller befintlig Azure-prenumeration. Se artikeln [Hur du skapar ett Media Services-konto](media-services-portal-create-account.md).
* Operativsystem: Windows 10, Windows 7, Windows 2008 R2 eller Windows 8.
* .NET Framework 4.5 eller senare.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt
I det här avsnittet visas hur du skapar ett projekt i Visual Studio och konfigurerar det för utveckling av Media Services.  I det här fallet är projektet ett C# Windows-konsolprogram, men samma inställningssteg som visas här gäller för andra typer av projekt som du kan skapa för Media Services-program (till exempel ett Windows-formulärprogram eller ett ASP.NET webbprogram).

I det här avsnittet visas hur du använder **NuGet** för att lägga till Media Services .NET SDK-tillägg och andra beroende bibliotek.

Du kan också hämta de senaste Media Services .NET SDK-bitarna från GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) eller [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), skapa lösningen och lägga till referenser till klientprojektet. Alla nödvändiga beroenden hämtas och extraheras automatiskt.

1. Skapa ett nytt C#-konsolprogram i Visual Studio. Ange **namn,** **plats**och **lösningsnamn**och klicka sedan på OK.
2. Skapa lösningen.
3. Använd **NuGet** för att installera och lägga till **Azure Media Services .NET SDK-tillägg** **(windowsazure.mediaservices.extensions**). När du installerar det här paketet installeras även **Media Services .NET SDK** och lägger till alla andra nödvändiga beroenden.
   
    Se till att du har den senaste versionen av NuGet installerad. Mer information och installationsanvisningar finns i [NuGet](https://nuget.codeplex.com/).

    1. Högerklicka på namnet på projektet i Solution Explorer och välj **Hantera NuGet-paket**.

    2. Dialogrutan Hantera NuGet-paket öppnas.

    3. Sök efter Azure MediaServices-tillägg i galleriet Online, välj **Azure Media Services .NET SDK-tillägg** **(windowsazure.mediaservices.extensions**) och klicka sedan på knappen **Installera.**
   
    4. Projektet ändras och referenser till Media Services .NET SDK-tillägg, Media Services .NET SDK och andra beroende sammansättningar läggs till.
4. För att främja en renare utvecklingsmiljö, överväga att aktivera NuGet Package Restore. Mer information finns i [NuGet Package Restore"](https://docs.nuget.org/consume/package-restore).
5. Lägg till en referens till **System.Configuration-sammansättning.** Den här sammansättningen innehåller System.Configuration. **ConfigurationManager-klass** som används för att komma åt konfigurationsfiler (till exempel App.config).
   
    1. Om du vill lägga till referenser med dialogrutan Hantera referenser högerklickar du på projektnamnet i Lösningsutforskaren. Klicka sedan på **Lägg till**och sedan på **Referens...**.
   
    2. Dialogrutan Hantera referenser visas.
    3. Leta reda på och välj monteringen System.Configuration under .NET framework assemblies och tryck på **OK**.
6. Öppna filen App.config och lägg till ett **avsnittet appSettings** i filen. Ange de värden som behövs för att ansluta till API:et för Medietjänster. Mer information finns [i Komma åt Azure Media Services API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

    Ange de värden som behövs för att ansluta med **tjänstens huvudautentiseringsmetod.**

        ```csharp
                <configuration>
                ...
                    <appSettings>
                        <add key="AMSAADTenantDomain" value="tenant"/>
                        <add key="AMSRESTAPIEndpoint" value="endpoint"/>
                        <add key="AMSClientId" value="id"/>
                        <add key="AMSClientSecret" value="secret"/>
                    </appSettings>
                </configuration>
        ```

7. Lägg till **systemkonfigurationsreferensen** i projektet.
8. Skriv över befintliga **med hjälp av** satser i början av Program.cs filen med följande kod:

    ```csharp      
            using System;
            using System.Configuration;
            using System.IO;
            using Microsoft.WindowsAzure.MediaServices.Client;
            using System.Threading;
            using System.Collections.Generic;
            using System.Linq;
    ```

    Nu är du redo att börja utveckla ett Media Services-program.    

## <a name="example"></a>Exempel

Här är ett litet exempel som ansluter till AMS API och listar alla tillgängliga medieprocessorer.

```csharp
        class Program
        {
            // Read values from the App.config file.

            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AMSAADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
            private static readonly string _AMSClientId =
                ConfigurationManager.AppSettings["AMSClientId"];
            private static readonly string _AMSClientSecret =
                ConfigurationManager.AppSettings["AMSClientSecret"];
        
            private static CloudMediaContext _context = null;
            static void Main(string[] args)
            {
                AzureAdTokenCredentials tokenCredentials = 
                    new AzureAdTokenCredentials(_AADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
        
                // List all available Media Processors
                foreach (var mp in _context.MediaProcessors)
                {
                    Console.WriteLine(mp.Name);
                }
        
            }
 ```

## <a name="next-steps"></a>Nästa steg

Nu [kan du ansluta till AMS API](media-services-use-aad-auth-to-access-ams-api.md) och börja [utveckla](media-services-dotnet-get-started.md).


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

