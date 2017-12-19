---
title: "Kom igång med Azure CDN-biblioteket för .NET | Microsoft Docs"
description: "Lär dig hur du skriver .NET-program för att hantera Azure CDN med Visual Studio."
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5379586355ece98af6295236d6cbd09cb31c742b
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-with-azure-cdn-development"></a>Kom igång med Azure CDN-utveckling
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Du kan använda den [Azure CDN-biblioteket för .NET](https://msdn.microsoft.com/library/mt657769.aspx) att automatisera skapande och hantering av CDN-profiler och slutpunkter.  Den här vägledningen visar genom att skapa en enkel .NET-konsolprogram som visar flera av de tillgängliga åtgärderna.  Den här kursen är inte avsedd att beskriva alla aspekter av Azure CDN-biblioteket för .NET i detalj.

Du måste Visual Studio 2015 att slutföra den här kursen.  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) är gratis.

> [!TIP]
> Den [slutförda projekt från den här självstudiekursen](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) är tillgänglig för hämtning på MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Skapa projektet och Lägg till Nuget-paket
Nu när vi har skapat en resursgrupp för våra CDN-profiler och våra Azure AD-programmet behörighet att hantera CDN profiler och slutpunkter inom den gruppen, kan vi börja skapa vårt program.

Klicka på i Visual Studio 2015 **filen**, **ny**, **projekt...**  att öppna dialogrutan Nytt projekt.  Expandera **Visual C#**och välj **Windows** i rutan till vänster.  Klicka på **konsolprogram** i mitten av fönstret.  Namnge projektet och klicka sedan på **OK**.  

![Nytt projekt](./media/cdn-app-dev-net/cdn-new-project.png)

Projektet kommer att använda vissa Azure bibliotek i Nuget-paket.  Lägg till dem i projektet.

1. Klicka på den **verktyg** menyn **Nuget Package Manager**, sedan **Pakethanterarkonsolen**.
   
    ![Hantera Nuget-paket](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. I Package Manager-konsolen kör du följande kommando för att installera den **Active Directory Authentication Library (ADAL)**:
   
    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Kör följande för att installera den **Azure CDN bibliotek**:
   
    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Direktiven, konstanter, main-metoden och hjälpmetoder
Det är dags grundstrukturen för vårt program som har skrivits.

1. Fliken Program.cs i ersätter den `using` direktiven överst med följande:
   
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
2. Vi behöver definiera vissa konstanter våra metoder ska använda.  I den `Program` class, men innan den `Main` metoden Lägg till följande.  Se till att ersätta platshållare, inklusive den  **&lt;hakparenteser&gt;**, med egna värden efter behov.
   
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
3. Också på klassnivå, definiera dessa två variabler.  Vi använder dessa senare för att avgöra om våra profil och en slutpunkt redan finns.
   
    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Ersätt den `Main` metoden på följande sätt:
   
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
5. Några av våra andra metoder kommer att uppmana användaren med frågor som ”Ja/Nej”.  Lägg till följande metod för att se att lite enklare:
   
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

Nu när den grundläggande strukturen i vårt program skrivs ska vi skapa metoderna anropas av den `Main` metoden.

## <a name="authentication"></a>Autentisering
Innan vi kan använda Azure CDN-hanteringsmodul behöver vi verifiera vår huvudnamn för tjänsten och hämta någon autentiseringstoken.  Den här metoden använder ADAL för att hämta token.

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

Om du använder autentisering av enskilda användare, den `GetAccessToken` metoden ser lite annorlunda ut.

> [!IMPORTANT]
> Använd bara det här kodexemplet om du väljer att ha enskilda användarautentisering i stället för en tjänstens huvudnamn.
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

Se till att ersätta `<redirect URI>` med omdirigerings-URI som du angav när du registrerade programmet i Azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>Lista CDN profiler och slutpunkter
Vi är nu redo att utföra åtgärder för CDN.  Det första våra webbmetoden är lista alla profiler och slutpunkter i vår resursgruppen och om den hittar en matchning för namnen på profil och slutpunkt har angetts i vår konstanter gör du anteckna som för senare så att vi inte att skapa dubbletter.

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

När profilen har skapats, skapar vi en slutpunkt.

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
> Exemplet ovan tilldelar slutpunkten ursprung med namnet *Contoso* med ett värdnamn `www.contoso.com`.  Du bör ändra så att den pekar till dina egna ursprungsvärdnamn.
> 
> 

## <a name="purge-an-endpoint"></a>Rensa en slutpunkt
Under förutsättning att slutpunkten har skapats, en gemensam uppgift som vi kan utföra i vårt program rensa innehållet i vår slutpunkt.

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
> I exemplet ovan, strängen `/*` anger att jag vill rensa allt i roten av sökväg för slutpunkt.  Detta motsvarar kontrollerar **Rensa alla** i Azure portal ”Rensa” dialogrutan. I den `CreateCdnProfile` -metoden I vår profil skapats som en **Azure CDN från Verizon** profilen genom att använda koden `Sku = new Sku(SkuName.StandardVerizon)`, så detta kommer att lyckas.  Dock **Azure CDN från Akamai** profiler har inte stöd för **Rensa alla**, så om jag använde en Akamai profil för den här självstudiekursen jag måste du inkludera specifika sökvägar för att rensa.
> 
> 

## <a name="delete-cdn-profiles-and-endpoints"></a>Ta bort CDN-profiler och slutpunkter
Senaste metoderna tar bort våra slutpunkt och profil.

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

## <a name="running-the-program"></a>Programmet körs
Vi kan nu kompilera och köra programmet genom att klicka på den **starta** knappen i Visual Studio.

![Program som körs](./media/cdn-app-dev-net/cdn-program-running-1.png)

När programmet har nått ovan uppmaningen, bör du kunna återgå till din resursgrupp i Azure portal och se att profilen har skapats.

![Klar](./media/cdn-app-dev-net/cdn-success.png)

Vi kan sedan bekräfta anvisningarna för att köra resten av programmet.

![Programmet Slutför](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Nästa steg
Se slutförda projektet från den här genomgången [hämta exempelfilerna](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Ytterligare dokumentation om Azure CDN Management biblioteket för .NET visar den [reference på MSDN](https://msdn.microsoft.com/library/mt657769.aspx).

Hantera dina CDN-resurser med [PowerShell](cdn-manage-powershell.md).

