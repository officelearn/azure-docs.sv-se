## <a name="test-your-code"></a>Testa din kod

### <a name="test-with-visual-studio"></a>Testa med Visual Studio
Om du använder Visual Studio trycker du på **F5** köra projektet. I webbläsaren öppnas http://<span></span>localhost: {port} plats och du ser den **anropa Microsoft Graph API** knappen.

<p/><!-- --> 

### <a name="test-with-python-or-other-web-server"></a>Testa med Python eller annan webbserver
Om du inte använder Visual Studio, kontrollera din webbserver är igång. Konfigurera servern att lyssna på TCP-port som baseras på platsen för din **index.html** fil. För Python, starta att lyssna på porten genom att köra kommandotolken terminal från mappen för program:
 
```bash
python -m http.server 8080
```
Öppna webbläsaren och skriv http://<span></span>localhost:8080 eller http://<span></span>localhost: {port} där **port** är den port som servern lyssnar på. Du bör se innehållet i filen index.html och **anropa Microsoft Graph API** knappen.

## <a name="test-your-application"></a>Testa programmet

När webbläsaren läser index.html-filen, Välj **anropa Microsoft Graph API**. Första gången du kör ditt program i webbläsaren omdirigeras till Microsoft Azure Active Directory (AD Azure) v2.0-slutpunkten och du uppmanas att logga in:
 
![Logga in på JavaScript SPA-konto](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>Ange tillstånd för åtkomst till program

Första gången du loggar in i tillämpningsprogrammet, uppmanas du att ge ditt medgivande för att tillåta åtkomst till din profil och logga in dig:

![Ge ditt medgivande för programåtkomst](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Visa program resultat
När du loggar in kan du bör se din profilinformation för användare i den **Graph API-anrop svar** rutan.
 
![Förväntat resultat från Microsoft Graph API-anrop](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

Du bör också se grundläggande information om den token som erhölls i den **åtkomst-Token** och **ID Token anspråk** rutor.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API kräver den **user.read** omfång att läsa en användares profil. Detta scope läggs automatiskt som standard i alla program som har registrerats på portalen för registrering. Andra för Microsoft Graph-API: er, samt anpassade API: er för backend-servern, kan kräva ytterligare scope. Microsoft Graph API kräver den **Calendars.Read** scope för att visa användarens kalendrar.

För att få åtkomst till användarkalendrar i kontexten för ett program måste du lägga till den **Calendars.Read** delegerad behörighet att registreringsinformation för programmet. Lägg sedan till den **Calendars.Read** omfånget för den **acquireTokenSilent** anropa. 

>[!NOTE]
>Användaren kan uppmanas att ytterligare medgivanden när du ökar antalet scope.

Om en backend-API: N inte kräver ett omfång (rekommenderas inte), kan du använda den **clientId** som scope i den **acquireTokenSilent** och **acquireTokenRedirect** anrop.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
