# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Lägga till inloggning med Microsoft till ett ASP.NET-webbprogram

Den här guiden visar hur du implementerar inloggning med Microsoft med hjälp av en ASP.NET MVC-lösning med en traditionell webbläsarbaserad webbapp med OpenID Connect. 

I slutet av den här guiden kommer programmet att kunna acceptera logga moduler av personliga konton (inklusive outlook.com och live.com) som fungerar och skolkonton från alla företag eller organisation som har integrerat med Azure Active Directory. 

> Den här guiden kräver Visual Studio 2015 Update 3 eller Visual Studio 2017.  Har det?  [Hämta Visual Studio 2017 gratis](https://www.visualstudio.com/downloads/)

## <a name="how-this-guide-works"></a>Hur den här guiden fungerar

![Hur den här guiden fungerar](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.png)

Den här guiden bygger på ett scenario där en webbläsare har åtkomst till en ASP.NET-webbplats, begär en användare autentiseras via en knapp för inloggning. I det här scenariot inträffar mesta av arbetet ska renderas webbsidan på serversidan.

## <a name="libraries"></a>Bibliotek

Den här guiden använder följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Mellanprogram som aktiverar ett program att använda OpenIdConnect för autentisering|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Mellanprogram som aktiverar ett program att underhålla användarsession med hjälp av cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Gör att OWIN-baserade program körs på IIS med hjälp av ASP.NET förfrågnings-pipelinen|

