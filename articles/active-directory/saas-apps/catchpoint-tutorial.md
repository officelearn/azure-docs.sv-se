---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Catchpoint | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Catchpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab3eead7-8eb2-4c12-bb3a-0e46ec899d37
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c56d34a331821ffbc3d0d4f2cf5e9b033f4011ff
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968504"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-catchpoint"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Catchpoint

I den här självstudien får du lära dig hur du integrerar Catchpoint med Azure Active Directory (Azure AD). När du integrerar Catchpoint med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Catchpoint.
* Gör det möjligt för användarna att logga in automatiskt till Catchpoint med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Catchpoint för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Catchpoint stöder **SP-och IDP** -INITIERAd SSO
* Catchpoint stöder **just-in-Time** User-etablering
* När du har konfigurerat Catchpoint kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-catchpoint-from-the-gallery"></a>Lägga till Catchpoint från galleriet

Om du vill konfigurera integreringen av Catchpoint i Azure AD måste du lägga till Catchpoint från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Catchpoint** i sökrutan.
1. Välj **Catchpoint** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>Konfigurera och testa enkel inloggning med Azure AD för Catchpoint

Konfigurera och testa Azure AD SSO med Catchpoint med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Catchpoint.

Om du vill konfigurera och testa Azure AD SSO med Catchpoint, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera CATCHPOINT SSO](#configure-catchpoint-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa Catchpoint test User](#create-catchpoint-test-user)** -om du vill ha en motsvarighet till B. Simon i Catchpoint som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Catchpoint** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP** initierat läge anger du värdena för följande fält:

    a. I textrutan **Identifierare** skriver du URL:en: `https://portal.catchpoint.com/SAML2`

    b. Skriv webb adressen i text rutan **svars-URL** : `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Catchpoint-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig Catchpoint-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ------------ | --------- |
    | namnområde | user.assignedrole |

    > [!NOTE]
    > namn områdes anspråk måste mappas med konto namnet. Det här konto namnet ska installeras som roller i Azure AD som kommer att skickas tillbaka i SAML-svar. Mer information om hur du konfigurerar rollerna finns i den här [artikeln](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) .

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Catchpoint** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **Ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Catchpoint.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Catchpoint**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-catchpoint-sso"></a>Konfigurera Catchpoint SSO

1. Logga in på Catchpoint-programmet som administratör i ett annat webbläsarfönster.

1. Klicka på **inställnings** ikonen och välj **SSO Identity Provider**.

    ![Catchpoint-konfiguration](./media/catchpoint-tutorial/configuration1.png)

1. Utför följande steg på sidan **enkel inloggning** :

    ![Catchpoint-konfiguration](./media/catchpoint-tutorial/configuration2.png)

    1. Ange ett giltigt namn områdes värde i text **områdets** text ruta.

    1. I textrutan **Identity Provider Issuer** (Utfärdare av identitetsprovider) anger du värdet för den **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    1. I text rutan **enkel inloggnings-URL** anger du **inloggnings-URL** -värdet, som du har kopierat från Azure Portal.

    1. Öppna den hämtade **certifikat filen (base64)** i anteckningar, kopiera innehållet i certifikat filen och klistra in den i **certifikat** text rutan.

    1. Du kan också ladda upp **XML-metadata för federationsmetadata** genom att klicka på alternativet **överför metadata** .

    1. Klicka på **Save** (Spara).

### <a name="create-catchpoint-test-user"></a>Skapa Catchpoint test användare

I det här avsnittet skapas en användare som kallas Britta Simon i Catchpoint. Catchpoint stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Catchpoint skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Catchpoint på åtkomst panelen, bör du loggas in automatiskt på den Catchpoint som du ställer in SSO för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

> [!NOTE]
> När du loggar in på Catchpoint-programmet via inloggnings sidan anger du det giltiga **namn område** svärdet i text rutan **företagets autentiseringsuppgifter (SSO)** när du har angett **Catchpoint-autentiseringsuppgifter**och klickar sedan på **Logga in**.

![Catchpoint-konfiguration](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Catchpoint med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)