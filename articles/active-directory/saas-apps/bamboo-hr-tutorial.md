---
title: 'Självstudier: Azure Active Directory-integration med BambooHR | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BambooHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: 77625296797ec8ed8364e7d8bff3e5a15b4b74b5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048046"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Självstudier: Azure Active Directory-integration med BambooHR

I den här självstudien får du lära dig hur du integrerar BambooHR med Azure Active Directory (AD Azure).

Integrera BambooHR med Azure AD ger följande fördelar:

- Du kan styra i Azure AD som har åtkomst till BambooHR.
- Du kan aktivera användarna att få loggas in automatiskt BambooHR genom att använda enkel inloggning (SSO) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats, Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med BambooHR, behöver du följande objekt:

- En Azure AD-prenumeration
- En BambooHR SSO-aktiverad prenumeration

> [!NOTE]
> När du testar stegen i den här självstudien, rekommenderar vi att du inte använder en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en kostnadsfri utvärderingsversion med månads](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. 

Det scenario som beskrivs i den här kursen består av två viktigaste byggstenarna:

1. Att lägga till BambooHR från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-bamboohr-from-the-gallery"></a>Lägg till BambooHR från galleriet
För att konfigurera integrering av BambooHR i Azure AD, lägga till BambooHR från galleriet i listan över hanterade SaaS-appar genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan väljer **Azure Active Directory**. 

    ![Azure Active Directory-knappen][1]

2. Välj **företagsprogram** > **alla program**.

    ![Fönstret för Enterprise-program][2]
    
3. Om du vill lägga till ett program, Välj **nytt program**.

    ![”Det nya programmet” knappen][3]

4. I sökrutan skriver **BambooHR**. I resultatlistan väljer **BambooHR**, och välj sedan **Lägg till**.

    ![BambooHR i resultatlistan](./media/bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med BambooHR med hjälp av testanvändare ”Britta Simon”.

Azure AD behöver du känna till dess motsvariga användaren i BambooHR för SSO ska fungera. Med andra ord måste du upprätta en relation med länken mellan Azure AD-användare och relaterade användaren i BambooHR.

Tilldela Azure AD för att upprätta länken relationen i BambooHR **användarnamn** värde som BambooHR **användarnamn** värde.

Om du vill konfigurera och testa Azure AD enkel inloggning med BambooHR, slutför du byggblocken i nästa fem avsnitt.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt BambooHR program genom att göra följande:

1. I Azure-portalen på den **BambooHR** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. I den **enkel inloggning** fönstret i den **läge** listrutan, väljer **SAML-baserad inloggning**.
 
    ![Fönstret för enkel inloggning](./media/bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. Under **BambooHR domän och URL: er**, gör du följande:

    ![Avsnittet BambooHR domän och URL: er](./media/bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. I den **inloggnings-URL** skriver du en URL i formatet: `https://<company>.bamboohr.com`.

    b. I den **identifierare** skriver ett värde: `BambooHR-SAML`.

    > [!NOTE] 
    > Den **inloggnings-URL** värdet är inte verkliga. Uppdatera den med din faktiska inloggnings-URL. Du kan hämta värdet genom att kontakta den [BambooHR klienten supportteamet](https://www.bamboohr.com/contact.php). 
 
4. Under **SAML-signeringscertifikat**väljer **certifikat (Base64)**, och sedan spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. Välj **Spara**.

    ![Knappen Spara](./media/bamboo-hr-tutorial/tutorial_general_400.png)

6. Under **BambooHR Configuration**väljer **konfigurera BambooHR** att öppna den **konfigurera inloggning** fönster. I den **Snabbreferens** avsnittet, kopiera den **SAML inloggnings-tjänst-URL för enkel** för senare användning.

    ![BambooHR konfiguration](./media/bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

7. I ett nytt fönster, loggar du in din BambooHR företagets webbplats som administratör.

8. Gör följande på startsidan:
   
    ![Sidan BambooHR Single Sign-On](./media/bamboo-hr-tutorial/ic796691.png "enkel inloggning")   

    a. Välj **appar**.
   
    b. I den **appar** väljer **enkel inloggning**.
   
    c. Välj **SAML enkel inloggning**.

9. I den **SAML enkel inloggning** fönstret gör du följande:
   
    ![Fönstret SAML enkel inloggning](./media/bamboo-hr-tutorial/IC796692.png "SAML enkel inloggning")
   
    a. I den **inloggnings-Url för enkel inloggning** rutan, klistra in den **SAML inloggnings-tjänst-URL för enkel** som du kopierade från Azure-portalen i steg 6.
      
    b. I anteckningar, öppna Base64-kodade certifikatet som du laddade ned från Azure-portalen, kopiera innehållet och klistra in den i den **X.509-certifikat** box.
   
    c. Välj **Spara**.

> [!TIP]
> När du konfigurerar appen, du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com). När du har lagt till appen från den **Active Directory** > **företagsprogram** bara väljer de **enkel inloggning** fliken och öppna den Embedded-dokumentation via den **Configuration** avsnittet längst ned. Mer information finns i [embedded-dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare kallas Britta Simon i Azure-portalen.

   ![Skapa Azure AD-testanvändare Britta Simon][100]

Om du vill skapa en testanvändare i Azure AD, gör du följande:

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](./media/bamboo-hr-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och välj sedan **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/bamboo-hr-tutorial/create_aaduser_02.png)

3. Överst på den **alla användare** väljer **Lägg till**.

    ![Knappen Lägg till](./media/bamboo-hr-tutorial/create_aaduser_03.png)

4. I den **användaren** fönstret gör du följande:

    ![Fönstret användare](./media/bamboo-hr-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Välj **Skapa**.
 
### <a name="create-a-bamboohr-test-user"></a>Skapa en BambooHR testanvändare

Om du vill aktivera Azure AD-användare att logga in på BambooHR lägga upp dem manuellt i BambooHR genom att göra följande:

1. Logga in på din **BambooHR** plats som administratör.

2. I verktygsfältet högst upp väljer **inställningar**.
   
    ![Knappen Inställningar](./media/bamboo-hr-tutorial/IC796694.png "inställningen")

3. Välj **Översikt**.

4. I den vänstra rutan väljer **Security** > **användare**.

5. Skriv användarnamnet, lösenordet och e-postadress av giltig Azure AD-konto som du vill konfigurera.

6. Välj **Spara**.
        
>[!NOTE]
>Om du vill konfigurera Azure AD-användarkonton kan du också använda BambooHR användaren kontoskapandet verktyg eller API: er.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

Aktivera användare Britta Simon att använda enkel inloggning för Azure genom att bevilja åtkomst till BambooHR.

![Tilldela rollen][200] 

Om du vill tilldela användaren Britta Simon BambooHR, gör du följande:

1. Öppna vyn program i Azure-portalen, gå till vyn directory och därefter **företagsprogram** > **alla program**.

    ![Tilldela användare][201] 

2. I den **företagsprogram** väljer **BambooHR**.

    ![Länken BambooHR i listan över företagsprogram](./media/bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

3. I den vänstra rutan väljer **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj den **Lägg till** knappen och klicka sedan på **Lägg till tilldelning** väljer **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** fönstret i den **användare** väljer **Britta Simon**.

6. Välj den **Välj** knappen.

7. I den **Lägg till tilldelning** väljer den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du väljer den **BambooHR** panelen i åtkomstpanelen, du bör få loggas in automatiskt BambooHR programmet.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om integrering av SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/bamboo-hr-tutorial/tutorial_general_203.png

