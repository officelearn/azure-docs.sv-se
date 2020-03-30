---
title: Visa och hantera tjänstleverantörer
description: Kunder kan använda sidan Tjänstleverantörer i Azure-portalen för att visa information om tjänsteleverantörer, tjänsteleverantörer erbjudanden och delegerade resurser.
ms.date: 02/25/2020
ms.topic: conceptual
ms.openlocfilehash: 94103c293ffa7ccfb9d7da0a237dc1b1c6540b72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270633"
---
# <a name="view-and-manage-service-providers"></a>Visa och hantera tjänstleverantörer

Kunder kan använda sidan **Tjänstleverantörer** i [Azure-portalen](https://portal.azure.com) för att visa information om tjänsteleverantörer och tjänsteleverantörer, delegera specifika resurser via [Azure-delegerad resurshantering](../concepts/azure-delegated-resource-management.md)och handla för nya tjänsteleverantörer erbjudanden. Även om vi hänvisar till tjänsteleverantörer och kunder här, kan företag som hanterar flera klienter använda samma process för att konsolidera sin hanteringsupplevelse.

För att komma åt sidan **Tjänstleverantörer** i Azure-portalen kan kunden välja **Alla tjänster**och sedan söka efter **tjänstleverantörer** och välja den. De kan också hitta den genom att ange "Tjänsteleverantörer" i sökrutan högst upp i Azure-portalen.

Tänk på att sidan **Tjänstleverantörer** endast visar information om de tjänsteleverantörer som har åtkomst till kundens prenumerationer eller resursgrupper via Azure-delegerad resurshantering. Om en kund arbetar med ytterligare tjänsteleverantörer som inte använder Azure-delegerad resurshantering för att komma åt kundens resurser visas inte information om dessa tjänsteleverantörer här.

> [!NOTE]
> Tjänsteleverantörer kan visa information om sina kunder genom att navigera till **Mina kunder** i Azure-portalen. Mer information finns i [Visa och hantera kunder och delegerade resurser](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Visa information om tjänsteleverantören

Om du vill visa information om tjänsteleverantörer kan kunden välja **Provider-erbjudanden** till vänster på sidan **Tjänsteleverantörer.**

För varje tjänsteleverantörserbjudande ser kunden tjänsteleverantörens namn och erbjudandet som är kopplat till det, tillsammans med det namn som kunden angav under introduktionsprocessen.

I kolumnen **Delegering** ser kunden hur många prenumerationer och/eller resursgrupper som har delegerats till tjänsteleverantören för det erbjudandet. Tjänsteleverantören kommer att kunna komma åt och hantera dessa prenumerationer och/eller resursgrupper enligt de åtkomstnivåer som anges i erbjudandet.

## <a name="delegate-resources"></a>Delegera resurser

Innan en tjänsteleverantör kan komma åt och hantera en kunds resurser måste de delegeras. Om en kund har accepterat ett erbjudande men ännu inte har delegerat några resurser visas en anteckning högst upp i avsnittet **Provider-erbjudanden.** Detta gör att kunden vet att de måste vidta åtgärder innan tjänsteleverantören kan komma åt någon av kundens resurser.

Så här delegerar du prenumerationer eller resursgrupper:

1. Markera rutan för raden som innehåller tjänsteleverantören, erbjudandet och namnet. Välj sedan **Delegera resurser** högst upp på skärmen.
1. I avsnittet **Erbjudandeinformation** på sidan **Delegera resurser** läser du informationen om tjänsteleverantören och erbjudandet. Om du vill granska rolltilldelningar för erbjudandet väljer du **Klicka här för att se information om det valda erbjudandet**.
1. I avsnittet **Delegera** väljer du **Delegera prenumerationer** eller **Delegera resursgrupper**.
1. Välj de prenumerationer och/eller resursgrupper som du vill delegera för det här erbjudandet och välj sedan **Lägg till**.
1. Markera kryssrutan längst ned på sidan för att bekräfta att du vill ge tjänsteleverantören åtkomst till de resurser som du har markerat och välj sedan **Delegera**.

## <a name="add-or-remove-service-provider-offers"></a>Lägga till eller ta bort tjänsteleverantörserbjudanden

En kund kan lägga till ett nytt tjänsteleverantörserbjudande från sidan **Provider-erbjudanden** genom att välja **Lägg till erbjudande**. Tjänsteleverantören måste ha publicerat ett erbjudande för den här kunden. Kunden kan sedan välja erbjudandet på skärmen **Privata erbjudanden** och sedan välja **Skapa**.

Om kunden vill ta bort ett tjänsteleverantörserbjudande kan de välja papperskorgen i raden för det erbjudandet. När du har bekräftat borttagningen har tjänsteleverantören inte längre åtkomst till de kundresurser som tidigare delegerats för erbjudandet.

## <a name="update-service-provider-offers"></a>Uppdatera tjänsteleverantörens erbjudanden

När en kund har lagt till ett erbjudande kan en tjänsteleverantör publicera en uppdaterad version av samma erbjudande till Azure Marketplace. De kanske till exempel vill lägga till en ny rolldefinition. Om en ny version av erbjudandet har publicerats visar **sidan Provider-erbjudanden** en "uppdateringsikon" på raden för det erbjudandet. Kunden kan välja den här ikonen om du vill se skillnaderna mellan den aktuella versionen av erbjudandet och den nya.

 ![Ikonen Uppdatera erbjudande](../media/update-offer.jpg)

När du har granskat ändringarna kan kunden välja att uppdatera till den nya versionen. När de gör det gäller de auktoriseringar och andra inställningar som anges i den nya versionen för alla prenumerationer och/eller resursgrupper som har delegerats för det erbjudandet.

## <a name="view-delegations"></a>Visa delegeringar

Delegeringar representerar de rolltilldelningar som ger behörighet till tjänsteleverantören för de resurser som en kund har delegerat. Om du vill visa den här informationen väljer du **Delegationer** till vänster på sidan **Tjänstleverantörer.**

Med filter högst upp på sidan kan du sortera och gruppera delegeringsinformationen. Du kan också filtrera efter specifika kunder, erbjudanden eller nyckelord.

> [!NOTE]
> Kunderna ser inte dessa rolltilldelningar, eller användare från tjänstleverantörens klientorganisation som har tilldelats dessa roller, när [de visar rolltilldelningsinformation för det delegerade scopet i Azure-portalen](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) eller via API:er.

## <a name="audit-delegations-in-your-environment"></a>Revisionsdelegationer i din miljö

Kunder kanske vill få insyn i prenumerationer och/eller resursgrupper som har delegerats till tjänstleverantörer för [Azure-delegerad resurshantering](../concepts/azure-delegated-resource-management.md). Detta är särskilt användbart för de kunder som har ett stort antal prenumerationer eller som har många användare som utför hanteringsuppgifter.

Vi tillhandahåller en [inbyggd principdefinition för Azure Policy](../../governance/policy/samples/built-in-policies.md#lighthouse) för granskning av scope till en hanterande klientorganisation. Du kan tilldela den här principen till en hanteringsgrupp som innehåller alla prenumerationer som du vill granska. När du söker efter efterlevnad av den här principen visas alla delegerade prenumerationer och/eller resursgrupper (inom den hanteringsgrupp som principen är tilldelad till) i ett icke-kompatibelt tillstånd. Du kan sedan granska resultaten och bekräfta att det inte finns några oväntade delegeringar.

Mer information om hur du tilldelar en princip och visa efterlevnadstillståndsresultat finns i [Snabbstart: Skapa en principtilldelning](../../governance/policy/assign-policy-portal.md).

## <a name="next-steps"></a>Nästa steg
 
- Läs mer om [Azure Lighthouse](../overview.md).
- Läs om hur tjänsteleverantörer kan [visa och hantera kunder](view-manage-customers.md) genom att gå till Mina **kunder** i Azure-portalen.