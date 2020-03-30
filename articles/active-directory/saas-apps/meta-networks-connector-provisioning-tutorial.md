---
title: 'Självstudiekurs: Konfigurera Meta Networks Connector för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Meta Networks Connector.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6e34e540-7761-4bdf-a2f9-8c12becc119d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 03c2dc6253fba5c2c7d59f3aefc5c1c663ed8248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061370"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Meta Networks Connector för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Meta Networks Connector och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Meta Networks Connector.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Meta Networks Connector-klient](https://www.metanetworks.com/)
* Ett användarkonto i Meta Networks Connector med administratörsbehörighet.

## <a name="assigning-users-to-meta-networks-connector"></a>Tilldela användare till Meta Networks Connector

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Meta Networks Connector. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Meta Networks Connector genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Viktiga tips för att tilldela användare till Meta Networks Connector

* Vi rekommenderar att en enda Azure AD-användare tilldelas Meta Networks Connector för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Meta Networks Connector måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Konfigurera Meta Networks Connector för etablering

1. Logga in på [administratörskonsolen Meta Networks Connector](https://login.metanetworks.com/login/) med ditt organisationsnamn. Navigera till **Administration > API-nycklar**.

    ![Administratörskonsol för MetaNätverkskoppling](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Klicka på plustecknet längst upp till höger på skärmen för att skapa en ny **API-nyckel**.

    ![Ikon för Meta Networks Connector plus](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Ange **API-nyckelnamn** och **API-nyckelbeskrivning**.

    ![Skapa token för metanätverkskoppling](media/meta-networks-connector-provisioning-tutorial/keyname.png)

4.  Aktivera **Write** skrivbehörighet för **grupper** och **användare**.

    ![Behörigheter för Meta-nätverksanslutning](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Klicka på **Lägg till**. Kopiera **SECRET** och spara den eftersom detta kommer att vara den enda gången du kan visa den. Det här värdet anges i fältet Hemlig token på fliken Etablering i ditt Meta Networks Connector-program i Azure-portalen.

    ![Skapa token för metanätverkskoppling](media/meta-networks-connector-provisioning-tutorial/token.png)

6.  Lägg till ett IDP genom att navigera till **Administration > Inställningar > IdP > Skapa nytt**.

    ![Lägg till IdP för MetaNätverkskoppling](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  På **idP-konfigurationssidan** kan du **namnge** IdP-konfigurationen och välja en **ikon**.

    ![IdP-namn för MetaNätverkskoppling](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Ikon för Meta Networks Connector IdP](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  Under **Konfigurera SCIM** väljer du api-nyckelnamnet som skapats i föregående steg. Klicka på **Spara**.

    ![MetaNätverkskoppling konfigurerar SCIM](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Navigera till **fliken Administration > Inställningar > IdP**. Klicka på namnet på IdP-konfigurationen som skapats i föregående steg för att visa **IdP-ID**. Det här **ID:t** läggs till i slutet av **klient-URL:en** när värdet anges i **url-fältet klient** i fliken Etablering i ditt Meta Networks Connector-program i Azure-portalen.

    ![Id-ID för MetaNätverkskoppling](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Lägga till Meta Networks Connector från galleriet

Innan du konfigurerar Meta Networks Connector för automatisk användaretablering med Azure AD måste du lägga till Meta Networks Connector från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Meta Networks Connector från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Meta Networks Connector**, väljer Meta Networks **Connector** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Meta Networks Connector i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Konfigurera automatisk användaretablering till Meta Networks Connector 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Meta Networks Connector baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Meta Networks Connector , enligt instruktionerna i [den enkla inloggningskursen För MetaNätverkskopplingen](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Meta Networks Connector i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Meta Networks Connector**.

    ![Länken för Meta Networks Connector i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://api.metanetworks.com/v1/scim/<IdP ID>` **klient-URL.** Ange värdet **för SCIM-autentiseringstoken** som hämtats tidigare i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Meta Networks Connector. Om anslutningen misslyckas kontrollerar du att ditt Meta Networks Connector-konto har administratörsbehörighet och försöker igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory Users till Meta Networks Connector**.

    ![Användarmappningar för Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Granska användarattributen som synkroniseras från Azure AD till Meta Networks Connector i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Meta Networks Connector för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Användarattribut för Meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory Groups till Meta Networks Connector**.

    ![Mappningar i Meta Networks Connector-grupp](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Meta Networks Connector i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Meta Networks Connector för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Attribut för Meta Networks Connector-grupp](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för Meta Networks Connector ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Meta Networks Connector genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Meta Networks Connector.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

