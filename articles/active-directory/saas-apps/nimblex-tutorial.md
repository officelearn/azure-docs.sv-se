---
title: 'Självstudier: Azure Active Directory-integrering med Nimblex | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Nimblex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d3e165a5-f062-4b50-ac0b-b400838e99cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7801b5ea73cf94439ae2974f91d2032f9bf8a3b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56166750"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>Självstudier: Azure Active Directory-integrering med Nimblex

I den här självstudien får du lära dig hur du integrerar Nimblex med Azure Active Directory (AD Azure).

Integrera Nimblex med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Nimblex.
- Du kan aktivera användarna att automatiskt få loggat in på Nimblex (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Nimblex, behöver du följande objekt:

- En Azure AD-prenumeration
- En Nimblex enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Nimblex från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-nimblex-from-the-gallery"></a>Att lägga till Nimblex från galleriet
För att konfigurera integrering av Nimblex i Azure AD, som du behöver lägga till Nimblex från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Nimblex från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Nimblex**väljer **Nimblex** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Nimblex i resultatlistan](./media/nimblex-tutorial/tutorial_nimblex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Nimblex baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Nimblex är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Nimblex upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Nimblex, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare Nimblex](#create-a-nimblex-test-user)**  – du har en motsvarighet för Britta Simon i Nimblex som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Nimblex program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Nimblex:**

1. I Azure-portalen på den **Nimblex** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/nimblex-tutorial/tutorial_nimblex_samlbase.png)

3. På den **Nimblex domän och URL: er** avsnittet, utför följande steg:

    ![Nimblex domän och URL: er med enkel inloggning för information](./media/nimblex-tutorial/tutorial_nimblex_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<YOUR APPLICATION PATH>/Login.aspx`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<YOUR APPLICATION PATH>/`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<path-to-application>/SamlReply.aspx`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska inloggnings-URL, identifierare och svars-URL. Kontakta [Nimblex klienten supportteamet](mailto:support@ebms.com.au) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/nimblex-tutorial/tutorial_nimblex_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/nimblex-tutorial/tutorial_general_400.png)

6. På den **Nimblex Configuration** klickar du på **konfigurera Nimblex** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Nimblex konfiguration](./media/nimblex-tutorial/tutorial_nimblex_configure.png) 

7. I ett annat webbläsarfönster, logga in på Nimblex som en administratör.

9. På upp till höger på sidan, klickar du på **inställningar** logotyp.

    ![Nimblex inställningar](./media/nimblex-tutorial/tutorial_nimblex_settings.png)

10. På den **Kontrollpanelen** sidan under **Security** klickar du på avsnittet **enkel inloggning**.

    ![Nimblex inställningar](./media/nimblex-tutorial/tutorial_nimblex_single.png)

11. På den **hantera enkel inloggning** väljer du din instansnamn och klicka på **redigera**.

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_saml.png)

12. På den **redigera SSO-leverantör** utför följande steg:

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_sso.png)

    a. I den **beskrivning** textrutan, Skriv instansnamnet på.

    b. I anteckningar, öppna Base64-kodade certifikatet som du laddade ned från Azure-portalen, kopiera innehållet och klistra in den i den **certifikat** box.

    c. I den **Sso mål-Url för identitetsprovider** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    d. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/nimblex-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/nimblex-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/nimblex-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/nimblex-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-nimblex-test-user"></a>Skapa en Nimblex testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Nimblex. Nimblex stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Nimblex om det inte finns ännu.

>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Nimblex klienten supportteamet](mailto:support@ebms.com.au).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Nimblex.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Nimblex, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Nimblex**.

    ![Länken Nimblex i listan med program](./media/nimblex-tutorial/tutorial_nimblex_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Nimblex i åtkomstpanelen du bör få automatiskt loggat in på ditt Nimblex program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/nimblex-tutorial/tutorial_general_01.png
[2]: ./media/nimblex-tutorial/tutorial_general_02.png
[3]: ./media/nimblex-tutorial/tutorial_general_03.png
[4]: ./media/nimblex-tutorial/tutorial_general_04.png

[100]: ./media/nimblex-tutorial/tutorial_general_100.png

[200]: ./media/nimblex-tutorial/tutorial_general_200.png
[201]: ./media/nimblex-tutorial/tutorial_general_201.png
[202]: ./media/nimblex-tutorial/tutorial_general_202.png
[203]: ./media/nimblex-tutorial/tutorial_general_203.png

