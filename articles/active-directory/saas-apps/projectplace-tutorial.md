---
title: 'Självstudier: Azure Active Directory-integrering med Projectplace | Microsoft Docs'
description: I de här självstudierna lär du dig att konfigurera enkel inloggning mellan Azure Active Directory och Projectplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 17fbc6bc4f022a15c34c5ca7b9465be392cdc639
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560633"
---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Självstudier: Azure Active Directory-integrering med Projectplace

I de här självstudierna lär du dig att integrera Projectplace med Azure Active Directory (AD Azure).

Den här integrationen har följande fördelar:

* Du kan använda Azure AD för att kontrollera vem som har åtkomst till Projectplace.
* Du kan aktivera användarna att logga in automatiskt till Projectplace (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har någon Azure-prenumeration [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Projectplace, behöver du:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du registrera dig för en [utvärderingsmånad](https://azure.microsoft.com/pricing/free-trial/) prenumeration.
* En Projectplace-prenumeration som har enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien får du konfigurera och testa Azure AD enkel inloggning i en testmiljö.

* Projectplace stöder SP-initierad SSO.

## <a name="add-projectplace-from-the-gallery"></a>Lägg till Projectplace från galleriet

Om du vill konfigurera integrering av Projectplace i Azure AD, som du behöver lägga till Projectplace från galleriet i din lista över hanterade SaaS-appar.

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan väljer **Azure Active Directory**:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **företagsprogram** > **alla program**:

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett program, Välj **nytt program** överst i fönstret:

    ![Välj nytt program](common/add-new-app.png)

4. I sökrutan anger **Projectplace**. Välj **Projectplace** i sökresultatet och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Projectplace med hjälp av en användare med namnet Britta Simon.
Om du vill aktivera enkel inloggning, måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i Projectplace.

Om du vill konfigurera och testa Azure AD enkel inloggning med Projectplace, måste du slutföra de här stegen:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  att aktivera funktionen för dina användare.
2. **[Konfigurera Projectplace enkel inloggning](#configure-projectplace-single-sign-on)**  på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Azure AD enkel inloggning för användaren.
5. **[Skapa en testanvändare Projectplace](#create-a-projectplace-test-user)**  som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen.

Konfigurera Azure AD enkel inloggning med Projectplace genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Projectplace** application integration markerar **enkel inloggning**:

    ![Välj enkel inloggning](common/select-sso.png)

2. I den **väljer du en metod för enkel inloggning** dialogrutan **SAML/WS-Fed** läge för att aktivera enkel inloggning:

    ![Välj en metod för enkel inloggning](common/select-saml-option.png)

3. På den **ange in enkel inloggning med SAML** väljer den **redigera** ikonen för att öppna den **SAML grundkonfiguration** dialogrutan:

    ![Ikonen Redigera](common/edit-urls.png)

4. I den **SAML grundkonfiguration** i dialogrutan den **inloggnings-URL** anger en URL i det här mönstret:

    `https://<company>.projectplace.com`

   ![Dialogrutan för grundläggande SAML-konfiguration](common/sp-signonurl.png)
    > [!NOTE]
    > Det här värdet är en platshållare. Du måste använda faktiska inloggnings-URL: en. Kontakta den [Projectplace-supportteamet](https://success.planview.com/Projectplace/Support) att hämta värdet. Du kan också referera till de mönster som visas i den **SAML grundkonfiguration** dialogrutan i Azure-portalen.

5. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** väljer den **hämta** länka bredvid **Federation Metadata-XML** enligt krav och spara certifikatet på datorn:

    ![Länk för nedladdning av certifikat](common/metadataxml.png)

6. I den **konfigurera Projectplace** avsnittet, kopiera de lämpliga URL: er, baserat på dina krav.

    ![Kopiera URL: er för konfiguration](common/copy-configuration-urls.png)

    1. **Inloggnings-URL**.

    1. **Azure AD-identifierare**.

    1. **URL för utloggning**.

### <a name="configure-projectplace-single-sign-on"></a>Konfigurera Projectplace enkel inloggning

Att konfigurera enkel inloggning på den **Projectplace** sida, som du behöver skicka de hämtade **XML-Metadata för Federation** certifikat och URL: er som du kopierade från Azure portal för att den [ Projectplace-supportteamet](https://success.planview.com/Projectplace/Support). Det här laget säkerställer SAML SSO-anslutningen är korrekt inställda på båda sidorna.

>[!NOTE]
>Konfigurationen för enkel inloggning måste utföras av den [Projectplace-supportteamet](https://success.planview.com/Projectplace/Support). Du får ett meddelande när konfigurationen är klar.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** i den vänstra rutan väljer **användare**, och välj sedan **alla användare**:

    ![Välj alla användare](common/users.png)

2. Välj **ny användare** överst på skärmen:

    ![Välj ny användare](common/new-user.png)

3. I den **användaren** dialogrutan rutan, vidta följande steg.

    ![Användardialogrutan](common/user-properties.png)

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I den **användarnamn** anger **BrittaSimon @\<företagsdomänen >.\< tillägget >**. (Till exempel BrittaSimon@contoso.com.)

    1. Välj **visa lösenord**, och sedan skriva ned det värde som är i den **lösenord** box.

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera Britta Simon att använda Azure AD enkel inloggning ger användarens företagsidentitet åtkomst till Projectplace.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**, och välj sedan **Projectplace**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Projectplace**.

    ![Lista över program](common/all-applications.png)

3. I den vänstra rutan väljer **användare och grupper**:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I den **användare och grupper** dialogrutan **Britta Simon** i listan och klicka sedan på den **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran i den **Välj roll** dialogrutan väljer du rätt roll för användaren i listan. Klicka på den **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-projectplace-test-user"></a>Skapa en Projectplace-testanvändare

Du måste lägga till dem till Projectplace om du vill aktivera Azure AD-användare att logga in till Projectplace. Du måste lägga till dem manuellt.

Skapa ett användarkonto genom att göra följande:

1. Logga in på din **Projectplace** företagets platsen som en administratör.

2. Gå till **personer**, och välj sedan **medlemmar**:
   
    ![Gå till personer och välj sedan medlemmar](./media/projectplace-tutorial/ic790228.png "personer")

3. Välj **Lägg till medlem**:
   
    ![Välj Lägg till medlem](./media/projectplace-tutorial/ic790232.png "lägga till medlemmar")

4. I den **Lägg till medlem** avsnittet, vidta följande steg.
   
    ![Lägg till medlem avsnitt](./media/projectplace-tutorial/ic790233.png "nya medlemmar")
   
    1. I den **nya medlemmar** anger du e-postadressen till en giltig Azure AD-konto som du vill lägga till.
   
    1. Välj **Skicka**.

   Ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan det aktiveras skickas till Azure AD-kontoinnehavaren.

>[!NOTE]
>Du kan också använda ett annat användarkonto skapas verktyg eller API tillhandahålls av Projectplace att lägga till användarkonton i Azure AD.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu ska du testa Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen Projectplace i åtkomstpanelen, bör det vara loggas in automatiskt till Projectplace-instansen som du ställer in enkel inloggning. Mer information finns i [öppna och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
