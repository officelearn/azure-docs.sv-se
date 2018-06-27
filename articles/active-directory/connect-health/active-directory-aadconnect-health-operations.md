---
title: Åtgärder i Azure Active Directory Connect Health
description: Den här artikeln beskriver ytterligare åtgärder som kan utföras när du har distribuerat Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: karavar
manager: mtillman
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 4377e9902696a4ee3b5bbb5fcb44d972ec14f822
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958733"
---
# <a name="azure-active-directory-connect-health-operations"></a>Åtgärder i Azure Active Directory Connect Health
Det här avsnittet beskrivs de olika åtgärder som du kan utföra med hjälp av Azure Active Directory (AD Azure) Connect Health.

## <a name="enable-email-notifications"></a>Aktivera e-postaviseringar
Du kan konfigurera Azure AD Connect Health-tjänsten för att skicka e-postmeddelanden när aviseringar anger identitetsinfrastrukturen inte är felfri. Detta inträffar när en avisering skapas och när den är löst.

![Skärmbild av Azure AD Connect Health e-postavisering inställningar](./media/active-directory-aadconnect-health/email_noti_discover.png)

> [!NOTE]
> E-postmeddelanden är aktiverade som standard.
>
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Aktivera Azure AD Connect Health e-postaviseringar
1. Öppna den **aviseringar** bladet för tjänsten som du vill få e-postavisering.
2. I Åtgärdsfältet klickar du på **Meddelandeinställningar**.
3. Välj på växeln e-postavisering **på**.
4. Markera kryssrutan om du vill att alla globala administratörer av e-postmeddelanden.
5. Om du vill ta emot e-postmeddelanden på e-postadresser du anger dem i den **ytterligare e-postmottagare** rutan. Om du vill ta bort en e-postadress från den här listan, högerklicka på posten och välj **ta bort**.
6. För att slutföra ändringarna klickar du på **spara**. Ändringarna börjar gälla förrän du sparar.

## <a name="delete-a-server-or-service-instance"></a>Ta bort en server eller service-instans

>[!NOTE] 
> Azure AD premium-licens krävs för steg som tas bort.

I vissa fall kanske du vill ta bort en server från som övervakas. Det här är vad du behöver veta för att ta bort en server från Azure AD Connect Health-tjänsten.

När du tar bort en server vara medveten om följande:

* Den här åtgärden slutar att samla in ytterligare data från servern. Den här servern tas bort från övervakningstjänsten. När den här åtgärden går det inte att visa nya aviseringar, övervakning och analys av användningsdata för den här servern.
* Den här åtgärden avinstalleras inte Hälsoagenten från din server. Om du inte har avinstallerat Hälsoagenten innan du utför det här steget kan det hända att fel som rör Health-agenten på servern.
* Den här åtgärden tar inte bort data som redan samlats in från den här servern. Data tas bort i enlighet med Azure databevarandeprincip.
* När du utför den här åtgärden om du vill börja övervaka samma server måste igen och du avinstallera och återinstallera Hälsoagenten på den här servern.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Ta bort en server från Azure AD Connect Health-tjänsten

>[!NOTE] 
> Azure AD premium-licens krävs för steg som tas bort.

Azure AD Connect Health för Active Directory Federation Services (AD FS) och Azure AD Connect (synkronisering):

1. Öppna den **Server** bladet från den **serverlista** bladet genom att markera namnet på servern som ska tas bort.
2. På den **Server** bladet från Åtgärdsfältet klickar du på **ta bort**.
3. Bekräfta genom att skriva namnet på servern i dialogrutan Bekräfta.
4. Klicka på **Ta bort**.

Azure AD Connect Health för Azure Active Directory Domain Services:

1. Öppna den **domänkontrollanter** instrumentpanelen.
2. Markera domänkontrollanten som ska tas bort.
3. I Åtgärdsfältet klickar du på **ta bort markerade**.
4. Bekräfta åtgärden för att ta bort servern.
5. Klicka på **Ta bort**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Ta bort en instans av tjänsten från Azure AD Connect Health service
I vissa fall kanske du vill ta bort en instans av tjänsten. Det här är vad du behöver veta för att ta bort en instans av tjänsten från Azure AD Connect Health-tjänsten.

När du tar bort en instans av tjänsten, Tänk på följande:

* Den här åtgärden tar bort den aktuella tjänstinstansen från övervakningstjänsten.
* Den här åtgärden inte avinstallera eller ta bort Hälsoagenten från någon av de servrar som har övervakas som en del av den här tjänstinstansen. Om du inte har avinstallerat Hälsoagenten innan du utför det här steget kan det hända att fel som rör Health-agenten på servrarna.
* Alla data från den här tjänstinstansen tas bort i enlighet med Azure databevarandeprincip.
* När du utför den här åtgärden om du vill börja övervaka tjänsten avinstallera och återinstallera Hälsoagenten på alla servrar. När du utför den här åtgärden om du vill börja övervaka samma server igen, avinstallera, installera och registrera Health-agenten på servern.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Ta bort en instans av tjänsten från Azure AD Connect Health-tjänsten
1. Öppna den **Service** bladet från den **Tjänstlista** bladet genom att välja service-identifier (servergruppens namn) som du vill ta bort.
2. På den **Server** bladet från Åtgärdsfältet klickar du på **ta bort**.
3. Bekräfta genom att skriva namnet på tjänsten i bekräftelserutan (till exempel: sts.contoso.com).
4. Klicka på **Ta bort**.
   <br><br>

[//]: # (Start av RBAC avsnitt)
## <a name="manage-access-with-role-based-access-control"></a>Hantera åtkomst med rollbaserad åtkomstkontroll
[Rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/role-assignments-portal.md) för Azure AD Connect Health tillhandahåller åtkomst till användare och grupper än globala administratörer. RBAC tilldelar roller till de avsedda användarna och grupper och tillhandahåller en mekanism för att begränsa de globala administratörerna i din katalog.

### <a name="roles"></a>Roller
Azure AD Connect Health stöder följande inbyggda roller:

| Roll | Behörigheter |
| --- | --- |
| Ägare |Ägare kan *hantera åtkomst* (till exempel tilldela en roll till en användare eller grupp), *se all information* (till exempel visa aviseringar) från portalen och *ändra inställningar* (för exempel, e-postmeddelanden) i Azure AD Connect Health. <br>Globala administratörer för Azure AD tilldelas rollen som standard, och detta kan inte ändras. |
| Deltagare |Deltagare kan *se all information* (till exempel visa aviseringar) från portalen och *ändra inställningar* (till exempel e-postmeddelanden) i Azure AD Connect Health. |
| Läsare |Läsare kan *se all information* (till exempel visa aviseringar) från i Azure AD Connect Health-portalen. |

Andra roller (till exempel Administratörer för användaren eller DevTest Labs användare) har ingen inverkan på åtkomst i Azure AD Connect Health, även om rollerna som är tillgängliga i portalen upplevelsen.

### <a name="access-scope"></a>Åtkomstscope
Azure AD Connect Health har stöd för hantering av åtkomst på två nivåer:

* **Alla instanser av tjänsten**: det här är den rekommenderade sökvägen i de flesta fall. Den styr åtkomst för alla tjänstinstanser (till exempel en AD FS-servergrupp) över alla rolltyper av som övervakas av Azure AD Connect Health.
* **Tjänstinstansen**: I vissa fall kan du behöva särskilja åtkomst baserat på rolltyper eller av en instans av tjänsten. I det här fallet kan du hantera åtkomst på instansnivå service.  

Tillstånd beviljas om en användare har åtkomst antingen vid angiven katalog eller service-instans nivå.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Tillåt användare eller grupper åtkomst till Azure AD Connect Health
Följande steg visar hur du tillåter åtkomst.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Steg 1: Välj lämpliga åtkomstscope
Att tillåta åtkomst till den *alla tjänstinstanser* nivå i Azure AD Connect Health, öppna huvudbladet i Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Steg 2: Lägg till användare och grupper och tilldela roller
1. Från den **konfigurera** klickar du på **användare**.<br>
   ![Skärmbild av Azure AD Connect Health resurs sidopanelen](./media/active-directory-aadconnect-health/startRBAC.png)
2. Välj **Lägg till**.
3. I den **Välj en roll** rutan, Välj en roll (till exempel **ägare**).<br>
   ![Skärmbild av Azure AD Connect Health RBAC användare fönster](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Ange namnet eller identifieraren för den aktuella användaren eller gruppen. Du kan välja en eller flera användare eller grupper på samma gång. Klicka på **Välj**.
   ![Skärmbild av Azure AD Connect Health RBAC användare fönster](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Välj **OK**.<br>
6. När rolltilldelningen är klar, visas de användare och grupper i listan.<br>
   ![Skärmbild av Azure AD Connect Health RBAC användare fönster, med nya användare markerat](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Nu visas användare och grupper har åtkomst till, enligt deras tilldelade roller.

> [!NOTE]
> * Globala administratörer ha alltid fullständig åtkomst till alla åtgärder, men globala administratörskonton finns inte i listan ovan.
> * Funktionen bjuda in användare stöds inte i Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Steg 3: Dela bladet plats med användare eller grupper
1. När du tilldelar behörigheter för en användare har åtkomst till Azure AD Connect Health genom att gå [här](https://aka.ms/aadconnecthealth).
2. I bladet kan Fäst användaren bladet eller olika delar av det, på instrumentpanelen. Klicka bara på den **fäst på instrumentpanelen** ikon.<br>
   ![Skärmbild av Azure AD Connect Health RBAC Fäst bladet, med fästikonen markerat](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)

> [!NOTE]
> En användare med rollen läsare som tilldelats är inte kan hämta Azure AD Connect Health tillägg från Azure Marketplace. Användaren kan inte utföra nödvändiga ”skapa” igen för att göra det. Användaren kan fortfarande komma åt bladet genom att gå till föregående länk. Användaren kan Fäst bladet på instrumentpanelen för senare användning.
>
>

### <a name="remove-users-or-groups"></a>Ta bort användare eller grupper
Du kan ta bort en användare eller grupp läggs till Azure AD Connect Health RBAC. Bara högerklicka på användaren eller gruppen och välj **ta bort**.<br>
![Skärmbild av Azure AD Connect Health RBAC användare fönster med ta bort markerat](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (Slutet av RBAC avsnitt)

## <a name="next-steps"></a>Nästa steg
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installation av Azure AD Connect Health Agent](active-directory-aadconnect-health-agent-install.md)
* [Använda Azure AD Connect Health med AD FS](active-directory-aadconnect-health-adfs.md)
* [Använda Azure AD Connect Health för synkronisering](active-directory-aadconnect-health-sync.md)
* [Använda Azure AD Connect Health med AD DS](active-directory-aadconnect-health-adds.md)
* [Vanliga frågor och svar om Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Versionshistorik för Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
