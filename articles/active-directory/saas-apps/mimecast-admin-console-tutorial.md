---
title: 'Självstudier: Azure Active Directory-integrering med Mimecast Admin Console | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mimecast Admin Console.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: 9ad9be1d1573bd3c57a3462f1f6ab53152a4469d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54809742"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Självstudier: Azure Active Directory-integrering med Mimecast Admin Console

I den här självstudien får du lära dig hur du integrerar Mimecast Admin Console med Azure Active Directory (AD Azure).
Integreringen av Mimecast Admin Console med Azure AD medför följande fördelar:

* Du kan styra vem som har åtkomst till Mimecast Admin Console från Azure AD.
* Du kan konfigurera inställningar så att dina användare kan logga in automatiskt i Mimecast Admin Console (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Mimecast Admin Console behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En Mimecast Admin Console-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Mimecast Admin Console stöder **SP**-initierad enkel inloggning

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Lägga till Mimecast Admin Console från galleriet

För att konfigurera integreringen av Mimecast Admin Console med Azure AD måste du lägga till Mimecast Admin Console från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Mimecast Admin Console från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Mimecast Admin Console**, väljer **Mimecast Admin Console** från resultatpanelen och klickar på **Lägg till** för att lägga till programmet.

     ![Mimecast Admin Console i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med Mimecast Admin Console baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Mimecast Admin Console upprättas.

För att konfigurera och testa enkel inloggning i Azure AD med Mimecast Admin Console måste du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Mimecast Admin Console](#configure-mimecast-admin-console-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Mimecast Admin Console-testanvändare](#create-mimecast-admin-console-test-user)**  – för att skapa en motsvarighet till Britta Simon i Mimecast Admin Console som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Mimecast Admin Console:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Mimecast Admin Console** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Mimecast Admin Console-domäner och -URL:er](common/sp-signonurl.png)

    I rutan **Inloggnings-URL** anger du URL:en:
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > Inloggnings-URL:en är regionsspecifik.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Mimecast Admin Console** kopierar du lämpliga URL:er baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-mimecast-admin-console-single-sign-on"></a>Konfigurera enkel inloggning för Mimecast Admin Console

1. Öppna ett nytt webbläsarfönster och logga in i Mimecast Admin Console som administratör.

2. Gå till **Tjänster \> Program**.

    ![Tjänster](./media/mimecast-admin-console-tutorial/ic794998.png "Tjänster")

3. Klicka på **Autentiseringsprofiler**.

    ![Autentiseringsprofiler](./media/mimecast-admin-console-tutorial/ic794999.png "Autentiseringsprofiler")
    
4. Klicka på **Ny autentiseringsprofil**.

    ![Nya autentiseringsprofiler](./media/mimecast-admin-console-tutorial/ic795000.png "Nya autentiseringsprofiler")

5. Utför följande steg i avsnittet **Autentiseringsprofil**:

    ![Autentiseringsprofil](./media/mimecast-admin-console-tutorial/ic795015.png "Autentiseringsprofil")
    
    a. Ange ett namn för din konfiguration i textrutan **Beskrivning**.
    
    b. Välj alternativet att **kräva SAML-autentisering för Mimecast Admin Console**.
    
    c. Som **Provider** väljer du **Azure Active Directory**.
    
    d. Klistra in **Azure AD-identifieraren**, som du har kopierat från Azure-portalen till textrutan **Utgivar-URL**.
    
    e. Klistra in **inloggnings-URL:en**, som du har kopierat från Azure-portalen till textrutan **Inloggnings-URL**.

    f. Klistra in **inloggnings-URL:en**, som du har kopierat från Azure-portalen till textrutan **Utloggnings-URL**.
    
    >[!NOTE]
    >Värdet för inloggnings-URL:en och värdet för utloggnings-URL:en är samma för Mimecast Admin Console.
    
    g. Öppna ditt base-64-certifikat som du hämtat från Azure-portalen i Anteckningar. Ta bort den första raden (”*--*”) och den sista raden (”*--*”). Kopiera resten av innehållet till Urklipp och klistra in det i textrutan **Identity Provider Certificate (Metadata)** (Certifikat för identitetsprovider (metadata)).
    
    h. Välj **Tillåt enkel inloggning**.
    
    i. Klicka på **Spara**.

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

I det här avsnittet ska du göra det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Mimecast Admin Console.

1. Välj **Företagsprogram** på Azure-portalen, välj **Alla program** och sedan **Mimecast Admin Console**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Skriv och välj **Mimecast Admin Console** i programlistan.

    ![Mimecast Admin Console-länken i listan över program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-mimecast-admin-console-test-user"></a>Skapa Mimecast Admin Console-testanvändare

För att Azure AD-användare ska kunna logga in i Mimecast Admin Console måste de vara etablerade i Mimecast Admin Console. När det gäller Mimecast Admin Console är etableringen en manuell åtgärd.

* Du måste registrera en domän innan du kan skapa användare.

**Utför följande steg för att konfigurera användarförsörjning:**

1. Logga in i **Mimecast Admin Console** som administratör.

2. Gå till **Kataloger \> Internt**.
   
    ![Kataloger](./media/mimecast-admin-console-tutorial/ic795003.png "Kataloger")

3. Klicka på **Registrera ny domän**.
   
    ![Registrera ny domän](./media/mimecast-admin-console-tutorial/ic795004.png "Registrera ny domän")

4. När den nya domänen har skapats klickar du på **Ny adress**.
   
    ![Ny adress](./media/mimecast-admin-console-tutorial/ic795005.png "Ny adress")

5. Utför följande steg i dialogrutan Ny adress:
   
    ![Spara](./media/mimecast-admin-console-tutorial/ic795006.png "Spara")
   
    a. Ange attributen **E-postadress**, **Globalt namn**, **Lösenord** och **Bekräfta lösenord** för ett giltigt Azure AD-konto som du vill etablera i de relevanta textrutorna.

    b. Klicka på **Spara**.

>[!NOTE]
>Du kan etablera Azure AD-användarkonton med hjälp av andra API:er eller genereringsverktyg för Mimecast Admin Console-användarkonton som är tillgängliga för Mimecast Admin Console. 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för Mimecast Admin Console på åtkomstpanelen bör du loggas in automatiskt i Mimecast Admin Console-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

