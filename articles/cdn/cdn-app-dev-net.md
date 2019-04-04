---
title: Komma igång med Azure CDN-biblioteket för .NET | Microsoft Docs
description: Lär dig hur du skriver .NET-program för att hantera Azure CDN via Visual Studio.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 838c76e6a383b61ff465f3ed7506af34c8cd01d4
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916622"
---
# <a name="get-started-with-azure-cdn-development"></a>Kom igång med Azure CDN-utveckling
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Du kan använda den [Azure CDN-biblioteket för .NET](/dotnet/api/overview/azure/cdn) att automatisera skapandet och hanteringen av CDN-profiler och slutpunkter.  Den här självstudiekursen beskriver skapandet av en enkel .NET-konsolprogram som visar flera av de tillgängliga åtgärderna.  Den här självstudien är inte avsedd att beskriva alla aspekter av Azure CDN-biblioteket för .NET i detalj.

Du behöver Visual Studio 2015 att slutföra den här självstudien.  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) är tillgängligt för hämtning.

> [!TIP]
> Den [slutförda projekt från den här självstudien](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) är tillgänglig för hämtning på MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Skapa projektet och Lägg till Nuget-paket
Nu när vi har skapat en resursgrupp för vår CDN-profiler och våra Azure AD-programmet behörighet att hantera CDN-profiler och slutpunkter inom gruppen, kan vi börja skapa vårt program.

Klicka på i Visual Studio 2015 **filen**, **New**, **projekt...**  att öppna dialogrutan för nytt projekt.  Expandera **Visual C#** och välj sedan **Windows** i fönstret till vänster.  Klicka på **konsolprogram** i den mittersta rutan.  Namnge projektet och klicka sedan på **OK**.  

![Nytt projekt](./media/cdn-app-dev-net/cdn-new-project.png)

Våra projekt kommer att använda vissa Azure-bibliotek i Nuget-paket.  Vi vill lägga till dem i projektet.

1. Klicka på den **verktyg** menyn **Nuget-Pakethanteraren**, sedan **Pakethanterarkonsolen**.
   
    ![Hantera Nuget-paket](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. I Package Manager-konsolen kör du följande kommando för att installera den **Active Directory Authentication Library (ADAL)**:
   
    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Kör följande för att installera den **Azure CDN Management Library**:
   
    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Direktiv, konstanter, main-metoden och hjälpmetoder
Nu ska vi hämta grundstrukturen för vårt program som skrivits.

1. Tillbaka i Program.cs-fliken ersätter den `using` direktiv överst med följande:
   
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
2. Vi måste definiera vissa konstanter våra metoder används.  I den `Program` class, men innan den `Main` metoden lägger du till följande.  Se till att ersätta platshållarna, inklusive den  **&lt;vinkelparenteser&gt;**, med dina egna värden efter behov.
   
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
3. Också definiera dessa två variabler på klassnivå.  Vi använder dessa senare för att avgöra om våra profil och slutpunkt redan finns.
   
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
5. Några av våra andra metoder kommer att uppmana användaren att ”Ja/Nej” några frågor.  Lägg till följande metod för att förenkla lite:
   
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

Nu när grundstrukturen för vårt program skrivs vi ska skapa de metoder som anropas av den `Main` metoden.

## <a name="authentication"></a>Authentication
Innan vi kan använda Azure CDN Management-biblioteket, måste vi att autentisera vår tjänstens huvudnamn och få en autentiseringstoken.  Den här metoden använder ADAL för att hämta token.

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

Om du använder autentisering av enskilda användare, den `GetAccessToken` metoden ser annorlunda ut.

> [!IMPORTANT]
> Använd bara det här kodexemplet om du väljer att använda enskilda användarautentisering i stället för ett huvudnamn för tjänsten.
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

## <a name="list-cdn-profiles-and-endpoints"></a>Lista CDN-profiler och slutpunkter
Nu är vi redo att utföra åtgärder för CDN.  Det första vår webbmetoden är lista alla profiler och slutpunkter i resursgrupp och om den hittar en matchning för namnen på profil och slutpunkt har angetts i vår konstanter, gör du ned som för senare så att vi inte försöker skapa dubbletter.

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
Nu ska skapa vi en profil.

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

När profilen har skapats kan skapa vi en slutpunkt.

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
> I exemplet ovan tilldelar slutpunkten ett ursprung med namnet *Contoso* med ett värdnamn som `www.contoso.com`.  Du bör ändra detta så att den pekar till dina egna ursprungets värdnamn.
> 
> 

## <a name="purge-an-endpoint"></a>Rensa en slutpunkt
Anta att slutpunkten har skapats och en gemensam uppgift som vi kan utföra i vårt program Rensa innehåll i vår slutpunkt.

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
> I exemplet ovan strängen `/*` anger att jag vill rensa allt innehåll i roten till sökvägen för slutpunkten.  Detta motsvarar kontrollerar **Rensa alla** i Azure portal ”Rensa” dialogrutan. I den `CreateCdnProfile` metod, jag har skapat vårt profil som en **Azure CDN från Verizon** profiler med koden `Sku = new Sku(SkuName.StandardVerizon)`, så att det kommer att lyckas.  Dock **Azure CDN från Akamai** profiler stöder inte **Rensa alla**, så om jag har använt en Akamai-profil för den här självstudiekursen, skulle jag behöver inkludera specifika sökvägar ska rensas.
> 
> 

## <a name="delete-cdn-profiles-and-endpoints"></a>Ta bort CDN-profiler och slutpunkter
De senaste metoderna tar bort vår slutpunkt och profil.

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

## <a name="running-the-program"></a>Kör programmet
Vi kan nu kompilera och kör programmet genom att klicka på den **starta** knappen i Visual Studio.

![Program som körs](./media/cdn-app-dev-net/cdn-program-running-1.png)

När programmet har nått ovanstående fråga, bör du kunna gå tillbaka till din resursgrupp i Azure portal och se att profilen har skapats.

![Lyckades!](./media/cdn-app-dev-net/cdn-success.png)

Vi kan sedan bekräfta anvisningarna för att köra resten av programmet.

![Programmet har slutfört](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Nästa steg
Se de slutförda projektet från den här genomgången [hämta exemplet](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Mer dokumentation om Azure CDN Management-biblioteket för .NET visar den [reference på MSDN](/dotnet/api/overview/azure/cdn).

Hantera dina CDN-resurser med [PowerShell](cdn-manage-powershell.md).

