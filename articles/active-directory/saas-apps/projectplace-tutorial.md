---
title: 'Självstudier: Azure Active Directory-integrering med Projectplace | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Projectplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 179721075484c35c5ebbb3d936b83bc407b75a8d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093531"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Självstudier: Integrera Projectplace med Azure Active Directory

I de här självstudierna lär du dig att integrera Projectplace med Azure Active Directory (AD Azure). När du integrerar Projectplace med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Projectplace.
* Ge dina användare att automatiskt inloggad till Projectplace med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.
* Användare kan etableras i Projectplace automatiskt.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Aktiverat prenumeration Projectplace enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för Projectplace **SP och IDP** initierad SSO och stöder **Just In Time** etableringen av användare.

## <a name="adding-projectplace-from-the-gallery"></a>Att lägga till Projectplace från galleriet

För att konfigurera integrering av Projectplace i Azure AD, som du behöver lägga till Projectplace från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Projectplace** i sökrutan.
1. Välj **Projectplace** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Projectplace med en testanvändare kallas **B. Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Projectplace för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med Projectplace, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
2. **[Konfigurera Projectplace](#configure-projectplace)**  att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med B. Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera B. Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare Projectplace](#create-projectplace-test-user)**  har en motsvarighet för B. Simon i Projectplace som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Projectplace** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** om du vill konfigurera programmet i **IDP** initierad läge, programmet är förkonfigurerad och nödvändiga URL: er är redan ifyllda på förhand med Azure . Användaren behöver för att spara konfigurationen genom att klicka på den **spara** knappen.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du en URL: `https://service.projectplace.com`

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på Kopiera **ikonen** att kopiera den **Appfederationsmetadata** , enligt dina behov och spara den i anteckningar.

   ![Länk för hämtning av certifikat](common/copy-metadataurl.png)

1. På den **konfigurera Projectplace** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Konfigurera Projectplace

Att konfigurera enkel inloggning på den **Projectplace** sida, som du behöver skicka den kopierade **Appfederationsmetadata** från Azure portal för att den [Projectplace-supportteamet](https://success.planview.com/Projectplace/Support) . Det här laget säkerställer SAML SSO-anslutningen är korrekt inställda på båda sidorna.

>[!NOTE]
>Konfigurationen för enkel inloggning måste utföras av den [Projectplace-supportteamet](https://success.planview.com/Projectplace/Support). Du får ett meddelande när konfigurationen är klar. 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas B. Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `B. Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon att använda Azure enkel inloggning om du beviljar åtkomst till Projectplace.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **Projectplace**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B. Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-projectplace-test-user"></a>Skapa Projectplace testanvändare

>[!NOTE]
>Du kan hoppa över det här steget om du har aktiverat i Projectplace-etablering. Du kan ställa den [Projectplace-supportteamet](https://success.planview.com/Projectplace/Support) att aktivera etablerar, när klar användare kommer att skapas i Projectplace under den första inloggningen.

Du måste lägga till dem till Projectplace om du vill aktivera Azure AD-användare att logga in till Projectplace. Du måste lägga till dem manuellt.

**Skapa ett användarkonto genom att göra följande:**

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


### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen Projectplace i åtkomstpanelen, bör det vara loggas in automatiskt till Projectplace som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)