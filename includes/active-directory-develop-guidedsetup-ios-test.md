## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Testa fråga Microsoft Graph API från iOS-program

Om du vill köra koden i simulatorn trycker du på **kommandot** + **R**.

![Testa programmet i simulatorn](media/active-directory-develop-guidedsetup-ios-test/iostestscreenshot.png)

När du är redo att testa väljer **anropa Microsoft Graph API**. När du uppmanas, ange ditt användarnamn och lösenord.

### <a name="provide-consent-for-application-access"></a>Ange tillstånd för åtkomst till program
Första gången du loggar in i tillämpningsprogrammet, uppmanas du att ge ditt medgivande för att tillåta åtkomst till din profil och logga in dig:

![Ge ditt medgivande för programåtkomst](media/active-directory-develop-guidedsetup-ios-test/iosconsentscreen.png)

### <a name="view-application-results"></a>Visa program resultat
När du loggar in kan du bör se din profilinformation för användaren som returneras av Microsoft Graph API-anrop i den **loggning** avsnitt. 

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API kräver den **user.read** omfång att läsa en användares profil. Detta scope läggs automatiskt som standard i alla program som har registrerats på portalen för registrering. Andra för Microsoft Graph-API: er, samt anpassade API: er för backend-servern, kan kräva ytterligare scope. Microsoft Graph API kräver den **Calendars.Read** scope för att visa användarens kalendrar.

För att få åtkomst till användarkalendrar i kontexten för ett program måste du lägga till den **Calendars.Read** delegerad behörighet att registreringsinformation för programmet. Lägg sedan till den **Calendars.Read** omfånget för den **acquireTokenSilent** anropa. 

>[!NOTE]
>Användaren kan uppmanas att ytterligare medgivanden när du ökar antalet scope.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
