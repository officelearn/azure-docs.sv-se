---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 29b7893bbe813ece7cece9d1591dd574158f4482
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56409689"
---
## <a name="set-up-your-project"></a>Konfigurera projektet

I det här avsnittet skapar du ett nytt projekt för att demonstrera hur du integrerar ett Windows Desktop .NET-program (XAML) med *logga In med Microsoft* så att programmet kan fråga webb-API: er som kräver en token.

Det program som du skapar med den här guiden visar en knapp som används för att anropa en graf, ett område för att visa resultaten på skärmen och en knapp för utloggning.

> [!NOTE]
> Om du vill ladda ned det här exemplet Visual Studio-projekt i stället? [Ladda ned ett projekt](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip), och gå vidare till den [konfigurationssteget](#register-your-application) konfigurera kodexemplet innan du kör den.
>

Skapa ditt program genom att göra följande:

1. Välj **Arkiv** > **Nytt** > **Projekt** i Visual Studio.
2. Under **mallar**väljer **Visual C#**.
3. Välj **WPF-App (.NET Framework)**, beroende på vilken version av Visual Studio-version som du använder.

## <a name="add-msal-to-your-project"></a>Lägg till MSAL i projektet

1. I Visual Studio väljer du **Verktyg** > **NuGet Package Manager**> **Package Manager Console**.
2. Klistra in följande Azure PowerShell-kommando i fönstret Package Manager-konsolen:

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

    > [!NOTE] 
    > Det här kommandot installerar Microsoft Authentication Library. MSAL hanterar hämtar, cachelagring och uppdatering användartoken som används för att få åtkomst till API: er som skyddas av Azure Active Directory v2.0
    >

## <a name="add-the-code-to-initialize-msal"></a>Lägg till kod för att initiera MSAL

I det här steget skapar du en klass för att hantera interaktion med MSAL, till exempel hantering av token.

1. Öppna den *App.xaml.cs* filen och sedan lägga till referensen för MSAL i klassen:

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. Uppdatera app-klassen till följande:

    ```csharp
    public partial class App : Application
    {
        //Below is the clientId of your app registration. 
        //You have to replace the below with the Application Id for your app registration
        private static string ClientId = "your_client_id_here";

        public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

    }
    ```

## <a name="create-the-application-ui"></a>Skapa programmets användargränssnitt

Det här avsnittet visar hur ett program kan använda för att fråga en skyddad backend-server, till exempel Microsoft Graph. 

En *MainWindow.xaml* fil bör skapas automatiskt som en del av din projektmall. Öppna den här filen och Ersätt sedan ditt programs  *\<Grid >* noden med följande kod:

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
