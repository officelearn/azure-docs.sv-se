---
title: 'Självstudier: Konfigurera Dropbox för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 0f3a42e4-6897-4234-af84-b47c148ec3e1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 3ae85b31e85ecc0f0df6500bc7cfda1719537f02
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814197"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Dropbox for Business för automatisk användaretablering

Målet med den här självstudien är att visa dig de steg som du behöver utföra i Dropbox för företag och Azure AD att automatiskt etablera och avetablera användarkonton från Azure AD till Dropbox för företag.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active directory-klient.
*   En Dropbox for Business enkel inloggning aktiverad prenumeration.
*   Ett användarkonto i Dropbox för företag med administratörsbehörighet för teamet.

## <a name="assigning-users-to-dropbox-for-business"></a>Tilldela användare till Dropbox för företag

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, synkroniseras de användare och grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till din Dropbox för företag. När du bestämt dig kan tilldela du dessa användare till din Dropbox för företag genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Viktiga tips för att tilldela användare till Dropbox för företag

*   Vi rekommenderar att en enda Azure AD-användare är tilldelad till Dropbox för företag att testa etablering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till Dropbox för företag, måste du välja en giltig användarroll. Rollen ”standard åtkomst” fungerar inte för att etablera...

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk Användaretablering

Det här avsnittet hjälper dig att ansluta din Azure AD till Dropbox för verksamhetens användarkonto etablering API och konfigurera etableringstjänsten att skapa, uppdatera och inaktivera tilldelade användarkonton i Dropbox for Business baserat på användare och grupper tilldelning i Azure AD.

>[!Tip]
>Du kan också välja att aktiveras SAML-baserad enkel inloggning för Dropbox for Business, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurera automatisk användarkontoetablering:

1. I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

2. Om du redan har konfigurerat Dropbox for Business för enkel inloggning, söka efter din instans av Dropbox för företag med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Dropbox for Business** i programgalleriet. Välj Dropbox for Business i sökresultatet och lägga till den i din lista över program.

3. Välj din instans av Dropbox för företag och välj sedan den **etablering** fliken.

4. Ange den **Etableringsläge** till **automatisk**. 

    ![etablering](./media/dropboxforbusiness-provisioning-tutorial/provisioning.png)

5. Under den **administratörsautentiseringsuppgifter** klickar du på **auktorisera**. En Dropbox for Business inloggningsruta öppnas den i ett nytt webbläsarfönster.

6. På den **logga in till Dropbox för att länka med Azure AD** dialogrutan Logga in på din Dropbox för företag-klient.

     ![Användaretablering](./media/dropboxforbusiness-provisioning-tutorial/ic769518.png "etableringen av användare")

7. Bekräfta att du vill ge Azure Active Directory-behörighet att göra ändringar i din Dropbox för företag-klient. Klicka på **Tillåt**.
    
      ![Användaretablering](./media/dropboxforbusiness-provisioning-tutorial/ic769519.png "etableringen av användare")

8. I Azure-portalen klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till din Dropbox för företag. Om anslutningen misslyckas se till att din Dropbox för företag-konto som har administratörsbehörigheter för Team och prova den **”auktorisera”** steg igen.

9. Ange e-postadress för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fältet och markera kryssrutan.

10. Klicka på **spara.**

11. Under avsnittet mappningar väljer **synkronisera Azure Active Directory-användare till Dropbox för företag.**

12. I den **attributmappningar** går du igenom användarattribut som synkroniseras från Azure AD till Dropbox för företag. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i Dropbox för företag för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

13. Om du vill aktivera den Azure AD-etableringstjänsten för Dropbox for Business, ändra den **Etableringsstatus** till **på** i avsnittet Inställningar

14. Klicka på **spara.**

Den startar den första synkroniseringen av användare och/eller grupper som har tilldelats till Dropbox för företag i avsnittet användare och grupper. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av etableringstjänsten på din Dropbox för företag.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](dropboxforbusiness-tutorial.md)