---
title: 'Självstudie: Azure Active Directory-integrering med TargetProcess | Microsoft Docs'
description: Lär dig att konfigurera enkel inloggning mellan Azure Active Directory och TargetProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/7/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6f5a20dc911faa9d6d8948543b6391d4a8ec2c9
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883757"
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Självstudie: Azure Active Directory-integrering med TargetProcess

I den här självstudien får du lära dig att integrera TargetProcess med Azure Active Directory (Azure AD).
Om du integrerar TargetProcess med Azure AD får du följande fördelar:

* Du kan använda Azure AD till att styra vilka som ska ha åtkomst till TargetProcess.
* Du kan låta dina användare loggas in automatiskt på TargetProcess (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med TargetProcess, måste du ha följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* TargetProcess-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* TargetProcess har stöd för **SP**-initierad enkel inloggning
* TargetProcess stöder användarförsörjning med **Just-in-time**

## <a name="adding-targetprocess-from-the-gallery"></a>Lägga till TargetProcess från galleriet

Om du vill konfigurera integreringen av TargetProcess i Azure AD, måste du lägga till TargetProcess från galleriet i din lista med hanterade SaaS-appar.

**Utför följande steg för att lägga till TargetProcess från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **TargetProcess** och väljer **TargetProcess** i resultatpanelen. Klicka sedan på knappen **Lägg till** för att lägga till programmet.

     ![TargetProcess i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med TargetProcess, baserat testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i TargetProcess upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med TargetProcess, måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa testanvändare för TargetProcess](#create-targetprocess-test-user)**  – för att få en motsvarighet till Britta Simon i TargetProcess som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure ADmed TargetProcess:

1. I [Azure Portal](https://portal.azure.com/) går du till sidan för **TargetProcess**-programintegration och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om TargetProcess-domän och URL:er med enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<subdomain>.tpondemand.com/`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<subdomain>.tpondemand.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare.  Kontakta [TargetProcess-klientens supportteam](mailto:support@targetprocess.com) för att hämta dessa värden.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera TargetProcess** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

7. Om du vill automatisera konfigurationen i **TargetProcess**, måste du installera **webbläsartillägget Enkel inloggning för mina appar** genom att klicka på **Installera tillägget**.

    ![image](./media/target-process-tutorial/install_extension.png)

8. När du har lagt till tillägg i webbläsaren klickar du på **Konfigurera TargetProcess**. Du omdirigeras då till TargetProcess-programmet. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på TargetProcess. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 9–13.

    **Om du vill konfigurera programmet manuellt utför du följande steg:**

9. Logga in på TargetProcess-programmet som administratör.

10. I menyn längst upp klickar du på **Konfiguration**.

    ![Konfiguration](./media/target-process-tutorial/tutorial_target_process_05.png)

11. Klicka på **Inställningar**.

    ![Inställningar](./media/target-process-tutorial/tutorial_target_process_06.png)

12. Klicka på **Enkel inloggning**.

    ![klicka på Enkel inloggning](./media/target-process-tutorial/tutorial_target_process_07.png)

13. Utför följande steg i inställningsdialogrutan för enkel inloggning:

    ![Konfigurera enkel inloggning](./media/target-process-tutorial/tutorial_target_process_08.png)

    a. Klicka på **Aktivera enkel inloggning**.

    b. I textrutan **Inloggnings-URL** klistrar du in värdet för **SAML-tjänstens URL för enkel inloggning** som du kopierade från Azure Portal.

    c. Öppna ditt nedladdade certifikatet i Anteckningar, kopiera innehållet och klistra sedan in det i textrutan **Certifikat**.

    d. klicka på **Aktivera JIT-etablering**.

    e. Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ser du till att Britta Simon ska kunna använda enkel inloggning i Azure genom att bevilja åtkomst till TargetProcess.

1. I Azure Portal väljer du **Företagsprogram**, **Alla program** och sedan **TargetProcess**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan anger du och väljer **TargetProcess**.

    ![Länken TargetProcess i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-targetprocess-test-user"></a>Skapa TargetProcess-testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i TargetProcess. TargetProcess har stöd för just-in-time-etablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas under ett försök att komma åt TargetProcess, om den inte redan finns.

> [!Note]
> Om du vill skapa en användare manuellt, kontaktar du  [TargetProcess-supportteamet](mailto:support@targetprocess.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på TargetProcess i åtkomstpanelen bör du bli automatiskt inloggad på den TargetProcess som du konfigurerade enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
