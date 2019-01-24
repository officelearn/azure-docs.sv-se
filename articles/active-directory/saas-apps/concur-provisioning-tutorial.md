---
title: 'Självstudier: Konfigurera Concur för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och Concur.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: e6e6d0d51174250954f886790dccc650064a6f45
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54821897"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Concur för automatisk användaretablering

Målet med den här självstudien är att visa dig de steg du måste utföra i Concur och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till Concur.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient.
*   En Concur enkel inloggning aktiverat prenumeration.
*   Ett användarkonto i Concur Team administratörsbehörigheter.

## <a name="assigning-users-to-concur"></a>Tilldela användare till Concur

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, synkroniseras de användare och grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till din app för Concur. När du valt, kan du tilldela dessa användare till Concur appen genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Viktiga tips för att tilldela användare till Concur

*   Vi rekommenderar att en enda Azure AD-användare tilldelas Concur att testa etablering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till Concur, måste du välja en giltig användarroll. Rollen ”standard åtkomst” fungerar inte för etablering.

## <a name="enable-user-provisioning"></a>Aktivera etableringen av användare

Det här avsnittet hjälper dig att ansluta din Azure AD till Concurs användarkonto etablering API och konfigurera etableringstjänsten att skapa, uppdatera och inaktivera tilldelade användarkonton i Concur baserat på användar- och grupptilldelningar i Azure AD.

> [!Tip] 
> Du kan också välja att aktiveras SAML-baserad enkel inloggning för Concur, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-user-account-provisioning"></a>Konfigurera etablering av användarkonto:

Målet med det här avsnittet som beskriver hur du aktivera etablering av Active Directory-användarkonton till Concur.

Om du vill aktivera appar i tjänsten Utgift, det måste vara korrekt inställning och användning av en Web Service Admin-profil. Lägg inte till WS-administratörsroll till din administratörsprofil som du använder för T & E administrativa funktioner.

Concurs konsulter eller klientadministratör måste skapa en egen Web Service-administratör-profil och klient-administratör måste använda den här profilen för Web Services Administrator (till exempel aktivera appar). De här profilerna måste hållas separat från klienten administratörens dagliga T & AT administratörsprofil (admin-profil på r & T inte ska ha WSAdmin rolltilldelningen).

När du skapar profilen som ska användas för att aktivera appen ingå klienten administratörsnamn Användarfält för profilen. Den här koden tilldelar ägarskap till profilen. När en eller flera profiler har skapats kan klienten måste logga in med den här profilen till klickar du på den ”*aktivera*” knappen för en App för Partner inom Web Services-menyn.

Av följande skäl bör den här åtgärden inte utföras med de använder för normal administration av d & E-profilen.

* Klienten måste vara det som klickar på ”*Ja*” på fönstret för dialogrutan som visas när en app har aktiverats. Detta klickar du på om klienten är villigt för Partner-program komma åt sina data, så att du eller partnern inte klickar du på knappen Ja.

* Om en klientadministratör som har aktiverat en app lämnar med hjälp av administratören d & E profil företaget (vilket innebär att profilen är inaktiverat), alla appar som har aktiverats med hjälp av profilen inte fungerar förrän appen har aktiverats med en annan active WS Admin-profil. Det här är anledningen till att du ska skapa distinkta WS-Admin-profiler.

* Om en administratör lämnar företaget, kan namnet som associeras till profilen WS-administratören ändras till ersättning-administratören om du vill utan att påverka aktiverade app eftersom profilen inte behöver inaktiverat.

**Utför följande steg för att konfigurera användarförsörjning:**

1. Logga in på din **Concur** klient.

2. Från den **Administration** menyn och välj **webbtjänster**.
   
    ![Concur klient](./media/concur-provisioning-tutorial/IC721729.png "Concur klient")

3. På vänster sida från den **webbtjänster** väljer **aktivera partnerprogrammet**.
   
    ![Aktivera partnerprogrammet](./media/concur-provisioning-tutorial/ic721730.png "gör Partner-program")

4. Från den **aktivera programmet** väljer **Azure Active Directory**, och klicka sedan på **aktivera**.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Klicka på **Ja** att Stäng den **bekräfta åtgärden** dialogrutan.
   
    ![Bekräfta åtgärden](./media/concur-provisioning-tutorial/ic721732.png "bekräfta åtgärden")

6. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

7. Om du redan har konfigurerat Concur för enkel inloggning, söka efter din instans av Concur med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Concur** i programgalleriet. Välj Concur i sökresultatet och lägga till den i din lista över program.

8. Välj din instans av Concur och välj sedan den **etablering** fliken.

9. Ange den **Etableringsläge** till **automatisk**. 
 
    ![etablering](./media/concur-provisioning-tutorial/provisioning.png)

10. Under den **administratörsautentiseringsuppgifter** anger den **användarnamn** och **lösenord** administratörens Concur.

11. I Azure-portalen klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till din app för Concur. Om anslutningen misslyckas se till att din Concur-kontot har administratörsbehörighet för teamet.

12. Ange e-postadress för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fältet och markera kryssrutan.

13. Klicka på **spara.**

14. Under avsnittet mappningar väljer **synkronisera Azure Active Directory-användare till Concur.**

15. I den **attributmappningar** går du igenom användarattribut som synkroniseras från Azure AD till Concur. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkontona i Concur för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

16. Om du vill aktivera den Azure AD-etableringstjänsten för Concur, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet

17. Klicka på **spara.**

Du kan nu skapa ett testkonto. Vänta i upp till 20 minuter att verifiera att kontot har synkroniserats till Concur.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](concur-tutorial.md)

