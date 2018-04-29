---
title: 'Självstudier: Azure Active Directory-integrering med Trisotech digitala Enterprise Server | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Trisotech digitala Enterprise Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: a70962c63fdf9ab723ce437563a4670d536300aa
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Självstudier: Azure Active Directory-integrering med Trisotech digitala Enterprise Server

I kursen får lära du att integrera Trisotech digitala Enterprise Server med Azure Active Directory (AD Azure).

Integrera Trisotech digitala Enterprise Server med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Trisotech digitala Enterprise Server.
- Du kan aktivera användarna att automatiskt hämta loggat in på Trisotech digitala Enterprise Server (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Trisotech digitala Enterprise Server behöver du följande:

- En Azure AD-prenumeration
- En Trisotech digitala företagsserver enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Trisotech digitala Enterprise Server från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Att lägga till Trisotech digitala Enterprise Server från galleriet
Du måste lägga till Trisotech digitala Enterprise Server från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Trisotech digitala Enterprise Server i Azure AD.

**Utför följande steg för att lägga till Trisotech digitala Enterprise Server från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Trisotech digitala Enterprise Server**väljer **Trisotech digitala Enterprise Server** resultatet-panelen klickar **Lägg till** för att lägga till den programmet.

    ![Trisotech digitala Enterprise Server i resultatlistan över](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Trisotech digitala Enterprise Server baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Trisotech digitala Enterprise Server till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Trisotech digitala företagsserver upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Trisotech digitala Enterprise Server, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Trisotech digitala företagsserver](#create-a-trisotech-digital-enterprise-server-test-user)**  – du har en motsvarighet för Britta Simon i Trisotech digitala Enterprise Server som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Trisotech digitala Enterprise Server-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Trisotech digitala Enterprise Server:**

1. I Azure-portalen på den **Trisotech digitala Enterprise Server** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_samlbase.png)

3. På den **Trisotech digitala Enterprise-domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Trisotech digitala Enterprise-domän med enkel inloggning information](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<companyname>.trisotech.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<companyname>.trisotech.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Trisotech digitala Enterprise Server Client supportteamet](mailto:support@trisotech.com) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **webbadress Federation Metadata** och klistra in den i anteckningar. 

    ![Länken hämta certifikatet](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_certificate.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_400.png)

6. I en annan webbläsarfönster loggar du in på webbplatsen Trisotech digitala Enterprise-serverkonfigurationen företag som administratör.

7. Klicka på den **menyn ikonen** och välj sedan **Administration**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/user1.png)

8. Välj **Användarprovider**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/user2.png)

9. I den **providern användarkonfigurationer** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Välj **skyddas Assertion Markup Language 2.2 SAML** i listrutan i den **autentiseringsmetod**.

    b. I den **URL för tjänstmetadata** textruta klistra in den **webbadress Federation Metadata** -värde som du har kopierat formuläret Azure-portalen.

    c. I den **program-ID** textruta ange URL-Adressen med följande mönster: `https://<companyname>.trisotech.com`.

    d. Klicka på **Spara**

    e. Ange domännamnet i den **tillåtna domäner (empty innebär alla)** textruta tilldelas den automatiskt licenser för användare som matchar de tillåtna domäner

    f. Klicka på **Spara**

 ### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-trisotech-digital-enterprise-server-test-user"></a>Skapa en testanvändare Trisotech digitala Enterprise Server

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Trisotech digitala Enterprise Server. Trisotech digitala Enterprise Server stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt Trisotech digitala Enterprise Server om den inte finns.
>[!Note]
>Om du behöver skapa en användare manuellt Kontakta [Trisotech digitala företagsserver supportteamet](mailto:support@trisotech.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Trisotech digitala Enterprise Server.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Trisotech digitala Enterprise Server, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Trisotech digitala Enterprise Server**.

    ![Länken Trisotech digitala Enterprise Server i listan med program](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Trisotech digitala Enterprise Server på åtkomstpanelen du bör få automatiskt loggat in på Trisotech digitala Enterprise Server-programmet.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_203.png

