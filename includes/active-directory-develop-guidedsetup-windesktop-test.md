
## <a name="test-your-code"></a>Testa din kod

Om du vill testa ditt program i Visual Studio trycker du på **F5** köra projektet. Den **MainWindow** för ditt program visas:

![Testa programmet](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

När du är redo att köra testet kan du använda Microsoft Azure Active Directory-konto (arbets- eller skolkonto konto) eller ett Microsoft-konto (live.com, outlook.com) för att logga in. Första gången du kör programmet, uppmanas du att logga in:

![Logga in till programmet](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Ange tillstånd för åtkomst till program
Första gången du loggar in i tillämpningsprogrammet, uppmanas du att ge ditt medgivande för att tillåta åtkomst till din profil och logga in dig: 

![Ge ditt medgivande för programåtkomst](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Visa program resultat
Du bör se användarens profilinformation som returneras av anropet till Microsoft Graph API när du loggar in. Resultatet visas i den **API-anrop resultat** rutan. Grundläggande information om den token som erhölls via anropet till **AcquireTokenAsync** eller **AcquireTokenSilentAsync** ska visas i den **Token Info** rutan. Det resulterar i följande egenskaper:

|Egenskap  |Format  |Beskrivning |
|---------|---------|---------|
|**Namn** |Användarens fullständiga namn |Användaren förnamn och efternamn.|
|**Användarnamn** |<span>user@domain.com</span> |Användarnamnet som används för att identifiera användaren.|
|**Token upphör att gälla** |DateTime |Tiden då token upphör att gälla. MSAL utökar utgångsdatumet genom att förnya token efter behov.|
|**Åtkomst-Token** |Sträng |Tokensträng som skickas till HTTP-begäranden som kräver en **auktorisering** huvud.|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API kräver den **user.read** omfång att läsa en användares profil. Detta scope läggs automatiskt som standard i alla program som har registrerats på portalen för registrering. Andra för Microsoft Graph-API: er, samt anpassade API: er för backend-servern, kan kräva ytterligare scope. Microsoft Graph API kräver den **Calendars.Read** scope för att visa användarens kalendrar.

För att få åtkomst till användarkalendrar i kontexten för ett program måste du lägga till den **Calendars.Read** delegerad behörighet att registreringsinformation för programmet. Lägg sedan till den **Calendars.Read** omfånget för den **acquireTokenSilent** anropa. 

>[!NOTE]
>Användaren kan uppmanas att ytterligare medgivanden när du ökar antalet scope.

<!--end-collapse-->

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
