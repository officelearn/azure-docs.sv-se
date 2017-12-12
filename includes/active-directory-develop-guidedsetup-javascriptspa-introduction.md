# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Anropa Microsoft Graph API från ett JavaScript Single-Page Application (SPA)

Den här guiden visar hur en JavaScript enda sidan program (SPA) kan logga in personliga arbete och skolkonton, hämta en åtkomst-token och anropa Microsoft Graph API eller andra API: er som kräver åtkomst-token från slutpunkten för Azure Active Directory-v2.

### <a name="how-this-guide-works"></a>Hur den här guiden fungerar

![Så här fungerar sample-appen som genererats av den här guiden](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Mer information

Det exempelprogram som skapats av den här guiden kan en JavaScript-SPA frågar Microsoft Graph API eller ett webb-API som accepterar token från Azure Active Directory v2-slutpunkten. För det här scenariot, när en användare loggar in, begärt en åtkomst-token och lägga till HTTP-förfrågningar via authorization-huvud. Token förvärv och förnyelse hanteras av Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotek

Den här guiden använder följande biblioteket:

|Bibliotek|Beskrivning|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft-Autentiseringsbibliotek för JavaScript-förhandsgranskning|

> [!NOTE]
> *msal.js* mål i *Azure Active Directory v2 endpoint* -vilket innebär att personliga, skolan och arbetsrelaterade konton att logga in och hämta token. Den *Azure Active Directory v2 endpoint* har [vissa begränsningar](..\articles\active-directory\develop\active-directory-v2-limitations.md). Om du bara vill skolan och arbetsrelaterade konton använder *adal.js* och *V1 endpoint*. Att förstå skillnaderna mellan v1 och v2-slutpunkter läsa den [v1 v2 jämförelse](..\articles\active-directory\develop\active-directory-v2-compare.md).

<!--end-collapse-->
