
## <a name="test-your-code"></a>Testa din kod

Om du vill testa ditt program i Visual Studio trycker du på **F5** köra projektet. I webbläsaren öppnas http://<span></span>localhost: {port} plats och du ser den **logga in med Microsoft** knappen. Klicka på knappen för att starta processen för inloggning.

När du är redo att köra testet kan använda ett Microsoft Azure Active Directory (Azure AD)-konto (arbets- eller skolkonto konto) eller ett personligt microsoftkonto (<span>live.</span> COM eller <span>outlook.</span> com) för att logga in.

![Logga in med Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Logga in på ditt Microsoft-konto](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Visa program resultat
När du har loggat in omdirigeras användaren till startsidan för din webbplats. Sidan är HTTPS-URL som anges i programmet registreringsinformation i portalen för registrering av Microsoft-program. Sidan innehåller ett välkomstmeddelande ”Hello \<användare >”, en länk till logga ut och en länk för att visa användarens anspråk. Länken för användarens anspråk bläddrar till den **auktorisera** domänkontrollant som du skapade tidigare.

### <a name="browse-to-see-the-users-claims"></a>Bläddra till användarens anspråk
Välj länken för att bläddra till vyn domänkontrollant som är tillgängligt endast för autentiserade användare för att se användarens anspråk.

#### <a name="view-the-claims-results"></a>Testresultaten anspråk
När du bläddrar till vyn domänkontrollanten bör du se en tabell som innehåller de grundläggande egenskaperna för användaren:

|Egenskap |Värde |Beskrivning |
|---|---|---|
|**Namn** |Användarens fullständiga namn | Användaren förnamn och efternamn.
|**Användarnamn** |användaren<span>@domain.com</span> | Användarnamnet som används för att identifiera användaren.
|**Ämne** |Ämne |En sträng som unikt identifierar användaren på Internet.|
|**Klient-ID** |GUID | En **guid** som unikt representerar användarens Azure AD-organisation.|

Dessutom bör du se en tabell med alla anspråk i autentiseringsbegäran. Mer information finns i [listan med anspråk som finns i en Azure AD-ID-Token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testa åtkomst till en metod som har attributet auktorisera (valfritt)
Att testa åtkomst till den **auktorisera** styrenhet för användarens anspråk som en anonym användare gör du följande:
1. Välj länken för att logga ut användaren och slutföra utloggning.
2. Skriv http:// i din webbläsare<span></span>localhost: {port} / autentiserad åtkomst till din domänkontrollant som är skyddat med den **auktorisera** attribut.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Förväntat resultat när du har åtkomst till en skyddad domänkontrollant
Du uppmanas att autentisera sig för att använda vyn skyddade domänkontrollant.

## <a name="additional-information"></a>Ytterligare information

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Skydda hela webbplatsen
Att skydda hela webbplatsen i den **Global.asax** lägger du till den **AuthorizeAttribute** attribut till den **GlobalFilters** filter i den **program _Starta** metoden:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-sign-in-access-to-your-application"></a>Begränsa inloggning åtkomsten till ditt program
Som standard logga personliga konton, till exempel outlook.com och live.com in på ditt program. Arbets- eller skolkonto konton i organisationer som är integrerade med Azure AD kan också logga in som standard.

Om du vill begränsa användarnas inloggning åtkomst för ditt program, finns flera tillgängliga alternativ.

#### <a name="restrict-access-to-a-single-organization"></a>Begränsa åtkomsten till en enda organisation
Du kan begränsa inloggning åtkomst för ditt program till endast de användarkonton som finns i en enda organisation Azure AD:
1. I den **web.config** fil, ändrar du värdet för den **klient** parameter. Ändrar du värdet från **vanliga** till innehavarens namn för organisationen som **contoso.onmicrosoft.com**.
2. I din **OWIN Start** klassen genom att ange den **ValidateIssuer** argumentet för **SANT**.

#### <a name="restrict-access-to-a-list-of-organizations"></a>Begränsa åtkomsten till en lista över organisationer
Du kan begränsa inloggning åtkomsten till endast användarkonton som finns i en Azure AD-organisation som finns i listan över tillåtna organisationer:
1. I den **web.config** filen i din **OWIN Start** klassen genom att ange den **ValidateIssuer** argumentet för **SANT**.
2. Ange värdet för den **ValidIssuers** parameter i listan över tillåtna organisationer.

#### <a name="use-a-custom-method-to-validate-issuers"></a>Använda en anpassad metod för att verifiera utfärdare
Du kan implementera en anpassad metod för att verifiera certifikatutfärdare med hjälp av den **IssuerValidator** parameter. Mer information om hur du använder den här parametern Läs mer om den [TokenValidationParameters klassen](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) på MSDN.

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
