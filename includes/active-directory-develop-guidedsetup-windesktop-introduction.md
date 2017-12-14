# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Anropa Microsoft Graph API från en Windows-skrivbordsapp

Den här guiden visar hur en intern Windows Desktop .NET (XAML)-programmet kan få en åtkomsttoken och anropa Microsoft Graph API eller andra API: er som kräver åtkomst-token från Azure Active Directory v2-slutpunkten.

I slutet av den här guiden kommer programmet att kunna anropa en skyddad API med hjälp av personliga konton (inklusive outlook.com och live.com) samt arbets- och skolkonton från alla företag eller organisation som har Azure Active Directory.  

> Den här guiden kräver Visual Studio 2015 Update 3 eller Visual Studio 2017.  Har det? [Hämta Visual Studio 2017 gratis](https://www.visualstudio.com/downloads/)

### <a name="how-this-guide-works"></a>Hur den här guiden fungerar

![Hur den här guiden fungerar](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

Det exempelprogram som skapats av den här guiden kan ett Windows-skrivbordet program frågar Microsoft Graph API eller ett webb-API som accepterar token från Azure Active Directory v2-slutpunkten. I det här scenariot läggs en token på HTTP-förfrågningar via Authorization-huvud. Token förvärv och förnyelse hanteras av Microsoft Authentication Library (MSAL).


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Hantera token förvärv för att komma åt skyddade webb-API: er

När användaren autentiseras får exempelprogrammet en token som kan användas för att fråga Microsoft Graph API eller ett webb-API som skyddas av Microsoft Azure Active Directory v2.

API: er som Microsoft Graph kräver en åtkomst-token för att tillåta åtkomst till specifika resurser – till exempel att läsa en användares profil, åtkomst till användarens kalender eller skicka ett e-postmeddelande. Programmet kan begära en åtkomst-token med MSAL åtkomst till dessa resurser genom att ange API-scope. Åtkomsttoken läggs sedan till http-Authorization-huvud för varje anrop som görs mot den skyddade resursen. 

MSAL hanterar cachelagring och uppdatera åtkomsttoken, så att programmet inte behöver.


### <a name="nuget-packages"></a>NuGet-paket

Den här guiden använder följande NuGet-paket:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|

