
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Konfigurera din ASP.NET-Webbapp med programmets registreringsinformation

I det här steget ska du konfigurera ditt projekt om du vill använda SSL, och sedan använda SSL-URL: en för att konfigurera ditt programs registreringsinformation. Därefter kan du lägga till programmet ' registreringsinformation i lösningen via *web.config*.

1.  Välj projektet i Solution Explorer och titta på den `Properties` fönstret (om du inte ser en egenskapsfönstret trycker på F4)
2.  Ändra `SSL Enabled` till `True`
3.  Kopiera värdet från `SSL URL` ovan och klistra in den i den `Redirect URL` fältet överst i den här sidan och klicka sedan på *uppdatering*:<br/><br/>![Projektegenskaper](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
4.  Lägg till följande i `web.config` finns i rotens mapp under avsnittet `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
