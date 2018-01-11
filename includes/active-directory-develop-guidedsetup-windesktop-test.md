## <a name="test-your-code"></a>Testa din kod

För att köra ditt projekt i Visual Studio, markera **F5**. Programmet **MainWindow** visas som visas här:

![Testa programmet](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Första gången du kör programmet och välj den **anropa Microsoft Graph API** knappen uppmanas du för att logga in. Använda en Azure Active Directory-konto (arbets- eller skolkonto konto) eller ett Microsoft-konto (live.com, outlook.com) för att testa den.

![Logga in till programmet](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Ange tillstånd för åtkomst till program
Första gången du loggar in till ditt program också uppmanas du att ange samtycke till att programmet ska kunna komma åt din profil och logga in dig i som visas här: 

![Ge ditt medgivande för programåtkomst](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Visa program resultat
Du bör se profil användarinformation som returneras av anropet till Microsoft Graph API när du loggar in. Resultatet visas i den **API-anrop resultat** rutan. Grundläggande information om den token som erhölls via anropet till `AcquireTokenAsync` eller `AcquireTokenSilentAsync` ska visas i den **Token Info** rutan. Det resulterar i följande egenskaper:

|Egenskap  |Format  |Beskrivning |
|---------|---------|---------|
|**Namn** |Användarens fullständiga namn |Användaren förnamn och efternamn.|
|**Användarnamn** |<span>user@domain.com</span> |Användarnamnet som används för att identifiera användaren.|
|**Token upphör att gälla** |DateTime |Tiden då token upphör att gälla. MSAL utökar utgångsdatumet genom att förnya token efter behov.|
|**Åtkomst-Token** |Sträng |Tokensträng som skickas till HTTP-begäranden som kräver en *Authorization-huvud*.|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API kräver den *user.read* omfång att läsa en användares profil. Detta scope läggs automatiskt som standard i alla program som har registrerats i portalen för registrering av programmet. Andra för Microsoft Graph-API: er, samt anpassade API: er för backend-servern, kan kräva ytterligare scope. Microsoft Graph API kräver den *Calendars.Read* scope för att visa användarens kalendrar.

För att få åtkomst till användarkalendrar i kontexten för ett program måste du lägga till den *Calendars.Read* delegerad behörighet att registreringsinformation för programmet. Lägg sedan till den *Calendars.Read* omfånget för den `acquireTokenSilent` anropa. 

>[!NOTE]
>Användaren kan uppmanas att ytterligare medgivanden när du ökar antalet scope.

<!--end-collapse-->

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
