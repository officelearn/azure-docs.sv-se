---
title: 'Självstudier: Azure Active Directory-integrering med Yodeck | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Yodeck.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.openlocfilehash: 8ff07d771085c6f51e3ebb81e9072683e1df3801
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35962170"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Självstudier: Azure Active Directory-integrering med Yodeck

I kursen får lära du att integrera Yodeck med Azure Active Directory (AD Azure).

Integrera Yodeck med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Yodeck.
- Du kan aktivera användarna att automatiskt hämta loggat in på Yodeck (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Yodeck, behöver du följande:

- En Azure AD-prenumeration
- En Yodeck enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Yodeck från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-yodeck-from-the-gallery"></a>Att lägga till Yodeck från galleriet
Du måste lägga till Yodeck från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Yodeck i Azure AD.

**Utför följande steg för att lägga till Yodeck från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Yodeck**väljer **Yodeck** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Yodeck i resultatlistan](./media/yodeck-tutorial/tutorial_yodeck_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Yodeck baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Yodeck motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Yodeck upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Yodeck, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Yodeck](#create-a-yodeck-test-user)**  – du har en motsvarighet för Britta Simon i Yodeck som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Yodeck program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Yodeck:**

1. I Azure-portalen på den **Yodeck** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning dialogrutan](./media/yodeck-tutorial/tutorial_yodeck_samlbase.png)

3. På den **Yodeck domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Yodeck domän med enkel inloggning information](./media/yodeck-tutorial/tutorial_yodeck_url.png)

    I den **identifierare (enhets-ID)** textruta anger du URL: `https://app.yodeck.com/api/v1/account/metadata/`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Yodeck domän med enkel inloggning information](./media/yodeck-tutorial/tutorial_yodeck_url1.png)

    I den **inloggnings-URL** textruta anger du URL: `https://app.yodeck.com/login`

5. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **webbadress Federation Metadata** och klistra in den i anteckningar.

    ![Länken hämta certifikatet](./media/yodeck-tutorial/tutorial_yodeck_certificate.png)

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/yodeck-tutorial/tutorial_general_400.png)
    
7. I en annan webbläsarfönster loggar du in på webbplatsen Yodeck företag som administratör.

8. Klicka på **användarinställningar** form det övre högra hörnet av sidan och väljer alternativet **kontoinställningar**.

    ![Yodeck konfiguration](./media/yodeck-tutorial/configure1.png)

9. Välj **SAML** och utför följande steg:

    ![Yodeck konfiguration](./media/yodeck-tutorial/configure2.png)

    a. Välj **importera från URL**.

    b. I den **URL** textruta klistra in den **webbadress Federation Metadata** -värde som du har kopierat från Azure-portalen och klicka på **importera**.
    
    c. När du har importerat **webbadress Federation Metadata**, Lägg till de återstående fälten i automatiskt.

    d. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/yodeck-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/yodeck-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/yodeck-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/yodeck-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-yodeck-test-user"></a>Skapa en testanvändare Yodeck

Om du vill aktivera Azure AD-användare kan logga in på Yodeck etableras de i Yodeck.
När det gäller Yodeck är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på webbplatsen Yodeck företag som administratör.

2. Klicka på **användarinställningar** form det övre högra hörnet av sidan och väljer alternativet **användare**.

    ![Lägga till medarbetare](./media/yodeck-tutorial/user1.png)

3. Klicka på **+ användaren** att öppna den **användarinformation** fliken.

    ![Lägga till medarbetare](./media/yodeck-tutorial/user2.png)

4. På den **användarinformation** dialogrutan utför följande steg:

    ![Lägga till medarbetare](./media/yodeck-tutorial/user3.png)

    a. I den **Förnamn** textruta Ange först namnet på användaren som **Britta**.

    b. I den **efternamn** textruta typen efternamn för användaren som **Simon**.

    c. I den **e-post** textruta typen e-postadressen för användaren som **brittasimon@contoso.com**.

    d. Välj lämplig **Kontobehörigheterna** alternativet enligt din organisations krav.
    
    e. Klicka på **Spara**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Yodeck.

![Tilldela rollen][200]

**Om du vill tilldela Yodeck Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201]

2. Välj i listan med program **Yodeck**.

    ![Länken Yodeck i listan med program](./media/yodeck-tutorial/tutorial_yodeck_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Yodeck på åtkomstpanelen du bör få automatiskt loggat in på ditt Yodeck program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/yodeck-tutorial/tutorial_general_01.png
[2]: ./media/yodeck-tutorial/tutorial_general_02.png
[3]: ./media/yodeck-tutorial/tutorial_general_03.png
[4]: ./media/yodeck-tutorial/tutorial_general_04.png

[100]: ./media/yodeck-tutorial/tutorial_general_100.png

[200]: ./media/yodeck-tutorial/tutorial_general_200.png
[201]: ./media/yodeck-tutorial/tutorial_general_201.png
[202]: ./media/yodeck-tutorial/tutorial_general_202.png
[203]: ./media/yodeck-tutorial/tutorial_general_203.png

