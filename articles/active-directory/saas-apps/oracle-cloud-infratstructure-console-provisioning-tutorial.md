---
title: 'Självstudiekurs: Konfigurera Oracle Cloud Infrastructure Console för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Läs om hur du automatiskt etablerar och avserlar användarkonton från Azure AD till Oracle Cloud Infrastructure Console.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e22c8746-7638-4a0f-ae08-7db0c477cf52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 5aa33529a1957b6e7728b3a87bacf6bb91d987ae
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378956"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Oracle Cloud Infrastructure Console för automatisk användaretablering

I den här självstudien beskrivs de steg du behöver utföra i både Oracle Cloud Infrastructure Console och Azure Active Directory (Azure AD) för att konfigurera automatisk användaretablering. När azure AD är konfigurerat avsersättningar och avsersättningar avsättningar till [Oracle Cloud Infrastructure Console](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) med hjälp av Azure AD-etableringstjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Oracles molninfrastrukturkonsol
> * Ta bort användare i Oracle Cloud Infrastructure Console när de inte längre behöver åtkomst
> * Synkronisera användarattribut mellan Azure AD och Oracle Cloud Infrastructure Console
> * Etableringsgrupper och gruppmedlemskap i Oracles molninfrastrukturkonsol
> * [Enkel inloggning till](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) Oracle Cloud Infrastructure Console (rekommenderas)

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användarkonto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör). 
* En Oracle Cloud Infrastructure [Control-klient](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton).
* Ett användarkonto i Oracle Cloud Infrastructure Control med administratörsbehörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera distributionen av etableringen
1. Läs mer om [hur etableringstjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bestäm vem som ska vara i [omfång för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Bestäm vilka data som ska [mappas mellan Azure AD och Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Oracle Cloud Infrastructure Console för att stödja etablering med Azure AD

1. Logga in på Administrationskonportalen för Oracle Cloud Infrastructure Console. I det övre vänstra hörnet av skärmen navigerar du till **Identity > Federation**.

    ![Oracle-administratör](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Klicka på webbadressen som visas på sidan bredvid Oracle Identity Cloud Service Console.

    ![Oracle URL](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Klicka på **Lägg till identitetsprovider** för att skapa en ny identitetsleverantör. Spara IdP-ID:et som ska användas som en del av klient-URL:en. Klicka på plusikonen bredvid **fliken Program** för att skapa en OAuth-klient och Grant IDCS Identity Domain Administrator AppRole.

    ![Ikon för Oracle Cloud](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Följ skärmbilderna nedan för att konfigurera ditt program. När konfigurationen är klar klicka på **Spara**.

    ![Oracle-konfiguration](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Policy för utgivning av Oracle-token](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Under fliken Konfigurationer i ditt program expandera alternativet **Allmän information** för att hämta klient-ID och klienthemlighet.

    ![Oracle token generation](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Så här skapar du en hemlig token Base64 koda klient-ID och klienthemlighet i formatet **klient-ID:Client Secret**. Spara den hemliga token. Det här värdet anges i fältet **Hemlig token** på etableringsfliken i ditt Oracle Cloud Infrastructure Console-program i Azure-portalen.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Steg 3. Lägga till Oracle Cloud Infrastructure Console från Azure AD-programgalleriet

Lägg till Oracle Cloud Infrastructure Console från Azure AD-programgalleriet för att börja hantera etablering till Oracle Cloud Infrastructure Console. Om du tidigare har konfigurerat Oracle Cloud Infrastructure Console för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integrationen från början. Läs mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska vara i utrymme för etablering 

Azure AD-etableringstjänsten gör att du kan begränsa vem som ska etableras baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att begränsa vem som ska etableras i din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att begränsa vem som ska etableras enbart baserat på attribut för användaren eller gruppen kan du använda ett omfångsfilter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till Oracle Cloud Infrastructure Console måste du välja en annan roll än **standardåtkomst**. Användare med rollen Standardåtkomst är undantagna från etablering och markeras som inte effektivt berättigade i etableringsloggarna. Om den enda roll som är tillgänglig för programmet är standardåtkomstrollen kan du [uppdatera programmanifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Börja i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När omfång för etablering är inställt på tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När scopet är inställt på alla användare och grupper kan du ange ett [attributbaserat omfångsfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Steg 5. Konfigurera automatisk användaretablering till Oracle Cloud Infrastructure Console 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar- och/eller grupptilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Oracle Cloud Infrastructure Console i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj Oracle Cloud **Infrastructure Console**i programlistan .

    ![Länken Oracle Cloud Infrastructure Console i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du `https://<IdP ID>.identity.oraclecloud.com/admin/v1` **klient-URL:en** i formatet . Till exempel `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Mata in det hemliga tokenvärdet som hämtats tidigare i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Oracles molninfrastrukturkonsol. Om anslutningen misslyckas kontrollerar du att ditt Oracle Cloud Infrastructure Console-konto har administratörsbehörighet och försöker igen.

    ![Etableringen](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar.**

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Oracle Cloud Infrastructure Console**.

9. Granska användarattributen som synkroniseras från Azure AD till Oracle Cloud Infrastructure Console i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Oracle Cloud Infrastructure Console för uppdateringsåtgärder. Om du väljer att ändra [det matchande målattributet](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att Oracles API för molninfrastrukturkonsol stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** om du vill utföra eventuella ändringar.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |userName|Sträng|
      |aktiv|Boolesk|
      |title|Sträng|
      |e-postmeddelanden[typ eq "arbete"].värde|Sträng|
      |preferredLanguage|Sträng|
      |name.givenName|Sträng|
      |name.familyName|Sträng|
      |adresser[skriv eq "work"].formaterad|Sträng|
      |adresser[typ eq "work"].locality|Sträng|
      |adresser[typ eq "arbete"].region|Sträng|
      |adresser[typ eq "arbete"].postalCode|Sträng|
      |adresser[typ eq "arbete"].land|Sträng|
      |adresser[typ eq "work"].streetAddress|Sträng|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Sträng|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Sträng|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Sträng|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Sträng|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referens|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|Sträng|
      |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:bypassNotification|Boolesk|
      |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:isFederatedUser|Boolesk|

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-grupper till Oracle Cloud Infrastructure Console**.

11. Granska gruppattributen som synkroniseras från Azure AD till Oracle Cloud Infrastructure Console i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Oracle Cloud Infrastructure Console för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |externt|Sträng|
      |medlemmar|Referens|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för Oracle Cloud Infrastructure Console ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Oracles molninfrastrukturkonsol genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den inledande synkroniseringscykeln för alla användare och grupper som **definierats** i Scope i avsnittet **Inställningar.** Den inledande cykeln tar längre tid att utföra än efterföljande cykler, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

* Använd [etableringsloggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att avgöra vilka användare som har etablerats eller utan framgång
* Kontrollera [förloppsindikatorn](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etableringscykeln och hur nära den är till slutförande
* Om etableringskonfigurationen verkar vara i feltillstånd kommer programmet att placeras i karantän. Läs mer om karantäntillstånd [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../manage-apps/check-status-user-account-provisioning.md)
