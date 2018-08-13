---
title: 'Självstudier: Azure Active Directory-integrering med N2F - utgiftsrapporter | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och N2F - utgiftsrapporter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.openlocfilehash: 27fb299bc3bbbbf75bdf40ae02eac627763ce6d4
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006650"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Självstudier: Azure Active Directory-integrering med N2F - utgiftsrapporter

I den här självstudien får du lära dig hur du integrerar N2F - utgiftsrapporter med Azure Active Directory (AD Azure).

Integrerande N2F - utgiftsrapporter med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till N2F - utgiftsrapporter.
- Du kan aktivera användarna att automatiskt få loggat in på N2F - utgiftsrapporter (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med N2F - utgiftsrapporter, behöver du följande objekt:

- En Azure AD-prenumeration
- En N2F - utgiftsrapporter enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till N2F - utgiftsrapporter från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Att lägga till N2F - utgiftsrapporter från galleriet

Om du vill konfigurera integreringen av N2F - utgiftsrapporter i Azure AD kan du behöva lägga till N2F - utgiftsrapporter från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till N2F - utgiftsrapporter från galleriet:**

1. I den ** [Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **N2F - utgiftsrapporter**väljer **N2F - utgiftsrapporter** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![N2F - utgiftsrapporter i resultatlistan](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med N2F - utgiftsrapporter baserat på en testanvändare kallas ”Britta Simon”.

Azure AD behöver du veta vilka motsvarande användaren i N2F - utgiftsrapporter är att en användare i Azure AD för enkel inloggning ska fungera. Med andra ord en länk förhållandet mellan en Azure AD-användare och relaterade användaren i N2F - utgiftsrapporter måste upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med N2F - utgiftsrapporter, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on) ** – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) ** – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en N2F - utgiftsrapporter testanvändare](#create-a-n2f---expense-reports-test-use) ** - du har en motsvarighet för Britta Simon i N2F - utgiftsrapporter som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user) ** – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on) ** – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din N2F - utgiftsrapporter program.

**Om du vill konfigurera Azure AD utför enkel inloggning med N2F - utgiftsrapporter, du följande steg:**

1. I Azure-portalen på den **N2F - utgiftsrapporter** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_samlbase.png)

3. På den **N2F - utgiftsrapporter domän och URL: er** om du vill konfigurera programmet i **IDP** initierad läge, användaren behöver inte utföra några steg eftersom den redan förintegrerat i appen Azure.

    ![N2F - utgiftsrapporter domän och URL: er med enkel inloggning för information](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url1.png)

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![N2F - utgiftsrapporter domän och URL: er med enkel inloggning för information](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url2.png)

    I den **inloggnings-URL** textrutan anger du URL: `https://www.n2f.com/app/`

5. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

    ![Länk för hämtning av certifikat](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_certificate.png)

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/n2f-expensereports-tutorial/tutorial_general_400.png)

7. På den **N2F - konfiguration för utgiftsrapporter** klickar du på **konfigurera N2F - utgiftsrapporter** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID** från den **Snabbreferens avsnittet.**

    ![N2F - utgiftsrapporter konfiguration](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_configure.png)

8. I ett annat webbläsarfönster, loggar du in din N2F - telekommunikation rapporter företagets plats som administratör.

9. Klicka på **inställningar** och välj sedan **avancerade inställningar** i listrutan.

    ![N2F - utgiftsrapporter konfiguration](./media/n2f-expensereports-tutorial/configure1.png)

10. Välj **kontoinställningar** fliken.

    ![N2F - utgiftsrapporter konfiguration](./media/n2f-expensereports-tutorial/configure2.png)

11. Välj **autentisering** och välj sedan **+ Lägg till en autentiseringsmetod** fliken.

    ![N2F - utgiftsrapporter konfiguration](./media/n2f-expensereports-tutorial/configure3.png)

12. Välj **SAML Microsoft Office 365** som autentiseringsmetod.

    ![N2F - utgiftsrapporter konfiguration](./media/n2f-expensereports-tutorial/configure4.png)

13. På den **autentiseringsmetod** avsnittet, utför följande steg:

    ![N2F - utgiftsrapporter konfiguration](./media/n2f-expensereports-tutorial/configure5.png)

    a. I den **entitets-ID** textrutan klistra in den **SAML entitets-ID** värde, som du har kopierat från Azure-portalen.

    b. I den **Metadata_url** textrutan klistra in den **Appfederationsmetadata** värde, som du har kopierat från Azure-portalen.

    c. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/n2f-expensereports-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/n2f-expensereports-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/n2f-expensereports-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/n2f-expensereports-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-n2f---expense-reports-test-user"></a>Skapa en N2F - utgiftsrapporter testanvändare

Om du vill aktivera Azure AD-användare att logga in på N2F - utgiftsrapporter, måste de etableras i N2F - utgiftsrapporter. När det gäller N2F - utgiftsrapporter, är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din N2F - telekommunikation rapporter företagets plats som administratör.

2. Klicka på **inställningar** och välj sedan **avancerade inställningar** i listrutan.

   ![N2F - telekommunikation Lägg till användare](./media/n2f-expensereports-tutorial/configure1.png)

3. Välj **användare** fliken från vänstra navigeringspanelen.

    ![N2F - utgiftsrapporter konfiguration](./media/n2f-expensereports-tutorial/user1.png)

4. Välj **+ ny användare** fliken.

   ![N2F - utgiftsrapporter konfiguration](./media/n2f-expensereports-tutorial/user2.png)

5. På den **användaren** avsnittet, utför följande steg:

    ![N2F - utgiftsrapporter konfiguration](./media/n2f-expensereports-tutorial/user3.png)

    a. I den **e-postadress** textrutan anger du e-postadressen för användaren som ** brittasimon@contoso.com **.

    b. I den **Förnamn** textrutan Ange först namnet på användaren som **Britta**.

    c. I den **namn** textrutan anger du namnet på användaren som **BrittaSimon**.

    d. Välj **närmsta chef (N + 1)-rollen**, och **Division** enligt krav för din organisation.

    e. Klicka på **verifiera och skicka inbjudan**.

    > [!NOTE]
    > Om du får problem när du lägger till användaren, kontakta [N2F - utgiftsrapporter supportteam](mailto:support@n2f.com)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till N2F – utgiftsrapporter.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon N2F - utgiftsrapporter, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **N2F - utgiftsrapporter**.

    ![N2F - utgiftsrapporter länk i listan med program](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på N2F - utgiftsrapporter panelen i åtkomstpanelen, du bör få automatiskt loggat in på ditt N2F - utgiftsrapporter program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/n2f-expensereports-tutorial/tutorial_general_01.png
[2]: ./media/n2f-expensereports-tutorial/tutorial_general_02.png
[3]: ./media/n2f-expensereports-tutorial/tutorial_general_03.png
[4]: ./media/n2f-expensereports-tutorial/tutorial_general_04.png

[100]: ./media/n2f-expensereports-tutorial/tutorial_general_100.png

[200]: ./media/n2f-expensereports-tutorial/tutorial_general_200.png
[201]: ./media/n2f-expensereports-tutorial/tutorial_general_201.png
[202]: ./media/n2f-expensereports-tutorial/tutorial_general_202.png
[203]: ./media/n2f-expensereports-tutorial/tutorial_general_203.png

