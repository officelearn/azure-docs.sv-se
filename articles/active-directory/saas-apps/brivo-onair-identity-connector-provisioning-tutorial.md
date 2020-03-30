---
title: 'Självstudiekurs: Konfigurera Brivo Onair Identity Connector för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Brivo Onair Identity Connector.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 542ce04c-ef7d-4154-9b0e-7f68e1154f6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: f3c1e7337c0ce07b7fbebb9f954deeb75f0b9584
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246661"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Brivo Onair Identity Connector för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Brivo Onair Identity Connector och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Brivo Onair Identity Connector.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Brivo Onair Identity Connector hyresgäst](https://www.brivo.com/lp/quote)
* Ett användarkonto i Brivo Onair Identity Connector med behörighet för senior administratör.

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Tilldela användare till Brivo Onair Identity Connector

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Brivo Onair Identity Connector. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Brivo Onair Identity Connector genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Viktiga tips för att tilldela användare till Brivo Onair Identity Connector

* Vi rekommenderar att en enda Azure AD-användare tilldelas Brivo Onair Identity Connector för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Brivo Onair Identity Connector måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>Konfigurera Brivo Onair Identity Connector för etablering

1.    Logga in på [administratörskonsolen Brivo Onair Identity Connector](https://acs.brivo.com/login/). Navigera till **kontoinställningar > konto**.

    ![Adminkonsol för Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2.  Klicka på fliken **Azure AD.** På sidan **Azure AD-information** anger du lösenordet för ditt seniora administratörskonto. Klicka på **Skicka**.

    ![Brivo Onair Identity Connector azure](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3.    Klicka på **Kopiera token** knappen och spara **den hemliga token**. Det här värdet anges i fältet Hemlig token på fliken Etablering i ditt Brivo Onair Identity Connector-program i Azure-portalen.

    ![Brivo Onair Identity Connector token](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>Lägg till Brivo Onair Identity Connector från galleriet

Innan du konfigurerar Brivo Onair Identity Connector för automatisk användaretablering med Azure AD måste du lägga till Brivo Onair Identity Connector från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Brivo Onair Identity Connector från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Brivo Onair Identity Connector**, väljer **Brivo Onair Identity Connector** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Brivo Onair Identity Connector i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Konfigurera automatisk användaretablering till Brivo Onair Identity Connector 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Brivo Onair Identity Connector baserat på användar- och/eller grupptilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Brivo Onair Identity Connector i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Brivo Onair Identity Connector**i programlistan .

    ![Länken Brivo Onair Identity Connector i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://scim.brivo.com/ActiveDirectory/v2/` **klient-URL.** Ange värdet **för SCIM-autentiseringstoken** som hämtats tidigare i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Brivo Onair Identity Connector. Om anslutningen misslyckas kontrollerar du att ditt Brivo Onair Identity Connector-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Brivo Onair Identity Connector**.

    ![Användarmappningar för Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. Granska användarattributen som synkroniseras från Azure AD till Brivo Onair Identity Connector i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Brivo Onair Identity Connector för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Användarattribut för Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory Groups till Brivo Onair Identity Connector**.

    ![Brivo Onair Identity Connector-gruppmappningar](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Brivo Onair Identity Connector i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Brivo Onair Identity Connector för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Brivo Onair Identity Connector-gruppattribut](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för Brivo Onair Identity Connector ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Brivo Onair Identity Connector genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Brivo Onair Identity Connector.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

