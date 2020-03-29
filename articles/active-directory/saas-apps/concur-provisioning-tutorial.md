---
title: 'Självstudiekurs: Konfigurera Concur för automatisk användaretablering med Azure Active Directory| Microsoft-dokument'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och Concur.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 441aa9805f2a453e22f207238315125d2a281838
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60280434"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Concur för automatisk etablering av användare

Syftet med den här självstudien är att visa de steg du behöver för att utföra i Concur och Azure AD för att automatiskt etablera och avetableras användarkonton från Azure AD till Concur.

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active-katalogklient.
*   En Samtidig enkel inloggning aktiverad prenumeration.
*   Ett användarkonto i Concur med behörigheter för teamadministratör.

## <a name="assigning-users-to-concur"></a>Tilldela användare till Concur

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk etablering av användarkonto synkroniseras endast användare och grupper som har "tilldelats" till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till din Concur-app. När du har bestämt dig kan du tilldela dessa användare till din Concur-app genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Viktiga tips för att tilldela användare till Concur

*   Vi rekommenderar att en enda Azure AD-användare tilldelas Concur för att testa etableringskonfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till Concur måste du välja en giltig användarroll. Rollen "Standardåtkomst" fungerar inte för etablering.

## <a name="enable-user-provisioning"></a>Aktivera etablering av användare

Det här avsnittet hjälper dig att ansluta din Azure AD till Concurs API för etablering av användarkonton och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i Concur baserat på användar- och grupptilldelning i Azure AD.

> [!Tip] 
> Du kan också välja att aktivera SAML-baserade Single Sign-On for Concur, enligt instruktionerna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-user-account-provisioning"></a>Så här konfigurerar du etablering av användarkonton:

Syftet med det här avsnittet är att beskriva hur du aktiverar etablering av Active Directory-användarkonton till Concur.

Om du vill aktivera appar i utgiftstjänsten måste det finnas korrekt installation och användning av en webbtjänstadministratörsprofil. Lägg inte till WS-administratörsrollen i din befintliga administratörsprofil som du använder för administrationsfunktioner t&E.

Concur Consultants eller klientadministratören måste skapa en särskild webtjänstadministratörsprofil och klientadministratören måste använda den här profilen för administratörsfunktionerna i Webbtjänster (till exempel aktivera appar). Dessa profiler måste hållas åtskilda från klientadministratörens dagliga T&E-administratörsprofil (T&E-administratörsprofilen bör inte ha WSAdmin-rollen tilldelad).

När du skapar profilen som ska användas för att aktivera appen anger du klientadministratörens namn i användarprofilfälten. Detta tilldelar ägarskapet till profilen. När en eller flera profiler har skapats måste klienten logga in med den här profilen för att klicka på knappen*Aktivera*för en partnerapp på Web Services-menyn.

Av följande skäl bör denna åtgärd inte göras med den profil de använder för normal T&E-administration.

* Klienten måste vara den som klickar på "*Ja*" i dialogfönstret som visas när en app har aktiverats. Det här klicket bekräftar att klienten är villig för partnerprogrammet att komma åt deras data, så att du eller partnern inte kan klicka på knappen Ja.

* Om en klientadministratör som har aktiverat en app med hjälp av administratörsprofilen T&E lämnar företaget (vilket resulterar i att profilen inaktiveras) fungerar inte alla appar som är aktiverade med den profilen förrän appen är aktiverad med en annan aktiv WS-administratörsprofil. Det är därför du ska skapa distinkta WS Admin profiler.

* Om en administratör lämnar företaget kan namnet som är kopplat till WS-administratörsprofilen ändras till ersättningsadministratören om så önskas utan att påverka den aktiverade appen eftersom profilen inte behöver inaktiveras.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på din **Concur-klient.**

2. Välj **Webbtjänster**på **menyn Administration** .
   
    ![Concur hyresgäst](./media/concur-provisioning-tutorial/IC721729.png "Concur hyresgäst")

3. Välj **Aktivera partnerprogram**i fönstret **Webbtjänster** till vänster.
   
    ![Aktivera partnerprogram](./media/concur-provisioning-tutorial/ic721730.png "Aktivera partnerprogram")

4. Välj **Azure Active Directory**i listan Aktivera **program** och klicka sedan på **Aktivera**.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Stäng dialogrutan **Bekräfta åtgärd** genom att klicka på **Ja.**
   
    ![Bekräfta åtgärd](./media/concur-provisioning-tutorial/ic721732.png "Bekräfta åtgärd")

6. I [Azure-portalen](https://portal.azure.com)bläddrar du till **avsnittet Azure Active Directory > Enterprise Apps > Alla program.**

7. Om du redan har konfigurerat Concur för enkel inloggning söker du efter din instans av Concur med hjälp av sökfältet. Annars väljer du **Lägg till** och söker efter **Concur** i programgalleriet. Välj Concur från sökresultaten och lägg till det i listan över program.

8. Välj din förekomst av Concur och välj sedan fliken **Etablering.**

9. Ställ in **etableringsläget** på **Automatiskt**. 
 
    ![Etableringen](./media/concur-provisioning-tutorial/provisioning.png)

10. Ange **användarnamn** och **lösenord** för din Concur-administratör under avsnittet **Administratörsautentiseringsuppgifter.**

11. Klicka på Testa **anslutning** i Azure-portalen för att säkerställa att Azure AD kan ansluta till din Concur-app. Om anslutningen misslyckas kontrollerar du att ditt Samtidiga konto har behörigheten Teamadministratör.

12. Ange e-postadressen till en person eller grupp som ska få etableringsfelmeddelanden i fältet **E-postmeddelande** och markera kryssrutan.

13. Klicka på **Spara.**

14. Under avsnittet Mappningar väljer du **Synkronisera Azure Active Directory-användare att konsändra.**

15. I avsnittet **Attributmappningar** granskar du de användarattribut som synkroniseras från Azure AD till Concur. De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Concur för uppdateringsåtgärder. Välj knappen Spara om du vill utföra eventuella ändringar.

16. Om du vill aktivera Azure AD-etableringstjänsten för Concur ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar**

17. Klicka på **Spara.**

Nu kan du skapa ett testkonto. Vänta i upp till 20 minuter för att kontrollera att kontot har synkroniserats till Concur.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](concur-tutorial.md)

