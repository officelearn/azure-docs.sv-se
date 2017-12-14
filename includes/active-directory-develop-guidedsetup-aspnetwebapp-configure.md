
## <a name="create-an-application-express"></a>Skapa ett program (snabb)
Nu måste du registrera ditt program i den *Microsoft Programregistreringsportalen*:
1. Registrera ditt program via den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)
2.  Ange ett namn för ditt program och din e-post
3.  Kontrollera att alternativet för interaktiv installation är markerat
4.  Följ instruktionerna för att lägga till en omdirigerings-URL för ditt program

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Lägga till registreringsinformationen program i lösningen (Avancerat)
Nu måste du registrera ditt program i den *Microsoft Programregistreringsportalen*:
1. Gå till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app) registrera ett program
2. Ange ett namn för ditt program och din e-post 
3.  Kontrollera att alternativet för interaktiv installation är markerat
4.  Klicka på `Add Platform`och välj`Web`
5.  Gå tillbaka till Visual Studio och i Solution Explorer, välj projektet och titta på fönstret Egenskaper (om du inte ser en egenskapsfönstret trycker du på F4)
6.  Ändra SSL aktiverat för`True`
7.  Kopiera den URL som SSL och lägga till denna URL i listan över omdirigerings-URL: er i portalen för registrering omdirigerings-URL-listan:<br/><br/>![Egenskaper för](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
8.  Lägg till följande i `web.config` finns i rotmappen under avsnittet `configuration\appSettings`:

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
<!-- Workaround for Docs conversion bug -->
<ol start="9">
<li>
Ersätt `ClientId` med program-Id som du precis har registrerats
</li>
<li>
Ersätt `redirectUri` med SSL-URL för ditt projekt
</li>
</ol>
<!-- End Docs -->
