---
title: 'Självstudier: Azure Active Directory-integration med Huddle | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Huddle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 050a5a210ab10a557b149513a0416b188f4618ba
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885911"
---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>Självstudier: Azure Active Directory-integration med Huddle

I den här självstudien får du lära dig hur du integrerar Huddle med Azure Active Directory (AD Azure).

Integrera Huddle med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Huddle
- Du kan aktivera användarna att automatiskt få loggat in på Huddle (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Huddle, behöver du följande objekt:

- En Azure AD-prenumeration
- En Huddle enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Huddle från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-huddle-from-the-gallery"></a>Att lägga till Huddle från galleriet

Om du vill konfigurera integreringen av Huddle i Azure AD, som du behöver lägga till Huddle från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Huddle från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Huddle**. Välj **Huddle** i resultatrutan och klicka sedan på den **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/huddle-tutorial/tutorial_huddle_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Huddle baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Huddle är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Huddle upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Huddle, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Huddle](#creating-a-huddle-test-user)**  – du har en motsvarighet för Britta Simon i Huddle som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Huddle program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Huddle:**

1. I Azure-portalen på den **Huddle** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. Klicka på **ändra enkel inloggningsläge** på skärmen för att välja den **SAML** läge.

    ![Konfigurera enkel inloggning](./media/huddle-tutorial/tutorial_general_300.png)

3. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/huddle-tutorial/tutorial_general_301.png)

4. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](./media/huddle-tutorial/tutorial_general_302.png)

5. På den **SAML grundkonfiguration** avsnittet utför följande steg om du vill konfigurera programmet i **IDP** intiated läge:

    > [!NOTE]
    > Din instans av huddle identifieras automatiskt från den domän som du anger nedan.

    ![Huddle domän och URL: er enkel inloggning för information](./media/huddle-tutorial/tutorial_huddle_url.png)

    a. I den **identifierare** textrutan anger du ett URL:

    | | |
    |--|--|
    | `https://login.huddle.net`|
    | `https://login.huddle.com`|
    | |

    b. I den **svars-URL** textrutan anger du ett URL:

    | | |
    |--|--|
    | `https://login.huddle.net/saml/browser-sso`|
    | `https://login.huddle.com/saml/browser-sso`|
    | `https://login.huddle.com/saml/idp-initiated-sso`|
    | |

    c. Klicka på **ange ytterligare webbadresser** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Huddle domän och URL: er enkel inloggning för information](./media/huddle-tutorial/tutorial_huddle_url1.png)

    I den **inloggnings-URL** textrutan skriver du en av URL-Adressen med följande mönster:

    | | |
    |--|--|
    | `https://<customsubdomain>.huddle.com`|
    | `https://us.huddle.com`|
    | |

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [Huddle klienten supportteamet](https://huddle.zendesk.com) att hämta det här värdet.

6. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned rätt certifikat som uppfyller dina krav och spara den på din dator.

    ![Konfigurera enkel inloggning](./media/huddle-tutorial/tutorial_huddle_certificate.png)

7. På den **konfigurera Huddle** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Konfigurera enkel inloggning](./media/huddle-tutorial/tutorial_huddle_configure.png)

8. Att konfigurera enkel inloggning på **Huddle** sida, som du behöver skicka det certifikat som du har hämtat och URL: er som du har kopierat från **konfigurera** **Huddle** avsnittet från Azure portal för att [Huddle klienten supportteamet](https://huddle.zendesk.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

    >[!NOTE]
    > Enkel inloggning måste aktiveras av Huddle support-teamet. Du får ett meddelande när konfigurationen har slutförts.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](./media/huddle-tutorial/create_aaduser_01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](./media/huddle-tutorial/create_aaduser_02.png)

    a. I den **namn** ange **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="creating-a-huddle-test-user"></a>Skapa en Huddle testanvändare

Om du vill aktivera Azure AD-användare att logga in på Huddle, måste de etableras i Huddle. När det gäller Huddle är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på din **Huddle** företagets plats som administratör.

2. Klicka på **arbetsytan**.

3. Klicka på **personer \> bjuda in människor**.

    ![Personer](./media/huddle-tutorial/IC787838.png "personer")

4. I den **skapa en ny inbjudan** avsnittet, utför följande steg:
  
    ![Ny inbjudan](./media/huddle-tutorial/IC787839.png "ny inbjudan")
  
    a. I den **Välj ett team för att bjuda in människor att ansluta till** väljer **team**.

    b. Typen i **e-postadress** av en giltig Azure AD-konto som du vill etablera i att **RETUR e-postadress för personer som du vill bjuda in** textrutan.

    c. Klicka på **bjuda in**.

    >[!NOTE]
    > Azure AD-kontoinnehavare får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktiv.

>[!NOTE]
>Du kan använda andra verktyg för Huddle att skapa användaren-konto eller API: er som tillhandahålls av Huddle för att etablera användarkonton i Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Huddle.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Huddle**.

    ![Konfigurera enkel inloggning](./media/huddle-tutorial/tutorial_huddle_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Huddle i åtkomstpanelen, får du automatiskt inloggningssidan i Huddle program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/huddle-tutorial/tutorial_general_01.png
[2]: ./media/huddle-tutorial/tutorial_general_02.png
[3]: ./media/huddle-tutorial/tutorial_general_03.png
[4]: ./media/huddle-tutorial/tutorial_general_04.png
[100]: ./media/huddle-tutorial/tutorial_general_100.png
[200]: ./media/huddle-tutorial/tutorial_general_200.png
[201]: ./media/huddle-tutorial/tutorial_general_201.png
[202]: ./media/huddle-tutorial/tutorial_general_202.png
[203]: ./media/huddle-tutorial/tutorial_general_203.png
