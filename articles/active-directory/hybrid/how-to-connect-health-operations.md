---
title: Azure Active Directory Connect Health-åtgärder
description: Den här artikeln beskriver ytterligare åtgärder som kan utföras när du har distribuerat Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: mtillman
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 7e0e2e19f2f21fa3199cbc4911fed3427cbc162c
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51280531"
---
# <a name="azure-active-directory-connect-health-operations"></a>Azure Active Directory Connect Health-åtgärder
Det här avsnittet beskriver de olika åtgärder som du kan utföra med hjälp av Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Aktivera e-postaviseringar
Du kan konfigurera Azure AD Connect Health-tjänsten för att skicka e-postmeddelanden när aviseringar tyder på att din infrastruktur för Identitetshantering inte är felfri. Detta inträffar när en avisering skapas och när den är löst.

![Skärmbild av Azure AD Connect Health e-postmeddelandeinställningarna](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> E-postmeddelanden är aktiverade som standard.
>
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Aktivera Azure AD Connect Health e-postaviseringar
1. Öppna den **aviseringar** bladet för tjänsten som du vill få e-postmeddelande.
2. I Åtgärdsfältet klickar du på **Meddelandeinställningar**.
3. På den e-notification-växeln, väljer **på**.
4. Markera kryssrutan om du vill att alla globala administratörer för att ta emot e-postmeddelanden.
5. Om du vill ta emot e-postmeddelanden på e-postadresser du anger dem i den **ytterligare e-postmottagare** box. Om du vill ta bort en e-postadress från den här listan, högerklicka på posten och välj **ta bort**.
6. Om du vill slutföra ändringarna, klickar du på **spara**. Ändringarna börjar gälla förrän du sparar.

## <a name="delete-a-server-or-service-instance"></a>Ta bort en server eller tjänstinstans

>[!NOTE] 
> Azure AD premium-licens krävs för borttagning av stegen.

I vissa fall kanske du vill ta bort en server från som övervakas. Här är vad du behöver veta för att ta bort en server från Azure AD Connect Health-tjänsten.

När du tar bort en server, är medveten om följande:

* Den här åtgärden stoppar all insamling av ytterligare data från den här servern. Den här servern tas bort från övervakningstjänsten. Efter den här åtgärden går det inte att visa nya aviseringar, övervakning och analys av användningsdata för den här servern.
* Den här åtgärden avinstalleras inte Hälsoagenten från din server. Om du inte har avinstallerat Hälsoagenten innan du utför det här steget kan uppstå fel som rör Health-agenten på servern.
* Den här åtgärden tar inte bort data som redan har samlats in från den här servern. Data tas bort i enlighet med policyn för datalagring i Azure-data.
* När du har genomfört den här åtgärden om du vill börja övervaka samma server måste igen, du avinstallera och återinstallera Hälsoagenten på den här servern.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Ta bort en server från Azure AD Connect Health-tjänsten

>[!NOTE] 
> Azure AD premium-licens krävs för borttagning av stegen.

Azure AD Connect Health för Active Directory Federation Services (AD FS) och Azure AD Connect (synkronisering):

1. Öppna den **Server** bladet från den **serverlista** bladet genom att välja servernamnet som ska tas bort.
2. På den **Server** bladet från Åtgärdsfältet klickar du på **ta bort**.
3. Bekräfta genom att skriva namnet på servern i dialogrutan.
4. Klicka på **Ta bort**.

Azure AD Connect Health för Azure Active Directory Domain Services:

1. Öppna den **domänkontrollanter** instrumentpanelen.
2. Markera domänkontrollanten som ska tas bort.
3. I Åtgärdsfältet klickar du på **ta bort valda**.
4. Bekräfta åtgärd för att ta bort servern.
5. Klicka på **Ta bort**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Ta bort en instans av tjänsten från Azure AD Connect Health-tjänsten
I vissa fall kanske du vill ta bort en tjänstinstans. Här är vad du behöver veta för att ta bort en instans av tjänsten från Azure AD Connect Health-tjänsten.

När du tar bort en tjänstinstans, Tänk på följande:

* Den här åtgärden tar bort den aktuella tjänstinstansen från övervakningstjänsten.
* Den här åtgärden varken avinstallerar eller tar bort Hälsoagenten från någon av de servrar som har övervakas som en del av den här tjänstinstansen. Om du inte har avinstallerat Hälsoagenten innan du utför det här steget kan uppstå fel som rör Health-agenten på servrar.
* Alla data från den här tjänstinstansen tas bort i enlighet med policyn för datalagring i Azure-data.
* När du har genomfört den här åtgärden om du vill börja övervaka tjänsten avinstallera och återinstallera Hälsoagenten på alla servrar. När du utför den här åtgärden om du vill börja övervaka samma server igen, avinstallera, installera och registrera Health-agenten på den servern.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Att ta bort en instans av tjänsten från Azure AD Connect Health-tjänsten
1. Öppna den **Service** bladet från den **tjänstelistan** bladet genom att välja service-identifier (servergruppens namn) som du vill ta bort.
2. På den **Server** bladet från Åtgärdsfältet klickar du på **ta bort**.
3. Bekräfta genom att skriva namnet på tjänsten i bekräftelserutan (till exempel: sts.contoso.com).
4. Klicka på **Ta bort**.
   <br><br>

[//]: # (Början av RBAC-avsnitt)
## <a name="manage-access-with-role-based-access-control"></a>Hantera åtkomst med rollbaserad åtkomstkontroll
[Rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/role-assignments-portal.md) för Azure AD Connect Health ger åtkomst till användare och grupper än globala administratörer. RBAC tilldelar roller till de avsedda användarna och grupper, och ger dig möjlighet att begränsa globala administratörer i din katalog.

### <a name="roles"></a>Roller
Azure AD Connect Health stöder följande inbyggda roller:

| Roll | Behörigheter |
| --- | --- |
| Ägare |Du har gjort *hantera åtkomst* (till exempel tilldela en roll till en användare eller grupp), *se all information* (till exempel visa aviseringar) från portalen och *ändra inställningarna för* (för exempel, e-postmeddelanden) i Azure AD Connect Health. <br>Globala administratörer för Azure AD är tilldelade den här rollen som standard och kan inte ändras. |
| Deltagare |Deltagare kan *se all information* (till exempel visa aviseringar) från portalen och *ändra inställningarna för* (till exempel e-postmeddelanden) i Azure AD Connect Health. |
| Läsare |Läsare kan *se all information* (till exempel visa aviseringar) från portalen i Azure AD Connect Health. |

Alla andra roller (till exempel Administratörer åtkomst eller DevTest Labs-användare) har ingen inverkan på åtkomst i Azure AD Connect Health, även om rollerna som är tillgängliga i en Portal.

### <a name="access-scope"></a>Åtkomstscope
Azure AD Connect Health stöder hantering av åtkomst på två nivåer:

* **Alla instanser av tjänsten**: det här är den rekommenderade sökvägen i de flesta fall. Den kontrollerar åtkomsten för alla tjänstinstanser (till exempel AD FS-servergrupp) för alla rolltyper som övervakas av Azure AD Connect Health.
* **Tjänstinstans**: I vissa fall kan du behöva särskilja åtkomst baserat på rolltyper eller genom en tjänstinstans. I det här fallet kan du hantera åtkomst på instansnivå service.  

Tillstånd beviljas om en användare har åtkomst antingen i katalogen eller tjänsten instans nivå.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Tillåt att användare och grupper åtkomst till Azure AD Connect Health
Följande steg visar hur du tillåter åtkomst.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Steg 1: Välj lämpliga åtkomstscope
Att tillåta åtkomst på den *alla tjänstinstanser* nivå i Azure AD Connect Health, öppna huvudbladet i Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Steg 2: Lägga till användare och grupper och tilldela roller
1. Från den **konfigurera** klickar du på **användare**.<br>
   ![Skärmbild av Azure AD Connect Health resource sidopanelen](./media/how-to-connect-health-operations/startRBAC.png)
2. Välj **Lägg till**.
3. I den **Välj en roll** fönstret, Välj en roll (till exempel **ägare**).<br>
   ![Skärmbild av Azure AD Connect Health RBAC användare fönster](./media/how-to-connect-health-operations/RBAC_add.png)
4. Ange namn eller ID för den aktuella användare eller grupp. Du kan välja en eller flera användare eller grupper på samma gång. Klicka på **Välj**.
   ![Skärmbild av Azure AD Connect Health RBAC användare fönster](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Välj **OK**.<br>
6. När rolltilldelningen är klar visas de användare och grupper i listan.<br>
   ![Skärmbild av Azure AD Connect Health RBAC användare fönster, med nya användare som är markerat](./media/how-to-connect-health-operations/RBAC_user_list.png)

Nu listas användare och grupper har åtkomst till, enligt deras tilldelade roller.

> [!NOTE]
> * Globala administratörer har alltid fullständig åtkomst till alla åtgärder, men globala administratörskonton finns inte i listan ovan.
> * Bjud in användare-funktionen stöds inte i Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Steg 3: Dela bladet platsen med användare eller grupper
1. När du har tilldelat behörigheter för en användare kan komma åt Azure AD Connect Health genom att gå [här](https://aka.ms/aadconnecthealth).
2. På bladet kan du fästa bladet eller olika delar av det, på instrumentpanelen. Klicka bara på den **fäst på instrumentpanelen** ikon.<br>
   ![Skärmbild av Azure AD Connect Health RBAC Fäst bladet, med fästikonen markerad](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> En användare med rollen Läsare tilldelade går inte att hämta Azure AD Connect Health-tillägget från Azure Marketplace. Användaren kan inte utföra nödvändiga ”Skapa” åtgärden om du vill göra detta. Användaren fortfarande komma åt bladet genom att gå till länken ovan. För efterföljande användning, kan du fästa bladet på instrumentpanelen.
>
>

### <a name="remove-users-or-groups"></a>Ta bort användare eller grupper
Du kan ta bort en användare eller grupp som lagts till Azure AD Connect Health RBAC. Bara högerklickar du på användaren eller gruppen och välj **ta bort**.<br>
![Skärmbild av Azure AD Connect Health RBAC användare fönstret med ta bort markerade](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Slutet av RBAC-avsnitt)

## <a name="next-steps"></a>Nästa steg
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Installation av Azure AD Connect Health Agent](how-to-connect-health-agent-install.md)
* [Använda Azure AD Connect Health med AD FS](how-to-connect-health-adfs.md)
* [Använda Azure AD Connect Health för synkronisering](how-to-connect-health-sync.md)
* [Använda Azure AD Connect Health med AD DS](how-to-connect-health-adds.md)
* [Vanliga frågor och svar om Azure AD Connect Health](reference-connect-health-faq.md)
* [Versionshistorik för Azure AD Connect Health](reference-connect-health-version-history.md)
