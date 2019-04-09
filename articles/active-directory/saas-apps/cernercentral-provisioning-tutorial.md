---
title: 'Självstudier: Konfigurera Cerner Central för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera användare till en lista i Cerner Central.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00a967d61a5f81fc871488ea48df9cb4cf18c269
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59058075"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Cerner Central för automatisk användaretablering

Målet med den här självstudien är att visa dig vad du behöver för att utföra i Cerner Central och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till en lista med användare i Cerner Central.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klientorganisation
* En Cerner Central-klient

> [!NOTE]
> Azure Active Directory kan integreras med Cerner Central med hjälp av den [SCIM](http://www.simplecloud.info/) protokoll.

## <a name="assigning-users-to-cerner-central"></a>Tilldela användare till Cerner Central

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, synkroniseras de användare och grupper som är ”kopplade” till ett program i Azure AD. 

Innan du konfigurerar och aktiverar etableringstjänsten, bör du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till Cerner Central. När du valt, kan du tilldela dessa användare till Cerner Central genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Viktiga tips för att tilldela användare till Cerner Central

* Vi rekommenderar att en enda Azure AD-användare tilldelas till Cerner Central att testa etablering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När den inledande testningen är klar för en enskild användare, rekommenderar Cerner centrala tilldela hela listan över användare som ska få åtkomst till någon Cerner-lösning (inte bara Cerner centrala) som ska tillhandahållas Cerners användaren lista.  Andra lösningar för Cerner utnyttja den här listan över användare i användarens lista.

* När du tilldelar en användare till Cerner Central, måste du välja den **användaren** roll i dialogrutan för tilldelning. Användare med rollen ”standard åtkomst” undantas från etablering.

## <a name="configuring-user-provisioning-to-cerner-central"></a>Konfigurera användaretablering till Cerner Central

Det här avsnittet hjälper dig att ansluta din Azure AD till Cerner centrala användare lista med Cerner's SCIM användarkonto etablering API och konfigurera etableringstjänsten att skapa, uppdatera och inaktivera tilldelad användare som har konton i Cerner centrala baserat på tilldelning av användare och grupper i Azure AD.

> [!TIP]
> Du kan också välja att aktiverat SAML-baserad enkel inloggning för Cerner Central, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna kompletterar varandra. Mer information finns i den [Cerner centrala enkel inloggning för självstudien](cernercentral-tutorial.md).

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Konfigurera automatisk etablering av användarkonto till Cerner Central i Azure AD:

För att kunna tillhandahålla användarkonton till Cerner Central behöver att begära ett Cerner centrala systemkonto från Cerner och generera en OAuth-ägartoken som Azure AD kan använda för att ansluta till Cerner's SCIM-slutpunkten. Vi rekommenderar också att integrationen ska utföras i en testmiljö för Cerner innan du distribuerar till produktion.

1. Det första steget är att se till att de personer som hanterar Cerner och Azure AD-integrering har ett konto för CernerCare som krävs för att få tillgång till dokumentationen som behövs för att slutföra anvisningarna. Om det behövs kan du använda adresserna nedan för att skapa CernerCare konton i varje tillämplig miljö.

   * Sandbox-miljön:  https://sandboxcernercare.com/accounts/create

   * Produktion:  https://cernercare.com/accounts/create  

2. Därefter måste du skapade ett systemkonto för Azure AD. Följ anvisningarna nedan om du vill begära ett systemkonto för din sandbox- och produktionsmiljöer.

   * Instruktioner:  https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Sandbox-miljön: https://sandboxcernercentral.com/system-accounts/

   * Produktion:  https://cernercentral.com/system-accounts/

3. Nu ska du generera en OAuth-ägartoken för var och en av dina Systemkonton. Följ anvisningarna nedan om du vill göra detta.

   * Instruktioner:  https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Sandbox-miljön: https://sandboxcernercentral.com/system-accounts/

   * Produktion:  https://cernercentral.com/system-accounts/

4. Slutligen måste du skaffa användaridentiteter lista sfär för både sandbox- och produktionsdistributionerna miljöer i Cerner att slutföra konfigurationen. Information om hur du skaffar detta finns: https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM. 

5. Nu kan du konfigurera Azure AD för att etablera användarkonton till Cerner. Logga in på den [Azure-portalen](https://portal.azure.com), och bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

6. Om du redan har konfigurerat Cerner Central för enkel inloggning, söka efter din instans av Cerner centrala med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Cerner centrala** i programgalleriet. Välj Cerner centrala från sökresultaten och lägga till den i din lista över program.

7. Välj din instans av Cerner Central och välj sedan den **etablering** fliken.

8. Ange den **Etableringsläge** till **automatisk**.

   ![Cerner Central etablering](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Fyll i följande fält under **administratörsautentiseringsuppgifter**:

   * I den **klient-URL** anger en URL i formatet nedan, där du ersätter ”User-lista-sfär-ID” med ID: T för sfär som du har köpt i steg #4.

    > Sandbox-miljön: https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > Produktion: https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * I den **hemlighet Token** fältet anger OAuth-ägartoken som du genererade i steg #3 Klicka **Testanslutningen**.

   * Du bör se ett meddelande om lyckad på upperright sida av portalen.

1. Ange e-postadress för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fältet och markera kryssrutan nedan.

1. Klicka på **Spara**.

1. I den **attributmappningar** går du igenom attribut för användare och grupper som ska synkroniseras från Azure AD till Cerner Central. Attribut som har markerats som **matchande** egenskaper som används för att matcha de användarkonton och grupper i Cerner Central för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

1. Om du vill aktivera den Azure AD-etableringstjänsten för Cerner Central, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet

1. Klicka på **Spara**.

Detta startar den första synkroniseringen av användare och/eller grupper som har tilldelats till Cerner Central i avsnittet användare och grupper. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsloggar som beskriver alla åtgärder som utförs av etableringstjänsten på din Cerner Central-app.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Cerner centrala: Publicera identitetsdata med hjälp av Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Självstudier: Konfigurera Cerner Central för enkel inloggning med Azure Active Directory](cernercentral-tutorial.md)
* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).
