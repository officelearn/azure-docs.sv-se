---
title: Konfigurera Azure AD-autentisering
description: Lär dig hur du konfigurerar Azure Active Directory-autentisering som identitetsprovider för appen AppTjänst eller Azure Functions.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 4b42f0966288e4ee72b689ddce6313a41e91f13e
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438035"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Konfigurera appen App Service eller Azure Functions så att den använder Azure AD-inloggning

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Den här artikeln visar hur du konfigurerar Azure App Service eller Azure Functions för att använda Azure Active Directory (Azure AD) som autentiseringsprovider.

> [!NOTE]
> För närvarande stöds inte [Azure Active Directory v2.0](../active-directory/develop/v2-overview.md) (inklusive [MSAL)](../active-directory/develop/msal-overview.md)för Azure App Service och Azure Functions. Kom tillbaka för uppdateringar.
>

Följ de här metodtipsen när du konfigurerar din app och autentisering:

- Ge varje App Service-app sina egna behörigheter och samtycke.
- Konfigurera varje App Service-app med egen registrering.
- Undvik behörighetsdelning mellan miljöer genom att använda separata appregistreringar för separata distributionsplatser. När du testar ny kod kan den här metoden förhindra problem från att påverka produktionsappen.

## <a name="configure-with-express-settings"></a><a name="express"> </a>Konfigurera med expressinställningar

> [!NOTE]
> **Alternativet Express** är inte tillgängligt för regeringsmoln. 

1. Sök efter och välj **App Services**i [Azure-portalen]och välj sedan din app.
2. Välj **Autentisering/auktorisering** > **på**den vänstra navigeringen .
3. Välj **Azure Active Directory** > **Express**.

   Om du vill välja en befintlig appregistrering i stället:

   1. Välj **Välj befintlig AD-app**och klicka sedan på **Azure AD App**.
   2. Välj en befintlig appregistrering och klicka på **OK**.

3. Välj **OK** om du vill registrera apptjänstappen i Azure Active Directory. En ny appregistrering skapas.
   
    ![Snabbinställningar i Azure Active Directory](./media/configure-authentication-provider-aad/express-settings.png)
   
4. (Valfritt) Som standard tillhandahåller App Service autentisering men begränsar inte denuktoriserat åtkomsten till webbplatsens innehåll och API:er. Du måste auktorisera användare i din appkod. Om du bara vill begränsa appåtkomst till användare som autentiseras av Azure Active Directory anger du **åtgärd som ska vidtas när begäran inte autentiseras** logga **in med Azure Active Directory**. När du ställer in den här funktionen kräver appen att alla begäranden ska autentiseras. Det omdirigerar också alla oautentiserade till Azure Active Directory för autentisering.

    > [!CAUTION]
    > Att begränsa åtkomsten på det här sättet gäller alla samtal till din app, vilket kanske inte är önskvärt för appar som har en allmänt tillgänglig startsida, som i många ensidiga program. För sådana program kan **tillåt anonyma begäranden (ingen åtgärd)** att föredra, med appen manuellt starta inloggningen själv. Mer information finns i [Autentiseringsflöde](overview-authentication-authorization.md#authentication-flow).
5. Välj **Spara**.

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>Konfigurera med avancerade inställningar

Du kan konfigurera appinställningar manuellt om du vill använda en appregistrering från en annan Azure AD-klientorganisation. Så här slutför du den här anpassade konfigurationen:

1. Skapa en registrering i Azure AD.
2. Ange några av registreringsuppgifterna till App Service.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Skapa en appregistrering i Azure AD för appen App Service

Du behöver följande information när du konfigurerar apptjänstappen:

- Klientorganisations-ID
- Klient-ID:t
- Klienthemlighet (valfritt)
- Program-ID URI

Utför följande steg:

1. Logga in på [Azure-portalen,]sök efter och välj **App Services**och välj sedan din app. Observera appens **webbadress**. Du ska använda den för att konfigurera registreringen av Din Azure Active Directory-app.
1. Välj **Azure Active Directory** > **App registreringar** > **Ny registrering**.
1. På sidan **Registrera ett program** anger du ett **namn** för din appregistrering.
1. I **Omdirigera URI**väljer `<app-url>/.auth/login/aad/callback`du **Webb** och skriver . Till exempel `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Välj **Skapa**.
1. När appregistreringen har skapats kopierar **du program-ID:t och** **katalog-ID :t** för senare.
1. Välj **Autentisering**. Under **Implicit beviljande**aktiverar du **ID-token** för att tillåta OpenID Connect-användarloggningar från App Service.
1. (Valfritt) Välj **Branding**. Ange URL:en för appen App Service på **startsidan**och välj **Spara**.
1. Välj Visa en**API-uppsättning** **Expose an API** > . Klistra in url:en till apptjänstappen och välj **Spara**.

   > [!NOTE]
   > Det här värdet är **program-ID-URI** för appregistreringen. Om webbappen kräver åtkomst till ett API i molnet behöver du **webbappens program-ID** när du konfigurerar molnapptjänstens resurs. Du kan till exempel använda detta om du vill att molntjänsten uttryckligen ska bevilja åtkomst till webbappen.

1. Välj **Lägg till omfång**.
   1. Skriv *user_impersonation i* **Scope-namn**.
   1. I textrutorna anger du namnet på och beskrivningen för samtyckesomfånget som du vill att användarna ska se på medgivandesidan. Ange till exempel *Access min app*. 
   1. Välj **Lägg till omfattning**.
1. (Valfritt) Om du vill skapa en klienthemlighet väljer du **Certifikat & hemligheter** > **Ny klienthemlighet** > **Lägg till**. Kopiera klientens hemliga värde som visas på sidan. Det kommer inte att visas igen.
1. (Valfritt) Om du vill lägga till flera **svarsadresser**väljer du **Autentisering**.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Aktivera Azure Active Directory i apptjänstappen

1. Sök efter och välj **App Services**i [Azure-portalen]och välj sedan din app. 
1. Välj **Autentisering/auktorisering** > **på**under **Inställningar**i den vänstra rutan .
1. (Valfritt) Som standard tillåter App Service-autentisering oautentiserade åtkomst till din app. Om du vill framtvinga användarautentisering anger du **åtgärd som ska vidtas när begäran inte autentiseras** logga **in med Azure Active Directory**.
1. Under **Autentiseringsleverantörer**väljer du **Azure Active Directory**.
1. I **hanteringsläge**väljer du **Avancerat** och konfigurerar App Service-autentisering enligt följande tabell:

    |Field|Beskrivning|
    |-|-|
    |Klientorganisations-ID| Använd **program-ID:t (klient)för** appregistreringen. |
    |Url till utfärdare| Använd `https://login.microsoftonline.com/<tenant-id>`och ersätt * \<klient-ID>* med **katalog-ID (klient)** för appregistreringen. Det här värdet används för att omdirigera användare till rätt Azure AD-klientorganisation, samt för att hämta lämpliga metadata för att fastställa lämpliga tokensigneringsnycklar och tokenutfärdare anspråksvärde till exempel. |
    |Klienthemlighet (valfritt)| Använd klienthemligheten som du skapade i appregistreringen.|
    |Tillåtna tokenmålningar| Om detta är en moln- eller serverapp och du vill tillåta autentiseringstoken från en webbapp lägger du till **webbappens program-ID-URI** här. Det konfigurerade **klient-ID:et** anses *alltid* implicit vara en tillåten målgrupp. |

2. Välj **OK**och välj sedan **Spara**.

Nu är du redo att använda Azure Active Directory för autentisering i apptjänstappen.

## <a name="configure-a-native-client-application"></a>Konfigurera ett inbyggt klientprogram

Du kan registrera inbyggda klienter så att autentisering till webb-API finns i appen med hjälp av ett klientbibliotek, till exempel **Active Directory Authentication Library**.

1. I [Azure-portalen]väljer du **Active Directory** > **App registreringar** > **Ny registrering**.
1. På sidan **Registrera ett program** anger du ett **namn** för din appregistrering.
1. I **Omdirigera URI**väljer du **Offentlig klient (mobilt &-skrivbordet)** och skriver URL:en `<app-url>/.auth/login/aad/callback`. Till exempel `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > För ett Microsoft Store-program använder du [paketet SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) som URI i stället.
1. Välj **Skapa**.
1. När appregistreringen har skapats kopierar du värdet **för Application (client) ID**.
1. Välj **API-behörigheter** > **Lägg till en behörighet** > **Mina API:er**.
1. Välj den appregistrering som du skapade tidigare för apptjänstappen. Om du inte ser appregistreringen kontrollerar du att du har lagt till **user_impersonation** omfattning i [Skapa en appregistrering i Azure AD för apptjänstappen](#register).
1. Välj **user_impersonation**och välj sedan **Lägg till behörigheter**.

Du har nu konfigurerat ett inbyggt klientprogram som kan komma åt apptjänstappen för en användares räkning.

## <a name="next-steps"></a><a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
