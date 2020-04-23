---
title: 'Självstudiekurs: Etablering av användare – LinkedIn Sales Navigator, Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till LinkedIn Sales Navigator.
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
ms.openlocfilehash: 48b9f2dc64d1d3ddd8253a253dcab8ef972032f9
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869733"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera LinkedIn Sales Navigator för automatisk etablering av användare

Syftet med den här självstudien är att visa de steg du behöver utföra i LinkedIn Sales Navigator och Azure AD för att automatiskt etablera och avetableras användarkonton från Azure AD till LinkedIn Sales Navigator.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klientorganisation
* En LinkedIn Sales Navigator-klient 
* Ett administratörskonto i LinkedIn Sales Navigator med åtkomst till LinkedIn-kontocenter

> [!NOTE]
> Azure Active Directory integreras med LinkedIn Sales Navigator med [SCIM-protokollet.](http://www.simplecloud.info/)

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Tilldela användare till LinkedIn Sales Navigator

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk etablering av användarkonto synkroniseras endast användare och grupper som har "tilldelats" till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till LinkedIn Sales Navigator. När du har bestämt dig kan du tilldela dessa användare till LinkedIn Sales Navigator genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Viktiga tips för att tilldela användare till LinkedIn Sales Navigator

* Vi rekommenderar att en enda Azure AD-användare tilldelas LinkedIn Sales Navigator för att testa etableringskonfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till LinkedIn Sales Navigator måste du välja användarrollen i **tilldelningsdialogrutan.** Rollen "Standardåtkomst" fungerar inte för etablering.

## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>Konfigurera användaretablering till LinkedIn Sales Navigator

Det här avsnittet hjälper dig att ansluta din Azure AD till LinkedIn Sales Navigators SCIM-användarkontotablering API och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i LinkedIn Sales Navigator baserat på användar- och grupptilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för LinkedIn Sales Navigator, enligt instruktionerna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>Så här konfigurerar du automatisk etablering av användarkonton till LinkedIn Sales Navigator i Azure AD:

Det första steget är att hämta din LinkedIn-åtkomsttoken. Om du är företagsadministratör kan du självetablering av en åtkomsttoken. Gå till **Globala &gt; inställningar i** ditt kontocenter och öppna panelen KONFIGURERA **SCIM.**

> [!NOTE]
> Om du använder kontocentret direkt i stället för via en länk kan du nå det med hjälp av följande steg.

1. Logga in på Account Center.

2. Välj ** &gt; Administratörsadministratörsinställningar** .

3. Klicka på **Avancerade integreringar** i det vänstra sidofältet. Du dirigeras till kontocentret.

4. Klicka på **+ Lägg till ny SCIM-konfiguration** och följ proceduren genom att fylla i varje fält.

    > [!NOTE]
    > När licenser för automatisk tilldelning inte är aktiverade betyder det att endast användardata synkroniseras.

    ![Etablering av LinkedIn-försäljningsnavigator](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

    > [!NOTE]
    > När automatisk licenstilldelning är aktiverat måste du notera programinstansen och licenstypen. Licenser tilldelas enligt först till kvarn-principen tills alla licenser tas.

    ![Etablering av LinkedIn-försäljningsnavigator](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5. Klicka på **Generera token**. Du bör se din åtkomsttokenvisning under **fältet Åtkomsttoken.**

6. Spara åtkomsttoken i Urklipp eller datorn innan du lämnar sidan.

7. Logga sedan in på [Azure-portalen](https://portal.azure.com)och bläddra till **avsnittet Azure Active Directory > Enterprise Apps > Alla program.**

8. Om du redan har konfigurerat LinkedIn Sales Navigator för enkel inloggning söker du efter din instans av LinkedIn Sales Navigator med hjälp av sökfältet. Annars väljer du **Lägg till** och söker efter **LinkedIn Sales Navigator** i programgalleriet. Välj LinkedIn Sales Navigator från sökresultaten och lägg till den i listan över program.

9. Välj din instans av LinkedIn Sales Navigator och välj sedan fliken **Etablering.**

10. Ställ in **etableringsläget** på **Automatiskt**.

    ![Etablering av LinkedIn-försäljningsnavigator](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11. Fyll i följande fält under **Administratörsautentiseringsuppgifter:**

    * Ange i fältet https://developer.linkedin.com **Klient-URL.**

    * I fältet **Hemlig token** anger du den åtkomsttoken som du skapade i steg 1 och klickar på **Testa anslutning** .

    * Du bör se en framgångsmeddelande på den övre sidan av portalen.

12. Ange e-postadressen till en person eller grupp som ska få etableringsfelmeddelanden i fältet **E-postmeddelande** och markera kryssrutan nedan.

13. Klicka på **Spara**.

14. I avsnittet **Attributmappningar** granskar du användar- och gruppattributen som ska synkroniseras från Azure AD till LinkedIn Sales Navigator. Observera att de attribut som valts som **matchande** egenskaper används för att matcha användarkonton och grupper i LinkedIn Sales Navigator för uppdateringsåtgärder. Välj knappen Spara om du vill utföra eventuella ändringar.

    ![Etablering av LinkedIn-försäljningsnavigator](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15. Om du vill aktivera Azure AD-etableringstjänsten för LinkedIn Sales Navigator ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar**

16. Klicka på **Spara**.

Då startas den första synkroniseringen av alla användare och/eller grupper som tilldelats LinkedIn Sales Navigator i avsnittet Användare och grupper. Observera att den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge tjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitetsloggar, som beskriver alla åtgärder som utförs av etableringstjänsten i din LinkedIn Sales Navigator-app.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
