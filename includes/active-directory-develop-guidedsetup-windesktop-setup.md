---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: d407b015699925a6a3ce6ef9108ace1e9900303f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
## <a name="set-up-your-project"></a>Konfigurera ditt projekt

I det här avsnittet skapar du ett nytt projekt för att demonstrera hur du integrerar Windows Desktop .NET-program (XAML) med *logga In med Microsoft* så att programmet kan fråga webb-API: er som kräver en token.

Det program som du skapar med den här guiden visar en knapp som används för att anropa ett diagram, ett område för att visa resultat på skärmen och en knapp för utloggning.

> [!NOTE]
> Om du vill hämta det här exemplet Visual Studio-projekt i stället? [Hämta ett projekt](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip), och gå vidare till den [konfigurationssteget](#register-your-application) konfigurera kodexemplet innan du kan köra den.
>

För att skapa ditt program, gör du följande:
1. I Visual Studio väljer **filen** > **ny** > **projekt**.
2. Under **mallar**väljer **Visual C#**.
3. Välj **WPF-program** eller **WPF-program**, beroende på vilken version av Visual Studio-version du använder.

## <a name="add-msal-to-your-project"></a>Lägg till MSAL i projektet
1. I Visual Studio väljer **verktyg** > **NuGet Package Manager**> **Pakethanterarkonsolen**.
2. Klistra in följande Azure PowerShell-kommando i fönstret Package Manager-konsolen:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Det här kommandot installerar Microsoft Authentication Library. MSAL hanterar hämtning av cachelagring och uppdatera användartoken som används för åtkomst till API: er som skyddas av Azure Active Directory v2.
    >

## <a name="add-the-code-to-initialize-msal"></a>Lägg till kod för att initiera MSAL
I det här steget skapar du en klass för att hantera interaktion med MSAL, till exempel hantering av token.

1. Öppna den *App.xaml.cs* filen och sedan lägga till en referens för MSAL i klassen:

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. Uppdatera app-klass för följande:

    ```csharp
    public partial class App : Application
    {
        //Below is the clientId of your app registration. 
        //You have to replace the below with the Application Id for your app registration
        private static string ClientId = "your_client_id_here";

        public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

    }
    ```

## <a name="create-the-application-ui"></a>Skapa UI-programmet

Det här avsnittet visar hur ett program kan fråga en skyddad backend-server, till exempel Microsoft Graph. 

En *MainWindow.xaml* fil automatiskt ska skapas som en del av projektmallen för. Öppna den här filen och Ersätt ditt programs  *\<rutnät >* nod med följande kod:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

