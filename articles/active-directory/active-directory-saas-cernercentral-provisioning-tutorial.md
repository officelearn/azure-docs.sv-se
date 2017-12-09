---
title: "Självstudier: Konfigurera Cerner Central för automatisk användaretablering med Azure Active Directory | Microsoft Docs"
description: "Lär dig hur du konfigurerar Azure Active Directory automatiskt etablera användare till en listan i Cerner Central."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: asmalser-msft
ms.openlocfilehash: f5e8d6f1e6c08d01f91b30ef40be616db2a16fcc
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="tutorial-configuring-cerner-central-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Cerner Central för automatisk Användaretablering

Syftet med den här kursen är att visa de steg som du behöver göra i Cerner Central och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till en användare listan i Cerner Central. 


## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

*   En Azure Active Directory-klient
*   En Cerner Central-klient 

> [!NOTE]
> Azure Active Directory kan integreras med Cerner Central med hjälp av den [SCIM](http://www.simplecloud.info/) protokoll.

## <a name="assigning-users-to-cerner-central"></a>Tilldela användare till Cerner Central

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare ska få åtkomst till valda appar. I samband med automatisk konto användaretablering, synkroniseras de användare och grupper som har ”tilldelats” till ett program i Azure AD. 

Innan du konfigurerar och aktiverar tjänsten etablering, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till Cerner Central. När du valt, kan du tilldela dessa användare till Cerner Central genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en enterprise-app](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Viktiga tips för att tilldela användare till Cerner Central

*   Vi rekommenderar att en enda Azure AD-användare tilldelas Cerner Central att testa allokering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När det första testet är klart för en enskild användare, rekommenderar Cerner Central tilldelar hela listan över användare som ska få åtkomst till alla Cerner lösningen (inte bara Cerner Central) som ska etableras till Cerners användaren listan.  Andra Cerner lösningar utnyttja den här listan över användare i listan för användaren.

*   När du tilldelar en användare Cerner Central, måste du välja den **användaren** roll i dialogrutan tilldelning. Användare med rollen ”standard åtkomst” undantas från etablering.


## <a name="configuring-user-provisioning-to-cerner-central"></a>Konfigurering av användarförsörjning till Cerner Central

Det här avsnittet hjälper dig att ansluta din Azure AD till Cerner Central användaren listan med Cerner's SCIM användarkonto API-etablering och konfigurera tjänsten etablering för att skapa, uppdatera och inaktivera tilldelad användare konton i Cerner Central baserat på tilldelning av användare och grupper i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkel inloggning för Cerner centrala, följa instruktionerna i [Azure-portalen (https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om dessa två funktioner kompletterar varandra. Mer information finns i [Cerner Central enkel inloggning kursen](active-directory-saas-cernercentral-tutorial.md).


### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Konfigurera automatisk konto användaretablering till Cerner Central i Azure AD:


För att kunna etablera användarkonton till Cerner Central måste du begära en Cerner Central systemkontot från Cerner och generera en OAuth ägar-token som Azure AD kan använda för att ansluta till Cerner's SCIM slutpunkt. Vi rekommenderar också att integrationen ska utföras i begränsat läge Cerner innan du distribuerar till produktionen.

1.  Det första steget är att se till att de personer som hanterar Cerner och Azure AD-integrering har ett konto för CernerCare som krävs för att få åtkomst till dokumentationen som behövs för att slutföra anvisningarna. Om det behövs kan du använda adresserna nedan för att skapa CernerCare konton i varje tillämplig miljö.

   * Sandbox: https://sandboxcernercare.com/accounts/create

   * Produktion: https://cernercare.com/accounts/create  

2.  Därefter måste en system-kontot skapas för Azure AD. Du kan följa anvisningarna nedan för att begära ett systemkonto för din sandbox och produktionsmiljöer.

   * Anvisningar: https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Sandbox: https://sandboxcernercentral.com/system-accounts/

   * Produktion: https://cernercentral.com/system-accounts/

3.  Därefter Generera en OAuth ägar-token för var och en av dina Systemkonton. Följ anvisningarna nedan om du vill göra detta.

   * Anvisningar: https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Sandbox: https://sandboxcernercentral.com/system-accounts/

   * Produktion: https://cernercentral.com/system-accounts/

4. Slutligen måste du hämta användaridentiteter listan sfär för både den sandbox och produktionsmiljöer i Cerner för att slutföra konfigurationen. Mer information om hur du skaffar detta finns: https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM. 

5. Du kan nu konfigurera Azure AD för att etablera användarkonton till Cerner. Logga in på den [Azure-portalen](https://portal.azure.com), och bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnitt.

6. Om du redan har konfigurerat Cerner Central för enkel inloggning söka efter din instans av Cerner Central med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Cerner Central** i programgalleriet. Välj Cerner Central i sökresultatet och lägga till den i listan med program.

7.  Välj din instans av Cerner Central och sedan den **etablering** fliken.

8.  Ange den **Etableringsläge** till **automatisk**.

   ![Cerner Central etablering](./media/active-directory-saas-cernercentral-provisioning-tutorial/Cerner.PNG)

9.  Fyll i följande fält under **administratörsautentiseringsuppgifter**:

   * I den **klient URL** , ange en URL i formatet nedan, ersätter du ”användare-listan-sfär-ID” med sfär-ID som du har införskaffade i steg #4.

> Sandbox: https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

> Produktion: https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * I den **hemlighet Token** skriver OAuth-ägar-token som du genererade i steg #3 och klicka på **Testanslutningen**.

   * Du bör se ett meddelande om lyckade på upperright sida av portalen.

10. Ange e-postadressen för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fält och markera kryssrutan nedan.

11. Klicka på **Spara**. 

12. I den **attributmappning** avsnittet kan du granska de användar- och attribut som ska synkroniseras från Azure AD till Cerner Central. De attribut som valts som **matchande** egenskaper som används för att matcha användarkonton och grupper i Cerner Central för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

13. Om du vill aktivera Azure AD etableras för Cerner centrala ändra den **Status för etablering** till **på** i den **inställningar** avsnitt

14. Klicka på **Spara**. 

Detta startar den första synkroniseringen av användare och/eller grupper som tilldelas till Cerner Central i avsnittet användare och grupper. Den första synkroniseringen tar längre tid än efterföljande synkroniseringar som sker ungefär var tjugonde minut så länge som Azure AD Etablerar-tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnittet för att övervaka förloppet och följ länkarna till att etablera aktivitetsrapporter som beskriver alla åtgärder som utförs av tjänsten etablering i appen Cerner Central.

Mer information om hur du tolkar Azure AD-etablering loggar finns [rapportering om automatisk konto användaretablering](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

## <a name="additional-resources"></a>Ytterligare resurser

* [Cerner Central: Publicering av identitetsdata med Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Självstudier: Konfigurera Cerner Central för enkel inloggning med Azure Active Directory](active-directory-saas-cernercentral-tutorial.md)
* [Hantera användare konto-etablering för företag-appar](active-directory-enterprise-apps-manage-provisioning.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Nästa steg
* [Lär dig hur du granska loggarna och få rapporter om etablering aktiviteten](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).
