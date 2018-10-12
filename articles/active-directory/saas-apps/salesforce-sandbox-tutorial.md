---
title: 'Självstudier: Azure Active Directory-integration med Salesforce Sandbox | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.openlocfilehash: 48dcb4a3b1e06ab62905092055ba1b48bd0dddb7
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114504"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Självstudier: Azure Active Directory-integrering med Salesforce begränsat läge

I den här självstudien får du lära dig hur du integrerar Salesforce Sandbox med Azure Active Directory (AD Azure).

Sandboxar ger dig möjlighet att skapa flera kopior av din organisation i separata miljöer för olika syften, till exempel utveckling, testning och utbildning, utan att kompromissa med data och program i din Salesforce-produktion organisation.
Mer information finns i [Sandbox översikt](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

Integrera Salesforce Sandbox med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Salesforce Sandbox.
- Du kan aktivera användarna att automatiskt få loggat in på Salesforce begränsat läge (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Salesforce begränsat läge, behöver du följande objekt:

- En Azure AD-prenumeration
- En Salesforce-Sandbox enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Salesforce Sandbox från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Att lägga till Salesforce Sandbox från galleriet

Om du vill konfigurera integreringen av Salesforce begränsat till Azure AD, som du behöver lägga till Salesforce Sandbox från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Salesforce Sandbox från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Salesforce Sandbox**väljer **Salesforce Sandbox** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Salesforce-Sandbox i resultatlistan](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Salesforce Sandbox baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Salesforce begränsat till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Salesforce Sandbox upprättas.

I Salesforce Sandbox tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Salesforce begränsat läge, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare i Salesforce Sandbox](#create-a-salesforce-sandbox-test-user)**  – du har en motsvarighet för Britta Simon i Salesforce-Sandbox som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Salesforce-Sandbox-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Salesforce begränsat läge:**

1. I Azure-portalen på den **Salesforce Sandbox** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. Klicka på **ändra enkel inloggningsläge** på skärmen för att välja den **SAML** läge.

      ![Konfigurera enkel inloggning för länken](./media/salesforce-sandbox-tutorial/tutorial_general_300.png)

3. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning för länken](./media/salesforce-sandbox-tutorial/tutorial_general_301.png)

4. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **SAML grundkonfiguration** dialogrutan.
   
    ![Konfigurera enkel inloggning för länken](./media/salesforce-sandbox-tutorial/tutorial_general_302.png)

5. På den **SAML grundkonfiguration** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    a. Klicka på **ladda upp metadatafilen**.

    ![Ladda upp metadatafil](./media/salesforce-sandbox-tutorial/upload_metadata.png)

    b. Klicka på **mappen logotyp** att välja metadatafilen och klicka på **överför**.

    ![Välj metadatafil](./media/salesforce-sandbox-tutorial/browse_upload_metadata.png)

    > [!NOTE]
    > Du får tjänstleverantören metadatafilen från Salesforce Sandbox-administrationsportalen som beskrivs senare i självstudien.

    c. När metadatafilen har överförts den **svars-URL** värde får automatiskt ifylld i **svars-URL** textrutan.

    ![Salesforce Sandbox-domän och URL: er med enkel inloggning för information](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

6. På den **SAML-signeringscertifikat** avsnittet, klicka på **hämta** att ladda ned **Federation Metadata XML** och spara xml-filen på datorn.

    ![Länk för hämtning av certifikat](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

7. Öppna en ny flik i webbläsaren och logga in på ditt Salesforce-Sandbox-administratörskonto.

8. Klicka på den **installationsprogrammet** under **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure1.png)

9. Rulla ned till den **inställningar** i det vänstra navigeringsfönstret klickar du på **identitet** att expandera avsnittet relaterade. Klicka sedan på **inställningar för enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. På den **inställningar för enkel inloggning** klickar du på den **redigera** knappen.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure3.png)

11. Välj **SAML aktiverat**, och klicka sedan på **spara**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. Om du vill konfigurera SAML enkel inloggning för, klickar du på **nytt från metadatafilen**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. Klicka på **Välj fil** att ladda upp den XML-fil som du har hämtat i Azure-portalen och klicka på **skapa**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/xmlchoose.png)

14. På den **SAML enkel inloggning inställningar** sidan fält fylla i automatiskt och klicka på Spara.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/salesforcexml.png)

15. På den **inställningar för enkel inloggning** klickar du på den **hämta Metadata** för att ladda ned metadatafilen från leverantören. Använd den här filen i den **SAML grundkonfiguration** avsnitt i Azure-portalen för att konfigurera de nödvändiga URL: er enligt beskrivningen ovan.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure4.png)

16. Om du vill konfigurera programmet i **SP** initierad läge är förutsättningarna för som:

    a. Du bör ha en verifierad domän.

    b. Du måste du konfigurera och aktivera din domän på Salesforce Sandbox beskrivs stegen för detta senare i den här självstudien.

    c. I Azure-portalen på den **SAML grundkonfiguration** klickar du på **ange ytterligare webbadresser** och utföra följande steg:
  
    ![Salesforce Sandbox-domän och URL: er med enkel inloggning för information](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    I den **inloggnings-URL** textrutan skriver du värdet med följande mönster: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Det här värdet ska kopieras från Salesforce Sandbox-portalen när du har aktiverat domänen.

17. På den **SAML-signeringscertifikat** klickar du på **Federation Metadata XML** och spara xml-filen på datorn.

    ![Länk för hämtning av certifikat](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. Öppna en ny flik i webbläsaren och logga in på ditt Salesforce-Sandbox-administratörskonto.

19. Klicka på den **installationsprogrammet** under **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure1.png)

20. Rulla ned till den **inställningar** i det vänstra navigeringsfönstret klickar du på **identitet** att expandera avsnittet relaterade. Klicka sedan på **inställningar för enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

21. På den **inställningar för enkel inloggning** klickar du på den **redigera** knappen.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure3.png)

22. Välj **SAML aktiverat**, och klicka sedan på **spara**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

23. Om du vill konfigurera SAML enkel inloggning för, klickar du på **nytt från metadatafilen**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

24. Klicka på **Välj fil** överföra den XML-fil och klicka på **skapa**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/xmlchoose.png)

25. På den **SAML enkel inloggning inställningar** sidan fält fylla automatiskt, skriver du namnet på konfigurationen (till exempel: *SPSSOWAAD_Test*) i den **namn** textrutan och Klicka på Spara.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

26. Om du vill aktivera din domän på Salesforce sandbox-miljön, utför du följande steg:

    > [!NOTE]
    > Innan du aktiverar domänen måste du skapa samma på Salesforce Sandbox. Mer information finns i [definiera domännamn](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). När domänen har skapats, kontrollera att den är korrekt konfigurerad.

    * Klicka på det vänstra navigeringsfönstret i Salesforce Sandbox **Företagsinställningar** Expandera avsnittet relaterade och klicka sedan på **min domän**.

         ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

    * I den **Autentiseringskonfiguration** klickar du på **redigera**.

        ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

    * I den **Autentiseringskonfiguration** avsnittet som **autentiseringstjänst**, Välj namnet på SAML enkel inloggning inställningen som du har angett under konfigurationen för enkel inloggning i Salesforce sandbox-miljön och Klicka på **spara**.

        ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](./media/salesforce-sandbox-tutorial/create_aaduser_01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

    a. I den **namn** ange **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="create-a-salesforce-sandbox-test-user"></a>Skapa en testanvändare i Salesforce Sandbox

I det här avsnittet skapas en användare som kallas Britta Simon i Salesforce Sandbox. Salesforce-Sandbox stöder just-in-time-etablering, som är aktiverat som standard. Det finns inga uppgift åt dig i det här avsnittet. Om en användare inte redan finns i Salesforce Sandbox, skapas en ny när du försöker få åtkomst till Salesforce Sandbox. Salesforce-Sandbox stöder även automatisk användaretablering, kan du hitta mer information om [här](salesforce-sandbox-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Salesforce Sandbox.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Salesforce sandbox-miljön, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Salesforce Sandbox**.

    ![Salesforce-Sandbox-länk i listan med program](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till användare** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Salesforce Sandbox i åtkomstpanelen du bör få automatiskt loggat in på ditt Salesforce-Sandbox-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](salesforce-sandbox-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png