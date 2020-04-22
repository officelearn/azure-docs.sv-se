---
title: SMS-baserad användarloggning för Azure Active Directory
description: Lär dig hur du konfigurerar och gör det möjligt för användare att logga in på Azure Active Directory med SMS (förhandsgranskning)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: 845948d9aec28ee79a11fb11aaef4cfbf1b263fa
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770555"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>Konfigurera och aktivera användare för SMS-baserad autentisering med Azure Active Directory (förhandsversion)

För att minska komplexiteten och säkerhetsriskerna för användare att logga in på program och tjänster innehåller Azure Active Directory (Azure AD) flera autentiseringsalternativ. SMS-baserad autentisering, för närvarande i förhandsgranskning, låter användare logga in utan att behöva ge, eller ens veta, deras användarnamn och lösenord. När kontot har skapats av en identitetsadministratör kan de ange sitt telefonnummer vid inloggningsprompten och tillhandahålla en autentiseringskod som skickas till dem via sms. Den här autentiseringsmetoden förenklar åtkomsten till program och tjänster, särskilt för frontlinjearbetare.

Den här artikeln visar hur du aktiverar SMS-baserad autentisering för utvalda användare eller grupper i Azure AD.

|     |
| --- |
| SMS-baserad autentisering för användare är en offentlig förhandsgranskningsfunktion i Azure Active Directory. Mer information om förhandsgranskningar finns i [Tilläggsvillkor för användning för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="before-you-begin"></a>Innan du börjar

För att kunna slutföra den här artikeln behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* Du behöver globala administratörsbehörighet i din Azure AD-klient för att aktivera SMS-baserad autentisering. *global administrator*
* Varje användare som är aktiverad i princip för textmeddelandeautentiseringsmetod måste licensieras, även om de inte använder den. Varje aktiverad användare måste ha någon av följande Azure AD- eller Microsoft 365-licenser:
    * [Azure AD Premium P1 eller P2][azuread-licensing]
    * [Microsoft 365 (M365) F1 eller F3][m365-firstline-workers-licensing]
    * [Enterprise Mobility + Security (EMS) E3 eller E5][ems-licensing] eller [Microsoft 365 (M365) E3 eller E5][m365-licensing]

## <a name="limitations"></a>Begränsningar

Under den offentliga förhandsversionen av SMS-baserad autentisering gäller följande begränsningar:

* SMS-baserad autentisering är för närvarande inte kompatibel med Azure Multi-Factor Authentication.
* Med undantag för Teams är SMS-baserad autentisering för närvarande inte kompatibel med inbyggda Office-program.
* SMS-baserad autentisering rekommenderas inte för B2B-konton.
* Federerade användare autentiseras inte i hemklienten. De autentiserar bara i molnet.

## <a name="enable-the-sms-based-authentication-method"></a>Aktivera den SMS-baserade autentiseringsmetoden

Det finns tre huvudsteg för att aktivera och använda SMS-baserad autentisering i din organisation:

* Aktivera principen för autentiseringsmetod.
* Välj användare eller grupper som kan använda den SMS-baserade autentiseringsmetoden.
* Tilldela ett telefonnummer för varje användarkonto.
    * Det här telefonnumret kan tilldelas i Azure-portalen (som visas i den här artikeln) och i *Min personal* eller *Min profil*.

Låt oss först aktivera SMS-baserad autentisering för din Azure AD-klientorganisation.

1. Logga in på [Azure-portalen][azure-portal] som *global administratör*.
1. Sök efter och välj **Azure Active Directory**.
1. Välj **Säkerhet > autentiseringsmetoder > autentiseringsmetodprincip (förhandsversion) på navigeringsmenyn**till vänster i Azure Active Directory-fönstret .

    [![](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png "Browse to and select the Authentication method policy (preview) window in the Azure portal")](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. Välj **Textmeddelande**i listan över tillgängliga autentiseringsmetoder .
1. Ange **Aktivera** till *Ja*.

    ![Aktivera textautentisering i principfönstret för autentiseringsmetod](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    Du kan välja att aktivera SMS-baserad autentisering för *alla användare* eller *Välj användare* och grupper. I nästa avsnitt aktiverar du SMS-baserad autentisering för en testanvändare.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>Tilldela autentiseringsmetoden till användare och grupper

Med SMS-baserad autentisering aktiverad i din Azure AD-klient väljer du nu vissa användare eller grupper som ska tillåtas att använda den här autentiseringsmetoden.

1. Ange **Mål** till *Select-användare*i principfönstret för textmeddelandeautentisering .
1. Välj att **lägga till användare eller grupper**och välj sedan en testanvändare eller grupp, till exempel *Contoso User* eller *Contoso SMS Users*.

    [![](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png "Choose users or groups to enable for SMS-based authentication in the Azure portal")](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. När du har valt dina användare eller grupper väljer du **Välj**och sedan **Spara** den uppdaterade principen för autentiseringsmetod.

Varje användare som är aktiverad i princip för textmeddelandeautentiseringsmetod måste licensieras, även om de inte använder den. Kontrollera att du har rätt licenser för de användare som du aktiverar i principen om autentiseringsmetod, särskilt när du aktiverar funktionen för stora grupper av användare.

## <a name="set-a-phone-number-for-user-accounts"></a>Ange ett telefonnummer för användarkonton

Användare är nu aktiverade för SMS-baserad autentisering, men deras telefonnummer måste associeras med användarprofilen i Azure AD innan de kan logga in. Användaren kan [ange det här telefonnumret själva](../user-help/sms-sign-in-explainer.md) i Min *profil*eller tilldela telefonnumret med Azure-portalen. Telefonnummer kan anges av *globala administratörer,* *autentiseringsadministratörer*eller *administratörer för privilegierad autentisering*.

När ett telefonnummer är inställt för SMS-sign, är det också då tillgängligt för användning med [Azure Multi-Factor Authentication][tutorial-azure-mfa] och [självbetjäning lösenordsåterställning][tutorial-sspr].

1. Sök efter och välj **Azure Active Directory**.
1. Välj **Användare**på navigeringsmenyn till vänster i Azure Active Directory-fönstret .
1. Välj den användare som du har aktiverat för SMS-baserad autentisering i föregående avsnitt, till exempel *Contoso User*, och välj sedan **Autentiseringsmetoder**.
1. Ange användarens telefonnummer, inklusive landskoden, till exempel *+1 xxxxxxxxx*. Azure-portalen validerar telefonnumret är i rätt format.

    ![Ange ett telefonnummer som en användare i Azure-portalen ska använda med SMS-baserad autentisering](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    Telefonnumret måste vara unikt i din klient. Om du försöker använda samma telefonnummer för flera användare visas ett felmeddelande.

1. Om du vill använda telefonnumret på en användares konto väljer du **Spara**.

När det har etablerats visas en bock för *SMS-inloggning aktiverad*.

## <a name="test-sms-based-sign-in"></a>Testa SMS-baserad inloggning

Så här testar du användarkontot som nu är aktiverat för SMS-baserad inloggning:

1. Öppna ett nytt webbläsarfönster InPrivate eller Incognito för att[https://www.office.com][office]
1. I det övre högra hörnet väljer du **Logga in**.
1. Vid inloggningsprompten anger du det telefonnummer som är kopplat till användaren i föregående avsnitt och väljer sedan **Nästa**.

    ![Ange ett telefonnummer vid inloggningsprompten för testanvändaren](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Ett sms skickas till det angivna telefonnumret. Om du vill slutföra inloggningsprocessen anger du den 6-siffriga koden som finns i sms:et vid inloggningsprompten.

    ![Ange bekräftelsekoden som skickas via sms till användarens telefonnummer](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. Användaren är nu inloggad utan att behöva ange ett användarnamn eller lösenord.

## <a name="troubleshoot-sms-based-sign-in"></a>Felsöka SMS-baserad inloggning

Följande scenarier och felsökningssteg kan användas om du har problem med att aktivera och använda SMS-baserad inloggning.

### <a name="phone-number-already-set-for-a-user-account"></a>Telefonnummer som redan angetts för ett användarkonto

Om en användare redan har registrerat sig för Azure Multi-Factor Authentication och / eller självbetjäning lösenordsåterställning (SSPR), har de redan ett telefonnummer som är kopplat till sitt konto. Det här telefonnumret är inte automatiskt tillgängligt för användning med SMS-baserad inloggning.

En användare som redan har angett ett telefonnummer för sitt konto visas en knapp *för Aktivera för SMS-inloggning* på sidan **Min profil.** Välj den här knappen och kontot är aktiverat för användning med SMS-baserad inloggning och den tidigare Azure Multi-Factor-autentiseringen eller SSPR-registreringen.

Mer information om slutanvändarens upplevelse finns [i SMS-inloggningsanvändarupplevelsen för telefonnummer (förhandsversion)](../user-help/sms-sign-in-explainer.md).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Fel vid försök att ange ett telefonnummer på en användares konto

Om du får ett felmeddelande när du försöker ange ett telefonnummer för ett användarkonto i Azure-portalen läser du följande felsökningssteg:

1. Kontrollera att du är aktiverad för den SMS-baserade förhandsversionen av inloggningen.
1. Bekräfta att användarkontot är aktiverat i principen för autentiseringsmetod för *sms.*
1. Se till att du anger telefonnumret med rätt formatering, som validerats i Azure-portalen (till exempel *+1 4251234567*).
1. Kontrollera att telefonnumret inte används någon annanstans i din klientorganisation.
1. Kontrollera att det inte finns något röstnummer inställt på kontot. Om ett röstnummer är inställt tar du bort och försöker till telefonnumret igen.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du loggar in på Azure AD utan lösenord, till exempel säkerhetsnycklarna i Microsoft Authenticator-appen eller FIDO2, finns i [Lösenordslösa autentiseringsalternativ för Azure AD][concepts-passwordless].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
