---
title: Visa och hantera tjänstleverantörer
description: Kunder kan använda sidan tjänst leverantörer i Azure Portal för att visa information om tjänst leverantörer, Service Provider-erbjudanden och delegerade resurser.
ms.date: 01/15/2020
ms.topic: conceptual
ms.openlocfilehash: ff3c37c02c580a833008a65315009d1e42e49043
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046120"
---
# <a name="view-and-manage-service-providers"></a>Visa och hantera tjänstleverantörer

Kunder kan använda sidan **tjänst leverantörer** i [Azure Portal](https://portal.azure.com) om du vill visa information om tjänst leverantörer och service provider-erbjudanden, delegera vissa resurser via [Azure delegerad resurs hantering](../concepts/azure-delegated-resource-management.md)och shoppa för ytterligare tjänst leverantörs erbjudanden. Vi kommer att hänvisa till tjänst leverantörer och kunder här, men företag som hanterar flera klienter kan använda samma process för att konsolidera sin hanterings upplevelse.

För att få åtkomst till sidan **tjänst leverantörer** i Azure Portal kan kunden välja **alla tjänster**och sedan söka efter **tjänst leverantörer** och välja den. De kan också hitta den genom att ange "tjänste leverantörer" i sökrutan längst upp i Azure Portal.

Tänk på att sidan **tjänst leverantörer** endast visar information om de tjänst leverantörer som har åtkomst till kundens prenumerationer eller resurs grupper via Azure delegerad resurs hantering. Om en kund arbetar med ytterligare tjänst leverantörer som inte använder Azure-delegerad resurs hantering för att få åtkomst till kundens resurser visas information om dessa leverantörer här.

> [!NOTE]
> Tjänste leverantörer kan visa information om sina kunder genom att gå till **Mina kunder** i Azure Portal. Mer information finns i [Visa och hantera kunder och delegerade resurser](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Visa information om tjänst leverantör

Om du vill visa information om de tjänst leverantörer som en kund arbetar med kan han eller hon välja **leverantörs erbjudanden** på vänster sida av sidan **tjänst leverantörer** .

För varje tjänst leverantörs erbjudande kommer kunden att se tjänst leverantörens namn och erbjudandet som är associerat med det, tillsammans med namnet som kunden angav under onboarding-processen.

I kolumnen **delegeringar** ser kunden hur många prenumerationer och/eller resurs grupper som har delegerats till tjänst leverantören för det erbjudandet. Tjänste leverantören kommer att kunna komma åt och hantera dessa prenumerationer och/eller resurs grupper enligt de åtkomst nivåer som anges i erbjudandet.

## <a name="delegate-resources"></a>Delegera resurser

Innan en tjänst leverantör kan komma åt och hantera en kunds resurser måste de delegeras. Om en kund har accepterat ett erbjudande men ännu inte har delegerat några resurser, ser de en anteckning överst i avsnittet med **leverantörs erbjudanden** . På så sätt kan kunden veta att de måste vidta åtgärder innan tjänste leverantören kan komma åt någon av kundens resurser.

Så här delegerar du prenumerationer eller resurs grupper:

1. Markera kryss rutan för raden som innehåller tjänst leverantören, erbjudandet och namnet. Välj sedan **delegera resurser** överst på skärmen.
1. I avsnittet **erbjudande information** på sidan **delegera resurser** granskar du information om tjänst leverantören och erbjudandet. Om du vill granska roll tilldelningar för erbjudandet väljer du **Klicka här för att visa information om det valda erbjudandet**.
1. I avsnittet **ombud** väljer du **delegera prenumerationer** eller **delegera resurs grupper**.
1. Välj de prenumerationer och/eller resurs grupper som du vill delegera för erbjudandet och välj sedan **Lägg till**.
1. Markera kryss rutan längst ned på sidan för att bekräfta att du vill ge denna tjänst leverantör åtkomst till de resurser som du har valt och välj sedan **delegera**.

## <a name="add-or-remove-service-provider-offers"></a>Lägg till eller ta bort tjänst leverantörs erbjudanden

En kund kan lägga till ett nytt erbjudande för service provider från sidan **leverantörs erbjudanden** genom att välja **Lägg till erbjudande**. Tjänste leverantören måste ha publicerat ett erbjudande för den här kunden. Kunden kan sedan välja erbjudandet från den **privata erbjudande** skärmen och sedan välja **skapa**.

Om kunden vill ta bort ett Service Provider-erbjudande kan de välja pappers korgs ikonen på raden för det erbjudandet. När du har bekräftat borttagningen kommer tjänste leverantören inte längre att ha åtkomst till de kund resurser som tidigare har delegerats för det erbjudandet.

## <a name="update-service-provider-offers"></a>Uppdatering av tjänst leverantören erbjuder

När en kund har lagt till ett erbjudande kan en tjänste leverantör publicera en uppdaterad version av samma erbjudande på Azure Marketplace. De kan till exempel vilja lägga till en ny roll definition. Om en ny version av erbjudandet har publicerats visas en "uppdatering"-ikon i raden för det erbjudandet på sidan **Provider-erbjudanden** . Kunden kan välja den här ikonen för att se skillnaderna mellan den aktuella versionen av erbjudandet och den nya.

 ![Uppdatera erbjudande ikon](../media/update-offer.jpg)

Efter att ha granskat ändringarna kan kunden välja att uppdatera till den nya versionen. När de gör det kommer de auktoriseringar och andra inställningar som anges i den nya versionen att gälla för alla prenumerationer och/eller resurs grupper som har delegerats för erbjudandet.

## <a name="view-delegations"></a>Visa delegeringar

Delegeringarna representerar roll tilldelningarna som beviljar behörigheter till tjänste leverantören för de resurser som en kund har delegerat. Om du vill visa den här informationen väljer du **delegeringar** på vänster sida av sidan **tjänst leverantörer** .

Med filter överst på sidan kan du sortera och gruppera Delegerings informationen eller filtrera efter vissa kunder, erbjudanden eller nyckelord.

> [!NOTE]
> Kunderna ser inte roll tilldelningarna, eller några användare från tjänst leverantörens klient organisation som har beviljats dessa roller, när [Roll tilldelnings information visas för det delegerade omfånget i Azure Portal](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) eller via API: er.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Lighthouse](../overview.md).
- Lär dig hur tjänst leverantörer kan [Visa och hantera kunder](view-manage-customers.md) genom att gå till **mina kunder** i Azure Portal.