---
title: "Konfigurera datorn för Media Services-utveckling med .NET"
description: "Lär dig mer om kraven för Media Services med Media Services SDK för .NET. Lär dig också att skapa en app i Visual Studio."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/23/2017
ms.author: juliako
ms.openlocfilehash: 024b4cbb13001d67e7c1f0b86a84dfb43478c49d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="media-services-development-with-net"></a>Media Services-utveckling med .NET
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Det här avsnittet beskrivs hur du startar utveckling av Media Services-program med hjälp av .NET.

Den **Azure Media Services .NET SDK** biblioteket kan du programmerar mot Media Services med hjälp av .NET. Att göra det enklare att utveckla med .NET, den **Azure Media Services .NET SDK-tilläggen** bibliotek har angetts. Det här biblioteket innehåller en uppsättning tilläggsmetoder och hjälpfunktioner som förenklar .NET-kod. Båda bibliotek är tillgängligt via **NuGet** och **GitHub**.

## <a name="prerequisites"></a>Krav
* Ett Media Services-konto i en ny eller befintlig Azure-prenumeration. Mer information finns i [Så här skapar du ett Media Services-konto](media-services-portal-create-account.md).
* Operativsystem: Windows 10, Windows 7, Windows 2008 R2 eller Windows 8.
* .NET framework 4.5.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt
Det här avsnittet visar hur du skapar ett projekt i Visual Studio och ställa in det för Media Services-utveckling.  I det här fallet projektet är ett Windows C#-konsolprogram men samma konfigurationsstegen visas här gäller för andra typer av projekt som du kan skapa för Media Services-program (till exempel en Windows Forms-program eller ASP.NET-webbprogram).

Det här avsnittet visar hur du använder **NuGet** att lägga till Media Services .NET SDK-tillägg och andra beroende bibliotek.

Alternativt kan du hämta den senaste Media Services .NET SDK-bits från GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) eller [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), skapa lösningen och Lägg till referenser till klientprojektet. Alla nödvändiga beroenden hämta har hämtat och extraherat automatiskt.

1. Skapa ett nytt C#-konsolprogram i Visual Studio. Ange den **namn**, **plats**, och **lösningsnamn**, och klicka sedan på OK.
2. Skapa lösningen.
3. Använd **NuGet** att installera och lägga till **Azure Media Services .NET SDK-tilläggen** (**windowsazure.mediaservices.extensions**). När du installerar det här paketet installeras även **Media Services .NET SDK** och lägger till alla andra nödvändiga beroenden.
   
    Se till att du har den senaste versionen av NuGet installerad. Mer information och installationsanvisningar finns i [NuGet](http://nuget.codeplex.com/).

    1. Högerklicka på projektet i Solution Explorer och välj **hantera NuGet-paket**.

    2. Dialogrutan Hantera NuGet-paket visas.

    3. I Online-galleriet, Sök efter Azure MediaServices tillägg, Välj **Azure Media Services .NET SDK-tilläggen** (**windowsazure.mediaservices.extensions**), och klicka sedan på den  **Installera** knappen.
   
    4. Projektet har ändrats och referenser till Media Services .NET SDK-tilläggen Media Services .NET SDK och andra beroende sammansättningar har lagts till.
4. Överväg att aktivera NuGet-Paketåterställning för att flytta upp en tydligare utvecklingsmiljö. Mer information finns i [NuGet-Paketåterställning ”](http://docs.nuget.org/consume/package-restore).
5. Lägg till en referens till **System.Configuration** sammansättning. Den här sammansättningen innehåller System.Configuration. **ConfigurationManager** klass som används för att komma åt konfigurationsfiler (till exempel App.config).
   
    1. Högerklicka på projektnamnet i Solution Explorer för att lägga till referenser genom att använda dialogrutan Hantera referenser. Klicka på **Lägg till**, klicka på **referens...** .
   
    2. Dialogrutan Hantera referenser visas.
    3. .NET framework-sammansättningar, leta upp och under väljer sammansättningen System.Configuration och trycker på **OK**.
6. Öppna filen App.config och Lägg till en **appSettings** avsnitt i filen.     
   
    Ange värden som behövs för att ansluta till Media Services API. Mer information finns i [åtkomst till Azure Media Services-API med Azure AD authentication](media-services-use-aad-auth-to-access-ams-api.md). 

    Om du använder [användarautentisering](media-services-use-aad-auth-to-access-ams-api.md#types-of-authentication) konfigurationsfilen har förmodligen värden för din Azure AD-klient domän och AMS REST API-slutpunkt.
    
    >[!Important]
    >De flesta kodexempel i dokumentationen för Azure Media Services inställd och använda en användare (interaktiva) typ av autentisering för att ansluta till AMS API: et. Den här autentiseringsmetoden fungerar bra för hantering och övervakning av interna appar: mobila appar, Windows-appar och konsolprogram. Den här autentiseringsmetoden är inte lämpligt för server, webbtjänster, API: er för typ av program.  Mer information finns i [åtkomst till AMS API med Azure AD authentication](media-services-use-aad-auth-to-access-ams-api.md).

        <configuration>
        ...
            <appSettings>
              <add key="AADTenantDomain" value="YourAADTenantDomain" />
              <add key="MediaServiceRESTAPIEndpoint" value="YourRESTAPIEndpoint" />
            </appSettings>

        </configuration>

7. Skriv över de befintliga **using**-satserna i början av filen Program.cs med följande kod.
           
        using System;
        using System.Configuration;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Collections.Generic;
        using System.Linq;

Du är nu redo att börja utveckla ett Media Services-program.    

## <a name="example"></a>Exempel

Här är en liten exempel som ansluter till AMS API: et och visar en lista över alla tillgängliga Media-processorer.
    
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];
    
        private static CloudMediaContext _context = null;
        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    
            // List all available Media Processors
            foreach (var mp in _context.MediaProcessors)
            {
                Console.WriteLine(mp.Name);
            }
    
        }

##<a name="next-steps"></a>Nästa steg

Nu [du kan ansluta till AMS API](media-services-use-aad-auth-to-access-ams-api.md) och starta [utveckla](media-services-dotnet-get-started.md).


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

