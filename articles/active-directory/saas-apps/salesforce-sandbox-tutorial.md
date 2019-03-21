---
title: 'Självstudier: Azure Active Directory-integrering med Salesforce Sandbox | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6794d7eaccb488bb345227161f0bca02f14bc518
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57852585"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Självstudier: Azure Active Directory-integrering med Salesforce Sandbox

I den här självstudien får du lära dig hur du integrerar Salesforce Sandbox med Azure Active Directory (AD Azure).

Sandboxar ger dig möjlighet att skapa flera kopior av din organisation i separata miljöer för olika syften, till exempel utveckling, testning och utbildning, utan att kompromissa med data och program i din Salesforce-produktion organisation.
Mer information finns i [Sandbox översikt](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

Integrera Salesforce Sandbox med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Salesforce Sandbox.
- Du kan aktivera användarna att automatiskt få loggat in på Salesforce begränsat läge (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Salesforce begränsat läge, behöver du följande objekt:

- En Azure AD-prenumeration
- En Salesforce-Sandbox enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
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

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Salesforce Sandbox**väljer **Salesforce Sandbox** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Salesforce-Sandbox i resultatlistan](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Salesforce Sandbox baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Salesforce begränsat till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Salesforce Sandbox upprättas.

I Salesforce Sandbox tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Salesforce begränsat läge, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare i Salesforce Sandbox](#create-a-salesforce-sandbox-test-user)**  – du har en motsvarighet för Britta Simon i Salesforce-Sandbox som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Salesforce-Sandbox-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Salesforce begränsat läge:**

1. I Azure-portalen på den **Salesforce Sandbox** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. Klicka på **ändra enkel inloggningsläge** på skärmen för att välja den **SAML** läge.

      ![Konfigurera länk för enkel inloggning](./media/salesforce-sandbox-tutorial/tutorial_general_300.png)

3. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera länk för enkel inloggning](./media/salesforce-sandbox-tutorial/tutorial_general_301.png)

4. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.
   
    ![Konfigurera länk för enkel inloggning](./media/salesforce-sandbox-tutorial/tutorial_general_302.png)

5. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du vill konfigurera programmet i **IDP**-initierat läge:

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](./media/salesforce-sandbox-tutorial/upload_metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](./media/salesforce-sandbox-tutorial/browse_upload_metadata.png)

    > [!NOTE]
    > Du får tjänstleverantören metadatafilen från Salesforce Sandbox-administrationsportalen som beskrivs senare i självstudien.

    c. När metadatafilen har överförts den **svars-URL** värde får automatiskt ifylld i **svars-URL** textrutan.

    ![Salesforce Sandbox-domän och URL: er med enkel inloggning för information](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

6. På den **SAML-signeringscertifikat** avsnittet, klicka på **hämta** att ladda ned **Federation Metadata XML** och spara xml-filen på datorn.

    ![Länk för nedladdning av certifikatet](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

7. Öppna en ny flik i webbläsaren och logga in på ditt Salesforce-Sandbox-administratörskonto.

8. Klicka på **Setup** (Konfiguration) under **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure1.png)

9. Rulla ned till den **inställningar** i det vänstra navigeringsfönstret klickar du på **identitet** att expandera avsnittet relaterade. Klicka sedan på **Inställningar för enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. På sidan med **Inställningar för enkel inloggning** klickar du på **Redigera**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure3.png)

11. Välj **SAML-aktiverat** och klicka på **Spara**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. Konfigurera inställningarna för enkel inloggning med SAML genom att klicka på **Nytt från metadatafil**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. Klicka på **Välj fil** för att ladda upp XML-filen med metadata som du har laddat ned från Azure-portalen och klicka på **Skapa**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/xmlchoose.png)

14. På sidan med **inställningar för enkel inloggning med SAML** fylls fälten i automatiskt. Klicka på Spara.

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

    ![Länk för nedladdning av certifikatet](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. Öppna en ny flik i webbläsaren och logga in på ditt Salesforce-Sandbox-administratörskonto.

19. Klicka på **Setup** (Konfiguration) under **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure1.png)

20. Rulla ned till den **inställningar** i det vänstra navigeringsfönstret klickar du på **identitet** att expandera avsnittet relaterade. Klicka sedan på **Inställningar för enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

21. På sidan med **Inställningar för enkel inloggning** klickar du på **Redigera**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure3.png)

22. Välj **SAML-aktiverat** och klicka på **Spara**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

23. Konfigurera inställningarna för enkel inloggning med SAML genom att klicka på **Nytt från metadatafil**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

24. Klicka på **Välj fil** överföra den XML-fil och klicka på **skapa**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/xmlchoose.png)

25. På den **SAML enkel inloggning inställningar** sidan fält fylla automatiskt, skriver du namnet på konfigurationen (till exempel: *SPSSOWAAD_Test*) i den **namn** textrutan och klicka på Spara.

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

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](./media/salesforce-sandbox-tutorial/create_aaduser_01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="create-a-salesforce-sandbox-test-user"></a>Skapa en testanvändare i Salesforce Sandbox

I det här avsnittet skapas en användare som kallas Britta Simon i Salesforce Sandbox. Salesforce-Sandbox stöder just-in-time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Salesforce Sandbox, skapas en ny när du försöker få åtkomst till Salesforce Sandbox. Salesforce-Sandbox stöder även automatisk användaretablering, kan du hitta mer information om [här](salesforce-sandbox-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Salesforce Sandbox.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Salesforce sandbox-miljön, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Salesforce Sandbox**.

    ![Salesforce-Sandbox-länk i listan med program](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”Användare och grupper”][202]

4. Klicka på **Lägg till användare** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Salesforce Sandbox i åtkomstpanelen du bör få automatiskt loggat in på ditt Salesforce-Sandbox-program.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera användarförsörjning](salesforce-sandbox-provisioning-tutorial.md)

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
