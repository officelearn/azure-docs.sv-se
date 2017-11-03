De flesta av autentiseringsfel tid bero på felaktig eller inkonsekvent konfigurationsinställningar. Här är några förslag till saker att kontrollera.

* Kontrollera att du inte missar i **spara** knappen var som helst. Det här är ofta enkelt göra och resultatet är att du ska titta på de rätta värdena på en Portalsida men de faktiskt inte har sparats i Azure-miljö eller Azure AD-program.
* För inställningar som konfigurerats i den **programinställningar** bladet i Azure-portalen, kontrollera att rätt API-app eller webbapp valdes när inställningarna har angetts.  Kontrollera också att inställningarna har angetts som **appinställningar** och inte **anslutningssträngar**eftersom formatet för avsnitten är liknande.
* Hämta manifestet igen för att kontrollera att för autentisering till en JavaScript-klientdel `oauth2AllowImplicitFlow` har ändrats till `true`.
* Kontrollera att du har använt HTTPS när du har konfigurerat URL: er:
  
  * I Projektkod
  * I CORS
  * I Azure App miljöinställningar för varje API-appen och webb-app
  * I inställningar för Azure AD-program.
    
    Observera att om du kopierar en API-app-URL från portalen, den ofta har `http://` och du måste manuellt ändra den till `https://`.
* Se till att ändringar koden har distribuerats. Till exempel i en lösning för flera projekt är det möjligt att ändra koden för ett projekt och välj ett av de andra av misstag när du planerar att distribuera ändringen.
* Kontrollera att du kommer att HTTPS-URL: er i din webbläsare inte HTTP-URL: er. Visual Studio skapar som standard publicera profiler med HTTP-URL: er och som är vad öppnas i webbläsaren när du distribuerar ett projekt.
* Kontrollera att CORS är korrekt konfigurerad på API-appen som JavaScript-kod anropar för autentisering till en JavaScript-klientdel. Om du tvekar om problemet är CORS-relaterade försök ”*” som URL för tillåtna ursprung. 
* Öppna din webbläsare Developer-konsolen på fliken om du vill ha mer information om fel för klientdelen för JavaScript och undersöka HTTP-begäranden på nätverket. Konsolen felmeddelanden kan emellertid vilseledande. Om du får ett meddelande om ett fel för CORS kanske verkliga problemet autentisering. Du kan kontrollera om så är fallet genom att köra appen med autentisering inaktiveras tillfälligt tillfälligt.
* För en .NET-API-app, kontrollerar du att du får så mycket information i felmeddelanden som möjligt genom att ange [customErrors läge för att inaktivera](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* En .NET-API-app, starta en [remote felsökningssessionen](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug), och undersöka värdena för variabler som skickas till kod som använder ADAL för att hämta ägartoken eller kod som kontrollerar fordringar förväntade tjänstens huvudnamn ID. Observera att din kod kan hämta konfigurationsvärden från olika källor, så det är möjligt att hitta överraskningar det här sättet. Till exempel om uppgifterna `ida:ClientId` som `ida:ClientID` när du konfigurerar Azure Apptjänst-miljöinställningar koden kan hämta den `ida:ClientId` värde som är ute efter från filen Web.config, ignorerar inställningen Azure App Service. 
* Om saker inte fungerar i ett normalt fönster i Internet Explorer, kan en befintlig inloggning hindra; InPrivate och försök Chrome eller Firefox.

