---
title: 'Självstudiekurs: Konfigurera RFPIO för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till RFPIO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 54419db4-47d5-4fb4-ab74-7b0b28afb11b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 6ae423305b39c1335b5db1cd893d5f817be1929b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060872"
---
# <a name="tutorial-configure-rfpio-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera RFPIO för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i RFPIO och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till RFPIO.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klientorganisation.
* [En RFPIO-klient](https://www.rfpio.com/product/).
* Ett användarkonto i RFPIO med administratörsbehörighet.

## <a name="assigning-users-to-rfpio"></a>Tilldela användare till RFPIO

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till RFPIO. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till RFPIO genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-rfpio"></a>Viktiga tips för att tilldela användare till RFPIO

* Vi rekommenderar att en enda Azure AD-användare tilldelas RFPIO för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till RFPIO måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="setup-rfpio-for-provisioning"></a>Konfigurera RFPIO för etablering

Innan du konfigurerar RFPIO för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering på RFPIO.

1.  Logga in på rfpio-administratörskonsolen. Klicka på **Klienten**längst ned till vänster på administratörskonsolen.

    ![Rfpio-administratörskonsol](media/rfpio-provisioning-tutorial/aadtest0.png)

2.  Klicka på **Organisationsinställningar**.
    
    ![RFPIO-administratör](media/rfpio-provisioning-tutorial/aadtest.png)

3.  Navigera till **USER MANAGEMENT** > **SECURITY** > **SCIM**.

    ![RFPIO Lägg till SCIM](media/rfpio-provisioning-tutorial/scim.png)

4.  Kontrollera att **automatisk användaretablering** är aktiverat. Klicka på **GENERERA SCIM API TOKEN**.

    ![Skapa token för RFPIO](media/rfpio-provisioning-tutorial/generate.png)

5.  Spara **SCIM API-token** eftersom den här token inte visas igen av säkerhetsskäl. Det här värdet anges i fältet **Hemlig token** på fliken Etablering i rfpio-programmet i Azure-portalen.

    ![Skapa token för RFPIO](media/rfpio-provisioning-tutorial/auth.png)

## <a name="add-rfpio-from-the-gallery"></a>Lägga till RFPIO från galleriet

Om du vill konfigurera RFPIO för automatisk användaretablering med Azure AD måste du lägga till RFPIO från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till RFPIO från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **RFPIO,** väljer **RFPIO** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![RFPIO i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-rfpio"></a>Konfigurera automatisk användaretablering till RFPIO 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i RFPIO baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för RFPIO, följ instruktionerna i [rfpio-självstudien Enkel inloggning](rfpio-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-rfpio-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för RFPIO i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **RFPIO**i programlistan .

    ![RFPIO-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://<RFPIO tenant instance>.rfpio.com/rfpserver/scim/v2 ` **klient-URL.** Ett exempelvärde `https://Azure-test1.rfpio.com/rfpserver/scim/v2`är . Mata in **scim API-tokenvärdet** som hämtats tidigare i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till RFPIO. Om anslutningen misslyckas kontrollerar du att ditt RFPIO-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till RFPIO**.

    ![Rfpio-användarmappningar](media/rfpio-provisioning-tutorial/usermapping.png)

9. Granska användarattributen som synkroniseras från Azure AD till RFPIO i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i RFPIO för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![RFPIO-användarattribut](media/rfpio-provisioning-tutorial/userattributes.png)

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för RFPIO ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till RFPIO genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på RFPIO.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

* RFPIO stöder inte grupper som etablerar sig för närvarande.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
