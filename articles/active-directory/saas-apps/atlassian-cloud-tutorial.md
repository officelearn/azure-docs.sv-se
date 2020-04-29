---
title: 'Självstudie: Azure Active Directory integrering med Atlassian Cloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46c48d6f7d751e6d767921fad21e6213c6b1203b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80384032"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Självstudie: integrera Atlassian Cloud med Azure Active Directory

I den här självstudien får du lära dig att integrera Atlassian Cloud med Azure Active Directory (Azure AD). När du integrerar Atlassian Cloud med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Atlassian-molnet.
* Gör det möjligt för användarna att logga in automatiskt till Atlassian-molnet med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en månads kostnads fri utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* Atlassian-prenumeration med enkel inloggning (SSO) för molnet.
* Om du vill aktivera enkel inloggning med SAML för Atlassian Cloud-produkter måste du konfigurera Atlassian-åtkomst. Läs mer om [Atlassians-åtkomst]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. 

* Atlassian Cloud stöder **SP- och IDP**-initierad enkel inloggning
* Atlassian Cloud stöder [Automatisk användar etablering och avetablering](atlassian-cloud-provisioning-tutorial.md)
* När du har konfigurerat Atlassian-molnet kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
## <a name="adding-atlassian-cloud-from-the-gallery"></a>Lägga till Atlassian Cloud från galleriet

Om du vill konfigurera integreringen av Atlassian Cloud i Azure AD måste du lägga till Atlassian Cloud från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Atlassian Cloud** i sökrutan.
1. Välj **Atlassian Cloud** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Atlassian-molnet med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Atlassian-molnet.

Om du vill konfigurera och testa Azure AD SSO med Atlassian-molnet slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Atlassian Cloud SSO](#configure-atlassian-cloud-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    * **[Skapa Atlassian Cloud test User](#create-atlassian-cloud-test-user)** – för att få en motsvarighet till B. Simon i Atlassian-molnet som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Atlassian Cloud** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster:`https://auth.atlassian.com/saml/<unique ID>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster:`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Klicka på **Ange ytterligare URL:er**.

    d. Skriv en URL med följande mönster i textrutan **Vidarebefordransstatus**: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Föregående värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL:en. Du får de här verkliga värdena från skärmen **Atlassian Cloud SAML-konfiguration** , som beskrivs senare i steg 7 i **Konfigurera Atlassian-molnet SSO** i självstudien.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:`https://<instancename>.atlassian.net`

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Klistra in värdet från den instans som du använder för att logga in på Atlassian Cloud admin-portalen.

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. Ditt Atlassian Cloud-program förväntar sig SAML-intygen i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i SAML-tokenattributkonfigurationen. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Atlassian Cloud-programmet förväntar sig att **nameidentifier** mappas med **user.mail**, så du behöver redigera attributmappningen genom att klicka på ikonen **Redigera** och ändra attributmappningen.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Atlassian Cloud** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Atlassian-molnet.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Atlassian Cloud**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-atlassian-cloud-sso"></a>Konfigurera Atlassian-molnets SSO

1. Om du vill automatisera konfigurationen i Atlassian-molnet måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren kan du klicka på **Atlassian Cloud** för att dirigera dig till Atlassian Cloud Application. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Atlassian-molnet. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera Atlassian-molnet manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Atlassian-moln företags webbplats som administratör och utför följande steg:

1. Du måste verifiera din domän innan du fortsätter med att konfigurera enkel inloggning. Mer information finns i dokumentet [Atlassian-domänverifiering](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

1. I den vänstra rutan väljer du **säkerhet** > **för SAML enkel inloggning**. Prenumerera på Atlassian Identity Manager om du inte redan gör det.

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

1. Gör följande i fönstret **Lägg till SAML-konfiguration**:

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. I rutan **entitets-ID för identitetsprovider** klistrar du in **Azure AD-identifieraren** som du kopierade från Azure Portal.

    b. I rutan **URL för identitets leverantörens SSO-URL** klistrar du in den **inloggnings-URL** som du kopierade från Azure Portal.

    c. Öppna det nedladdade certifikatet från Azure Portal i en txt-fil, kopiera värdet (utan raderna *Starta certifikat* och *Avsluta certifikat*) och klistra in det i rutan **Offentligt X509-certifikat**.

    d. Klicka på **Spara konfiguration**.

1. Säkerställ att du har konfigurerat rätt URL:er genom att uppdatera Azure AD-inställningarna på följande sätt:

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. I SAML-fönstret kopierar du **SP Identity ID** och i Azure Portal under Atlassian Cloud **Basic SAML Configuration**, klistrar du in det i rutan **identifierare** .

    b. I SAML-fönstret kopierar du **URL: en för tjänsten för försäkrans konsument tjänst** och klistrar sedan in den i rutan **svars-URL** i Azure Portal under Atlassian Cloud **Basic SAML Configuration**. Inloggnings-URL:en är klient-URL:en för ditt Atlassian-moln.

    > [!NOTE]
    > Välj **Ja, uppdatera konfigurationen**, om du är en befintlig kund, när du har uppdaterat värdena för **SP-identitets-ID** och **URL för SP-konsumenttjänst för försäkran** i Azure Portal. Om du är en ny kund kan du hoppa över det här steget.

### <a name="create-atlassian-cloud-test-user"></a>Skapa testanvändare för Atlassian Cloud

Om du vill aktivera Azure AD-användare så att de kan logga in på Atlassian Cloud måste du etablera användarkontona manuellt i Atlassian Cloud genom att göra följande:

1. Välj **Användare** på **Administration**-panelen.

    ![Atlassian Cloud-användarlänken](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Välj **Bjud in användare** om du vill skapa en användare i Atlassian Cloud.

    ![Skapa en Atlassian Cloud-användare](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. Ange användarens e-postadress i rutan **E-postadress** och tilldela sedan programmet åtkomst.

    ![Skapa en Atlassian Cloud-användare](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Välj **Bjud in användare** om du vill skicka en e-postinbjudan till användaren. En e-postinbjudan skickas till användaren och efter det att användaren har accepterat inbjudan aktiveras hen i systemet.

> [!NOTE]
> Du kan också skapa flera användare genom att använda knappen **Skapa flera** i avsnittet **Användare**.

### <a name="test-sso"></a>Testa SSO

När du väljer moln panelen Atlassian i åtkomst panelen, bör du loggas in automatiskt till det Atlassian-moln som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Atlassian Cloud med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Atlassian-molnet med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)