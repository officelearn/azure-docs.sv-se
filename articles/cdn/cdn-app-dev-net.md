---
title: Komma igång med Azure CDN-biblioteket för .NET | Microsoft-dokument
description: Lär dig hur du skriver .NET-program för att hantera Azure CDN med Visual Studio.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 7e3ad3a5928b36c221bb83b1c4012c3c9e14f35d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594168"
---
# <a name="get-started-with-azure-cdn-development"></a>Kom igång med Azure CDN-utveckling
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Du kan använda [Azure CDN-biblioteket för .NET för](/dotnet/api/overview/azure/cdn) att automatisera skapandet och hanteringen av CDN-profiler och slutpunkter.  Den här självstudien går igenom skapandet av ett enkelt .NET-konsolprogram som visar flera av de tillgängliga åtgärderna.  Den här självstudien är inte avsedd att beskriva alla aspekter av Azure CDN-biblioteket för .NET i detalj.

Du behöver Visual Studio 2015 för att slutföra den här självstudien.  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) är fritt tillgänglig för nedladdning.

> [!TIP]
> Det [slutförda projektet från den här självstudien](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) är tillgängligt för nedladdning på MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Skapa ditt projekt och lägg till Nuget-paket
Nu när vi har skapat en resursgrupp för våra CDN-profiler och gett vår Azure AD-programbehörighet för att hantera CDN-profiler och slutpunkter i den gruppen, kan vi börja skapa vårt program.

I Visual Studio 2015 klickar du på **Arkiv**, **Nytt**, **Projekt...** för att öppna den nya projektdialogrutan.  Expandera **Visual C#** och välj sedan **Windows** i fönstret till vänster.  Klicka på **Konsolprogram** i mittfönstret.  Namnge projektet och klicka sedan på **OK**.  

![Nytt projekt](./media/cdn-app-dev-net/cdn-new-project.png)

Vårt projekt kommer att använda vissa Azure-bibliotek som finns i Nuget-paket.  Låt oss lägga till dem i projektet.

1. Klicka på **Verktyg-menyn,** **Nuget Package Manager**och sedan **Package Manager Console**.
   
    ![Hantera Nuget-paket](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. Kör följande kommando i Package Manager Console för att installera **Active Directory Authentication Library (ADAL):**
   
    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Kör följande för att installera **Azure CDN Management Library:**
   
    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Direktiv, konstanter, huvudmetod och hjälpmetoder
Låt oss få den grundläggande strukturen i vårt program skrivet.

1. Tillbaka på fliken Program.cs ersätter `using` du direktiven högst upp med följande:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
2. Vi måste definiera några konstanter våra metoder kommer att använda.  Lägg `Program` till följande i `Main` klassen, men före metoden.  Var noga med att ersätta platshållarna, inklusive ** &lt;vinkelparenteserna,&gt;** med dina egna värden efter behov.
   
    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";
   
    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Också på klassnivå definierar du dessa två variabler.  Vi använder dessa senare för att avgöra om vår profil och slutpunkt redan finns.
   
    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Ersätt `Main` metoden enligt följande:
   
   ```csharp
   static void Main(string[] args)
   {
       //Get a token
       AuthenticationResult authResult = GetAccessToken();
   
       // Create CDN client
       CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
           { SubscriptionId = subscriptionId };
   
       ListProfilesAndEndpoints(cdn);
   
       // Create CDN Profile
       CreateCdnProfile(cdn);
   
       // Create CDN Endpoint
       CreateCdnEndpoint(cdn);
   
       Console.WriteLine();
   
       // Purge CDN Endpoint
       PromptPurgeCdnEndpoint(cdn);
   
       // Delete CDN Endpoint
       PromptDeleteCdnEndpoint(cdn);
   
       // Delete CDN Profile
       PromptDeleteCdnProfile(cdn);
   
       Console.WriteLine("Press Enter to end program.");
       Console.ReadLine();
   }
   ```
5. Några av våra andra metoder kommer att uppmana användaren med "Ja / Nej" frågor.  Lägg till följande metod för att göra det lite enklare:
   
    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

Nu när den grundläggande strukturen i vårt program är skriven, bör vi skapa de metoder som kallas med `Main` metoden.

## <a name="authentication"></a>Autentisering
Innan vi kan använda Azure CDN Management Library måste vi autentisera vårt tjänsthuvudnamn och hämta en autentiseringstoken.  Den här metoden använder ADAL för att hämta token.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority); 
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult = 
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

Om du använder individuell användarautentisering ser `GetAccessToken` metoden lite annorlunda ut.

> [!IMPORTANT]
> Använd bara det här kodexemplet om du väljer att ha individuell användarautentisering i stället för ett huvudnamn för tjänsten.
> 
> 

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

Var noga `<redirect URI>` med att ersätta med den omdirigera URI du angav när du registrerade programmet i Azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>Lista CDN-profiler och slutpunkter
Nu är vi redo att utföra CDN-åtgärder.  Det första vår metod gör är att lista alla profiler och slutpunkter i vår resursgrupp, och om den hittar en matchning för profil- och slutpunktsnamn som anges i våra konstanter, noterar det för senare så att vi inte försöker skapa dubbletter.

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Skapa CDN-profiler och slutpunkter
Därefter skapar vi en profil.

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

När profilen har skapats skapar vi en slutpunkt.

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

> [!NOTE]
> Exemplet ovan tilldelar slutpunkten ett ursprung med namnet `www.contoso.com` *Contoso* med ett värdnamn .  Du bör ändra detta så att det pekar på ditt eget ursprungs värdnamn.
> 
> 

## <a name="purge-an-endpoint"></a>Rensa en slutpunkt
Förutsatt att slutpunkten har skapats, en gemensam uppgift som vi kanske vill utföra i vårt program är att rensa innehållet i vår slutpunkt.

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

> [!NOTE]
> I exemplet ovan anger `/*` strängen att jag vill rensa allt i roten på slutpunktssökvägen.  Detta motsvarar att kontrollera **Rensa alla** i Azure-portalens "purge"-dialog. I `CreateCdnProfile` metoden skapade jag vår profil som en **Azure CDN från Verizon-profil** med koden `Sku = new Sku(SkuName.StandardVerizon)`, så detta kommer att lyckas.  Azure **CDN från Akamai-profiler** stöder dock inte **Rensa alla**, så om jag använde en Akamai-profil för den här självstudien skulle jag behöva inkludera specifika sökvägar för att rensa.
> 
> 

## <a name="delete-cdn-profiles-and-endpoints"></a>Ta bort CDN-profiler och slutpunkter
De sista metoderna kommer att ta bort vår slutpunkt och profil.

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>Köra programmet
Vi kan nu sammanställa och köra programmet genom att klicka på **Start-knappen** i Visual Studio.

![Program igång](./media/cdn-app-dev-net/cdn-program-running-1.png)

När programmet når ovanstående fråga bör du kunna återgå till din resursgrupp i Azure-portalen och se att profilen har skapats.

![Lyckades!](./media/cdn-app-dev-net/cdn-success.png)

Vi kan sedan bekräfta uppmaningarna att köra resten av programmet.

![Programmet slutförs](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Efterföljande moment
Om du vill se det slutförda projektet från den här genomgången [hämtar du exemplet](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Om du vill hitta ytterligare dokumentation om Azure CDN Management Library för .NET kan du visa [referensen på MSDN](/dotnet/api/overview/azure/cdn).

Hantera CDN-resurser med [PowerShell](cdn-manage-powershell.md).

