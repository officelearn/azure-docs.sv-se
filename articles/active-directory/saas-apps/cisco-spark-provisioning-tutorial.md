---
title: 'Självstudier: Konfigurera Cisco Spark för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Cisco Spark.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: v-wingf
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fd7145be5f82a90acced0f02e35f6c8880dbcad
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120405"
---
# <a name="tutorial-configure-cisco-spark-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Cisco Spark för automatisk användaretablering


Målet med den här självstudien är att ange vilka åtgärder som ska utföras i Cisco Spark och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare till Cisco Spark.


> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

*   En Azure AD-klient
*   En Cisco Spark-klient
*   Ett användarkonto i Cisco Spark med administratörsbehörighet


> [!NOTE]
> Azure AD etablering integration förlitar sig på den [Cisco Spark webbtjänsten](https://developer.webex.com/getting-started.html), som är tillgängliga för Cisco Spark-team.

## <a name="adding-cisco-spark-from-the-gallery"></a>Lägga till Cisco Spark från galleriet
Du måste lägga till Cisco Spark från Azure AD-programgalleriet i listan över hanterade SaaS-program innan du konfigurerar Cisco Spark för automatisk användaretablering med Azure AD.

**Utför följande steg för att lägga till Cisco Spark från Azure AD-programgalleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, på den vänstra navigeringspanelen klickar du på den **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram** > **alla program**.

    ![Företagsprogram avsnittet][2]

3. Lägg till Cisco Spark, klicka på den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Cisco Spark**.

    ![Cisco Spark etablering](./media/cisco-spark-provisioning-tutorial/AppSearch.png)

5. I resultatpanelen väljer **Cisco Spark**, och klicka sedan på den **Lägg till** för att lägga till Cisco Spark i listan med SaaS-program.

    ![Cisco Spark etablering](./media/cisco-spark-provisioning-tutorial/AppSearchResults.png)

    ![Cisco Spark etablering](./media/cisco-spark-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-spark"></a>Tilldela användare till Cisco Spark

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare i Azure AD behöver åtkomst till Cisco Spark. När du valt, kan du tilldela dessa användare till Cisco Spark genom att följa instruktionerna här:

*   [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-spark"></a>Viktiga tips för att tilldela användare till Cisco Spark

*   Vi rekommenderar att en enda Azure AD-användare har tilldelats Cisco Spark för att testa konfigurationen för automatisk användaretablering. Ytterligare användare kan tilldelas senare.

*   När du tilldelar en användare till Cisco Spark, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-cisco-spark"></a>Konfigurera automatisk användaretablering för Cisco Spark

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare i Cisco Spark baserat på användare i Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cisco-spark-in-azure-ad"></a>Konfigurera automatisk användaretablering för Cisco Spark i Azure AD:


1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory > företagsprogram > alla program**.

2. Välj Cisco Spark från din lista över SaaS-program.

    ![Cisco Spark etablering](./media/cisco-spark-provisioning-tutorial/Successcenter2.png)

3. Välj den **etablering** fliken.

    ![Cisco Spark etablering](./media/cisco-spark-provisioning-tutorial/ProvisioningTab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Cisco Spark etablering](./media/cisco-spark-provisioning-tutorial/ProvisioningCredentials.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, ange den **klient-URL**, och **hemlighet Token** för din Cisco Spark-kontot.

    *   I den **klient-URL** fältet, fylla i Cisco Spark SCIM API-URL för din klient, som i form av `https://api.ciscospark.com/v1/scim/[Tenant ID]/`, där `[Tenant ID]` är en alfanumerisk sträng som beskrivs i steg 6.

    *   I den **hemlighet Token** fältet, Fyll hemlighet-Token enligt beskrivningen i steg 6.

1. Den **klient-ID** och **hemlighet Token** för Cisco-Spark konto kan hittas genom att logga in på [Cisco Spark-utvecklarwebbplatsen](https://developer.webex.com/) med ditt administratörskonto. Efter att du loggat in –
   * Gå till den [sidan komma igång](https://developer.webex.com/getting-started.html)
   * Rulla ned till den [autentisering](https://developer.webex.com/getting-started.html#authentication)
     ![Cisco Spark-autentiseringstoken](./media/cisco-spark-provisioning-tutorial/SecretToken.png)
   * Alfanumerisk sträng i rutan är din **hemlighet Token**. Kopiera den här variabeln till Urklipp
   * Gå till den [få min egen informationssida](https://developer.webex.com/endpoint-people-me-get.html)
       * Se till att testa läge är Aktiverat
       * Skriv ordet ”ägar” följt av ett blanksteg och sedan klistrar in hemligheten-Token i fältet auktorisering ![Cisco Spark-autentiseringstoken](./media/cisco-spark-provisioning-tutorial/GetMyDetails.png)
       * Klicka på Kör
   * I svarstext till höger, i **klient-ID** visas som ”orgId”:

     ```json
     {
       "id": "(...)",
       "emails": [
           "admin.user@contoso.com"
       ],
       "displayName": "John Smith",
       "nickName": "John",
       "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
       (...)
     }
     ```

1. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till Cisco Spark. Om anslutningen misslyckas, kontrollera Cisco Spark-kontot har administratörsbehörighet och försök igen.

    ![Cisco Spark etablering](./media/cisco-spark-provisioning-tutorial/TestConnection.png)

8. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan - **skicka ett e-postmeddelande när ett fel inträffar**.

    ![Cisco Spark etablering](./media/cisco-spark-provisioning-tutorial/EmailNotification.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Cisco Spark**.

    ![Cisco Spark etablering](./media/cisco-spark-provisioning-tutorial/UserMapping.png)

11. Granska användarattribut som ska synkroniseras från Azure AD med Cisco Spark i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i Cisco Spark för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Cisco Spark etablering](./media/cisco-spark-provisioning-tutorial/UserMappingAttributes.png)

12. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera den Azure AD-etableringstjänsten för Cisco Spark, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Cisco Spark etablering](./media/cisco-spark-provisioning-tutorial/ProvisioningStatus.png)

14. Ange användare och/eller grupper som du vill att etablera till Cisco Spark genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Cisco Spark etablering](./media/cisco-spark-provisioning-tutorial/SyncScope.png)

15. När du är redo att etablera, klickar du på **spara**.

    ![Cisco Spark etablering](./media/cisco-spark-provisioning-tutorial/Save.png)


Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på Cisco Spark.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutningen

* Cisco Spark är för närvarande under Ciscos tidig fältet testning (EFT). För mer information kontaktar [Ciscos supportteamet](https://www.webex.co.in/support/support-overview.html). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_03.png
