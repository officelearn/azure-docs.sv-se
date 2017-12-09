
## <a name="create-an-application-express"></a>Skapa ett program (snabb)
Nu måste du registrera ditt program i den *Microsoft Programregistreringsportalen*:
1. Registrera ditt program via den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure)
2.  Ange ett namn för ditt program och din e-post
3.  Kontrollera att alternativet för interaktiv installation är markerat
4.  Följ instruktionerna för att hämta program-ID och klistra in den i din kod

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Lägga till registreringsinformationen program i lösningen (Avancerat)
Nu måste du registrera ditt program i den *Microsoft Programregistreringsportalen*:
1. Gå till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app) registrera ett program
2. Ange ett namn för ditt program och din e-post 
3. Kontrollera att alternativet för interaktiv installation är markerat
4. Klicka på `Add Platform`och välj `Native Application` och tryck på Spara
5. Kopiera GUID i program-ID, gå tillbaka till Visual Studio, öppna `App.xaml.cs` och Ersätt `your_client_id_here` med program-ID som du just har registrerat:

```csharp
private static string ClientId = "your_application_id_here";
```
