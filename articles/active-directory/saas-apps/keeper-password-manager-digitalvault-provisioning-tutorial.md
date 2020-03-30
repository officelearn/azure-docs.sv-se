---
title: 'Självstudiekurs: Konfigurera Keeper Password Manager & Digital Vault för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Keeper Password Manager & Digital Vault.
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
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 236527a9889879f872ef8c3867a7ec3c1b1ba0a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057549"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Keeper Password Manager & Digital Vault för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Keeper Password Manager & Digital Vault och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Keeper Password Manager & Digital Vault.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Keeper Password Manager & Digital Vault-klient](https://keepersecurity.com/pricing.html?t=e)
* Ett användarkonto i Keeper Password Manager & Digital Vault med administratörsbehörighet.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Lägg till Keeper Password Manager & Digital Vault från galleriet

Innan du konfigurerar Keeper Password Manager & Digital Vault för automatisk användaretablering med Azure AD måste du lägga till Keeper Password Manager & Digital Vault från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Keeper Password Manager & Digital Vault från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Keeper Password Manager & Digital Vault**, väljer Keeper Password Manager & Digital **Vault** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Keeper Password Manager & Digital Vault i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Tilldela användare till Keeper Password Manager & Digital Vault

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Keeper Password Manager & Digital Vault. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Keeper Password Manager & Digital Vault genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Viktiga tips för att tilldela användare till Keeper Password Manager & Digital Vault

* Vi rekommenderar att en enda Azure AD-användare tilldelas Keeper Password Manager & Digital Vault för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Keeper Password Manager & Digital Vault måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Konfigurera automatisk användaretablering till Keeper Password Manager & Digital Vault 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Keeper Password Manager & Digital Vault baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Keeper Password Manager & Digital Vault, i enlighet med instruktionerna i [Keeper Password Manager & Digital Vault enda inloggningshandledning](keeperpasswordmanager-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Keeper Password Manager & Digital Vault i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Keeper Password Manager & Digital Vault**.

    ![Keeper Password Manager & Digital Vault-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du **klientadressen** och **den hemliga token för** din Keeper Password Manager & Digital Vaults konto enligt beskrivningen i steg 6.

6. Logga in på [administratörskonsolen för Keeper](https://keepersecurity.com/console/#login). Klicka på **Admin** och välj en befintlig nod eller skapa en ny. Navigera till fliken **Etablering** och välj **Lägg till metod**.

    ![Administratörskonsol för djurhållare](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Välj **SCIM (System för identitetshantering mellan domäner**.

    ![Keeper Lägg SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Klicka på **Skapa etableringstoken**.

    ![Slutpunkt för Keeper Skapa](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Kopiera värdena för **URL** och **Token** och klistra in dem i **klient-URL** och **hemlig token** i Azure AD. Klicka på **Spara** för att slutföra etableringsinställningarna på Keeper.

    ![Skapa token för keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. När du fyller i fälten som visas i steg 5 klickar du på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Keeper Password Manager & Digital Vault. Om anslutningen misslyckas kontrollerar du att keeper password manager-kontot & Digital Vault har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

8. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory -användare till keeper password manager & Digital Vault**.

    ![Mappningar för keeperanvändare](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Granska användarattributen som synkroniseras från Azure AD till Keeper Password Manager & Digital Vault i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Keeper Password Manager & Digital Vault för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Attribut för keeperanvändare](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory Groups to Keeper Password Manager & Digital Vault**.

    ![Kartläggningar av keeper-grupp](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Granska gruppattributen som synkroniseras från Azure AD till Keeper Password Manager & Digital Vault i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Keeper Password Manager & Digital Vault för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Attribut för keepergrupp](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera Azure AD-etableringstjänsten för Keeper Password Manager & Digital Vault ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

16. Definiera de användare och/eller grupper som du vill etablera till Keeper Password Manager & Digital Vault genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

17. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Keeper Password Manager & Digital Vault.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

* Keeper Password Manager & Digital Vault kräver **e-post** och **användarnamn** för att ha samma källvärde, eftersom alla uppdateringar av något av attributen kommer att ändra det andra värdet.
* Keeper Password Manager & Digital Vault stöder inte användarborttagningar, inaktiverar bara. Inaktiverade användare visas som låsta i gränssnittet för administratörskonsolen för Djurhållare.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

