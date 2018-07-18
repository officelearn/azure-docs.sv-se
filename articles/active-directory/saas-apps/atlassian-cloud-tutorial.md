---
title: 'Självstudier: Azure Active Directory-integrering med Atlassians moln | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Atlassians molnet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2018
ms.author: jeedes
ms.openlocfilehash: 68613b8613a2e5a9139b83eb23e66884659efc47
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114942"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Självstudier: Azure Active Directory-integration med Atlassians molnet

I den här självstudien får du lära dig hur du integrerar Atlassians molnet med Azure Active Directory (AD Azure).

Integrera Atlassians molnet med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Atlassians moln.
- Du kan ge användarna vara inloggad automatiskt (enkel inloggning) till Atlassians moln med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats, Azure-portalen.

Mer information om programvara som en tjänst (SaaS) app-integrering med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Atlassians molnet, behöver du följande objekt:

- En Azure AD-prenumeration.
- Om du vill aktivera Security Assertion Markup Language (SAML) enkel inloggning för Atlassians molnprodukter, måste du konfigurera Atlassians åtkomst. Läs mer om [Atlassians åtkomst]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> När du testar stegen i den här självstudien, rekommenderar vi att du inte använder en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i kursen består av två viktigaste byggstenarna:

* Att lägga till Atlassians molnet från galleriet
* Konfigurera och testa Azure AD enkel inloggning

## <a name="add-atlassian-cloud-from-the-gallery"></a>Lägg till Atlassians molnet från galleriet
Om du vill konfigurera integreringen av Atlassians molnet med Azure AD, lägga till Atlassians molnet från galleriet i listan över hanterade SaaS-appar genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com), i rutan till vänster väljer du den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen][1]

2. Välj **företagsprogram** > **alla program**.

    ![Fönstret för Enterprise-program][2]
    
3. Om du vill lägga till ett program, Välj **nytt program**.

    ![”Det nya programmet” knappen][3]

4. I sökrutan skriver **Atlassians molnet**, i resultatlistan väljer **Atlassians molnet**, och välj sedan **Lägg till**.

    ![Atlassians molnet i resultatlistan](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Atlassians molnet, baserat på en användare med namnet *Britta Simon*.

För enkel inloggning att fungera, behöver Azure AD identifiera Atlassians molnanvändnings och dess motsvarighet i Azure AD. Med andra ord måste du upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Atlassians molnet.

För att upprätta länken relationen, tilldela som molnet Atlassians *användarnamn* samma värde som tilldelas till Azure AD *användarnamn*.

Om du vill konfigurera och testa Azure AD enkel inloggning med Atlassians moln, måste du slutföra byggblocken i följande avsnitt.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Atlassians molnprogram.

Om du vill konfigurera Azure AD enkel inloggning med Atlassians molnet, gör du följande:

1. I Azure-portalen i den **Atlassians molnet** application integration väljer **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. I den **enkel inloggning** fönstret i den **läge för enkel inloggning** väljer **SAML-baserad inloggning**.

    ![Fönstret för enkel inloggning](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Konfigurera programmet i **IDP-initierad** läge under **Atlassians molnet domän och URL: er**, gör du följande:

    ![Atlassians molnet domän och URL: er med enkel inloggning för information](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. I den **identifierare** skriver du en URL med följande mönster: `https://auth.atlassian.com/saml/<unique ID>`.
    
    b. I den **svars-URL** skriver du en URL med följande mönster: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`.

    c. Kontrollera **visa avancerade URL-inställningar**.

    d. I den **Vidarebefordransstatus** skriver du en URL med följande mönster: `https://<instancename>.atlassian.net`.

    > [!NOTE]
    > Föregående värden är inte verkliga. Uppdatera dessa värden med det faktiska ID: t och svars-URL. Du får dessa verkliga värden från skärmen Atlassians Cloud SAML-konfiguration som beskrivs senare i självstudien.

4. För att konfigurera programmet i SP-initierat läge, Välj den **visa avancerade URL-inställningar** och klicka sedan på **inloggnings-URL** skriver du en URL med följande mönster: `https://<instancename>.atlassian.net`.

    ![Atlassians molnet domän och URL: er med enkel inloggning för information](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    > [!NOTE]
    > Föregående inloggnings-URL-värdet är inte verkliga. Uppdatera värdet med det faktiska URL: en för inloggning. Kontakta [Atlassians Cloud klienten supportteamet](https://support.atlassian.com/) att hämta det här värdet.

5. Under **SAML-signeringscertifikat**väljer **Certificate(Base64)**, och sedan spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png)

6. Ditt Atlassians molnprogram förväntar sig att hitta SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-Tokenattribut konfigurationen. 

    Som standard den **användaridentifierare** värdet mappas till user.userprincipalname. Ändra det här värdet ska mappas till user.mail. Du kan också välja ett annat lämpligt värde enligt din organisations inställningar, men i de flesta fall kan e-post ska fungera.

    ![Länk för hämtning av certifikat](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png)

7. Välj **Spara**.

    ![Den Konfigurera enkel inloggning spara knappen](./media/atlassian-cloud-tutorial/tutorial_general_400.png)

8. Öppna den **konfigurera inloggning** fönstret i den **Atlassians Molnkonfigurationen** väljer **konfigurera Atlassians molnet**.

9. I den **Snabbreferens** avsnittet, kopiera den **SAML entitets-ID** och **SAML enkel inloggning för tjänst-URL**.

    ![Atlassians molnkonfigurationen](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png)

10. För att få SSO konfigurerats för ditt program kan du logga in på Atlassians-portalen med administratörsbehörighet.

11. Du behöver verifiera din domän innan du fortsätter att konfigurera enkel inloggning. Mer information finns i [Atlassians domänverifiering](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) dokumentet.

12. I den vänstra rutan väljer **SAML enkel inloggning**. Om du inte redan gjort det, kan du prenumerera till Atlassians Identity Manager.

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

13. I den **lägga till SAML-konfiguration** fönstret gör du följande:

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. I den **identitetsprovider entitets-ID** rutan, klistra in SAML entitets-ID som du kopierade från Azure-portalen.

    b. I den **identitetsprovider SSO URL** rutan, klistra in Webbadressen för SAML enkel inloggning som du kopierade från Azure-portalen.

    c. Öppna det nedladdade certifikatet från Azure-portalen i en txt-fil, Kopiera värdet (utan den *börjar certifikat* och *End Certificate* rader), och klistra in den i den **offentliga X509 certifikatet** box.
    
    d. Välj **spara konfigurationen**.
     
14. Uppdatera Azure AD-inställningarna för att säkerställa att du har ställt in rätt URL: er, genom att göra följande:

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. I fönstret SAML kopiera den **SP identitets-ID** och sedan i Azure-portalen under Atlassians molnet **domän och URL: er**, klistra in den i den **identifierare** box.
    
    b. I fönstret SAML kopiera den **SP URL för Konsumenttjänst för försäkran** och sedan i Azure-portalen under Atlassians molnet **domän och URL: er**, klistra in den i den **svars-URL** box. Inloggnings-URL: en är klient-URL för din Atlassians moln.

    > [!NOTE]
    > Om du inte har en befintlig kund, när du har uppdaterat den **SP identitets-ID** och **SP URL för Konsumenttjänst för försäkran** värden i Azure portal, markera **Ja, uppdatera konfigurationen**. Om du är en ny kund kan du hoppa över det här steget.
    
15. I Azure-portalen väljer du **spara**.

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial_general_400.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du testanvändare Britta Simon i Azure portal genom att göra följande:

   ![Skapa en Azure AD-testanvändare][100]

1. I Azure-portalen, i rutan till vänster väljer du den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/atlassian-cloud-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, Välj **användare och grupper** > **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/atlassian-cloud-tutorial/create_aaduser_02.png)

3. I den **alla användare** väljer **Lägg till**.

    ![Knappen Lägg till](./media/atlassian-cloud-tutorial/create_aaduser_03.png)

4. I den **användaren** fönstret gör du följande:

    ![Fönstret användare](./media/atlassian-cloud-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Välj **Skapa**.

### <a name="create-an-atlassian-cloud-test-user"></a>Skapa en testanvändare Atlassians molnet

Om du vill aktivera Azure AD-användare att logga in på molnet Atlassians etablera användarkonton manuellt i Atlassians molnet genom att göra följande:

1. I den **Administration** väljer **användare**.

    ![Länken Atlassians Molnanvändare](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Om du vill skapa en användare i Atlassians molnet, Välj **inbjudan användaren**.

    ![Skapa en Atlassians Cloud-användare](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. I den **e-postadress** rutan, ange användarens e-postadress och tilldela sedan programmet åtkomst.

    ![Skapa en Atlassians Cloud-användare](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. Om du vill skicka ett e-postinbjudan till användaren, Välj **bjuda in användare**. En e-postinbjudan skickas till användaren och efter att acceptera inbjudan, användaren är aktiv i systemet.

>[!NOTE]
>Du kan också bulk-skapa användare genom att välja den **Bulk skapa** knappen i den **användare** avsnittet.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du användaren Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Atlassians moln. Du gör detta genom att göra följande:

![Tilldela rollen][200]

1. I Azure-portalen öppnar du den **program** visa, gå till vyn directory och välj sedan **företagsprogram** > **alla program**.

    ![Tilldela användare][201]

2. I den **program** väljer **Atlassians molnet**.

    ![Länken Atlassians molnet i listan med program](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)

3. I den vänstra rutan väljer **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj **Lägg till** och klicka sedan på **Lägg till tilldelning** väljer **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** fönstret i den **användare** väljer **Britta Simon**.

6. I den **användare och grupper** väljer **Välj**.

7. I den **Lägg till tilldelning** väljer **tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer den **Atlassians molnet** panelen i åtkomstpanelen, du bör vara inloggad automatiskt till ditt Atlassians molnprogram.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/atlassian-cloud-tutorial/tutorial_general_203.png
