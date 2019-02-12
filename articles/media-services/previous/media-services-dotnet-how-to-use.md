---
title: Konfigurera datorn för Media Services-utveckling med .NET
description: Läs mer om kraven för Media Services med hjälp av Media Services SDK för .NET. Lär dig också att skapa en app i Visual Studio.
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
ms.date: 02/09/2019
ms.author: juliako
ms.openlocfilehash: fb6f492f465f6570afea911dfca8a92d4ed3dc7c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56001417"
---
# <a name="media-services-development-with-net-legacy"></a>Media Services-utveckling med .NET (äldre)
[!INCLUDE [media-services-selector-setup](../../../includes/media-services-selector-setup.md)]

Den här artikeln beskriver hur du börja utveckla Media Services-program med hjälp av .NET.

Den **Azure Media Services .NET SDK** klientbiblioteket hjälper dig att programmera mot Media Services med hjälp av .NET. Att göra det ännu enklare att utveckla med .NET och den **Azure Media Services .NET SDK-tilläggen** bibliotek har angetts. Det här biblioteket innehåller en uppsättning tilläggsmetoder och hjälpfunktioner som förenklar din .NET-kod. Båda bibliotek är tillgängliga via **NuGet** och **GitHub**.

## <a name="prerequisites"></a>Förutsättningar
* Ett Media Services-konto i en ny eller befintlig Azure-prenumeration. Finns i artikeln [så här skapar du ett Media Services-konto](media-services-portal-create-account.md).
* Operativsystem: Windows 10, Windows 7, Windows 2008 R2 eller Windows 8.
* .NET framework 4.5 eller senare.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt
Det här avsnittet visar hur du skapar ett projekt i Visual Studio och ställa in det för Media Services-utveckling.  I det här fallet projektet är en C# Windows-program, men samma installationsanvisningarna visas här gäller för andra typer av projekt som du kan skapa för Media Services-program (till exempel en Windows Forms-program eller en ASP.NET-webbapp).

Det här avsnittet visar hur du använder **NuGet** att lägga till Media Services .NET SDK-tillägg och andra beroende bibliotek.

Du kan också hämta de senaste nyheterna för Media Services .NET SDK från GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) eller [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), bygg lösningen och Lägg till referenser till klientprojektet. Alla nödvändiga beroenden hämta har hämtat och extraherat automatiskt.

1. Skapa ett nytt C#-konsolprogram i Visual Studio. Ange den **namn**, **plats**, och **lösningsnamn**, och klicka sedan på OK.
2. Skapa lösningen.
3. Använd **NuGet** att installera och lägga till **Azure Media Services .NET SDK-tilläggen** (**windowsazure.mediaservices.extensions**). När du installerar det här paketet installeras även **Media Services .NET SDK** och lägger till alla andra nödvändiga beroenden.
   
    Kontrollera att du har den senaste versionen av NuGet som är installerad. Mer information och installationsanvisningar finns i [NuGet](http://nuget.codeplex.com/).

    1. Högerklicka på namnet på projektet i Solution Explorer och välj **hantera NuGet-paket**.

    2. Dialogrutan Hantera NuGet-paket visas.

    3. I Online-galleriet, Sök efter Azure MediaServices tillägg, Välj **Azure Media Services .NET SDK-tilläggen** (**windowsazure.mediaservices.extensions**), och klicka sedan på den  **Installera** knappen.
   
    4. Projektet har ändrats och referenser till Media Services .NET SDK-tilläggen, Media Services .NET SDK och andra beroende sammansättningar har lagts till.
4. Överväg att aktivera NuGet-Paketåterställning för att flytta upp en tydligare utvecklingsmiljö. Mer information finns i [NuGet-Paketåterställning ”](http://docs.nuget.org/consume/package-restore).
5. Lägg till en referens till **System.Configuration** sammansättningen. Den här sammansättningen innehåller System.Configuration. **ConfigurationManager** klass som används för att komma åt configuration-filer (till exempel App.config).
   
    1. Högerklicka på projektnamnet i Solution Explorer för att lägga till referenser med hjälp av dialogrutan Hantera referenser. Klicka sedan på **Lägg till**, klicka sedan på **referens...** .
   
    2. Dialogrutan Hantera referenser visas.
    3. Under .NET framework-sammansättningar, hitta och välja i sammansättningen System.Configuration och tryck på **OK**.
6. Öppna filen App.config och Lägg till en **appSettings** avsnitt i den. Ange de värden som behövs för att ansluta till Media Services-API. Mer information finns i [åtkomst till Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

    Ange värden som behövs för att ansluta med hjälp av den **tjänstens huvudnamn** autentiseringsmetod.

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

7. Lägg till den **System.Configuration** referens till ditt projekt.
8. Skriv över de befintliga **med** instruktioner i början av filen Program.cs med följande kod:

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

Här är en liten exempel som ansluter till AMS-API och visar en lista över alla tillgängliga Media-processorer.

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

Nu [du kan ansluta till AMS API](media-services-use-aad-auth-to-access-ams-api.md) och starta [utveckla](media-services-dotnet-get-started.md).


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

