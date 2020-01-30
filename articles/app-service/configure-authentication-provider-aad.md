---
title: Konfigurera Azure AD-autentisering
description: Lär dig hur du konfigurerar Azure Active Directory-autentisering som identitets leverantör för din App Service-app.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 69959418c52eb7324efe19ca41481e426b822ab4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842373"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>Konfigurera din App Service-app att använda Azure AD-inloggning

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Den här artikeln visar hur du konfigurerar Azure App Service att använda Azure Active Directory (Azure AD) som autentiseringsprovider.

Följ dessa rekommendationer när du konfigurerar din app och autentisering:

- Ge varje App Service app sina egna behörigheter och sitt medgivande.
- Konfigurera varje App Service-app med en egen registrering.
- Undvik att dela behörighet mellan miljöer genom att använda separata registrerings program för olika distributions platser. När du testar ny kod kan den här övningen hjälpa till att förhindra att problem påverkar produktions programmet.

## <a name="express"> </a>Konfigurera med Express inställningar

1. I [Azure-portalen]söker du efter och väljer **app Services**och väljer sedan din app.
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

## <a name="advanced"> </a>Konfigurera med avancerade inställningar

Du kan konfigurera inställningar för appar manuellt om du vill använda en app-registrering från en annan Azure AD-klient. För att slutföra den här anpassade konfigurationen:

1. Skapa en registrering i Azure AD.
2. Ange en del av registrerings informationen för att App Service.

### <a name="register"> </a>Skapa en app-registrering i Azure AD för din app service-app

Du behöver följande information när du konfigurerar din App Service-app:

- Klientorganisations-ID
- Klient-ID:t
- Klient hemlighet (valfritt)
- Program-ID-URI

Utför följande steg:

1. Logga in på [Azure-portalen], Sök efter och välj **app Services**och välj sedan din app. Anteckna appens **URL**. Du använder den för att konfigurera din Azure Active Directory app-registrering.
1. Välj **Azure Active Directory** > **Appregistreringar** > **ny registrering**.
1. På sidan **Registrera ett program** anger du ett **namn** för din app-registrering.
1. I **omdirigerings-URI**väljer du **webb** och typ `<app-url>/.auth/login/aad/callback`. Till exempel `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Välj **Skapa**.
1. När appens registrering har skapats kopierar du **program-ID: t** och **katalogen (klient)-ID:** t för senare.
1. Välj **anpassning**. På **Start sidans URL**anger du URL: en för din app service-app och väljer **Spara**.
1. Välj **exponera en API** - > **uppsättning**. Klistra in URL: en för din App Service-app och välj **Spara**.

   > [!NOTE]
   > Det här värdet är **program-ID-URI: n** för appens registrering. Om din webbapp kräver åtkomst till ett API i molnet behöver du **program-ID-URI: n** för webbappen när du konfigurerar Cloud App Service-resursen. Du kan använda detta, till exempel om du vill att moln tjänsten uttryckligen ska bevilja åtkomst till webbappen.

1. Välj **Lägg till omfång**.
   1. I **omfångs namn**anger du *user_impersonation*.
   1. I text rutorna anger du namn och beskrivning för medgivande omfånget som du vill att användarna ska se på sidan för medgivande. Ange till exempel *åtkomst till min app*. 
   1. Välj **Lägg till omfattning**.
1. Valfritt Om du vill skapa en klient hemlighet väljer du **certifikat & hemligheter** > **ny klient hemlighet** > **Lägg till**. Kopiera klientens hemliga värde som visas på sidan. Den visas inte igen.
1. Valfritt Om du vill lägga till flera **svars-URL: er**väljer du **autentisering**.

### <a name="secrets"> </a>Aktivera Azure Active Directory i App Service-appen

1. I [Azure-portalen]söker du efter och väljer **app Services**och väljer sedan din app. 
1. Välj **autentisering/auktorisering** > **på**i den vänstra rutan under **Inställningar**.
1. Valfritt Som standard tillåter App Service autentisering oautentiserad åtkomst till din app. Om du vill framtvinga användarautentisering anger du **åtgärden som ska vidtas när begäran inte autentiseras** att **logga in med Azure Active Directory**.
1. Under **autentiseringsproviders**väljer du **Azure Active Directory**.
1. I **hanterings läge**väljer du **avancerat** och konfigurerar app service autentisering enligt följande tabell:

    |Field|Beskrivning|
    |-|-|
    |Klientorganisations-ID| Använd **program-ID: t (klient)** för appens registrering. |
    |Utfärdar-ID| Använd `https://login.microsoftonline.com/<tenant-id>`och Ersätt *\<klient-id >* med katalog- **ID** för appens registrering. |
    |Klient hemlighet (valfritt)| Använd den klient hemlighet som du genererade i appens registrering.|
    |Tillåtna token-mottagare| Om det här är en moln-eller Server App och du vill tillåta autentiseringstoken från en webbapp lägger du till **program-ID-URI: n** för webbappen här. Det konfigurerade **klient-ID: t** anses *alltid* vara en tillåten mål grupp. |

2. Välj **OK**och välj sedan **Spara**.

Du är nu redo att använda Azure Active Directory för autentisering i din App Service-app.

## <a name="configure-a-native-client-application"></a>Konfigurera ett internt klient program

Du kan registrera interna klienter för att tillåta autentisering med hjälp av ett klient bibliotek som **Active Directory-autentiseringsbibliotek**.

1. I [Azure-portalen]väljer du **Active Directory** > **Appregistreringar** > **ny registrering**.
1. På sidan **Registrera ett program** anger du ett **namn** för din app-registrering.
1. I **omdirigerings-URI**väljer du **offentlig klient (mobil & Desktop)** och anger URL: en `<app-url>/.auth/login/aad/callback`. Till exempel `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > Använd [paket-sid](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) som URI i stället för ett Windows-program.
1. Välj **Skapa**.
1. När appens registrering har skapats kopierar du värdet för **program-ID (klient)** .
1. Välj **API-behörigheter** > **Lägg till en behörighet** > **Mina API: er**.
1. Välj den app-registrering som du skapade tidigare för din App Service-app. Om du inte ser appens registrering ser du till att du har lagt till **user_impersonation** omfattning i [skapa en app-registrering i Azure AD för din app service-app](#register).
1. Välj **user_impersonation**och välj sedan **Lägg till behörigheter**.

Nu har du konfigurerat ett internt klient program som har åtkomst till din App Service-app.

## <a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure-portalen]: https://portal.azure.com/
