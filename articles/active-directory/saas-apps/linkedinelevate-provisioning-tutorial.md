---
title: 'Självstudiekurs: Användaretablering för LinkedIn Elevate - Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till LinkedIn Elevate.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa0a26eaeac431ed2c78c5bd938bbbe7dff14e0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057421"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera LinkedIn Elevate för automatisk användaretablering

Syftet med den här självstudien är att visa de steg du behöver för att utföra i LinkedIn Elevate och Azure AD för att automatiskt etablera och avetableras användarkonton från Azure AD till LinkedIn Elevate.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klientorganisation
* En LinkedIn Elevate-klient
* Ett administratörskonto i LinkedIn Elevate med åtkomst till LinkedIn Account Center

> [!NOTE]
> Azure Active Directory integreras med LinkedIn Elevate med [SCIM-protokollet.](http://www.simplecloud.info/)

## <a name="assigning-users-to-linkedin-elevate"></a>Tilldela användare till LinkedIn Elevate

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk etablering av användarkonto synkroniseras endast användare och grupper som har "tilldelats" till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till LinkedIn Elevate. När du har bestämt dig kan du tilldela dessa användare till LinkedIn Elevate genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Viktiga tips för att tilldela användare till LinkedIn Elevate

* Vi rekommenderar att en enda Azure AD-användare tilldelas LinkedIn Elevate för att testa etableringskonfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till LinkedIn Elevate måste du välja **användarrollen** i tilldelningsdialogrutan. Rollen "Standardåtkomst" fungerar inte för etablering.

## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Konfigurera användaretablering till LinkedIn Elevate

Det här avsnittet hjälper dig att ansluta din Azure AD till LinkedIn Elevates SCIM-användarkontos etablerings-API och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i LinkedIn Elevate baserat på användar- och grupptilldelning i Azure AD.

**Tips:** Du kan också välja att aktivera SAML-baserad enkel inloggning för LinkedIn Elevate, enligt instruktionerna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Så här konfigurerar du automatisk etablering av användarkonton till LinkedIn Elevate i Azure AD:

Det första steget är att hämta din LinkedIn-åtkomsttoken. Om du är företagsadministratör kan du självetablering av en åtkomsttoken. Gå till **Globala &gt; inställningar i** ditt kontocenter och öppna panelen KONFIGURERA **SCIM.**

> [!NOTE]
> Om du använder kontocentret direkt i stället för via en länk kan du nå det med hjälp av följande steg.

1. Logga in på Account Center.

2. Välj ** &gt; Administratörsadministratörsinställningar** .

3. Klicka på **Avancerade integreringar** i det vänstra sidofältet. Du dirigeras till kontocentret.

4. Klicka på **+ Lägg till ny SCIM-konfiguration** och följ proceduren genom att fylla i varje fält.

    > [!NOTE]
    > När licenser för automatisk tilldelning inte är aktiverade betyder det att endast användardata synkroniseras.

    ![LinkedIn Höja etablering](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

    > [!NOTE]
    > När automatisk licenstilldelning är aktiverat måste du notera programinstansen och licenstypen. Licenser tilldelas enligt först till kvarn-principen tills alla licenser tas.

    ![LinkedIn Höja etablering](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5. Klicka på **Generera token**. Du bör se din åtkomsttokenvisning under **fältet Åtkomsttoken.**

6. Spara åtkomsttoken i Urklipp eller datorn innan du lämnar sidan.

7. Logga sedan in på [Azure-portalen](https://portal.azure.com)och bläddra till **avsnittet Azure Active Directory > Enterprise Apps > Alla program.**

8. Om du redan har konfigurerat LinkedIn Elevate för enkel inloggning söker du efter din instans av LinkedIn Elevate med hjälp av sökfältet. Annars väljer du **Lägg till** och sök efter **LinkedIn Elevate** i programgalleriet. Välj LinkedIn Elevate från sökresultaten och lägg till det i listan över program.

9. Välj din instans av LinkedIn Elevate och välj sedan fliken **Etablering.**

10. Ställ in **etableringsläget** på **Automatiskt**.

    ![LinkedIn Höja etablering](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11. Fyll i följande fält under **Administratörsautentiseringsuppgifter:**

    * Ange i fältet `https://api.linkedin.com` **Klient-URL.**

    * I fältet **Hemlig token** anger du den åtkomsttoken som du skapade i steg 1 och klickar på **Testa anslutning** .

    * Du bör se en framgångsmeddelande på den övre sidan av portalen.

12. Ange e-postadressen till en person eller grupp som ska få etableringsfelmeddelanden i fältet **E-postmeddelande** och markera kryssrutan nedan.

13. Klicka på **Spara**.

14. I avsnittet **Attributmappningar** granskar du användar- och gruppattributen som ska synkroniseras från Azure AD till LinkedIn Elevate. Observera att de attribut som valts som **matchande** egenskaper används för att matcha användarkonton och grupper i LinkedIn Elevate för uppdateringsåtgärder. Välj knappen Spara om du vill utföra eventuella ändringar.

    ![LinkedIn Höja etablering](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15. Om du vill aktivera Azure AD-etableringstjänsten för LinkedIn Elevate ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar**

16. Klicka på **Spara**.

Detta startar den första synkroniseringen av alla användare och/eller grupper som tilldelats LinkedIn Elevate i avsnittet Användare och grupper. Observera att den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge tjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitetsloggar, som beskriver alla åtgärder som utförs av etableringstjänsten i din LinkedIn Elevate-app.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
