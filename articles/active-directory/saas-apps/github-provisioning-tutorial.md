---
title: 'Självstudiekurs: Användaretablering för GitHub – Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till GitHub.
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
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82f7252f2d9cdd2c54fae593d8463bfe84bd6ce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057659"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera GitHub för automatisk etablering av användare

Syftet med den här självstudien är att visa de steg du behöver för att utföra i GitHub och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till GitHub.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klient
* En GitHub-organisation som har skapats i [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) och kräver [GitHub Enterprise-faktureringsplanen](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)
* Ett användarkonto i GitHub med administratörsbehörighet till organisationen
* Se till att OAuth-åtkomst har tillhandahållits för din organisation enligt beskrivningen [här](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)

> [!NOTE]
> Azure AD-etableringsintegreringen är beroende av [GitHub SCIM-API:et](https://developer.github.com/v3/scim/), som är tillgängligt för [GitHub Enterprise Cloud-kunder](https://help.github.com/articles/github-s-products/#github-enterprise) på [GitHub Enterprise-faktureringsplanen](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations).

## <a name="assigning-users-to-github"></a>Tilldela användare till GitHub

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk etablering av användarkonto synkroniseras endast användare och grupper som har "tilldelats" till ett program i Azure AD. 

Innan du konfigurerar och aktiverar etableringstjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till din GitHub-app. När du har bestämt dig kan du tilldela dessa användare till din GitHub-app genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Viktiga tips för att tilldela användare till GitHub

* Vi rekommenderar att en enda Azure AD-användare tilldelas GitHub för att testa etableringskonfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till GitHub måste du välja antingen **användarrollen** eller en annan giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. **Rollen Standardåtkomst** fungerar inte för etablering och dessa användare hoppas över.

## <a name="configuring-user-provisioning-to-github"></a>Konfigurera användaretablering till GitHub

Det här avsnittet hjälper dig att ansluta ditt Azure AD till GitHubs API för etablering av användarkonton och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i GitHub baserat på användar- och grupptilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för GitHub, enligt instruktionerna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Konfigurera automatisk etablering av användarkonton till GitHub i Azure AD

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till **avsnittet Azure Active Directory > Enterprise Apps > Alla program.**

2. Om du redan har konfigurerat GitHub för enkel inloggning söker du efter din instans av GitHub med hjälp av sökfältet. Annars väljer du **Lägg till** och söker efter **GitHub** i programgalleriet. Välj GitHub i sökresultaten och lägg till det i listan över program.

3. Välj din instans av GitHub och välj sedan fliken **Etablering.**

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![GitHub-etablering](./media/github-provisioning-tutorial/GitHub1.png)

5. Klicka på **Auktorisera**under avsnittet **Administratörsautentiseringsuppgifter** . Den här åtgärden öppnar en GitHub-auktoriseringsdialogruta i ett nytt webbläsarfönster. Observera att du måste se till att du är godkänd för att godkänna åtkomst. Följ anvisningarna som beskrivs [här](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization).

6. Logga in på GitHub i det nya fönstret med ditt admin-konto. I den resulterande auktoriseringsdialogrutan väljer du det GitHub-team som du vill aktivera etablering för och väljer sedan **Auktorisera**. När du är klar går du tillbaka till Azure-portalen för att slutföra etableringskonfigurationen.

    ![Dialogrutan Auktorisering](./media/github-provisioning-tutorial/GitHub2.png)

7. I Azure-portalen, ange **klient-URL** och klicka på **Testa anslutning** för att säkerställa Att Azure AD kan ansluta till din GitHub-app. Om anslutningen misslyckas, se till att ditt GitHub-konto har administratörsbehörighet och **att klient-URl** matas in `https://api.github.com/scim/v2/organizations/<Organization_name>`korrekt och försök sedan med steget "Auktorisera" igen (du kan utgöra **klientadressen** för regel: kan du hitta dina organisationer under ditt GitHub-konto: **Inställningar** > **organisationer**).

    ![Dialogrutan Auktorisering](./media/github-provisioning-tutorial/GitHub3.png)

8. Ange e-postadressen till en person eller grupp som ska få etableringsfelmeddelanden i fältet **E-postmeddelande** och markera kryssrutan "Skicka ett e-postmeddelande när ett fel inträffar".

9. Klicka på **Spara**.

10. Under avsnittet Mappningar väljer du **Synkronisera Azure Active Directory-användare till GitHub**.

11. I avsnittet **Attributmappningar** granskar du de användarattribut som synkroniseras från Azure AD till GitHub. De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i GitHub för uppdateringsåtgärder. Välj knappen Spara om du vill utföra eventuella ändringar.

12. Om du vill aktivera Azure AD-etableringstjänsten för GitHub ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar**

13. Klicka på **Spara**.

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som tilldelats GitHub i avsnittet Användare och grupper. Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge tjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitetsloggar, som beskriver alla åtgärder som utförs av etableringstjänsten.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
