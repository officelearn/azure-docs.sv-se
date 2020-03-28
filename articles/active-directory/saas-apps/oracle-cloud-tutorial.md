---
title: 'Självstudiekurs: Azure Active Directory-integrering med Oracle Cloud Infrastructure Console | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Oracle Cloud Infrastructure Console.
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
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64cae5812a380725d612d27190042797542ee255
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76289109"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Självstudiekurs: Integrera Oracles molninfrastrukturkonsol med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Oracle Cloud Infrastructure Console med Azure Active Directory (Azure AD). När du integrerar Oracle Cloud Infrastructure Console med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Oracle Cloud Infrastructure Console.
* Gör det möjligt för användarna att automatiskt loggas in på Oracles molninfrastrukturkonsol med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Oracle Cloud Infrastructure Console enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Oracle Cloud Infrastructure Console stöder **SP** initierade SSO.
* När du har konfigurerat Oracles molninfrastrukturkonsol kan du framtvinga sessionskontroller som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollerna sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Lägga till Oracle Cloud Infrastructure Console från galleriet

Om du vill konfigurera integreringen av Oracle Cloud Infrastructure Console i Azure AD måste du lägga till Oracle Cloud Infrastructure Console från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Oracle Cloud Infrastructure Console** i sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **Oracle Cloud Infrastructure Console** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Oracle Cloud Infrastructure Console med hjälp av en testanvändare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Oracle Cloud Infrastructure Console.

Så här konfigurerar och testar du Azure AD SSO med Oracle Cloud Infrastructure Console:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med B. Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera B. Simon för att använda Azure AD enkel inloggning.
1. **[Konfigurera Oracle Cloud Infrastructure Console](#configure-oracle-cloud-infrastructure-console)** för att konfigurera SSO-inställningarna på programsidan.
    1. **[Skapa Oracle Cloud Infrastructure Console-testanvändare](#create-oracle-cloud-infrastructure-console-test-user)** för att ha en motsvarighet till B. Simon i Oracles molninfrastrukturkonsol som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan **Hantera** på sidan [Azure](https://portal.azure.com/)Portal och välj Enkel inloggning på sidan Oracle Cloud Infrastructure **Console-programintegrering.** **Oracle Cloud Infrastructure Console**
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

   > [!NOTE]
   > Du får metadatafilen för Tjänsteleverantören från avsnittet **Konfigurera Oracle Cloud Infrastructure Console Enkel inloggning i** självstudien.
    
   1. Klicka på **Ladda upp metadatafil**.

   1. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

   1. När metadatafilen har överförts fylls **URL-värdena identifierare** och **svar** i i id i textrutan **Grundläggande SAML-konfiguration.**
    
      > [!NOTE]
      > Om värdena **Identifierare** och **Svars-URL** inte fylls i automatiskt fyller du i värdena manuellt enligt dina krav.

      Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Oracles supportteam för molninfrastrukturkonsolklienter](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

   ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Oracle Cloud Infrastructure Console-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

   ![image](common/edit-attribute.png)

1. Utöver ovanstående förväntar sig Oracle Cloud Infrastructure Console-programmet att få fler attribut skickas tillbaka i SAML-svar. Gör följande i avsnittet **Användarattribut & anspråk** i dialogrutan **Gruppanspråk (förhandsversion):**

   1. Klicka på **pennan** **bredvid namnidentifieringsvärde**.

   1. Välj **Beständigt** som **Välj namnidentifierarformat**.
 
   1. Klicka på **Spara**.

      ![image](./media/oracle-cloud-tutorial/config07.png)
    
      ![image](./media/oracle-cloud-tutorial/config11.png)

   1. Klicka på **pennan** **bredvid Grupper som returneras i anspråk**.

   1. Välj **Säkerhetsgrupper** i radiolistan.

   1. Välj **källattribut för** **grupp-ID**.

   1. Markera **Anpassa namnet på gruppanspråket**.

   1. Skriv **groupName**i textrutan **Namn** .

   1. Skriv `https://auth.oraclecloud.com/saml/claims`i textrutan **Namnområde (valfritt)** .

   1. Klicka på **Spara**.

      ![image](./media/oracle-cloud-tutorial/config08.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Oracle Cloud Infrastructure Console** baserat på dina krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)



### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B. Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B. Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B. Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Oracle Cloud Infrastructure Console.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj Oracle Cloud **Infrastructure Console**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

   ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B. Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-oracle-cloud-infrastructure-console"></a>Konfigurera Oracles molninfrastrukturkonsol

1. I ett annat webbläsarfönster loggar du in på Oracle Cloud Infrastructure Console som administratör.

1. Klicka på vänster sida av menyn och klicka på **Identitet** sedan navigera till **Federation**.

   ![Konfiguration](./media/oracle-cloud-tutorial/config01.png)

1. Spara **metadatafilen för Tjänstprovidern** genom att klicka på länken **Hämta det här dokumentet** och ladda upp den till avsnittet Grundläggande **SAML-konfiguration** i Azure-portalen och klicka sedan på **Lägg till identitetsprovider**.

   ![Konfiguration](./media/oracle-cloud-tutorial/config02.png)

1. Gör följande på popup-fönstret **Lägg till identitetsprovider:**

   ![Konfiguration](./media/oracle-cloud-tutorial/config03.png)

   1. Ange ditt namn i textrutan **NAMN.**

   1. Ange din beskrivning i textrutan **BESKRIVNING.**

   1. Välj **Microsoft Active Directory Federation Service (ADFS) eller SAML 2.0-kompatibel IDENTITETSPROVIDER** SOM **TYP**.

   1. Klicka på **Bläddra** om du vill ladda upp XML-koden för federationsmetadata som du har hämtat från Azure-portalen.

   1. Klicka på **Fortsätt** och utför följande steg i avsnittet **Redigera identitetsprovider:**

      ![Konfiguration](./media/oracle-cloud-tutorial/config09.png)

   1. **IDENTITETSPROVIDERGRUPPEN** ska väljas som anpassad grupp. GROUP-ID:et ska vara GUID för gruppen från Azure Active Directory. Gruppen måste mappas med motsvarande grupp i **fältet OCI GROUP.**

   1. Du kan mappa flera grupper enligt din konfiguration i Azure Portal och ditt organisationsbehov. Klicka på **+ Lägg till mappning** för att lägga till så många grupper som du behöver.

   1. Klicka på **Skicka**.
   
### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Skapa testanvändare för Oracle Cloud Infrastructure Console

 Oracle Cloud Infrastructure Console stöder just-in-time-etablering, vilket är som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas inte under ett försök att komma åt och behöver inte heller skapa användaren.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Oracle Cloud Infrastructure Console på åtkomstpanelen omdirigeras du till inloggningssidan för Oracle Cloud Infrastructure Console. Välj **IDENTITETSPROVIDER** PÅ den nedrullningsbara menyn och klicka på **Fortsätt** som visas nedan för att logga in. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

![Konfiguration](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Så här skyddar du Oracles molninfrastrukturkonsol med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
