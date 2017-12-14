
## <a name="set-up-your-project"></a>Konfigurera ditt projekt

Det här avsnittet innehåller stegvisa instruktioner för hur du skapar ett nytt projekt för att demonstrera hur du integrerar Windows Desktop .NET-program (XAML) med *logga In med Microsoft* så att den kan fråga Web API: er som kräver ett token.

Program som skapats av den här guiden visar en knapp för att ett diagram och visa resultaten på skärmen och en knapp för utloggning.

> Om du vill hämta det här exemplet Visual Studio-projekt i stället? [Hämta ett projekt](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip) och gå vidare till den [konfigurationssteget](#create-an-application-express) konfigurera kodexemplet innan du kör.


### <a name="create-your-application"></a>Skapa programmet
1. I Visual Studio:`File` > `New` > `Project`<br/>
2. Under *mallar*väljer`Visual C#`
3. Välj `WPF App` (eller *WPF-program* beroende på vilken version av din Visual Studio)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>Lägg till Microsoft Authentication Library (MSAL) i projektet
1. I Visual Studio:`Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Kopiera och klistra in följande i fönstret Package Manager-konsolen:

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

> Ovanstående paketet installerar Microsoft Authentication Library (MSAL). MSAL hanterar införskaffa, cachelagring och uppdatera användaren toskens som används för åtkomst till API: er som skyddas av Azure Active Directory v2.

## <a name="add-the-code-to-initialize-msal"></a>Lägg till kod för att initiera MSAL
Det här steget hjälper dig att skapa en klass för att hantera interaktion med MSAL bibliotek, t.ex hantering av token.

1. Öppna den `App.xaml.cs` filen och Lägg till referens för MSAL bibliotek i klassen:

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Uppdatera App-klass för följande:
</li>
</ol>

```csharp
public partial class App : Application
{
    //Below is the clientId of your app registration. 
    //You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";

    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

}
```

## <a name="create-your-applications-ui"></a>Skapa ditt programs gränssnitt
Avsnittet nedan visar hur ett program kan fråga skyddade backend-servern som Microsoft Graph. En MainWindow.xaml-fil ska skapas automatiskt som en del av projektmallen för. Öppna filen för den här filen och följ sedan instruktionerna nedan:

Ersätt ditt programs `<Grid>` med vara följande:

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
