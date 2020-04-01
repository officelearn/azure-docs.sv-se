---
title: 'Självstudiekurs: Azure Active Directory-integrering med Projectplace | Microsoft-dokument'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093531"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Självstudiekurs: Integrera Projectplace med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Projectplace med Azure Active Directory (Azure AD). När du integrerar Projectplace med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Projectplace.
* Aktivera dina användare så att de automatiskt loggas in i Projectplace med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.
* Användare kan etableras automatiskt i Projectplace.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Projectplace single sign-on (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. Projectplace stöder **SP och IDP** initierade SSO och stöder just in time-användaretablering. **Just In Time**

## <a name="adding-projectplace-from-the-gallery"></a>Lägga till Projectplace från galleriet

Om du vill konfigurera integreringen av Projectplace i Azure AD måste du lägga till Projectplace från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Projectplace** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Projektplats** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Projectplace med en testanvändare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Projectplace.

Om du vill konfigurera och testa Azure AD SSO med Projectplace slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera Projectplace](#configure-projectplace)** för att konfigurera SSO-inställningarna på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med B. Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera B. Simon för att använda Azure AD enkel inloggning.
5. **[Skapa Projectplace-testanvändare](#create-projectplace-test-user)** för att ha en motsvarighet till B. Simon i Projectplace som är länkad till Azure AD-representationen för användaren.
6. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **Programintegrering** i **Projectplace** [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga url:erna redan är förifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på **knappen Spara.**

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i textrutan **Sign-on-URL:**`https://service.projectplace.com`

1. Klicka på **kopiaikonen** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** för att kopiera webbadressen för **appfederationsmetadata**, enligt dina krav och spara den i Anteckningar.

   ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Projectplace** baserat på dina krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Konfigurera Projectplace

Om du vill konfigurera enkel inloggning på **Projectplace-sidan** måste du skicka **url:en kopierad appfederationsmetadata** från Azure-portalen till [Projectplace-supportteamet](https://success.planview.com/Projectplace/Support). Det här teamet ser till att SAML SSO-anslutningen är korrekt inställd på båda sidor.

>[!NOTE]
>Den enda inloggningskonfigurationen måste utföras av [Projectplace-supportteamet](https://success.planview.com/Projectplace/Support). Du får en avisering så fort konfigurationen är klar. 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B. Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B. Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Projectplace.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Projectplace**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B. Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-projectplace-test-user"></a>Skapa Projectplace-testanvändare

>[!NOTE]
>Du kan hoppa över det här steget om du har etablering aktiverat i Projectplace. Du kan be [Projectplace supportteam](https://success.planview.com/Projectplace/Support) för att möjliggöra provisoning, när gjort användare kommer att skapas i Projectplace under den första inloggningen.

Om du vill att Azure AD-användare ska kunna logga in på Projectplace måste du lägga till dem i Projectplace. Du måste lägga till dem manuellt.

**Så här skapar du ett användarkonto:**

1. Logga in **Projectplace** på projectplace-företagswebbplatsen som administratör.

2. Gå till **Kontakter**och välj sedan **Medlemmar:**
   
    ![Gå till Kontakter och välj sedan Medlemmar](./media/projectplace-tutorial/ic790228.png "People")

3. Välj **Lägg till medlem:**
   
    ![Välj Lägg till medlem](./media/projectplace-tutorial/ic790232.png "Lägg till medlemmar")

4. Gör följande i avsnittet **Lägg till medlem.**
   
    ![Lägg till medlemsavsnitt](./media/projectplace-tutorial/ic790233.png "Nya medlemmar")
   
    1. I rutan **Nya medlemmar** anger du e-postadressen till ett giltigt Azure AD-konto som du vill lägga till.
   
    1. Välj **Skicka**.

   Ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan det blir aktivt skickas till Azure AD-kontoinnehavaren.

>[!NOTE]
>Du kan också använda andra verktyg eller API för att skapa användarkonton som tillhandahålls av Projectplace för att lägga till Azure AD-användarkonton.


### <a name="test-sso"></a>Testa SSO

När du väljer panelen Projectplace på åtkomstpanelen bör du automatiskt loggas in på den Projektplats som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)