---
title: "Självstudier: Azure Active Directory-integrering med Symantec Web Security Service (WSS) | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Symantec Web Security Service (WSS)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 61576d3a915d209e7355e04432e586dcf66e7c5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Självstudier: Azure Active Directory-integrering med Symantec Web Security Service (WSS)

I kursen får du lära dig hur du integrerar Symantec Web Security Service (WSS)-konto med ditt konto i Azure Active Directory (AD Azure) så att WSS kan autentisera en användare etableras i Azure AD med hjälp av SAML-autentisering och tvinga användaren eller säkerhetsnivå för gruppregler.

Integrera Symantec Web Security Service (WSS) med Azure AD ger dig följande fördelar:

- Hantera alla användare och grupper som används av ditt konto för WSS från Azure AD-portalen. 

- Tillåt användarna att autentisera sig i WSS med sina Azure AD-autentiseringsuppgifter.

- Aktivera verkställandet av användare och grupp säkerhetsnivå för regler som definierats i WSS-konto.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Symantec Web Security Service (WSS) behöver du följande:

- En Azure AD-prenumeration
- Ett konto med Symantec Web Security Service (WSS)

> [!NOTE]
> Om du vill testa stegen i den här självstudiekursen, rekommenderas inte med en WSS-konto som används för produktion ändamål.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte din WSS-konto som används för produktion syftet med det här testet om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här kursen ska du konfigurera din Azure AD för att aktivera enkel inloggning till WSS med autentiseringsuppgifterna för slutanvändare som definierats i Azure AD-kontot.
Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Symantec Web Security Service (WSS)-app från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Att lägga till Symantec Web Security Service (WSS) från galleriet
Du måste lägga till Symantec Web Security Service (WSS) från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Symantec Web Security Service (WSS) i Azure AD.

**Utför följande steg för att lägga till Symantec Web Security Service (WSS) från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Symantec Web Security Service (WSS)**väljer **Symantec Web Security Service (WSS)** resultatet-panelen klickar **Lägg till** för att lägga till den programmet.

    ![Symantec Web Security Service (WSS) i resultatlistan](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med Symantec Web Security Service (WSS) baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Symantec Web Security Service (WSS) till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Symantec Web Security Service (WSS) upprättas.

I Symantec Web Security Service (WSS), tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Symantec Web Security Service (WSS), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Symantec Web Security Service (WSS)](#create-a-symantec-web-security-service-wss-test-user)**  – du har en motsvarighet för Britta Simon i Symantec Web Security Service (WSS) som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Symantec Web Security Service (WSS)-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Symantec Web Security Service (WSS):**

1. I Azure-portalen på den **Symantec Web Security Service (WSS)** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_samlbase.png)

3. På den **URL: er och Symantec Web Service (WSS) säkerhetsdomän** avsnittet, utför följande steg:

    ![URL: er och Symantec Web Security Service (WSS)-domän med enkel inloggning information](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_url.png)

    a. I den **identifierare** textruta anger du URL:`https://saml.threatpulse.net:8443/saml/saml_realm`

    b. I den **Reply URL** textruta anger du URL:`https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Kontakta den [Symantec Web Security Service (WSS) klienten supportteamet](https://www.symantec.com/contact-us) om värdena för den **identifierare** och **Reply URL** inte är av någon anledning.

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-symantec-tutorial/tutorial_general_400.png)
    
6. Om du vill konfigurera enkel inloggning på sida Symantec Web Security Service (WSS) finns i onlinedokumentationen WSS. Den hämtade **XML-Metadata för** behöver importeras till WSS-portalen. Kontakta den [Symantec Web Security Service (WSS) supportteam](https://www.symantec.com/contact-us) om du behöver hjälp med konfigurationen i WSS-portal.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-symantec-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-symantec-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-symantec-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-symantec-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-symantec-web-security-service-wss-test-user"></a>Skapa en testanvändare Symantec Web Security Service (WSS)

I det här avsnittet skapar du en användare som kallas Britta Simon i Symantec Web Security Service (WSS). Motsvarande end-användarnamnet kan skapas manuellt i WSS-portalen eller vänta tills de användare eller grupper som etableras i Azure AD som ska synkroniseras till WSS-portalen efter några minuter (~ 15 minuter). Användare måste skapas och aktiveras innan du använder enkel inloggning. Offentliga IP-adressen för den datorn för slutanvändare som ska användas för att bläddra webbplatser måste också vara etablerade i portalen Symantec Web Security Service (WSS).

> [!NOTE]
> Kontrollera [Klicka här](http://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) att hämta din dator är offentlig IP-adress.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Symantec Web Security Service (WSS).

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon till Symantec Web Security Service (WSS), utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Symantec Web Security Service (WSS)**.

    ![Länken Symantec Web Security Service (WSS) i listan med program](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Du kan testa funktionen för enkel inloggning nu när du har konfigurerat ett WSS-konto för att använda din Azure AD för SAML-autentisering i det här avsnittet.

När du har konfigurerat webbläsaren att proxy trafik till WSS, när du öppnar din webbläsare och försök att bläddra till en plats och du ska omdirigeras till sidan för Azure-inloggning. Ange autentiseringsuppgifter för slutanvändaren test som har etablerats i Azure AD (det vill säga BrittaSimon) och tillhörande lösenord. När autentiseringen är kommer du att kunna bläddra till webbplatsen som du har valt. Du bör skapa en regel på WSS-sida för att blockera BrittaSimon från att gå till en viss plats, och du bör se sidan WSS block vid försök att bläddra till platsen som användare BrittaSimon.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_203.png

