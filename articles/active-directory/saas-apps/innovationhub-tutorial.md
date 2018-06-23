---
title: 'Självstudier: Azure Active Directory-integrering med Innovation Hub | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Innovation hubb.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d72e4da0-0123-409b-96c2-e613f3f83fb1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 612cce99b1a510dfb53ec4a6c1e2a40185367db1
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320753"
---
# <a name="tutorial-azure-active-directory-integration-with-innovation-hub"></a>Självstudier: Azure Active Directory-integrering med Innovation Hub

I kursen får lära du att integrera Innovation hubb med Azure Active Directory (AD Azure).

Integrera Innovation hubb med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Innovation hubb.
- Du kan aktivera användarna att automatiskt hämta loggat in på Innovation hubb (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Innovation Hub, behöver du följande:

- En Azure AD-prenumeration
- En Innovation hubb enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Innovation hubb från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-innovation-hub-from-the-gallery"></a>Att lägga till Innovation hubb från galleriet
Du måste lägga till Innovation hubb från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Innovation hubb i Azure AD.

**Utför följande steg för att lägga till Innovation hubb från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Innovation hubb**väljer **Innovation hubb** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Innovation hubben i resultatlistan](./media/innovationhub-tutorial/tutorial_innovationhub_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med Innovation hubb baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Innovation hubben till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Innovation hubb upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Innovation Hub, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Innovation hubb](#create-an-innovation-hub-test-user)**  – du har en motsvarighet för Britta Simon i Innovation hubb som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Innovation hubb.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Innovation hubben:**

1. I Azure-portalen på den **Innovation hubb** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/innovationhub-tutorial/tutorial_innovationhub_samlbase.png)

3. På den **URL: er och Innovation hubb domän** avsnittet, utför följande steg:

    ![URL: er och innovation hubb domän med enkel inloggning information](./media/innovationhub-tutorial/tutorial_innovationhub_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<domainname>.innohb.com/auth/saml2/login`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<domainname>.innohb.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Innovation hubb klienten supportteamet](mailto:support@readify.net) att hämta dessa värden.

4. Innovation hubb program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.

    ![Konfigurera enkel inloggning](./media/innovationhub-tutorial/attribute.png)

5. Klicka på **visa och redigera andra användarattribut** kryssrutan i den **användarattribut** avsnittet för att expandera attribut. Utför följande steg på varje visas attribut-

    | Attributnamn | Attributvärde | Namespace-värde|
    | ---------------| --------------- |----------------|
    | visningsnamn | User.userPrincipalName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/innovationhub-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/innovationhub-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Från den **Namespace värdet** anger namnområdesvärdet som visas för den raden.

    e. Klicka på **OK**.

4. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **webbadress Federation Metadata** och klistra in den i anteckningar.

    ![Länken hämta certifikatet](./media/innovationhub-tutorial/tutorial_innovationhub_certificate.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/innovationhub-tutorial/tutorial_general_400.png)

6. Konfigurera enkel inloggning på **Innovation hubb** sida, måste du skicka den kopierade **Federation Metadata Url** till [Innovation hubb supportteamet](mailto:support@readify.net). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/innovationhub-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/innovationhub-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/innovationhub-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/innovationhub-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-innovation-hub-test-user"></a>Skapa en testanvändare Innovation Hub

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Innovation hubb. Innovation hubb stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt Innovation hubb om den inte finns.
>[!Note]
>Om du behöver skapa en användare manuellt Kontakta [Innovation hubb supportteamet](mailto:support@readify.net).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Innovation hubb.

![Tilldela rollen][200] 

**Om du vill tilldela Innovation hubb Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Innovation hubb**.

    ![Länken Innovation hubb i listan med program](./media/innovationhub-tutorial/tutorial_innovationhub_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Innovation hubb på åtkomstpanelen du bör få automatiskt loggat in på Innovation NAV-programmet.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
<!--Image references-->

[1]: ./media/innovationhub-tutorial/tutorial_general_01.png
[2]: ./media/innovationhub-tutorial/tutorial_general_02.png
[3]: ./media/innovationhub-tutorial/tutorial_general_03.png
[4]: ./media/innovationhub-tutorial/tutorial_general_04.png

[100]: ./media/innovationhub-tutorial/tutorial_general_100.png

[200]: ./media/innovationhub-tutorial/tutorial_general_200.png
[201]: ./media/innovationhub-tutorial/tutorial_general_201.png
[202]: ./media/innovationhub-tutorial/tutorial_general_202.png
[203]: ./media/innovationhub-tutorial/tutorial_general_203.png

