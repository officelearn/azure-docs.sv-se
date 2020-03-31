---
title: Hälsoåtgärder för Azure Active Directory Connect
description: I den här artikeln beskrivs ytterligare åtgärder som kan utföras när du har distribuerat Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef908429d359020282920d73480a472bfde0aa60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261520"
---
# <a name="azure-active-directory-connect-health-operations"></a>Hälsoåtgärder för Azure Active Directory Connect
I det här avsnittet beskrivs de olika åtgärder som du kan utföra med hjälp av Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Aktivera e-postmeddelanden
Du kan konfigurera azure AD Connect Health-tjänsten för att skicka e-postmeddelanden när aviseringar visar att din identitetsinfrastruktur inte är felfri. Detta inträffar när en avisering genereras och när den är löst.

![Skärmbild av e-postmeddelanden om Azure AD Connect Health-e-postavisering](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> E-postmeddelanden är aktiverade som standard.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Så här aktiverar du e-postmeddelanden om Azure AD Connect Health
1. Öppna bladet **Aviseringar** för den tjänst som du vill få ett e-postmeddelande för.
2. Klicka på Meddelandeinställningar i **åtgärdsfältet**.
3. Vid e-postmeddelandet väljer du **PÅ**.
4. Markera kryssrutan om du vill att alla globala administratörer ska få e-postmeddelanden.
5. Om du vill få e-postmeddelanden på andra e-postadresser anger du dem i rutan **Ytterligare e-postmottagare.** Om du vill ta bort en e-postadress från den här listan högerklickar du på posten och väljer **Ta bort**.
6. Om du vill slutföra ändringarna klickar du på **Spara**. Ändringarna börjar gälla först när du har sparat.

>[!NOTE] 
> När det finns problem med att bearbeta synkroniseringsbegäranden i vår serverd-tjänst skickar den här tjänsten ett e-postmeddelande med information om felet till klientens administrativa kontakt-e-postadress.When there are issues processing synchronization requests in our backend service, this service sends a notification email with the details of the error to the administrative contact email address(es) of your tenant. Vi hörde feedback från kunder att volymen av dessa meddelanden i vissa fall är oöverkomligt stor så vi ändrar vårt sätt att skicka dessa meddelanden. 
>
> Istället för att skicka ett meddelande för varje synkroniseringsfel varje gång det inträffar kommer vi att skicka ut en daglig sammanfattning av alla fel som serverda tjänsten har returnerat. Detta gör det möjligt för kunder att bearbeta dessa fel på ett mer effektivt sätt och minskar antalet dubblettfelmeddelanden.
>
> Vi planerar att genomföra denna ändring den 15 januari 2020.

## <a name="delete-a-server-or-service-instance"></a>Ta bort en server eller tjänstinstans

>[!NOTE] 
> Azure AD-premiumlicens krävs för borttagningsstegen.

I vissa fall kanske du vill ta bort en server från att övervakas. Det här behöver du veta för att ta bort en server från azure AD Connect Health-tjänsten.

När du tar bort en server bör du tänka på följande:

* Den här åtgärden slutar samla in ytterligare data från den servern. Den här servern tas bort från övervakningstjänsten. Efter den här åtgärden kan du inte visa nya aviseringar, övervaknings- eller användningsanalysdata för den här servern.
* Den här åtgärden avinstallerar inte hälsoagenten från servern. Om du inte har avinstallerat hälsoagenten innan du utför det här steget kan du se fel som är relaterade till hälsoagenten på servern.
* Den här åtgärden tar inte bort data som redan samlats in från den här servern. Dessa data tas bort i enlighet med Azure-datalagringsprincipen.
* När du har utfört den här åtgärden måste du avinstallera och installera om hälsoagenten på den här servern om du vill börja övervaka samma server igen.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Ta bort en server från azure AD Connect Health-tjänsten

>[!NOTE] 
> Azure AD-premiumlicens krävs för borttagningsstegen.

Azure AD Connect Health for Active Directory Federation Services (AD FS) och Azure AD Connect (Sync):

1. Öppna **serverbladet** från **bladet Serverlista** genom att välja det servernamn som ska tas bort.
2. Klicka på **Ta bort**från åtgärdsfältet i **serverbladet.**
![Skärmbild av borttagningsservern för Azure AD Connect Health](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Bekräfta genom att skriva servernamnet i bekräftelserutan.
4. Klicka på **Ta bort**.

Azure AD Connect-hälsotillstånd för Azure Active Directory Domain Services:

1. Öppna instrumentpanelen **Domain Controllers.**
2. Välj den domänkontrollant som ska tas bort.
3. Klicka på Ta **bort markerat i åtgärdsfältet**.
4. Bekräfta åtgärden för att ta bort servern.
5. Klicka på **Ta bort**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Ta bort en tjänstinstans från Azure AD Connect Health-tjänsten
I vissa fall kanske du vill ta bort en tjänstinstans. Det här behöver du veta för att ta bort en tjänstinstans från Azure AD Connect Health-tjänsten.

När du tar bort en tjänstinstans bör du tänka på följande:

* Den här åtgärden tar bort den aktuella tjänstinstansen från övervakningstjänsten.
* Den här åtgärden avinstallerar eller tar inte bort hälsoagenten från någon av servrarna som övervakades som en del av den här tjänstinstansen. Om du inte har avinstallerat hälsoagenten innan du utför det här steget kan du se fel som är relaterade till hälsoagenten på servrarna.
* Alla data från den här tjänstinstansen tas bort i enlighet med Azure-datalagringsprincipen.
* När du har utfört den här åtgärden avinstallerar och installerar du om hälsoagenten på alla servrar om du vill börja övervaka tjänsten. När du har utfört den här åtgärden avinstallerar, installerar du om, installerar om och registrerar hälsoagenten på den servern om du vill börja övervaka samma server igen.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Så här tar du bort en tjänstinstans från azure AD Connect Health-tjänsten
1. Öppna **bladet Tjänst** från **bladet Servicelista** genom att välja den tjänstidentifierare (servergruppsnamnet) som du vill ta bort. 
2. Klicka på **Ta bort**från åtgärdsfältet i **åtgärdsfältet.** 
![Skärmbild av tjänsten delete-tjänst för Azure AD Connect Health](./media/how-to-connect-health-operations/DeleteServer.png)
3. Bekräfta genom att skriva tjänstnamnet i bekräftelserutan (till exempel: sts.contoso.com).
4. Klicka på **Ta bort**.
   <br><br>

[//]: # (Start av avsnittet RBAC)
## <a name="manage-access-with-role-based-access-control"></a>Hantera åtkomst med rollbaserad åtkomstkontroll
[Rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/role-assignments-portal.md) för Azure AD Connect Health ger åtkomst till andra användare och grupper än globala administratörer. RBAC tilldelar roller till avsedda användare och grupper och tillhandahåller en mekanism för att begränsa de globala administratörerna i katalogen.

### <a name="roles"></a>Roller
Azure AD Connect Health stöder följande inbyggda roller:

| Roll | Behörigheter |
| --- | --- |
| Ägare |Ägare kan *hantera åtkomst* (till exempel tilldela en roll till en användare eller grupp), visa *all information* (till exempel visa aviseringar) från portalen och ändra *inställningar* (till exempel e-postmeddelanden) i Azure AD Connect Health. <br>Som standard tilldelas Azure AD-globala administratörer den här rollen och detta kan inte ändras. |
| Deltagare |Deltagare kan *visa all information* (till exempel visa aviseringar) från portalen och ändra *inställningar* (till exempel e-postmeddelanden) i Azure AD Connect Health. |
| Läsare |Läsare kan *visa all information* (till exempel visa aviseringar) från portalen i Azure AD Connect Health. |

Alla andra roller (till exempel User Access-administratörer eller DevTest Labs-användare) har ingen inverkan på åtkomsten inom Azure AD Connect Health, även om rollerna är tillgängliga i portalupplevelsen.

### <a name="access-scope"></a>Åtkomstomfång
Azure AD Connect Health stöder hantering av åtkomst på två nivåer:

* **Alla tjänstinstanser**: Detta är den rekommenderade sökvägen i de flesta fall. Den styr åtkomsten för alla tjänstinstanser (till exempel en AD FS-servergrupp) för alla rolltyper som övervakas av Azure AD Connect Health.
* **Tjänstinstans:** I vissa fall kan du behöva segregera åtkomst baserat på rolltyper eller av en tjänstinstans. I det här fallet kan du hantera åtkomst på tjänstinstansnivå.  

Behörighet beviljas om en slutanvändare har åtkomst antingen på katalog- eller tjänstinstansnivå.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Ge användare eller grupper åtkomst till Azure AD Connect Health
Följande steg visar hur du tillåter åtkomst.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Steg 1: Välj lämpligt åtkomstomfång
Om du vill tillåta en användare åtkomst på *alla tjänstinstansnivåer* i Azure AD Connect Health öppnar du huvudbladet i Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Steg 2: Lägga till användare och grupper och tilldela roller
1. Klicka på **Användare**i avsnittet **Konfigurera** .<br>
   ![Skärmbild av azure AD Connect health-resurssidofältet](./media/how-to-connect-health-operations/startRBAC.png)
2. Välj **Lägg till**.
3. I fönstret **Välj en roll** väljer du en roll (till exempel **Ägare**).<br>
   ![Skärmbild av azure AD Connect Health RBAC-användares fönster](./media/how-to-connect-health-operations/RBAC_add.png)
4. Skriv namnet eller identifieraren för den riktade användaren eller gruppen. Du kan välja en eller flera användare eller grupper samtidigt. Klicka på **Markera**.
   ![Skärmbild av azure AD Connect Health RBAC-användares fönster](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Välj **OK**.<br>
6. När rolltilldelningen är klar visas användarna och grupperna i listan.<br>
   ![Skärmbild av Azure AD Connect Health RBAC-användare, med nya användare markerade](./media/how-to-connect-health-operations/RBAC_user_list.png)

Nu har de listade användarna och grupperna åtkomst, enligt deras tilldelade roller.

> [!NOTE]
> * Globala administratörer har alltid fullständig åtkomst till alla åtgärder, men globala administratörskonton finns inte i föregående lista.
> * Funktionen Bjud in användare stöds inte i Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Steg 3: Dela bladplatsen med användare eller grupper
1. När du har tilldelat behörigheter kan en användare komma åt Azure AD Connect Health genom att gå [hit](https://aka.ms/aadconnecthealth).
2. På bladet kan användaren fästa bladet, eller olika delar av det, på instrumentpanelen. Klicka bara på ikonen **Fäst på instrumentpanelen.**<br>
   ![Skärmbild av Azure AD Connect Health RBAC-stiftsblad, med pinikonen markerad](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> En användare med rollen Läsare tilldelad kan inte hämta Azure AD Connect Health-tillägget från Azure Marketplace. Användaren kan inte utföra den nödvändiga "skapa"-åtgärden för att göra det. Användaren kan fortfarande komma till bladet genom att gå till föregående länk. För efterföljande användning kan användaren fästa bladet på instrumentpanelen.
>
>

### <a name="remove-users-or-groups"></a>Ta bort användare eller grupper
Du kan ta bort en användare eller en grupp som lagts till i Azure AD Connect Health RBAC. Högerklicka bara på användaren eller gruppen och välj **Ta bort**.<br>
![Skärmbild av azure AD Connect Health RBAC-användare, med Ta bort markerat](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Slutet av AVSNITTET RBAC)

## <a name="next-steps"></a>Nästa steg
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Installation av Azure AD Connect-hälsoagent](how-to-connect-health-agent-install.md)
* [Använda Azure AD Connect Health med AD FS](how-to-connect-health-adfs.md)
* [Använda Azure AD Connect Health för synkronisering](how-to-connect-health-sync.md)
* [Använda Azure AD Connect Health med AD DS](how-to-connect-health-adds.md)
* [Vanliga frågor och svar om Azure AD Connect Health](reference-connect-health-faq.md)
* [Versionshistorik för Azure AD Connect Health](reference-connect-health-version-history.md)
