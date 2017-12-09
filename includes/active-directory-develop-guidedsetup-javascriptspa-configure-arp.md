
## <a name="add-the-applications-registration-information-to-your-app"></a>Lägg till programmets registreringsinformation i appen

I det här steget måste du konfigurera omdirigerings-URL för din registreringsinformation för programmet och sedan lägga till det program-Id i JavaScript SPA-programmet.

### <a name="configure-redirect-url"></a>Konfigurera omdirigerings-URL

Konfigurera den `Redirect URL` fältet ovan med URL-Adressen för index.html sidan baserat på din webbserver och klicka sedan på *uppdatering*.


> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Visual Studio-instruktionerna för att skaffa omdirigerings-URL
> Följ anvisningarna nedan om du vill hämta omdirigerings-URL:
> 1.    I *Solution Explorer*projektet och välj titta på den `Properties` fönstret (om du inte ser en egenskapsfönstret trycker du på `F4`)
> 2.    Kopiera värdet från `URL` till Urklipp:<br/> ![Egenskaper för](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Klistra in värdet som en `Redirect URL` överst i den här sidan, klicka på`Update`

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Inställningen omdirigerings-URL för Python
> För Python, kan du ange webben serverport via kommandoraden. Den interaktiva installationen använder port 8080 för referens men kan använda någon annan port som är tillgängliga. I varje fall följer du anvisningarna nedan för att ställa in en omdirigerings-URL i registreringsinformation program:<br/>
> Ange `http://localhost:8080/` som en `Redirect URL` ovanpå den här sidan, eller Använd `http://localhost:[port]/` om du använder en anpassad TCP-port (där *[port]* är anpassade TCP-portnummer), och klicka sedan på ”Uppdatera”

### <a name="configure-your-javascript-spa-application"></a>Konfigurera JavaScript SPA-program

1.  Skapa en fil med namnet `msalconfig.js` som innehåller programmet registreringsinformation. Om du använder Visual Studio, markera projekt (rotmapp projekt), högerklicka och välj: `Add`  >  `New Item`  >  `JavaScript File`. Ge den namnet`msalconfig.js`
2.  Lägg till följande kod i din `msalconfig.js` fil:

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
