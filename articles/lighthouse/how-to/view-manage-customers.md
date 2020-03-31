---
title: Visa och hantera kunder och delegerade resurser
description: Som en tjänsteleverantör som använder Azure-delegerad resurshantering kan du visa alla dina delegerade kundresurser och prenumerationer genom att gå till Mina kunder i Azure-portalen.
ms.date: 01/22/2020
ms.topic: conceptual
ms.openlocfilehash: 0d4b3187066754e8a549f029623762df539b30b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543434"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Visa och hantera kunder och delegerade resurser

Tjänstleverantörer som använder [Azure-delegerad resurshantering](../concepts/azure-delegated-resource-management.md) kan använda sidan **Mina kunder** i [Azure-portalen](https://portal.azure.com) för att visa delegerade kundresurser och prenumerationer. Även om vi hänvisar till tjänsteleverantörer och kunder här, kan företag som hanterar flera klienter använda samma process för att konsolidera sin hanteringsupplevelse.

Om du vill komma åt sidan **Mina kunder** i Azure-portalen väljer du **Alla tjänster**och söker sedan efter Mina **kunder** och väljer den. Du kan också hitta den genom att ange "Mina kunder" i sökrutan högst upp i Azure-portalen.

Tänk på att avsnittet **Bästa Kunder** på sidan **Mina kunder** bara visar information om kunder som har delegerat prenumerationer eller resursgrupper. Om du arbetar med andra kunder (till exempel via [Cloud Solution Provider-programmet](https://docs.microsoft.com/partner-center/csp-overview)visas inte information om dessa kunder i avsnittet **Kunder** om du inte har ockitlat deras resurser för Azure-delegerad resurshantering.

Lägre på sidan visas ett separat avsnitt som heter **Cloud Solution Provider (Preview)** faktureringsinformation och resurser för csp-kunder som har [undertecknat Microsofts kundavtal (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) och som omfattas [av Azure-planen](https://docs.microsoft.com/partner-center/azure-plan-get-started). Mer information finns i [Komma igång med ditt faktureringskonto för Microsoft Partner Agreement](../../billing/mpa-overview.md). Observera att sådana CSP-kunder visas i det här avsnittet oavsett om du också har onboarded dem för Azure delegerad resurshantering. På samma sätt behöver en CSP-kund inte visas i avsnittet **Cloud Solution Provider (Preview)** i **Mina kunder** för att du ska kunna gå ombord på dem för Azure-delegerad resurshantering.

> [!NOTE]
> Dina kunder kan visa information om tjänsteleverantörer genom att navigera till **tjänstleverantörer** i Azure-portalen. Mer information finns i [Visa och hantera tjänsteleverantörer](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Visa och hantera kundinformation

Om du vill visa kundinformation väljer du **Kunder** till vänster på sidan **Mina kunder.**

För varje kund ser du kundens namn, kund-ID (klient-ID) och erbjudandet som är kopplat till engagemanget. I kolumnen **Delegering** visas antalet delegerade prenumerationer och/eller antalet delegerade resursgrupper.

> [!IMPORTANT]
> För att kunna se en delegering måste användarna ha tilldelats [rollen Läsare](../../role-based-access-control/built-in-roles.md#reader) (eller en annan inbyggd roll som inkluderar läsaråtkomst) i introduktionsprocessen.

Med filter högst upp på sidan kan du sortera och gruppera din kundinformation eller filtrera efter specifika kunder, erbjudanden eller sökord.

Du kan visa följande information från den här sidan:

- Om du vill visa alla prenumerationer, erbjudanden och delegeringar som är associerade med en kund väljer du kundens namn.
- Om du vill se mer information om ett erbjudande och dess delegationer väljer du erbjudandenamnet.
- Om du vill visa mer information om rolltilldelningar för delegerade prenumerationer eller resursgrupper väljer du posten i kolumnen **Delegering.**

## <a name="view-and-manage-delegations"></a>Visa och hantera delegeringar

Delegeringar visar den prenumerations-/resursgrupp som har delegerats tillsammans med de användare och behörigheter som har åtkomst till den. Om du vill visa den här informationen väljer du **Delegationer** till vänster på sidan **Mina kunder.**

Med filter högst upp på sidan kan du sortera och gruppera din åtkomsttilldelningsinformation eller filtrera efter specifika kunder, erbjudanden eller nyckelord.

### <a name="view-role-assignments"></a>Visa rolltilldelningar

De användare och behörigheter som är associerade med varje delegering visas i kolumnen **Rolltilldelningar.** Du kan välja varje post om du vill visa den fullständiga listan över användare, grupper och tjänsthuvudnamn som har beviljats åtkomst till prenumerationen eller resursgruppen. Därifrån kan du välja en viss användare, grupp eller tjänstens huvudnamn för att få mer information.

### <a name="remove-delegations"></a>Ta bort delegeringar

Om du inkluderade användare med [rollen Ta bort behörighet för hanterade tjänster](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) när du registrerar en kund för Azure-delegerad resurshantering, kan dessa användare ta bort en delegering genom att välja papperskorgen som visas på raden för den delegeringen. När de gör det kan inga användare i tjänsteleverantörens klient att komma åt de resurser som tidigare delegerats.


## <a name="work-in-the-context-of-a-delegated-subscription"></a>Arbeta i samband med en delegerad prenumeration

Du kan arbeta direkt i samband med en delegerad prenumeration i Azure-portalen, utan att byta katalog som du arbetar i. Gör så här:

1. Välj ikonen **Katalog + Prenumeration** högst upp på Azure-portalen.
2. Kontrollera att endast rutan för den delegerade prenumerationen är markerad i filtret **Global prenumeration.** Du kan använda listrutan **Aktuella + delegerade kataloger** för att bara visa prenumerationer i en viss katalog. (Använd inte alternativet **Växla katalog** eftersom det ändrar katalogen som du är inloggad på.)

Om du sedan öppnar en tjänst som stöder [hantering av korsfästfunktioner](../concepts/cross-tenant-management-experience.md)kommer tjänsten som standard att kontexten för den delegerade prenumeration som du har valt. Du kan ändra detta genom att följa stegen ovan och markera rutan **Markera alla** (eller välja en eller flera prenumerationer som ska arbeta i stället).

> [!NOTE]
> Om du har beviljats åtkomst till en eller flera resursgrupper i stället för åtkomst till en hel prenumeration kan du välja den prenumeration som resursgruppen tillhör. Du kommer då att arbeta i samband med den prenumerationen, men kan bara komma åt de angivna resursgrupperna.

Du kan också komma åt funktioner som är relaterade till delegerade prenumerationer eller resursgrupper inifrån tjänster som stöder hanteringsupplevelser mellan klienter genom att välja prenumerationen eller resursgruppen inifrån den tjänsten.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanteringsupplevelser mellan klienter.](../concepts/cross-tenant-management-experience.md)
- Läs om hur dina kunder kan [visa och hantera tjänsteleverantörer](view-manage-service-providers.md) genom att gå till **tjänstleverantörer** i Azure-portalen.
