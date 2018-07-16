---
title: 'Självstudier: Azure Active Directory-integration med Onit | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Onit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: 330dbf31a1c1af6146ae1272a42ecc3621271bb1
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046577"
---
# <a name="tutorial-azure-active-directory-integration-with-onit"></a>Självstudier: Azure Active Directory-integration med Onit

I den här självstudien får du lära dig hur du integrerar Onit med Azure Active Directory (AD Azure).

Integrera Onit med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Onit.
- Du kan aktivera användarna att automatiskt få loggat in på Onit (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Onit, behöver du följande objekt:

- En Azure AD-prenumeration
- En Onit enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Onit från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-onit-from-the-gallery"></a>Att lägga till Onit från galleriet
För att konfigurera integrering av Onit i Azure AD, som du behöver lägga till Onit från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Onit från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Onit**väljer **Onit** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Onit i resultatlistan](./media/onit-tutorial/tutorial_onit_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Onit baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Onit är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Onit upprättas.

I Onit, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Onit, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Onit](#create-an-onit-test-user)**  – du har en motsvarighet för Britta Simon i Onit som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Onit program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Onit:**

1. I Azure-portalen på den **Onit** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/onit-tutorial/tutorial_onit_samlbase.png)

3. På den **Onit domän och URL: er** avsnittet, utför följande steg:

    ![Onit domän och URL: er med enkel inloggning för information](./media/onit-tutorial/tutorial_onit_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<sub-domain>.onit.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<sub-domain>.onit.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [Onit klienten supportteamet](https://www.onit.com/support) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCK** värdet för certifikatet.

    ![Länk för hämtning av certifikat](./media/onit-tutorial/tutorial_onit_certificate.png) 

5. Onit program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **”Atrribute”** fliken av programmet. Följande skärmbild visar ett exempel för detta. 

    ![Konfigurera enkel inloggning](./media/onit-tutorial/tutorial_onit_attribute.png) 

6. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ------------------- | -------------------- |
    | e-post | User.Mail |
    
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/onit-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/onit-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Lämna den **Namespace** tom.
    
    e. Klicka på **OK**.

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/onit-tutorial/tutorial_general_400.png)

8. På den **Onit Configuration** klickar du på **konfigurera Onit** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning, enkel inloggning för tjänsten URL för SAML** från den **Snabbreferens avsnittet.**

    ![Onit konfiguration](./media/onit-tutorial/tutorial_onit_configure.png)

9. Logga in på webbplatsen Onit företag som en administratör i ett annat webbläsarfönster.

10. Klicka på menyn längst upp **Administration**.
   
   ![Administration](./media/onit-tutorial/IC791174.png "Administration")
11. Klicka på **redigera Corporation**.
   
   ![Redigera Corporation](./media/onit-tutorial/IC791175.png "redigera Corporation")
   
12. Klicka på den **Security** fliken.
    
    ![Redigera företagsinformation](./media/onit-tutorial/IC791176.png "Redigera företagsinformation")

13. På den **Security** fliken, utför följande steg:

    ![Enkel inloggning](./media/onit-tutorial/IC791177.png "enkel inloggning")

    a. Som **autentiseringsstrategi**väljer **enkel inloggning och lösenord**.
    
    b. I **mål-URL för IDP: N** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    c. I **utloggnings-URL för IDP: N** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.

    d. I **Idp Cert fingeravtryck (SHA1)** textrutan klistra in den **tumavtryck** värdet för certifikat som du har kopierat från Azure-portalen.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/onit-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/onit-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/onit-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/onit-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-onit-test-user"></a>Skapa en Onit testanvändare

För att aktivera Azure AD-användare att logga in på Onit, måste de etableras i Onit.  

När det gäller Onit är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på din **Onit** företagets plats som administratör.
2. Klicka på **lägga till användare**.
   
   ![Administration](./media/onit-tutorial/IC791180.png "Administration")
3. På den **Lägg till användare** dialogrutan utför följande steg:
   
   ![Lägg till användare](./media/onit-tutorial/IC791181.png "lägga till användare")
   
  1. Typen i **namn** och **e-postadress** av en giltig Azure AD-konto som du vill etablera till relaterade textrutor.
  2. Klicka på **Skapa**.    
   
 > [!NOTE]
 > Azure Active Directory-kontoinnehavare tar emot ett e-postmeddelande och följer en länk för att bekräfta sina konton innan den blir aktiv.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Onit.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Onit, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Onit**.

    ![Länken Onit i listan med program](./media/onit-tutorial/tutorial_onit_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Onit i åtkomstpanelen du bör få automatiskt loggat in på ditt Onit program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/onit-tutorial/tutorial_general_01.png
[2]: ./media/onit-tutorial/tutorial_general_02.png
[3]: ./media/onit-tutorial/tutorial_general_03.png
[4]: ./media/onit-tutorial/tutorial_general_04.png

[100]: ./media/onit-tutorial/tutorial_general_100.png

[200]: ./media/onit-tutorial/tutorial_general_200.png
[201]: ./media/onit-tutorial/tutorial_general_201.png
[202]: ./media/onit-tutorial/tutorial_general_202.png
[203]: ./media/onit-tutorial/tutorial_general_203.png

