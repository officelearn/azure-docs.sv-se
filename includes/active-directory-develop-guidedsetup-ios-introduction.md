
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>Anropa Microsoft Graph API från ett iOS-program

Den här guiden visar hur interna iOS-program (Swift) kan anropa API: er som kräver åtkomst-token från Microsoft Azure Active Directory (AD Azure) v2.0-slutpunkten. Guiden förklarar hur du skaffar åtkomst-token och använda dem i anrop till Microsoft Graph API och andra API: er.

När du har slutfört övningarna i den här guiden kan programmet anropa skyddade API från alla företag eller organisation som har Azure AD. Programmet kan göra skyddade API-anrop med hjälp av personliga konton, till exempel outlook.com och live.com, samt arbets-eller skolkonton.

## <a name="prerequisites"></a>Förutsättningar
- XCode version 8.x krävs för som skapas i den här guiden. Du kan hämta XCode från den [iTunes webbplats](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode hämta URL").
- Den [Carthage](https://github.com/Carthage/Carthage) beroende manager krävs för hantering av paketet.

## <a name="how-this-guide-works"></a>Hur den här guiden fungerar

![Hur den här guiden fungerar](media/active-directory-develop-guidedsetup-ios-introduction/iosintro.png)

I den här guiden kan exempelprogrammet ett iOS-program till Microsoft Graph API eller ett webb-API som accepterar token från Azure AD v2.0-slutpunkten. Det här scenariot kan en token har lagts till HTTP-begäranden med hjälp av den **auktorisering** huvud. Token förvärv och förnyelse hanteras av Microsoft Authentication Library (MSAL).


### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>Hantera token förvärv för åtkomst till skyddade web API: er

När användaren autentiseras får exempelprogrammet en token. Token som används för att fråga Microsoft Graph API eller ett webb-API som skyddas av Azure AD v2.0-slutpunkten.

API: er, till exempel Microsoft Graph kräver en åtkomst-token för att tillåta åtkomst till specifika resurser. Token krävs för att läsa en användares profil, åtkomst till en användares kalender, skickar ett e- och så vidare. Programmet kan begära en åtkomst-token med hjälp av MSAL och ange API-scope. Åtkomsttoken har lagts till i HTTP **auktorisering** huvud för varje anrop som görs mot den skyddade resursen.

MSAL hanterar cachelagring och uppdatera åtkomsttoken, så att programmet inte behöver.


## <a name="libraries"></a>Bibliotek

Den här guiden använder följande biblioteket:

|Bibliotek|Beskrivning|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Microsoft Authentication Library Preview för iOS|

