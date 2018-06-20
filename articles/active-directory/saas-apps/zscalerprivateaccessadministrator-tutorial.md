---
title: 'Självstudier: Azure Active Directory-integrering med Zscaler privata RAS-administratören | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler privata RAS-administratören.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: 25517f6f04b43152c72ad1f223e12456aac89a00
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221401"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Självstudier: Azure Active Directory-integrering med Zscaler privat åtkomst administratör

I kursen får lära du att integrera Zscaler privat åtkomst administratör med Azure Active Directory (AD Azure).

Integrera Zscaler privat åtkomst administratör med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Zscaler privata RAS-administratören.
- Du kan aktivera användarna att automatiskt hämta inloggade till Zscaler privat åtkomst administratör (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Zscaler privata administratören behöver du följande:

- En Azure AD-prenumeration
- En Zscaler privata administratören enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till administratören Zscaler privata från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Att lägga till administratören Zscaler privata från galleriet
Du måste lägga till administratören Zscaler privata från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Zscaler privat åtkomst administratör i Azure AD.

**Utför följande steg för att lägga till administratören Zscaler privata från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Zscaler privata administratören**väljer **Zscaler privata administratören** resultatet-panelen klickar **Lägg till** för att lägga till den programmet.

    ![Zscaler privata administratören i resultatlistan](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Zscaler privata administratören baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Zscaler privat åtkomst administratör till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Zscaler privat åtkomst administratör upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Zscaler privat åtkomst administratör, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Zscaler privata administratören](#create-a-zscaler-private-access-administrator-test-user)**  – du har en motsvarighet för Britta Simon i Zscaler privata administratören som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Zscaler privata RAS-administratören.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Zscaler privat åtkomst administratör:**

1. I Azure-portalen på den **Zscaler privata administratören** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_samlbase.png)

3. På den **Zscaler privata administratören domän och URL: er** avsnittet om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Zscaler privata administratören domän med enkel inloggning information](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Kontrollera **visa avancerade URL-inställningar**

    d. I den **RelayState** textruta, ange ett värde: `idpadminsso`

4.  Om du vill konfigurera programmet i **SP** initierade läge utför följande steg:

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.private.zscaler.com/auth/sso`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare, Reply URL och inloggning-URL. Kontakta [Zscaler privata administratören supportteamet](https://help.zscaler.com/zpa-submit-ticket) att hämta dessa värden.
 
5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_400.png)

7. I en annan webbläsarfönstret, inloggning till Zscaler privata administratören som administratör.

8. Klicka på överst **Administration** och gå till **AUTENTISERING** avsnittet klickar du på **IdP Configuration**.

    ![Zscaler privat åtkomst administratör admin](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

9. I övre högra hörnet, klickar du på **Lägg till IdP konfiguration**. 

    ![Zscaler privata administratören addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

10. På den **Lägg till IdP konfiguration** sidan utför följande steg:
 
    ![Zscaler privata administratören idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Klicka på **Välj fil** att ladda upp den hämta filen Metadata från Azure AD i den **IdP Metadata filöverföring** fältet.

    b. Det läser de **IdP metadata** från Azure AD och fylls fälten informationen som visas nedan.

    ![Zscaler privata administratören idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Välj **enkel inloggning** som **administratör**.

    d. Välj din domän från **domäner** fältet.
    
    e. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-a-zscaler-private-access-administrator-test-user"></a>Skapa en testanvändare Zscaler privat åtkomst administratör

De måste etableras i Zscaler privata RAS-administratören om du vill aktivera Azure AD-användare kan logga in till Zscaler privata RAS-administratören. När det gäller Zscaler privat åtkomst administratör etablering är en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på webbplatsen administratören Zscaler privata företag som administratör.

2. Klicka på överst **Administration** och gå till **AUTENTISERING** avsnittet klickar du på **IdP Configuration**.

    ![Zscaler privat åtkomst administratör admin](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klicka på **administratörer** från vänster sida av menyn.

    ![Administratören Zscaler privata administratör](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. I övre högra hörnet, klickar du på **Lägg till administratör**:

    ![Zscaler privata administratören lägga till administratör](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. I den **Lägg till administratör** utför följande steg:

    ![Administratören Zscaler privata Användaradministration](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. I den **användarnamn** textruta ange e-postadress för användaren som **BrittaSimon@contoso.com**.

    b. I den **lösenord** textruta skriver du lösenordet.

    c. I den **Bekräfta lösenord** textruta skriver du lösenordet.

    d. Välj **rollen** som **Zscaler privata administratören**.

    e. I den **e-post** textruta ange e-postadress för användaren som **BrittaSimon@contoso.com**.

    f. I den **Phone** textruta ange telefonnumret.

    g. I den **tidszonen** textruta Välj tidszonen.

    h. Klicka på **Spara**.  

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Zscaler privata RAS-administratören.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon till Zscaler privat åtkomst administratör, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Zscaler privata administratören**.

    ![Länken Zscaler privat åtkomst administratör i listan med program](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Zscaler privata administratören på åtkomstpanelen du bör få automatiskt inloggade i tillämpningsprogrammet Zscaler privata RAS-administratören.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_01.png
[2]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_02.png
[3]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_03.png
[4]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_04.png

[100]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_100.png

[200]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_200.png
[201]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_201.png
[202]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_202.png
[203]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_203.png

