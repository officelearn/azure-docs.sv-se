---
title: "Självstudier: Azure Active Directory-integrering med BambooHR | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BambooHR."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: 081144a645683d4d00ed0d464e23558378dc1b38
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Självstudier: Azure Active Directory-integrering med BambooHR

I kursen får lära du att integrera BambooHR med Azure Active Directory (AD Azure).

Integrera BambooHR med Azure AD ger följande fördelar:

- Du kan styra i Azure AD som har åtkomst till BambooHR.
- Du kan aktivera användarna att få loggas automatiskt BambooHR med enkel inloggning (SSO) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats, Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med BambooHR, behöver du följande:

- En Azure AD-prenumeration
- En prenumeration BambooHR SSO-aktiverad

> [!NOTE]
> När du testar stegen i den här kursen rekommenderar vi att du inte använder en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en kostnadsfri utvärderingsversion med en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. 

Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till BambooHR från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-bamboohr-from-the-gallery"></a>Lägg till BambooHR från galleriet
För att konfigurera integrering av BambooHR i Azure AD, lägga till BambooHR från galleriet i listan över hanterade SaaS-appar genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan, Välj **Azure Active Directory**. 

    ![Azure Active Directory-knappen][1]

2. Välj **företagsprogram** > **alla program**.

    ![Fönstret Enterprise program][2]
    
3. Om du vill lägga till ett program, Välj **nytt program**.

    ![”Det nya programmet” knappen][3]

4. I sökrutan skriver **BambooHR**. Välj i resultatlistan **BambooHR**, och välj sedan **Lägg till**.

    ![BambooHR i resultatlistan](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD SSO med BambooHR med hjälp av testanvändare ”Britta Simon”.

För SSO ska fungera måste Azure AD du känna till dess motsvarande användare i BambooHR. Med andra ord måste du upprätta en länk relation mellan Azure AD-användare och relaterade användaren i BambooHR.

Tilldela Azure AD för att upprätta länken relationen i BambooHR **användarnamn** värde som BambooHR **användarnamn** värde.

Slutför byggblock i följande fem avsnitt för att konfigurera och testa Azure AD SSO med BambooHR.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet BambooHR genom att göra följande:

1. I Azure-portalen på den **BambooHR** programmet integration anger **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. I den **enkel inloggning** fönster i den **läge** listrutan, Välj **SAML-baserade inloggning**.
 
    ![Fönster för enkel inloggning](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. Under **BambooHR domän och URL: er**, gör du följande:

    ![Avsnittet BambooHR domän och URL: er](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. I den **inloggning URL** skriver en URL i följande format: `https://<company>.bamboohr.com`.

    b. I den **identifierare** skriver ett värde: `BambooHR-SAML`.

    > [!NOTE] 
    > Den **inloggning URL** värdet är inte verkliga. Uppdatera den med ditt faktiska inloggnings-URL. Om du vill hämta värdet kontakta den [BambooHR klienten supportteamet](https://www.bamboohr.com/contact.php). 
 
4. Under **SAML-signeringscertifikat**väljer **certifikat (Base64)**, och sedan spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. Välj **Spara**.

    ![Knappen Spara](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_400.png)

6. Under **BambooHR Configuration**väljer **konfigurera BambooHR** att öppna den **konfigurera inloggning** fönster. I den **Snabbreferens** avsnittet, kopiera den **SAML inloggning tjänst-URL för enkel** för senare användning.

    ![BambooHR konfiguration](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

7. I ett nytt fönster, loggar du in på ditt BambooHR företagets webbplats som administratör.

8. På sidan, gör du följande:
   
    ![Sidan BambooHR enkel inloggning](./media/active-directory-saas-bamboo-hr-tutorial/ic796691.png "enkel inloggning")   

    a. Välj **appar**.
   
    b. I den **appar** väljer **enkel inloggning**.
   
    c. Välj **SAML enkel inloggning**.

9. I den **SAML enkel inloggning** rutan gör du följande:
   
    ![Fönstret SAML enkel inloggning](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "SAML enkel inloggning")
   
    a. I den **inloggnings-Url för SSO** klistra in den **SAML inloggning tjänst-URL för enkel** som du kopierade från Azure-portalen i steg 6.
      
    b. I anteckningar, öppna Base64-kodade certifikatet som du hämtade från Azure-portalen, kopiera innehållet och klistrar in det i den **X.509-certifikat** rutan.
   
    c. Välj **Spara**.

> [!TIP]
> När du konfigurerar appen, kan du läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com). När du har lagt till appen från den **Active Directory** > **företagsprogram** bara väljer den **enkel inloggning** fliken och sedan ansluta till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Mer information finns i [Azure AD inbäddade dokumentationen]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare kallas Britta Simon i Azure-portalen.

   ![Skapa Azure AD-testanvändare Britta Simon][100]

Om du vill skapa en testanvändare i Azure AD, gör du följande:

1. I Azure-portalen i den vänstra rutan, Välj **Azure Active Directory**.

    ![Azure Active Directory-knappen](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och välj sedan **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_02.png)

3. Längst upp i den **alla användare** väljer **Lägg till**.

    ![Knappen Lägg till](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_03.png)

4. I den **användaren** fönster, gör du följande:

    ![Fönstret användare](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Välj **Skapa**.
 
### <a name="create-a-bamboohr-test-user"></a>Skapa en testanvändare BambooHR

Om du vill aktivera Azure AD-användare att logga in på BambooHR lägga upp dem manuellt i BambooHR genom att göra följande:

1. Logga in på ditt **BambooHR** platsen som en administratör.

2. I verktygsfältet högst upp väljer **inställningar**.
   
    ![Knappen Inställningar](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "inställning")

3. Välj **översikt**.

4. I den vänstra rutan, Välj **säkerhet** > **användare**.

5. Ange användarnamn, lösenord och e-postadress giltig Azure AD-kontot som du vill ställa in.

6. Välj **Spara**.
        
>[!NOTE]
>Om du vill konfigurera Azure AD-användarkonton, kan du också använda BambooHR användaren skapande av konto verktyg eller API: er.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

Aktivera användare Britta Simon att använda Azure SSO genom att bevilja åtkomst till BambooHR.

![Tilldela rollen][200] 

Om du vill tilldela användaren Britta Simon BambooHR, gör du följande:

1. Öppna program i Azure-portalen, gå till vyn directory och välj sedan **företagsprogram** > **alla program**.

    ![Tilldela användare][201] 

2. I den **företagsprogram** väljer **BambooHR**.

    ![Länken BambooHR i listan över företagsprogram](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

3. I den vänstra rutan, Välj **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj den **Lägg till** knappen och klicka sedan på **Lägg uppdrag** väljer **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** fönster i den **användare** väljer **Britta Simon**.

6. Välj den **Välj** knappen.

7. I den **Lägg uppdrag** väljer den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Testa Azure AD SSO konfigurationen med hjälp av åtkomstpanelen.

När du väljer den **BambooHR** panelen i panelen åtkomst ska hämta loggas du automatiskt till BambooHR-program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om att integrera SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_203.png

