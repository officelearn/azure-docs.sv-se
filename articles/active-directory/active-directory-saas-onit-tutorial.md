---
title: "Självstudier: Azure Active Directory-integrering med Onit | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Onit."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: 47c0055b89dbcf6a30a7f9ac5a33913e7bf463fa
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-onit"></a>Självstudier: Azure Active Directory-integrering med Onit

I kursen får lära du att integrera Onit med Azure Active Directory (AD Azure).

Integrera Onit med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Onit.
- Du kan aktivera användarna att automatiskt hämta loggat in på Onit (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Onit, behöver du följande:

- En Azure AD-prenumeration
- En Onit enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Onit från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-onit-from-the-gallery"></a>Att lägga till Onit från galleriet
Du måste lägga till Onit från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Onit i Azure AD.

**Utför följande steg för att lägga till Onit från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Onit**väljer **Onit** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Onit i resultatlistan](./media/active-directory-saas-onit-tutorial/tutorial_onit_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Onit baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Onit motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Onit upprättas.

I Onit, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Onit, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Onit](#create-an-onit-test-user)**  – du har en motsvarighet för Britta Simon i Onit som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Onit program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Onit:**

1. I Azure-portalen på den **Onit** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-onit-tutorial/tutorial_onit_samlbase.png)

3. På den **Onit domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Onit domän med enkel inloggning information](./media/active-directory-saas-onit-tutorial/tutorial_onit_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<sub-domain>.onit.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://<sub-domain>.onit.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Onit klienten supportteamet](https://www.onit.com/support) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCKET** värdet för certifikatet.

    ![Länken hämta certifikatet](./media/active-directory-saas-onit-tutorial/tutorial_onit_certificate.png) 

5. Onit program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den **”Atrribute”** för programmet. Följande skärmbild visar ett exempel för det här. 

    ![Konfigurera enkel inloggning](./media/active-directory-saas-onit-tutorial/tutorial_onit_attribute.png) 

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden och utför följande steg:
    
    | Attributets namn | Attributvärdet |
    | ------------------- | -------------------- |
    | E-post | User.Mail |
    
    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-onit-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/active-directory-saas-onit-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Lämna den **Namespace** tomt.
    
    e. Klicka på **OK**.

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-onit-tutorial/tutorial_general_400.png)

8. På den **Onit Configuration** klickar du på **konfigurera Onit** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Onit konfiguration](./media/active-directory-saas-onit-tutorial/tutorial_onit_configure.png)

9. Logga in på webbplatsen Onit företag som en administratör i en annan webbläsarfönster.

10. Klicka på menyn högst upp **Administration**.
   
   ![Administration](./media/active-directory-saas-onit-tutorial/IC791174.png "Administration")
11. Klicka på **redigera Corporation**.
   
   ![Redigera Corporation](./media/active-directory-saas-onit-tutorial/IC791175.png "redigera Corporation")
   
12. Klicka på den **säkerhet** fliken.
    
    ![Redigera företagsinformation](./media/active-directory-saas-onit-tutorial/IC791176.png "Redigera företagsinformation")

13. På den **säkerhet** fliken, utför följande steg:

    ![Enkel inloggning](./media/active-directory-saas-onit-tutorial/IC791177.png "enkel inloggning")

    a. Som **autentiseringsstrategi**väljer **enkel inloggning och lösenord**.
    
    b. I **Idp mål-URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.

    c. I **Idp logga ut URL** textruta klistra in värdet för **Sign-Out URL**, som du har kopierat från Azure-portalen.

    d. I **Idp Cert fingeravtryck (SHA1)** textruta klistra in den **tumavtrycket** värdet för certifikat som du har kopierat från Azure-portalen.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-onit-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-onit-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-onit-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-onit-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-onit-test-user"></a>Skapa en testanvändare Onit

För att aktivera Azure AD-användare att logga in på Onit etableras de i Onit.  

När det gäller Onit är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på ditt **Onit** företagets webbplats som administratör.
2. Klicka på **lägga till användare**.
   
   ![Administration](./media/active-directory-saas-onit-tutorial/IC791180.png "Administration")
3. På den **Lägg till användare** dialogrutan utför följande steg:
   
   ![Lägg till användare](./media/active-directory-saas-onit-tutorial/IC791181.png "lägga till användare")
   
  1. Typ av **namn** och **e-postadress** av ett giltigt Azure AD-kontot som du vill etablera i relaterade textrutor.
  2. Klicka på **Skapa**.    
   
 > [!NOTE]
 > Azure Active Directory kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta sina konton innan den aktiveras.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Onit.

![Tilldela rollen][200] 

**Om du vill tilldela Onit Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Onit**.

    ![Länken Onit i listan med program](./media/active-directory-saas-onit-tutorial/tutorial_onit_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Onit på åtkomstpanelen du bör få automatiskt loggat in på ditt Onit program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-onit-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-onit-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-onit-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-onit-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-onit-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-onit-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-onit-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-onit-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-onit-tutorial/tutorial_general_203.png

