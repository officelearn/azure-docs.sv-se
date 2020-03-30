---
title: 'Självstudiekurs: Konfigurera automatisk användaretablering av Figma med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Figma.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: a50f1c81f5eda78ee6834aba3085f685c197b4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057965"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Figma för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Figma och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Figma.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klientorganisation.
* [En Figma hyresgäst](https://www.figma.com/pricing/).
* Ett användarkonto i Figma med administratörsbehörighet.

## <a name="assign-users-to-figma"></a>Tilldela användare till Figma.
Azure Active Directory använder ett koncept som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Figma. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Figma genom att följa instruktionerna här:
 
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Viktiga tips för att tilldela användare till Figma

 * Vi rekommenderar att en enda Azure AD-användare tilldelas Figma för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Figma måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen Standardåtkomst är undantagna från etablering.

## <a name="set-up-figma-for-provisioning"></a>Ställ in Figma för etablering

Innan du konfigurerar Figma för automatisk användaretablering med Azure AD måste du hämta viss etableringsinformation från Figma.

1. Logga in på [figma-administratörskonsolen](https://www.Figma.com/). Klicka på växelikonen bredvid din hyresgäst.

    ![FigmaFigma-anställd-avsättning](media/Figma-provisioning-tutorial/image0.png)

2. Navigera till **Allmänna > Uppdatera inställningarna för inloggning**.

    ![FigmaFigma-anställd-avsättning](media/Figma-provisioning-tutorial/figma03.png)

3. Kopiera **klient-ID.** Det här värdet används för att konstruera SCIM-slutpunkts-URL:en som ska anges i fältet **Klient-URL** på fliken Etablering på figma-programmet i Azure-portalen.

    ![Figma Skapa Token](media/Figma-provisioning-tutorial/figma-tenantid.png)

4. Bläddra nedåt och klicka på **Generera API Token**.

    ![Figma Skapa Token](media/Figma-provisioning-tutorial/token.png)

5. Kopiera **API-tokenvärdet.** Det här värdet anges i fältet **Hemlig token** på fliken Etablering i figma-programmet i Azure-portalen. 

    ![Figma Skapa Token](media/Figma-provisioning-tutorial/figma04.png)

## <a name="add-figma-from-the-gallery"></a>Lägg till Figma från galleriet

Om du vill konfigurera Figma för automatisk användaretablering med Azure AD måste du lägga till Figma från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Figma**, väljer **Figma** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Figma i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Konfigurera automatisk användaretablering till Figma 

Det här avsnittet guidar dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Figma baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Figma, enligt instruktionerna i [Figma Single sign-on tutorial](figma-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Figma i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Figma**.

    ![Figma-länken i listan Program](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://www.figma.com/scim/v2/<TenantID>` **klient-URL** där **TenantID** är det värde som du hämtade från Figma tidigare. Ange **API-tokenvärdet** i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Figma. Om anslutningen misslyckas kontrollerar du att ditt Figma-konto har administratörsbehörighet och försök igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

8. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Figma**.

    ![Figma-användarmappningar](media/Figma-provisioning-tutorial/figma05.png)

11. Granska användarattributen som synkroniseras från Azure AD till Figma i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Figma för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Figma-användarattribut](media/Figma-provisioning-tutorial/figma06.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för Figma ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Figma genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Figma.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)