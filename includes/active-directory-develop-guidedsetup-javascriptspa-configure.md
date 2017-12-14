
## <a name="register-your-application"></a>Registrera ditt program

Det finns flera sätt att skapa ett program, Välj en av dem:

### <a name="option-1-register-your-application-express-mode"></a>Alternativ 1: Registrera ditt program (Express-läge)
Nu måste du registrera ditt program i den *Microsoft Programregistreringsportalen*:

1.  Registrera ditt program via den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)
2.  Ange ett namn för ditt program och din e-post
3.  Kontrollera att alternativet *interaktiv installation* kontrolleras
4.  Följ instruktionerna för att hämta program-ID och klistra in den i din kod

### <a name="option-2-register-your-application-advanced-mode"></a>Alternativ 2: Registrera ditt program (Avancerat läge)

1. Gå till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app) registrera ett program
2. Ange ett namn för ditt program och din e-post 
3. Kontrollera att alternativet *interaktiv installation* är avmarkerat
4.  Klicka på `Add Platform`och välj`Web`
5. Lägg till den `Redirect URL` som motsvarar programmets URL baserat på din webbserver. I avsnitten nedan för instruktioner om hur du ange / hämta omdirigerings-URL i Visual Studio och Python.
6. Klicka på *spara*

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Visual Studio-instruktionerna för att skaffa omdirigerings-URL
> Följ instruktionerna för att hämta omdirigerings-URL:
> 1.    I *Solution Explorer*projektet och välj titta på den `Properties` fönstret (om du inte ser en egenskapsfönstret trycker du på `F4`)
> 2.    Kopiera värdet från `URL` till Urklipp:<br/> ![Egenskaper för](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Växla tillbaka till den *Programregistreringsportalen* och klistra in värdet som en `Redirect URL` och klicka på ”Spara”

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Inställningen omdirigerings-URL för Python
> För Python, kan du ange webben serverport via kommandoraden. Den interaktiva installationen använder port 8080 för referens men kan använda någon annan port som är tillgängliga. I varje fall följer du anvisningarna nedan för att ställa in en omdirigerings-URL i registreringsinformation program:<br/>
> - Växla tillbaka till den *Programregistreringsportalen* och ange `http://localhost:8080/` som en `Redirect URL`, eller Använd `http://localhost:[port]/` om du använder en anpassad TCP-port (där *[port]* är anpassade TCP-port antalet) och klicka på ”Spara”


#### <a name="configure-your-javascript-spa"></a>Konfigurera din JavaScript SPA

1.  Skapa en fil med namnet `msalconfig.js` som innehåller programmet registreringsinformation. Om du använder Visual Studio, markera projekt (rotmapp projekt), högerklicka och välj: `Add`  >  `New Item`  >  `JavaScript File`. Ge den namnet`msalconfig.js`
2.  Lägg till följande kod i din `msalconfig.js` fil:

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
Ersätt <code>Enter_the_Application_Id_here</code> med program-Id som du precis har registrerats
</li>
</ol>
