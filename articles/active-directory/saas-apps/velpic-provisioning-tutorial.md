---
title: 'Självstudier: Konfigurera Velpic för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Velpic.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 059ce3a23a9bdacfb978ccad775c7da853772e3f
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344830"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Velpic för automatisk Användaretablering


Målet med den här självstudien är att visa dig de steg du måste utföra i Velpic och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till Velpic. 

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active Directory-klientorganisation
*   En Velpic klient med den [företagsplanen](https://www.velpic.com/pricing.html) eller bättre har aktiverats 
*   Ett användarkonto i Velpic med administratörsbehörighet 

## <a name="assigning-users-to-velpic"></a>Tilldela användare till Velpic

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användarkontoetablering, kommer endast de användare och grupper som är ”kopplade” till ett program i Azure AD att synkroniseras. 

Innan du konfigurerar och aktiverar etableringstjänsten, måste du bestämma vilka användare och/eller grupper i Azure AD representerar de användare som behöver åtkomst till din Velpic-app. När du valt, kan du tilldela dessa användare till Velpic appen genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Viktiga tips för att tilldela användare till Velpic

*   Vi rekommenderar att en enda Azure AD-användare tilldelas Velpic att testa etablering konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till Velpic, måste du välja antingen den **användaren** roll eller en annan giltig programspecifika roll (om tillgängligt) i dialogrutan för tilldelning. Observera att den **standard åtkomst** rollen fungerar inte för etablering och dessa användare kommer att hoppas över.


## <a name="configuring-user-provisioning-to-velpic"></a>Konfigurera användaretablering för Velpic 

Det här avsnittet hjälper dig att ansluta din Azure AD till Velpics användarkonto etablering API och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i Velpic baserat på användare och grupptilldelning i Azure AD.

>[!TIP]
>Du kan också välja att aktiveras SAML-baserad enkel inloggning för Velpic, följa anvisningarna enligt [Azure-portalen](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av Automatisk etablering, även om de här två funktionerna komplettera varandra.


### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Konfigurera automatisk etablering av användarkonto till Velpic i Azure AD:

1.  I den [Azure-portalen](https://portal.azure.com), bläddra till den **Azure Active Directory > Företagsappar > alla program** avsnittet.

2. Om du redan har konfigurerat Velpic för enkel inloggning, söka efter din instans av Velpic med hjälp av sökfältet. Annars väljer **Lägg till** och Sök efter **Velpic** i programgalleriet. Välj Velpic i sökresultatet och lägga till den i din lista över program.

3.  Välj din instans av Velpic och välj sedan den **etablering** fliken.

4.  Ange den **Etableringsläge** till **automatisk**.

    ![Velpic etablering](./media/velpic-provisioning-tutorial/Velpic1.png)

5.  Under den **administratörsautentiseringsuppgifter** avsnittet, ange den **klient-URL och hemlighet Token** av Velpic. () Du hittar dessa värden under Velpic-kontot: **Manage** > **Integration** > **Plugin** > **SCIM**)

    ![Auktoriseringsvärden](./media/velpic-provisioning-tutorial/Velpic2.png)

6. I Azure-portalen klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till din Velpic-app. Om anslutningen misslyckas, kontrollera Velpic-kontot har administratörsbehörighet och försök steg 5 igen.

7. Ange e-postadress för en person eller grupp som ska få meddelanden om etablering fel i den **e-postmeddelande** fältet och markera kryssrutan nedan.

8. Klicka på **Spara**. 

9. Under avsnittet mappningar väljer **synkronisera Azure Active Directory-användare till Velpic**.

10. I den **attributmappningar** går du igenom användarattribut som ska synkroniseras från Azure AD till Velpic. Observera att attribut som är markerade som **matchande** egenskaper som används för att matcha användarkontona i Velpic för uppdateringsåtgärder. Välj knappen Spara för att genomföra ändringarna.

11. Om du vill aktivera den Azure AD-etableringstjänsten för Velpic, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet

12. Klicka på **Spara**. 

Detta startar den första synkroniseringen av användare och/eller grupper som tilldelats till Velpic i avsnittet användare och grupper. Observera att den inledande synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporter som beskriver alla åtgärder som utförs av etableringstjänsten.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)