## <a name="test-your-code"></a>Testa din kod

1. Distribuera din kod till din emulatorn.
2. När du är redo att testa ditt program kan du använda Microsoft Azure Active Directory-konto (arbets- eller skolkonto konto) eller ett Microsoft-konto (live.com, outlook.com) för att logga in. 

    ![Testa programmet](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![Ange användarnamn och lösenord](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="provide-consent-for-application-access"></a>Ange tillstånd för åtkomst till program
Första gången du loggar in i tillämpningsprogrammet, uppmanas du att ge ditt medgivande för att tillåta åtkomst till din profil och logga in dig: 

![Ge ditt medgivande för programåtkomst](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="view-application-results"></a>Visa program resultat
Du bör se resultatet som returneras av anropet till Microsoft Graph API när du loggar in. Anropet till Microsoft Graph API **mig** endpoint returnerar användarens profil https://graph.microsoft.com/v1.0/me. En lista över vanliga Microsoft Graph-slutpunkter, se [utvecklardokumentationen Microsoft Graph API](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API kräver den **user.read** omfång att läsa en användares profil. Detta scope läggs automatiskt som standard i alla program som har registrerats på portalen för registrering. Andra för Microsoft Graph-API: er, samt anpassade API: er för backend-servern, kan kräva ytterligare scope. Microsoft Graph API kräver den **Calendars.Read** scope för att visa användarens kalendrar.

För att få åtkomst till användarkalendrar i kontexten för ett program måste du lägga till den **Calendars.Read** delegerad behörighet att registreringsinformation för programmet. Lägg sedan till den **Calendars.Read** omfånget för den **acquireTokenSilent** anropa. 

>[!NOTE]
>Användaren kan uppmanas att ytterligare medgivanden när du ökar antalet scope.

<!--end-collapse-->

[!INCLUDE  [Help and support](active-directory-develop-help-support-include.md)]
