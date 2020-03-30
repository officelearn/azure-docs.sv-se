---
title: 'Självstudiekurs: Konfigurera Atlassian Cloud för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Atlassian Cloud.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 7ddccef00cf1b5ad524c0e1eaa7aed52c0e55197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059376"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Atlassian Cloud för automatisk etablering av användare

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Atlassian Cloud och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Atlassian Cloud.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Atlassian Cloud-klient](https://www.atlassian.com/licensing/cloud)
* Ett användarkonto i Atlassian Cloud med administratörsbehörighet.

> [!NOTE]
> Azure AD-etableringsintegreringen är beroende av **Atlassian Cloud SCIM API**, som är tillgängligt för Atlassian Cloud-team.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Lägg till Atlassian Cloud från galleriet

Innan du konfigurerar Atlassian Cloud för automatisk användaretablering med Azure AD måste du lägga till Atlassian Cloud från Azure AD-programgalleriet i din lista över hanterade SaaS-program.

**Så här lägger du till Atlassian Cloud från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Atlassian Cloud**, väljer **Atlassian Cloud** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Atlassian Cloud i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Tilldela användare till Atlassian Cloud

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Atlassian Cloud. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Atlassian Cloud genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Viktiga tips för att tilldela användare till Atlassian Cloud

* Vi rekommenderar att en enda Azure AD-användare tilldelas Atlassian Cloud för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Atlassian Cloud måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Konfigurera automatisk användaretablering till Atlassian Cloud 

Det här avsnittet hjälper dig att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Atlassian Cloud baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Atlassian Cloud, enligt instruktionerna i [atlassianska molnets enstaka inloggningshandledning](atlassian-cloud-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Atlassian Cloud i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com) och välj **Enterprise Applications**, välj **Alla program**och välj sedan **Atlassian Cloud**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Atlassian Cloud**i programlistan .

    ![Atlassian Cloud-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Atlassian Cloud Etablering](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Atlassian Cloud Etablering](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Navigera till [Atlassian Organization Manager](https://admin.atlassian.com) **> väljer den > katalogen för >**.

    ![Atlassian Cloud Etablering](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. Klicka på **Etablera användare** och klicka på Skapa **en katalog**. Kopiera **url:en för katalogbas** och **innehavartoken** till fälten **Klient-URL** och **Hemlig token.**

    ![Atlassian CloudTablering Atlassian](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![CloudTablering Atlassian Cloud Provisioning Atlassian Cloud Provisioning Atlassian Cloud Provisioning Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian Cloud Provisioning Atlassian Cloud Provisioning Atlassian Cloud Provisioning Atlass](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Under avsnittet **Administratörsautentiseringsuppgifter** anger du **klientadressen** och **den hemliga token för** Atlassian-molnets konto. Exempel på dessa värden är:

   * I fältet **Klient-URL** fyller du den specifika klientslutpunkten som du får från Atlassian, enligt beskrivningen i steg 6. Till exempel: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * Fyll i den hemliga token som beskrivs i steg 6 i fältet **Hemlig token.**

8. När du fyller i fälten som visas i steg 7 klickar du på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Atlassian Cloud. Om anslutningen misslyckas kontrollerar du att ditt Atlassian Cloud-konto har administratörsbehörighet och försöker igen.

    ![Atlassian Cloud Etablering](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![Atlassian Cloud Etablering](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. Klicka på **Spara**.

11. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Atlassian Cloud**.

    ![Atlassian Cloud Etablering](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. Granska användarattributen som synkroniseras från Azure AD till Atlassian Cloud i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Atlassian Cloud för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Atlassian Cloud Etablering](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-grupper mot Atlassian Cloud**.

    ![Atlassian Cloud Etablering](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. Granska gruppattributen som synkroniseras från Azure AD till Atlassian Cloud i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Atlassian Cloud för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Atlassian Cloud Etablering](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

16. Om du vill aktivera Azure AD-etableringstjänsten för Atlassian Cloud ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Atlassian Cloud Etablering](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. Definiera de användare och/eller grupper som du vill etablera till Atlassian Cloud genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Atlassian Cloud Etablering](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. När du är redo att etablera klickar du på **Spara**.

    ![Atlassian Cloud Etablering](./media/atlassian-cloud-provisioning-tutorial/save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Atlassian Cloud.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

* Atlassian Cloud tillåter etablering av användare endast från [verifierade domäner](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian Cloud stöder inte gruppbyten idag. Det innebär att alla ändringar i visningsnamnet för en grupp i Azure AD inte kommer att uppdateras och återspeglas i Atlassian Cloud.
* Värdet för **attributet e-postanvändare** i Azure AD fylls bara i om användaren har en Microsoft Exchange-postlåda. Om användaren inte har ett, rekommenderas att mappa ett annat önskat attribut till **attributet e-post** i Atlassian Cloud.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png