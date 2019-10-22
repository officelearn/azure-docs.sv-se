---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Veracode | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50056e6b201313805e7b9253e7a962e096b9df65
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72372979"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Veracode

I den här självstudien får du lära dig hur du integrerar Veracode med Azure Active Directory (Azure AD). När du integrerar Veracode med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Veracode.
* Gör det möjligt för användarna att logga in automatiskt till Veracode med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Veracode för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Veracode stöder **IDP** INITIERAd SSO
* Veracode stöder **just-in-Time** User-etablering

## <a name="adding-veracode-from-the-gallery"></a>Lägga till Veracode från galleriet

Om du vill konfigurera integreringen av Veracode i Azure AD måste du lägga till Veracode från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Veracode** i sökrutan.
1. Välj **Veracode** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>Konfigurera och testa enkel inloggning med Azure AD för Veracode

Konfigurera och testa Azure AD SSO med Veracode med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Veracode.

Om du vill konfigurera och testa Azure AD SSO med Veracode, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera VERACODE SSO](#configure-veracode-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa Veracode test User](#create-veracode-test-user)** -om du vill ha en motsvarighet till B. Simon i Veracode som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Veracode** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga URL: erna är redan ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Veracode-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![mallar](common/default-attributes.png)

1. Utöver ovan förväntar sig Veracode-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ---------------| --------------- |
    | förnamn |User. givenName |
    | efternamn |användare. efter namn |
    | e-post |User.mail |

1. I avsnittet **Konfigurera Veracode** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>Konfigurera Veracode SSO

1. Logga in på din Veracode-företags webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på **Inställningar**på menyn högst upp och klicka sedan på **admin**.
   
    ![Administrationsverktyg](./media/veracode-tutorial/ic802911.png "Administration")

1. Klicka på fliken **SAML** .

1. I avsnittet **organisations SAML-inställningar** utför du följande steg:

    ![Administrationsverktyg](./media/veracode-tutorial/ic802912.png "Administration")

    a.  I text rutan **utfärdare** klistrar du in värdet för **Azure AD-identifieraren** som du kopierade från Azure Portal.

    b. Klicka på **Välj fil**om du vill ladda upp det hämtade certifikatet från Azure Portal.

    c. Välj **Aktivera själv registrering**.

1. I avsnittet **Inställningar för själv registrering** utför du följande steg och klickar sedan på **Spara**:

    ![Administrationsverktyg](./media/veracode-tutorial/ic802913.png "Administration")

    a. Välj **ingen aktivering**som **ny användar aktivering**krävs.

    b. Välj **Preference Veracode user data**som **uppdateringar av användar data**.

    c. För **information om SAML-attribut**väljer du följande:
      * **Användar roller**
      * **Princip administratör**
      * **Granskare**
      * **Säkerhets lead**
      * **Executive**
      * **Jobbets avsändare**
      * **Skapare**
      * **Alla genomsöknings typer**
      * **Grupp medlemskap**
      * **Standard team**

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Veracode.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Veracode**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-veracode-test-user"></a>Skapa Veracode test användare

För att Azure AD-användare ska kunna logga in på Veracode måste de tillhandahållas i Veracode. När det gäller Veracode är etableringen en automatiserad uppgift. Det finns inget åtgärds objekt för dig. Användare skapas automatiskt vid behov under det första försöket vid enkel inloggning.

> [!NOTE]
> Du kan använda andra verktyg för Veracode av användar konton eller API: er som tillhandahålls av Veracode för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Veracode på åtkomst panelen, bör du loggas in automatiskt på den Veracode som du ställer in SSO för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Veracode med Azure AD](https://aad.portal.azure.com/)