---
title: 'Självstudiekurs: Konfigurera Netskope Administrator Console för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Netskope Administrator Console.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e99f0e0f-28d0-43c6-a52b-df873fb9d551
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: eaee8e3305572d696e52c3879be2e2b9924bc93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061339"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Netskope Administrator Console för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Netskope Administrator Console och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Netskope Administrator Console.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Netskope Administratör Konsol klient](https://www.netskope.com/)
* Ett användarkonto i Netskope Administrator Console med administratörsbehörighet.

## <a name="assigning-users-to-netskope-administrator-console"></a>Tilldela användare till Netskope Administrator Console

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Netskope Administrator Console. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Netskope Administrator Console genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>Viktiga tips för att tilldela användare till Netskope Administrator Console

* Vi rekommenderar att en enda Azure AD-användare tilldelas Netskope Administrator Console för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Netskope Administrator Console måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>Konfigurera Netskope Administrator Console för etablering

1. Logga in på [administratörskonsolen Netskope Administrator Console](https://netskope.goskope.com/). Navigera till **inställningar för > i hemmet**.

    ![Administratörskonsol för Netskope-administratörskonsol](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Navigera till **Verktyg**. Under **Verktyg-menyn** navigerar du till **katalogverktyg > SCIM INTEGRATION**.

    ![Netskope Administrator Console verktyg](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope Administratörskonsol Lägg TILL SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Bläddra nedåt och klicka på **Knappen Lägg till token.** I dialogrutan **Lägg till OAuth-klientnamn** finns ett **klientnamn** och klickar på knappen **Spara.**

    ![Netskope Administrator Console Lägg till token](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope Administratörskonsol CLient Namn](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Kopiera **SCIM-server-URL:en** och **TOKEN**. Dessa värden anges i fälten Klient-URL och Hemlig token på fliken Etablering i netskope-administratörskonsolen i Azure-portalen.

    ![Skapa token för netskope-administratörskonsol](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Lägg till Netskope Administrator Console från galleriet

Innan du konfigurerar Netskope Administrator Console för automatisk användaretablering med Azure AD måste du lägga till Netskope Administrator Console från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Netskope Administrator Console från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Netskope Administrator Console**, väljer **Netskope Administrator Console** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Netskope Administrator Console i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>Konfigurera automatisk användaretablering till Netskope Administrator Console 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Netskope Administrator Console baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Netskope Administrator Console genom att följa instruktionerna i [Netskope Administrator Console Enkel inloggningshandledning](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

> [!NOTE]
> Mer information om Netskope Administrator Consoles SCIM-slutpunkt finns [i detta](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon).

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Netskope Administrator Console i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Netskope Administrator Console**i programlistan .

    ![Länken Netskope Administrator Console i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** har du läst **IN-URL-värdet för SCIM-servern** som hämtats tidigare i **klient-URL.** Mata in **token-värdet** som hämtats tidigare i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Netskope Administrator Console. Om anslutningen misslyckas kontrollerar du att netskope Administrator Console-kontot har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Netskope Administrator Console**.

    ![Användarmappningar för Netskope-administratörskonsol](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Granska användarattributen som synkroniseras från Azure AD till Netskope Administrator Console i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Netskope Administrator Console för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Användareattribut för Netskope-administratörskonsol](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory Groups till Netskope Administrator Console**.

    ![Netskope Administratör Konsol Gruppmappningar](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Netskope Administrator Console i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Netskope Administrator Console för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Netskope-administratörskonsolgruppattribut](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för Netskope Administrator Console ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Netskope Administrator Console genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Netskope Administrator Console.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

