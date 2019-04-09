---
title: 'Självstudier: Konfigurera Atlassians moln för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Atlassians molnet.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.openlocfilehash: f4e76121f7815702270d6601413ff7a4c2c25839
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056987"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Atlassians moln för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i Atlassians molnet och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Atlassians moln.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningsappen är för närvarande i offentlig förhandsversion. Läs mer på allmänna Microsoft Azure-villkor för användning av förhandsversionsfunktioner [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En Atlassians molnklient](https://www.atlassian.com/licensing/cloud)
* Ett användarkonto i Atlassians molnet med administratörsbehörighet.

> [!NOTE]
> Azure AD etablering integration förlitar sig på den **Atlassians Cloud SCIM API**, som är tillgängliga för Atlassians molnet team.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Lägg till Atlassians molnet från galleriet

Du måste lägga till Atlassians molnet från Azure AD-programgalleriet i listan över hanterade SaaS-program innan du konfigurerar Atlassians molnet för automatisk användarförsörjning med Azure AD.

**Utför följande steg för att lägga till Atlassians molnet från Azure AD-programgalleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företagsprogram**, och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger **Atlassians molnet**väljer **Atlassians molnet** i resultatrutan och klicka sedan på den **Lägg till** för att lägga till programmet.

    ![Atlassian Cloud i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Tilldela användare till Atlassians molnet

Azure Active Directory använder ett begrepp som kallas *tilldelningar* att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Atlassians molnet. När du valt, kan du tilldela dessa användare och/eller grupper till Atlassians moln genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Viktiga tips för att tilldela användare till Atlassians molnet

* Vi rekommenderar att en enda Azure AD-användare har tilldelats Atlassians molnet för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Atlassians moln, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Konfigurera automatisk användaretablering till Atlassians moln 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i Atlassians molnet baserat på användare och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Atlassians moln, följa anvisningarna enligt den [Atlassians molnet enkel inloggning för självstudien](atlassian-cloud-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Konfigurera automatisk användaretablering för Atlassians molnet i Azure AD:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och välj **företagsprogram**väljer **alla program**och välj sedan **Atlassians molnet**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Atlassians molnet**.

    ![Atlassian Cloud-länken i programlistan](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Atlassians molnet etablering](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Atlassians molnet etablering](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, ange den **klient-URL** och **hemlighet Token** av din Atlassians molnkonto. Exempel på dessa värden är:

   * I den **klient-URL** fältet, Fyll specifik klient-slutpunkten som du får från Atlassians, enligt beskrivningen i steg 6. Till exempel: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * I den **hemlighet Token** fältet, Fyll hemlig token enligt beskrivningen i steg 6.

6. Gå till [Atlassians organisation Manager](https://admin.atlassian.com) **> Användaretablering** och klicka på **skapa en Token**. Kopiera den **Directory bas-URL** och **ägar-Token** till den **klient-URL** och **hemlighet Token** respektive fält.

    ![Atlassians molnet etablering](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassians molnet etablering](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)

    ![Atlassians molnet etablering](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till Atlassians moln. Om anslutningen misslyckas se till att ditt Atlassians Cloud-konto har administratörsbehörighet och försök igen.

    ![Atlassians molnet etablering](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

8. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![Atlassians molnet etablering](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Atlassians moln**.

    ![Atlassians molnet etablering](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. Granska användarattribut som synkroniseras från Azure AD till Atlassians moln i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i Atlassians molnet för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Atlassians molnet etablering](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. Under den **mappningar** väljer **synkronisera Azure Active Directory-grupper till Atlassians moln**.

    ![Atlassians molnet etablering](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. Granska Gruppattribut som synkroniseras från Azure AD till Atlassians moln i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha grupper i Atlassians molnet för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Atlassians molnet etablering](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera Azure AD etablering-Molntjänsten för Atlassians, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Atlassians molnet etablering](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. Ange användare och/eller grupper som du vill att etablera Atlassians molnet genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Atlassians molnet etablering](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. När du är redo att etablera, klickar du på **spara**.

    ![Atlassians molnet etablering](./media/atlassian-cloud-provisioning-tutorial/save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på Atlassians moln.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen

* Atlassians molnet gör att etableringen av användare från [verifierat domäner](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassians molnet stöder inte byter namn på gruppen i dag. Det innebär att ändringar av displayName för en grupp i Azure AD inte uppdateras och visas i Atlassians molnet.
* Värdet för den **e** användarattribut i Azure AD fylls bara om användaren har en Microsoft Exchange-postlåda. Om användaren inte har något vi rekommenderar att mappa ett annat önskade attribut till den **e-postmeddelanden** attribut i Atlassians molnet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
