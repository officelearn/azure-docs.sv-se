
## <a name="register-your-application"></a>Registrera ditt program
Du kan registrera ditt program på två sätt.

### <a name="option-1-express-mode"></a>Alternativ 1: Express-läge
Du kan snabbt registrera ditt program genom att göra följande:
1. Gå till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure).

2. Välj **Lägg till en app**.

3. I den **programnamn** ange ett namn för ditt program.

4. Se till att den **interaktiv installation** kryssrutan är markerad och välj sedan **skapa**.

5. Följ instruktionerna för att hämta program-ID och klistra in den i din kod.

### <a name="option-2-advanced-mode"></a>Alternativ 2: Avancerat läge
För att registrera ditt program och lägga till registreringsinformationen program i lösningen måste du göra följande:
1. Om du inte redan har registrerat ditt program, gå till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app).

2. Välj **Lägg till en app**.

3. I den **programnamn** ange ett namn för ditt program. 

4. Se till att den **interaktiv installation** kryssrutan är avmarkerad och välj sedan **skapa**.

5. Välj **lägga till plattformen**väljer **programspecifika**, och välj sedan **spara**.

6. I den **program-ID** rutan, kopiera GUID.

7. Gå till Visual Studio, öppna den *App.xaml.cs* filen och ersätter sedan `your_client_id_here` med program-ID som du just registrerade och kopieras.

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```
