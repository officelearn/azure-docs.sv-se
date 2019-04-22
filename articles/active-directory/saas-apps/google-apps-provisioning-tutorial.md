---
title: 'Självstudier: Konfigurera G Suite för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablera och avetablera användarkonton från Azure AD till G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea1f4d4a6b60961515826a1ba7409bf149b318e8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281309"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Självstudier: Konfigurera G Suite för automatisk användaretablering

Målet med den här självstudien är att visa dig hur du automatiskt etablera och avetablera användarkonton från Azure Active Directory (AD Azure) till G Suite.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med G Suite, behöver du följande objekt:

- En Azure AD-prenumeration
- En prenumeration med enkel inloggning med G Suite aktiverat
- En Google Apps-prenumeration eller Google Cloud Platform-prenumeration.

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-g-suite"></a>Tilldela användare till G Suite

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, synkroniseras de användare och grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare eller grupper i Azure AD behöver åtkomst till din app. När du har gjort detta beslut, du kan tilldela dessa användare till din app genom att följa instruktionerna i [tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

> [!IMPORTANT]
> Vi rekommenderar att en enda Azure AD-användare tilldelas till G Suite för att testa etablering konfigurationen. Du kan tilldela ytterligare användare och grupper senare.
> 
> När du tilldelar en användare till G Suite, väljer du den **användaren** eller **grupp** roll i dialogrutan tilldelning. Den **standard åtkomst** rollen fungerar inte för etablering.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk användaretablering

Det här avsnittet vägleder dig genom processen för att ansluta din Azure AD till det användarkonto som etablerar API för G Suite. Du kan dessutom konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i G Suite baserat på användare och grupptilldelning i Azure AD.

>[!TIP]
>Du kan också välja att aktivera SAML-baserad enkel inloggning för G-paket genom att följa anvisningarna i den [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna komplettera varandra.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurera automatisk användarens kontoetablering

> [!NOTE]
> En annan genomförbart alternativ för att automatisera användaretablering för G Suite är att använda [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en). GADS etablerar din lokala Active Directory-identiteter på G Suite. Lösningen i den här självstudien etablerar däremot din Azure Active Directory (moln)-användare och e-postaktiverade grupper till G Suite. 

1. Logga in på den [Google Apps-administratörskonsolen](https://admin.google.com/) med ditt administratörskonto och välj sedan **Security**. Om du inte ser länken, kan den vara dold den **fler kontroller** menyn längst ned på skärmen.

    ![Välj säkerhet.][10]

1. På den **Security** väljer **API-referens**.

    ![Välj API-referens.][15]

1. Välj **aktivera API-åtkomst**.

    ![Välj API-referens.][16]

   > [!IMPORTANT]
   > För varje användare som du vill etablera till G Suite sina användarnamn i Azure Active Directory *måste* vara bundna till en anpassad domän. Exempelvis användarens namn som bob@contoso.onmicrosoft.com tillåts inte av G Suite. Å andra sidan bob@contoso.com accepteras. Du kan ändra en befintlig användares domän genom att redigera deras egenskaper i Azure AD. Innehåller instruktioner för hur du ställer in en anpassad domän för både Azure Active Directory och G Suite i följande steg.

1. Om du inte har lagt till ett anpassat domännamn till Azure Active Directory ännu, gör du följande:
  
    a. I den [Azure-portalen](https://portal.azure.com), i det vänstra navigeringsfönstret väljer **Active Directory**. I kataloglistan väljer du din katalog.

    b. Välj **domännamn** på den vänstra navigeringsfönstret och välj sedan **Lägg till**.

    ![Domain](./media/google-apps-provisioning-tutorial/domain_1.png)

    ![Lägg till domän](./media/google-apps-provisioning-tutorial/domain_2.png)

    c. Skriv ditt domännamn till den **domännamn** fält. Det här domännamnet måste vara samma domännamn som du planerar att använda för G Suite. Välj sedan den **Lägg till domän** knappen.

    ![Domännamn](./media/google-apps-provisioning-tutorial/domain_3.png)

    d. Välj **nästa** att gå till sidan för verifiering. Kontrollera att du äger den här domänen genom att redigera domänens DNS-poster enligt de värden som finns på den här sidan. Du kan välja att verifiera genom att använda antingen **MX-poster** eller **TXT-poster**, beroende på vad du väljer för den **posttypen** alternativet.

    Mer omfattande information om hur du verifierar domännamn med Azure AD finns [lägga till ditt eget domännamn i Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).

    ![Domain](./media/google-apps-provisioning-tutorial/domain_4.png)

    e. Upprepa föregående steg för alla domäner som du vill lägga till i din katalog.

    > [!NOTE]
    > Den anpassade domänen måste matcha domännamnet för Azure AD-källan för etableringen av användare. Om de inte matchar kanske du kan lösa problemet genom att implementera attributet mappning anpassning.

1. Nu när du har verifierat dina domäner med Azure AD, måste du kontrollera dem igen med Google Apps. För varje domän som inte redan har registrerats med Google, gör du följande:

    a. I den [Google Apps-administratörskonsolen](https://admin.google.com/)väljer **domäner**.

    ![Välj domäner][20]

    b. Välj **lägga till en domän eller en domän alias**.

    ![Lägg till en ny domän][21]

    c. Välj **lägga till en annan domän**, och skriv sedan namnet på den domän som du vill lägga till.

    ![Skriv ditt domännamn][22]

    d. Välj **Fortsätt och verifiera domänägarskap**. Följ stegen för att verifiera att du äger domännamnet. Omfattande anvisningar för hur du verifierar din domän med Google, se [verifiera ditt platsägarskap med Google Apps](https://support.google.com/webmasters/answer/35179).

    e. Upprepa föregående steg för eventuella ytterligare domäner som du vill lägga till i Google Apps.

    > [!WARNING]
    > Om du ändrar den primära domänen för din G Suite-klient, och om du redan har konfigurerat enkel inloggning med Azure AD, måste du upprepa steg #3 under steg 2: Aktivera enkel inloggning.

1. I den [Google Apps-administratörskonsolen](https://admin.google.com/)väljer **Administratörsrollerna**.

    ![Select Google Apps][26]

1. Avgör vilket administratörskonto som du vill använda för att hantera etableringen av användare. För den **administratörsroll** för kontot, redigera den **privilegier** för rollen. Se till att aktivera alla **API administratörsprivilegier** så att det här kontot kan användas för etablering.

    ![Select Google Apps][27]

    > [!NOTE]
    > Om du konfigurerar en produktionsmiljö är det bästa sättet att skapa ett administratörskonto i G Suite specifikt för det här steget. Dessa konton måste ha en administratörsroll som är associerade med dem som har de nödvändiga privilegierna för API: et.

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory** > **Företagsappar** > **alla program** avsnittet.

1. Om du redan har konfigurerat G Suite för enkel inloggning kan du söka efter din G Suite-instans med hjälp av sökfältet. Annars väljer **Lägg till**, och söker sedan efter **G Suite** eller **Google Apps** i programgalleriet. Välj din app från sökresultaten och sedan lägga till den i listan med program.

1. Välj din instans av G Suite och välj sedan den **etablering** fliken.

1. Ange den **Etableringsläge** till **automatisk**. 

    ![Etablering](./media/google-apps-provisioning-tutorial/provisioning.png)

1. Under den **administratörsautentiseringsuppgifter** väljer **auktorisera**. Det öppnar en dialogruta för Google-auktorisering i ett nytt webbläsarfönster.

1. Bekräfta att du vill ge Azure Active Directory-behörighet att göra ändringar i din G Suite-klient. Välj **Acceptera**.

    ![Bekräfta behörigheter.][28]

1. I Azure-portalen väljer du **Testanslutningen** så att Azure AD kan ansluta till din app. Om anslutningen misslyckas, kan du kontrollera att ditt G Suite-konto har administratörsbehörigheter för teamet. Försök sedan den **auktorisera** steg igen.

1. Ange e-postadress för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fält. Markera sedan kryssrutan.

1. Välj **Spara.**

1. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Google Apps**.

1. I den **attributmappningar** går du igenom användarattribut som synkroniseras från Azure AD till G Suite. Attribut som är **matchande** egenskaper som används för att matcha användarkonton i G Suite för uppdateringsåtgärder. Välj **spara** att genomföra ändringarna.

1. Om du vill aktivera den Azure AD-etableringstjänsten för G Suite, ändra den **Etableringsstatus** till **på** i **inställningar**.

1. Välj **Spara**.

Den här processen startar den första synkroniseringen av alla användare eller grupper som är kopplade till G Suite i avsnittet användare och grupper. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär varje 40 minuter medan tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar. Dessa loggar beskrivs alla åtgärder som utförs av etableringstjänsten i din app.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](google-apps-tutorial.md)

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
