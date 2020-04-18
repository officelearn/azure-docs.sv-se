---
title: 'Självstudiekurs: Konfigurera Juno Journey för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Läs om hur du automatiskt etablerar och avserar användarkonton från Azure AD till Juno Journey.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 79813b19-c96e-4459-a4e5-636a6b6f5041
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2020
ms.author: Zhchia
ms.openlocfilehash: 08de07a52d1e43dea91e6684d33027d8bcad61fc
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641854"
---
# <a name="tutorial-configure-juno-journey-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Juno Journey för automatisk användaretablering

I den här självstudien beskrivs de steg du behöver utföra i både Juno Journey och Azure Active Directory (Azure AD) för att konfigurera automatisk användaretablering. När azure AD är konfigurerat avsersättningar och avsersättningar avsättningar till [Juno Journey](https://www.junojourney.com/) med hjälp av Azure AD-etableringstjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Juno Journey
> * Ta bort användare i Juno Journey när de inte längre behöver åtkomst
> * Synkronisera användarattribut mellan Azure AD och Juno Journey
> * [Enkel inloggning till](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial) Juno Journey (rekommenderas)

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användarkonto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör). 
*  En [Juno Journey hyresgäst](https://www.junojourney.com/getstartedwithjuno).
*  Ett användarkonto i Juno Journey med administratörsbehörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera distributionen av etableringen
1. Läs mer om [hur etableringstjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bestäm vem som ska vara i [omfång för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Bestäm vilka data som ska [mappas mellan Azure AD och Juno Journey](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-juno-journey-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Juno Journey för att stödja etablering med Azure AD

1. För **hemlig token** och **klient-URL** kontakta support@the-juno.comJuno Journey supportteam på . Det här värdet anges i url-fälten **Hemlig token** respektive **klient** i fliken Etablering i ditt Juno Journey-program i Azure-portalen. 

## <a name="step-3-add-juno-journey-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till Juno Journey från Azure AD-programgalleriet

Lägg till Juno Journey från Azure AD-programgalleriet för att börja hantera etablering till Juno Journey. Om du tidigare har konfigurerat Juno Journey för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integrationen från början. Läs mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska vara i utrymme för etablering 

Azure AD-etableringstjänsten gör att du kan begränsa vem som ska etableras baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att begränsa vem som ska etableras i din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att begränsa vem som ska etableras enbart baserat på attribut för användaren eller gruppen kan du använda ett omfångsfilter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till Juno Journey måste du välja en annan roll än **Standardåtkomst**. Användare med rollen Standardåtkomst är undantagna från etablering och markeras som inte effektivt berättigade i etableringsloggarna. Om den enda roll som är tillgänglig för programmet är standardåtkomstrollen kan du [uppdatera programmanifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Börja i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När omfång för etablering är inställt på tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När scopet är inställt på alla användare och grupper kan du ange ett [attributbaserat omfångsfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-juno-journey"></a>Steg 5. Konfigurera automatisk användaretablering till Juno Journey 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar- och/eller grupptilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-juno-journey-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Juno Journey i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Juno Journey**i programlistan .

    ![Länken Juno Journey i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du klient-URL-värdet som hämtats tidigare i **klient-URL.** Mata in det hemliga tokenvärdet som hämtats tidigare i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Juno Journey. Om anslutningen misslyckas kontrollerar du att ditt Juno Journey-konto har administratörsbehörighet och försöker igen.

    ![Etableringen](./media/juno-journey-provisioning-tutorial/provisioning.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan **Skicka ett e-postmeddelande när ett fel inträffar.**

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Juno-färd**.

9. Granska användarattributen som synkroniseras från Azure AD till Juno Journey i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Juno Journey för uppdateringsåtgärder. Om du väljer att ändra [det matchande målattributet](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att Api:et för Juno Journey stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** om du vill utföra eventuella ändringar.

   |Variabel|Typ|
   |---|---|
   |userName|Sträng|
   |externt|Sträng|
   |displayName|Sträng|
   |title|Sträng|
   |aktiv|Boolesk|
   |preferredLanguage|Sträng|
   |e-postmeddelanden[typ eq "arbete"].värde|Sträng|
   |adresser[typ eq "arbete"].land|Sträng|
   |adresser[typ eq "arbete"].region|Sträng|
   |adresser[typ eq "work"].locality|Sträng|
   |adresser[typ eq "arbete"].postalCode|Sträng|
   |adresser[skriv eq "work"].formaterad|Sträng|
   |adresser[typ eq "work"].streetAddress|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |name.middleName|Sträng|
   |name.formatted|Sträng|
   |phoneNumbers[typ eq "fax"].värde|Sträng|
   |phoneNumbers[typ eq "mobil"].värde|Sträng|
   |phoneNumbers[typ eq "arbete"].värde|Sträng|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Sträng|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Sträng|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Sträng|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Sträng|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Sträng|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|Sträng|


10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för Juno Journey ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Juno Journey genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

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
