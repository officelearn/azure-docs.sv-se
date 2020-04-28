---
title: Konfigurera Azure AD-autentisering
description: Lär dig hur du konfigurerar Azure Active Directory-autentisering som identitets leverantör för din App Service-eller Azure Functions-app.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 913aac7755e6c4f9a4b42d45933728fcc8840bfb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190018"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Konfigurera din App Service-eller Azure Functions-app för att använda Azure AD-inloggning

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Den här artikeln visar hur du konfigurerar Azure App Service eller Azure Functions att använda Azure Active Directory (Azure AD) som autentiseringsprovider.

> [!NOTE]
> Med Express Settings Flow konfigurerar du en AAD v1-programregistrering. Om du vill använda [Azure Active Directory v 2.0](../active-directory/develop/v2-overview.md) (inklusive [MSAL](../active-directory/develop/msal-overview.md)) följer du [anvisningarna för Avancerad konfiguration](#advanced).

Följ dessa rekommendationer när du konfigurerar din app och autentisering:

- Ge varje App Service app sina egna behörigheter och sitt medgivande.
- Konfigurera varje App Service-app med en egen registrering.
- Undvik att dela behörighet mellan miljöer genom att använda separata registrerings program för olika distributions platser. När du testar ny kod kan den här övningen hjälpa till att förhindra att problem påverkar produktions programmet.

> [!NOTE]
> Den här funktionen är för närvarande inte tillgänglig i användnings planen för Linux för Azure Functions

## <a name="configure-with-express-settings"></a><a name="express"> </a>Konfigurera med Express inställningar

> [!NOTE]
> **Express** alternativet är inte tillgängligt för offentliga moln. 

1. I [Azure Portal]söker du efter och väljer **app Services**och väljer sedan din app.
2. Välj **autentisering/auktorisering** > **på**i det vänstra navigerings fältet.
3. Välj **Azure Active Directory** > **Express**.

   Om du vill välja en befintlig app-registrering i stället:

   1. Välj **Välj befintlig AD-App**och klicka sedan på **Azure AD App**.
   2. Välj en befintlig app-registrering och klicka på **OK**.

3. Välj **OK** för att registrera App Service-appen i Azure Active Directory. En ny app-registrering skapas.
   
    ![Express inställningar i Azure Active Directory](./media/configure-authentication-provider-aad/express-settings.png)
   
4. Valfritt Som standard tillhandahåller App Service autentisering, men begränsar inte tillåten åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din app-kod. Om du vill begränsa åtkomsten till appar enbart till användare som autentiserats av Azure Active Directory anger du **åtgärd som ska vidtas när begäran inte autentiseras** att **logga in med Azure Active Directory**. När du ställer in den här funktionen kräver appen att alla begär Anden ska autentiseras. Det omdirigerar också all oautentiserad till Azure Active Directory för autentisering.

    > [!CAUTION]
    > Att begränsa åtkomsten på det här sättet gäller alla anrop till appen, vilket kanske inte är önskvärt för appar som har en offentligt tillgänglig start sida, som i många program med en enda sida. För sådana program kan du **tillåta anonyma begär Anden (ingen åtgärd)** , med appen manuellt startar inloggningen. Mer information finns i [Authentication Flow](overview-authentication-authorization.md#authentication-flow).
5. Välj **Spara**.

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>Konfigurera med avancerade inställningar

Du kan konfigurera inställningar för appar manuellt om du vill använda en app-registrering från en annan Azure AD-klient. För att slutföra den här anpassade konfigurationen:

1. Skapa en registrering i Azure AD.
2. Ange en del av registrerings informationen för att App Service.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Skapa en app-registrering i Azure AD för din app service-app

Du behöver följande information när du konfigurerar din App Service-app:

- Klientorganisations-ID
- Klient-ID:t
- Klient hemlighet (valfritt)
- Program-ID-URI

Utför följande steg:

1. Logga in på [Azure Portal], Sök efter och välj **app Services**och välj sedan din app. Anteckna appens **URL**. Du använder den för att konfigurera din Azure Active Directory app-registrering.
1. Välj **Azure Active Directory** > **Appregistreringar** > **ny registrering**.
1. På sidan **Registrera ett program** anger du ett **namn** för din app-registrering.
1. I **omdirigerings-URI**väljer du `<app-url>/.auth/login/aad/callback` **webb** och typ. Till exempel `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Välj **Skapa**.
1. När appens registrering har skapats kopierar du **program-ID: t** och **katalogen (klient)-ID:** t för senare.
1. Välj **Autentisering**. Under **implicit beviljande**, aktiverar du **ID-token** för att tillåta OpenID Connect-användarkonton från App Service.
1. Valfritt Välj **anpassning**. På **Start sidans URL**anger du URL: en för din app service-app och väljer **Spara**.
1. Välj **exponera en API** > -**uppsättning**. För en app med en klient, klistra in URL: en för din App Service-app och välj **Spara** och för appar för flera klient organisationer klistrar du in webb adressen som baseras på en av klientens verifierade domäner och väljer sedan **Spara**.

   > [!NOTE]
   > Det här värdet är **program-ID-URI: n** för appens registrering. Om din webbapp kräver åtkomst till ett API i molnet behöver du **program-ID-URI: n** för webbappen när du konfigurerar Cloud App Service-resursen. Du kan använda detta, till exempel om du vill att moln tjänsten uttryckligen ska bevilja åtkomst till webbappen.

1. Välj **Lägg till omfång**.
   1. I **omfångs namn**anger du *user_impersonation*.
   1. I text rutorna anger du namn och beskrivning för medgivande omfånget som du vill att användarna ska se på sidan för medgivande. Ange till exempel *åtkomst till min app*. 
   1. Välj **Lägg till omfattning**.
1. Valfritt Om du vill skapa en klient hemlighet väljer du **certifikat & hemligheter** > **ny klient hemlighet** > **Lägg till**. Kopiera klientens hemliga värde som visas på sidan. Den visas inte igen.
1. Valfritt Om du vill lägga till flera **svars-URL: er**väljer du **autentisering**.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Aktivera Azure Active Directory i App Service-appen

1. I [Azure Portal]söker du efter och väljer **app Services**och väljer sedan din app. 
1. Välj **autentisering/auktorisering** > **på**i den vänstra rutan under **Inställningar**.
1. Valfritt Som standard tillåter App Service autentisering oautentiserad åtkomst till din app. Om du vill framtvinga användarautentisering anger du **åtgärden som ska vidtas när begäran inte autentiseras** att **logga in med Azure Active Directory**.
1. Under **autentiseringsproviders**väljer du **Azure Active Directory**.
1. I **hanterings läge**väljer du **avancerat** och konfigurerar app service autentisering enligt följande tabell:

    |Field|Beskrivning|
    |-|-|
    |Klientorganisations-ID| Använd **program-ID: t (klient)** för appens registrering. |
    |Utfärdar-URL| Använd `<authentication-endpoint>/<tenant-id>/v2.0`och Ersätt * \<autentiserings slut punkts>* med [slut punkten för autentiseringen för din moln miljö](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (thttps://login.microsoft.com. ex. "" för Global Azure), och ersätter * \<även klient-ID>* med den **katalog (klient)-ID** som program registreringen skapades i. Det här värdet används för att omdirigera användare till rätt Azure AD-klient, samt för att hämta lämpliga metadata för att fastställa lämpliga token för signerings nycklar och token Issuer-anspråk till exempel. `/v2.0` Avsnittet kan utelämnas för program som använder AAD v1. |
    |Klient hemlighet (valfritt)| Använd den klient hemlighet som du genererade i appens registrering.|
    |Tillåtna token-mottagare| Om det här är en moln-eller Server App och du vill tillåta autentiseringstoken från en webbapp lägger du till **program-ID-URI: n** för webbappen här. Det konfigurerade **klient-ID: t** anses *alltid* vara en tillåten mål grupp. |

2. Välj **OK**och välj sedan **Spara**.

Du är nu redo att använda Azure Active Directory för autentisering i din App Service-app.

## <a name="configure-a-native-client-application"></a>Konfigurera ett internt klient program

Du kan registrera interna klienter för att tillåta autentisering till webb-API: n som finns i din app med hjälp av ett klient bibliotek som **Active Directory-autentiseringsbibliotek**.

1. I [Azure Portal]väljer du **Active Directory** > **Appregistreringar** > **ny registrering**.
1. På sidan **Registrera ett program** anger du ett **namn** för din app-registrering.
1. I **omdirigerings-URI**väljer du **offentlig klient (mobil & Desktop)** och `<app-url>/.auth/login/aad/callback`anger URL: en. Till exempel `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > För ett Microsoft Store-program använder du [paket-sid](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) som URI i stället.
1. Välj **Skapa**.
1. När appens registrering har skapats kopierar du värdet för **program-ID (klient)**.
1. Välj **API-behörigheter** > **Lägg till en behörighet** > **Mina API: er**.
1. Välj den app-registrering som du skapade tidigare för din App Service-app. Om du inte ser appens registrering ser du till att du har lagt till **user_impersonation** omfattning i [skapa en app-registrering i Azure AD för din app service-app](#register).
1. Välj **user_impersonation**och välj sedan **Lägg till behörigheter**.

Nu har du konfigurerat ett internt klient program som kan komma åt din App Service-app för en användares räkning.

## <a name="next-steps"></a><a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
