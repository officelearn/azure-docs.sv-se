
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>Anropa Microsoft Graph API från ett iOS-program

Den här guiden visar hur ett program för interna iOS (Swift) kan anropa API: er som kräver åtkomsttoken från Microsoft Azure Active Directory (Azure AD) v2.0-slutpunkten. Guiden förklarar hur du hämta åtkomsttoken och använda dem i anrop till Microsoft Graph API och API: er.

När du har slutfört övningarna i den här guiden kan programmet anropa ett skyddat API från alla företag eller organisation som har Azure AD. Ditt program kan göra skyddade API-anrop med hjälp av personliga konton som outlook.com, live.com och andra, samt arbets-eller skolkonto.

## <a name="prerequisites"></a>Förutsättningar
- XCode version 8.x krävs för exemplet som skapas i den här guiden. Du kan hämta XCode från den [iTunes webbplats](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode hämta URL: en").
- Den [Carthage](https://github.com/Carthage/Carthage) Beroendehanteraren krävs för pakethantering.

## <a name="how-this-guide-works"></a>Hur fungerar den här guiden

![Hur fungerar den här guiden](media/active-directory-develop-guidedsetup-ios-introduction/iosintro.png)

I den här guiden kan exempelprogrammet ett iOS-program att fråga Microsoft Graph API eller ett webb-API som accepterar token från Azure AD v2.0-slutpunkten. Det här scenariot kan en token har lagts till HTTP-begäranden med hjälp av den **auktorisering** rubrik. Tokenförvärv och förnyelse hanteras av Microsoft Authentication Library (MSAL).


### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>Hantera tokenförvärv för åtkomst till skyddade webb-API: er

När användaren autentiseras, får en token i exempelprogrammet. Det används aktuellt token för att fråga Microsoft Graph API eller ett webb-API som skyddas av Azure AD v2.0-slutpunkten.

API: er, till exempel Microsoft Graph, kräver en åtkomsttoken för att tillåta åtkomst till specifika resurser. Token krävs för att läsa en användares profil, få åtkomst till en användares kalender, skicka ett e-postmeddelande och så vidare. Ditt program kan begära en åtkomsttoken genom med MSAL och ange API-omfång. Åtkomst-token har lagts till i HTTP **auktorisering** rubrik för varje anrop som görs mot den skyddade resursen.

MSAL hanterar cachelagring och uppdatera åtkomsttoken för dig, så att ditt program inte behöver.


## <a name="libraries"></a>Bibliotek

Den här guiden använder följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Förhandsversionen av Microsoft Authentication Library för iOS|

