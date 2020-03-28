---
title: 'Självstudiekurs: Azure Active Directory-integrering med Costpoint | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Costpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01168540e27605db0d240c0774159a710b5d5254
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "71840126"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Självstudiekurs: Integrera Costpoint med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Costpoint med Azure Active Directory (Azure AD). När du integrerar Costpoint med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Costpoint.
* Aktivera dina användare så att de automatiskt loggas in på Costpoint med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En Costpoint-prenumeration (Single Sign-on) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. Costpoint stöder **SP och IDP** initierad SSO.

## <a name="generate-costpoint-metadata"></a>Generera Costpoint-metadata

Costpoint SAML SSO konfiguration **förklaras i DeltekCostpoint711Security.pdf** guide. Hämta den här guiden från supportwebbplatsen Deltek Costpoint och se **saml-installationen** > för enkel inloggning**Konfigurera SAML Enkel inloggning mellan Costpoint och Microsoft Azure.** Följ instruktionerna och generera en **COSTPOINT SP Federation Metadata XML-fil.** 

![Konfigurationsverktyg för Kostnadpunkt](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Lägg till Kostnadsställe från galleriet

Om du vill integrera Costpoint med Azure AD lägger du först till Costpoint i listan över hanterade SaaS-appar från galleriet i Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.

1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.

   ![Azure Active Directory-knappen](common/select-azuread.png)

1. Välj **Företagsprogram** > **Alla program**.

   ![Bladet Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program väljer du **Nytt program**.

   ![Knappen Nytt program](common/add-new-app.png)

1. I avsnittet **Lägg till från galleriet** anger du **Costpoint** i sökrutan.

   ![Kostnadsställe i resultatlistan](common/search-new-app.png)

1. Välj **Kostnadpunkt**i resultatlistan och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Konfigurera och testa Azure AD single sgn-on

Konfigurera och testa Azure AD SSO med Costpoint med hjälp av en testanvändare med namnet **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Costpoint.

Om du vill konfigurera och testa Azure AD SSO med Costpoint slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
1. **[Konfigurera Costpoint](#configure-costpoint)** för att konfigurera SAML SSO-inställningarna på programsidan.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med B.Simon.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera B.Simon att använda Azure AD enkel inloggning.
1. **[Skapa en Costpoint-testanvändare](#create-a-costpoint-test-user)** för att ha en motsvarighet till B.Simon i Costpoint som är länkad till Azure AD-representationen för användaren.
1. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Så här aktiverar du Azure AD SSO i Azure-portalen:

1. På sidan Integrering av **Costpoint-program** väljer du **Enkel inloggning**.

   ![Konfigurera den enda inloggningslänken](common/select-sso.png)

1. Gör så här om du har *metadatafilen för Tjänsteleverantören*i avsnittet **Grundläggande SAML-konfiguration:**

   > [!NOTE]
   > Du får metadatafilen för Tjänsteleverantören i [Generera Costpoint-metadata](#generate-costpoint-metadata). Hur du använder filen förklaras senare i självstudien.
 
   1. Välj knappen **Ladda upp metadatafil,** välj sedan **den XML-fil** för Costpoint SP Federation Metadata som tidigare genererats av Costpoint och välj sedan knappen **Lägg** till för att överföra filen.

      ![Ladda upp metadatafilen](./media/costpoint-tutorial/upload-metadata.png)
    
   1. När metadatafilen har överförts fylls **URL-värdena identifierare** och **svara** i automatiskt i avsnittet Kostnadsställe.

      > [!NOTE]
      > Om **URL-värdena identifierare** och **Svara** inte är automatisk polulated anger du värdena manuellt enligt dina krav. Kontrollera att **identifieraren (entitets-ID)** och **Svars-URL :n (Url för kontroll av konsumenttjänster)** har angetts korrekt och att **ACS-URL** är en giltig Costpoint-URL som slutar med **/LoginServlet.cps**.

   1. Välj **Ange ytterligare webbadresser**. För **Relay State**anger du ett`system=[your system]` värde med följande mönster: (till exempel **system=DELTEKCP**).

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** väljer du ikonen **Kopiera** för att kopiera **url:en för appfederationsmetadata** och spara den på Anteckningar.

   ![SAML-signeringscertifikat](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Konfigurera kostnadsställe

1. Återgå till konfigurationsverktyget för Kostnadpunkt. Klistra in innehållet i *url-filen App Federation Metadata Url* i **textrutan IdP Federation Metadata.** 

   ![Konfigurationsverktyg för Kostnadpunkt](./media/costpoint-tutorial/config-utility-idp.png)

1. Fortsätt instruktionerna från **DeltekCostpoint711Security.pdf** guide för att slutföra Costpoint SAML-installationen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen B.Simon.

1. I Den vänstra rutan i Azure-portalen väljer du **Azure Active Directory** > **Users** > **All users**.

   ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

1. Välj **Ny användare**.

   ![Knappen Ny användare](common/new-user.png)

1. Gör så här i **egenskaperna Användare:**

   ![Dialogrutan Användare](common/user-properties.png)

   1. Ange **B.Simon**i fältet **Namn** .
   
   1. I fältet **Användarnamn** anger `b.simon\@yourcompanydomain.extension` du (till exempel B.Simon@contoso.com).
   
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned värdet som visas i fältet **Lösenord.**
   
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja B.Simon åtkomst till Costpoint.

1. I Azure-portalen väljer du Alla**program för** >  **företagsprogram**.

1. Välj **Kostnadpunkt**i programlistan .

1. I avsnittet **Hantera** på appens översiktssida väljer du **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**. Välj **Användare och grupper**i dialogrutan Lägg till **tilldelning** .

   ![Länken Lägg till användare](common/add-assign-user.png)

1. Välj **B.Simon**i listan **Användare** **och grupper** . **Välj**sedan .

1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och väljer sedan **Välj**.

1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-costpoint-test-user"></a>Skapa en costpointtestanvändare

I det här avsnittet skapar du en användare i Costpoint. Anta att användar-ID är **B.SIMON** och användarens namn är **B.Simon**. Arbeta med [supportteamet för Costpoint-klienten](https://www.deltek.com/about/contact-us) för att lägga till användaren i Costpoint-plattformen. Användaren måste skapas och aktiveras innan de kan använda enkel inloggning.

När användaren har skapats måste användarens val **av autentiseringsmetod** vara **Active Directory,** kryssrutan **SAML-enkel inloggning** måste markeras och användarnamnet från Azure Active Directory måste vara Active Directory eller **certifikat-ID** (visas i följande skärmbild).

![Costpoint-användare](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Costpoint på åtkomstpanelen bör du automatiskt loggas in i Costpoint-programmet eftersom du ställer in SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
