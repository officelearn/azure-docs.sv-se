---
title: 'Självstudiekurs: Konfigurera Velpic för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableras användarkonton till Velpic.
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
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b7a6c2c9b7ecb0b160f7481d95f7682f3f7a109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064129"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Velpic för automatisk etablering av användare

Syftet med den här självstudien är att visa de steg du behöver för att utföra i Velpic och Azure AD för att automatiskt etablera och avetableras användarkonton från Azure AD till Velpic.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klientorganisation
* En Velpic-hyresgäst med [Enterprise-planen](https://www.velpic.com/pricing.html) eller bättre aktiverad
* Ett användarkonto i Velpic med administratörsbehörighet

## <a name="assigning-users-to-velpic"></a>Tilldela användare till Velpic

Azure Active Directory använder ett koncept som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk etablering av användarkonto synkroniseras endast användare och grupper som har "tilldelats" till ett program i Azure AD. 

Innan du konfigurerar och aktiverar etableringstjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till din Velpic-app. När du har bestämt dig kan du tilldela dessa användare till din Velpic-app genom att följa instruktionerna här:

[Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Viktiga tips för att tilldela användare till Velpic

* Vi rekommenderar att en enda Azure AD-användare tilldelas Velpic för att testa etableringskonfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Velpic måste du välja antingen **användarrollen** eller en annan giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. Observera att **standardåtkomstrollen** inte fungerar för etablering och att dessa användare hoppas över.

## <a name="configuring-user-provisioning-to-velpic"></a>Konfigurera användaresetablering till Velpic

Det här avsnittet hjälper dig att ansluta din Azure AD till Velpics API för etablering av användarkonton och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i Velpic baserat på användar- och grupptilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Velpic, enligt instruktionerna i [Azure portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Så här konfigurerar du automatisk etablering av användarkonton till Velpic i Azure AD:

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till **avsnittet Azure Active Directory > Enterprise Apps > Alla program.**

2. Om du redan har konfigurerat Velpic för enkel inloggning söker du efter din instans av Velpic med hjälp av sökfältet. Annars väljer du **Lägg till** och sök efter **Velpic** i programgalleriet. Välj Velpic i sökresultaten och lägg till det i listan över program.

3. Välj din förekomst av Velpic och välj sedan fliken **Etablering.**

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Velpic Etablering](./media/velpic-provisioning-tutorial/Velpic1.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du **klient-URL:en&hemlig token** för Velpic. (Du kan hitta dessa värden under ditt Velpic-konto: **Hantera** > **Integration** > **Plugin** > **SCIM**)

    ![Auktoriseringsvärden](./media/velpic-provisioning-tutorial/Velpic2.png)

6. Klicka på Testa **anslutning** i Azure-portalen för att säkerställa att Azure AD kan ansluta till din Velpic-app. Om anslutningen misslyckas kontrollerar du att ditt Velpic-konto har administratörsbehörighet och försök med steg 5 igen.

7. Ange e-postadressen till en person eller grupp som ska få etableringsfelmeddelanden i fältet **E-postmeddelande** och markera kryssrutan nedan.

8. Klicka på **Spara**.

9. Under avsnittet Mappningar väljer du **Synkronisera Azure Active Directory-användare till Velpic**.

10. I avsnittet **Attributmappningar** granskar du de användarattribut som ska synkroniseras från Azure AD till Velpic. Observera att de attribut som valts som **matchande** egenskaper kommer att användas för att matcha användarkontona i Velpic för uppdateringsåtgärder. Välj knappen Spara om du vill utföra eventuella ändringar.

11. Om du vill aktivera Azure AD-etableringstjänsten för Velpic ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar**

12. Klicka på **Spara**.

Detta startar den första synkroniseringen av alla användare och/eller grupper som tilldelats Velpic i avsnittet Användare och grupper. Observera att den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge tjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitetsrapporter, som beskriver alla åtgärder som utförs av etableringstjänsten.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)