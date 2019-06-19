---
title: 'Självstudier: Azure Active Directory-integrering med Oracle Cloud infrastruktur-konsolen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och infrastrukturen Oracle Cloud-konsolen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: f045fe19-11f8-4ccf-a3eb-8495fdc8716f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 456c984e577e3427ce8cd62d6f63987118f2c8ed
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164157"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Självstudier: Integrera Oracle Cloud-konsolen för infrastruktur med Azure Active Directory

I de här självstudierna lär du dig att integrera Oracle Cloud-konsolen för infrastruktur med Azure Active Directory (AD Azure). När du integrerar Oracle Cloud-konsolen för infrastruktur med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Oracle Cloud infrastruktur-konsolen.
* Ge dina användare att automatiskt inloggad till Oracle Cloud infrastruktur-konsolen med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Aktiverat prenumeration Oracle Cloud-konsolen för infrastruktur för enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för Oracle Cloud infrastruktur-konsolen **SP** -initierad SSO.

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Att lägga till Oracle Cloud infrastruktur-konsolen från galleriet

För att konfigurera integrering av infrastruktur Oracle Cloud-konsolen i Azure AD, som du behöver lägga till Oracle Cloud infrastruktur-konsolen från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Oracle Cloud infrastruktur-konsolen** i sökrutan.
1. Välj **Oracle Cloud infrastruktur-konsolen** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Oracle Cloud infrastruktur-konsolen med hjälp av en testanvändare kallas **B. Simon**. För enkel inloggning ska fungera, måste du upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Oracle Cloud infrastruktur-konsolen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Oracle Cloud infrastruktur-konsolen, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
1. **[Konfigurera Oracle Cloud infrastruktur-konsolen](#configure-oracle-cloud-infrastructure-console)**  att konfigurera inställningar för enkel inloggning på programsidan.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med B. Simon.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera B. Simon att använda Azure AD enkel inloggning.
1. **[Skapa testanvändare i Oracle Cloud infrastruktur-konsolen](#create-oracle-cloud-infrastructure-console-test-user)**  har en motsvarighet för B. Simon i Oracle Cloud infrastruktur-konsolen som är länkad till en Azure AD-representation av användaren.
1. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Oracle Cloud infrastruktur-konsolen** programsidan integration, hitta den **hantera** och väljer **enda inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** ange värdena för följande fält:

   > [!NOTE]
   > Du får tjänstleverantör metadatafilen från den **konfigurera Oracle Cloud Infrastructure konsolen enkel inloggning** avsnittet av självstudiekursen.
    
   1. Klicka på **ladda upp metadatafilen**.

   1. Klicka på **mappen logotyp** att välja metadatafilen och klicka på **överför**.

   1. När metadatafilen har laddats upp den **identifierare** och **svars-URL** värden får automatiskt ifylld i **SAML grundkonfiguration** avsnittet textrutan.
    
      > [!NOTE]
      > Om värdena **Identifierare** och **Svars-URL** inte fylls i automatiskt fyller du i värdena manuellt enligt dina krav.

      I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Oracle Cloud-konsolen Infrastrukturklienten supportteamet](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **XML-Metadata för Federation** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/metadataxml.png)

1. Oracle Cloud Infrastructure konsolprogram förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen **Redigera** för att öppna dialogrutan Användarattribut.

   ![image](common/edit-attribute.png)

1. Dessutom ovan Oracle Cloud infrastruktur-konsolen program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I den **användarattribut och anspråk** avsnittet på den **gruppanspråk (förhandsversion)**  dialogrutan utför följande steg:

   1. Klicka på den **penna** bredvid **namnge identifierarvärde**.

   1. Välj **beständiga** som **Välj format för namn på identifierare**.
 
   1. Klicka på **Spara**.

      ![image](./media/oracle-cloud-tutorial/config07.png)
    
      ![image](./media/oracle-cloud-tutorial/config11.png)

   1. Klicka på den **penna** bredvid **grupper returneras i anspråk**.

   1. Välj **säkerhetsgrupper** från listan över radio.

   1. Välj **källattributet** av **grupp-ID:** .

   1. Kontrollera **har ändrat namnet på gruppanspråket**.

   1. I den **namn** textruta, typ **groupName**.

   1. I den **Namespace (valfritt)** textruta, typ `https://auth.oraclecloud.com/saml/claims`.

   1. Klicka på **Spara**.

      ![image](./media/oracle-cloud-tutorial/config08.png)

1. På den **ställa in Oracle Cloud infrastruktur-konsolen** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-oracle-cloud-infrastructure-console"></a>Konfigurera Oracle Cloud infrastruktur-konsolen

1. I ett annat webbläsarfönster, loggar du in Oracle Cloud infrastruktur-konsolen som administratör.

1. Klicka på menyn till vänster och klicka på **identitet** gå sedan till **Federation**.

   ![Konfiguration](./media/oracle-cloud-tutorial/config01.png)

1. Spara den **tjänstleverantör metadatafil** genom att klicka på den **ladda ned det här dokumentet** länka och överför den till den **grundkonfiguration SAML** på Azure portal och sedan Klicka på **Lägg till identitetsprovider**.

   ![Konfiguration](./media/oracle-cloud-tutorial/config02.png)

1. På den **Lägg till identitetsprovider** popup, utför följande steg:

   ![Konfiguration](./media/oracle-cloud-tutorial/config03.png)

   1. I den **namn** text, ange ditt namn.

   1. I den **beskrivning** text, ange en beskrivning.

   1. Välj **MICROSOFT ACTIVE DIRECTORY FEDERATION SERVICE (AD FS) eller KOMPATIBLA SAML 2.0-IDENTITETSPROVIDER** som **typ**.

   1. Klicka på **Bläddra** att ladda upp Federation Metadata-XML som du har hämtat från Azure-portalen.

   1. Klicka på **Fortsätt** på den **redigera identitetsprovider** avsnittet utför följande steg:

      ![Konfiguration](./media/oracle-cloud-tutorial/config09.png)

   1. För **identitet PROVIDERN grupp** fält, anger du ett namn och grupp-ID som har ställts in på Azure-portalen. Gruppen måste mappas med motsvarande grupp i **OCI grupp** fält.

   1. Du kan mappa flera grupper enligt inställningarna i Azure portal och din organisation behöver. Klicka på **+ Lägg till mappning** att lägga till så många grupper som du behöver.

   1. Klicka på **Skicka**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas B. Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `B. Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `B. Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Oracle Cloud infrastruktur-konsolen.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **Oracle Cloud infrastruktur-konsolen**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

   ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B. Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Skapa testanvändare i Oracle Cloud infrastruktur-konsolen

 Oracle Cloud infrastruktur-konsolen stöder just-in-time-etablering, vilket är som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare inte skapas vid ett försök till åtkomst och även behöver inte skapa användaren.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen Oracle Cloud infrastruktur-konsolen i åtkomstpanelen, omdirigeras till inloggningssidan för Oracle Cloud infrastruktur-konsolen. Välj den **IDENTITETSPROVIDERN** från den nedrullningsbara menyn och klicka på **Fortsätt** som visas nedan för att logga in. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

![Konfiguration](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
