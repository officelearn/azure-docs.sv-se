---
title: 'Självstudier: Azure Active Directory-integrering med Dropbox för företag | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Dropbox för företag.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jeedes
ms.openlocfilehash: aa00a88f8325345b1b45d7d0971a03590bce1029
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Självstudier: Azure Active Directory-integrering med Dropbox för företag

I kursen får lära du att integrera Dropbox för företag med Azure Active Directory (AD Azure).

Integrera Dropbox för företag med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Dropbox för företag.
- Du kan aktivera användarna att automatiskt hämta loggat in på Dropbox för företag (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Dropbox för företag, behöver du följande:

- En Azure AD-prenumeration
- En Dropbox för Business enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Dropbox för företag från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Att lägga till Dropbox för företag från galleriet
Du måste lägga till Dropbox för företag från galleriet i listan över hanterade SaaS-appar för att konfigurera Dropbox för företag till Azure AD-integrering.

**Utför följande steg för att lägga till Dropbox för företag från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Dropbox för företag**väljer **Dropbox för företag** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Dropbox för företag i resultatlistan](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Dropbox för företag som baseras på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Dropbox för företag till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Dropbox för företag upprättas.

I Dropbox för företag att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Dropbox för företag, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en Dropbox för Business testanvändare](#create-a-dropbox-for-business-test-user)**  – du har en motsvarighet för Britta Simon i Dropbox för företag som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din Dropbox för affärsprogram.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Dropbox för företag:**

1. I Azure-portalen på den **Dropbox för företag** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_samlbase.png)

3. På den **Dropbox Business domän och URL: er** avsnittet, utför följande steg:

    ![Dropbox för Business domän och URL: er enkel inloggning](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_url1.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://www.dropbox.com/sso/<id>`

    b. I den **identifierare** textruta, ange ett värde: `Dropbox`

    > [!NOTE] 
    > Föregående inloggnings-URL-värdet är inte verkliga värde. Du uppdaterar värdet med det faktiska inloggnings-URL, som beskrivs senare i självstudierna. Kontakta [Dropbox för Business Client supportteamet](https://www.dropbox.com/business/contact) värdet hämtas. 
 

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_400.png)

6. På den **Dropbox för konfiguration av företag** klickar du på **konfigurera Dropbox för företag** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Dropbox för företag-konfiguration](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_configure.png) 

7. Konfigurera enkel inloggning på **Dropbox för företag** sidan finns på din Dropbox för företag-klient.

    a. Logga in på din Dropbox för företag-klient. 
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769509.png "Konfigurera enkel inloggning")
   
    b. I navigeringsfönstret till vänster klickar du på **administratörskonsolen**. 
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769510.png "Konfigurera enkel inloggning")
   
    c. På den **administratörskonsolen**, klickar du på **autentisering** i det vänstra navigeringsfönstret. 
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769511.png "Konfigurera enkel inloggning")
   
    d. I den **enkel inloggning** väljer **aktivera enkel inloggning**, och klicka sedan på **mer** att expandera det här avsnittet.  
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769512.png "Konfigurera enkel inloggning")
   
    e. Kopiera URL-Adressen bredvid **användarna kan logga in genom att ange sina e-postadress eller de kan gå direkt till** och klistrar in det i den **inloggnings-URL** textruta för **Dropbox Business domän och URL: er** avsnitt på Azure-portalen. 
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769513.png)
    
8. I den **enkel inloggning** avsnitt i den **autentisering** utför följande steg: 
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Konfigurera enkel inloggning")
   
    a. Klicka på **krävs**.
   
    b. I den den **inloggning URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    c. Klicka på **Välj certifikat**, och bläddra sedan till din **Base64-kodade certifikatfilen**.

    d. Klicka på **spara ändringar** för att slutföra konfigurationen på din DropBox för företag-klient.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-dropbox-for-business-test-user"></a>Skapa en Dropbox för Business testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i Dropbox för företag. Dropbox för företag stöder just-in-time-allokering som är aktiverad som standard.

Det finns ingen åtgärd objekt i det här avsnittet. Om en användare inte redan finns i Dropbox för företag, skapas en ny när du försöker få åtkomst till Dropbox för företag.

>[!Note]
>Om du behöver skapa en användare manuellt, kontakta [Dropbox för Business Client supportteamet](https://www.dropbox.com/business/contact) 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Dropbox för företag.

![Tilldela rollen][200] 

**Utför följande steg om du vill tilldela Britta Simon till Dropbox för företag:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Dropbox för företag**.

    ![Dropbox för Business länk i listan med program](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på Dropbox för företag-panelen på åtkomstpanelen bör du hämta inloggningssidan i din Dropbox för affärsprogram.
 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_203.png

