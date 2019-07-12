---
title: Visa och hantera leverantörer av tjänster i Azure portal
description: Kunder kan använda sidan providers i Azure-portalen för att visa information om leverantörer, tjänstleverantören erbjuder och delegerad resurser.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: a45458e7417bba058522fdc0dbc34fee04ad9af8
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809843"
---
# <a name="view-and-manage-service-providers"></a>Visa och hantera leverantörer

Kunder kan använda den **onlineleverantörer** sidan i den [Azure-portalen](https://portal.azure.com) om du vill visa information om tjänsteleverantörer och tjänstleverantören erbjuder delegera specifika resurser via [Azure delegerad resurshantering](../concepts/azure-delegated-resource-management.md), och leta efter ytterligare tjänstleverantören erbjuder. Vi ska referera till leverantörer och kunder här, kan företag hantera flera klienter använda samma process för att konsolidera sina hanteringsmiljö.

Åtkomst till den **onlineleverantörer** i Azure-portalen, kunden välja **alla tjänster**, Sök sedan efter **tjänsteleverantörer** och markera den. De kan även hitta den genom att ange ”tjänstleverantörer” i sökrutan längst upp i Azure-portalen.

Tänk på att den **tjänsteleverantörer** sidan visar bara information om de leverantörer som har åtkomst till kundens prenumerationer eller resursgrupper via Azure delegerade resource Manager. Om en kund fungerar med ytterligare leverantörer som inte använder Azure delegerade resource Manager för att komma åt kundens resurser, visas inte information om dessa leverantörer här.

> [!NOTE]
> Tjänstleverantörer kan visa information om sina kunder genom att gå till **mina kunder** i Azure-portalen. Mer information finns i [visa och hantera kunder och delegerad resurser](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Visa information om providern av tjänsten

Om du vill visa information om tjänst-leverantörer som arbetar med en kund, kan de välja **leverantör erbjuder** på vänster sida av den **tjänsteleverantörer** sidan.

För varje service provider-erbjudande visas kunden tjänstleverantörens namn och erbjudandet som är associerade med den, tillsammans med det namn som kunden har angett under onboarding-processen.

I den **delegeringar** kolumnen kunden ser hur många prenumerationer och/eller resursgrupper har delegerats till tjänstleverantör för erbjudandet. Tjänstleverantören kommer att kunna komma åt och hantera dessa prenumerationer och/eller resursgrupper efter åtkomstnivåerna som angetts i erbjudandet.

## <a name="delegate-resources"></a>Delegera resurser

Innan en tjänstleverantör kan komma åt och hantera en kunds resurser, som de behöver delegeras. Om en kund har godkänt ett erbjudande, men har inte har delegerat några resurser, visas en anteckning överst i den **leverantör erbjuder** avsnittet. På så sätt kan kunden vet att de behöver vidta åtgärder innan tjänstleverantören kan komma åt någon av kundens resurser.

Att delegera prenumerationer eller resursgrupper:

1. Markera kryssrutan för den rad som innehåller den tjänstleverantör, erbjudande och namn. Välj sedan **delegera resurser** överst på skärmen.
1. I den **Erbjudandeinformationen** delen av den **delegera resurser** granskar information om tjänsteleverantören och erbjuder. Om du vill granska rolltilldelningar för erbjudandet, Välj **Klicka här om du vill se information om det valda erbjudandet**.
1. I den **ombud** väljer **delegera prenumerationer** eller **delegera resursgrupper**.
1. Välj prenumerationer och/eller resursgrupper som du vill delegera för det här erbjudandet, välj sedan **Lägg till**.
1. Markera kryssrutan längst ned på sidan för att bekräfta att du vill bevilja åtkomst till de resurser som du har valt, och välj sedan service provider **ombud**.

## <a name="add-or-remove-service-provider-offers"></a>Lägg till eller ta bort tjänstleverantören erbjuder

En kund kan lägga till ett nytt service provider-erbjudande från den **leverantör erbjuder** genom att välja **Lägg till erbjudandet**. Tjänstleverantören måste har publicerat ett erbjudande för kunden. Kunden kan välja erbjudandet från den **privata erbjudanden** skärmen och välj sedan **skapa**.

Om kunden vill ta bort ett tjänsterbjudande providern, kan de välja på papperskorgsikonen i raden för erbjudandet. När du har bekräftat borttagningen, har den tjänstleverantören inte längre åtkomst till kundresurser som tidigare har delegerats för erbjudandet.

## <a name="view-delegations"></a>Visa delegeringar

Delegeringar representerar rolltilldelningar som beviljar behörigheter till tjänstleverantören för de resurser som en kund har delegerats. Om du vill visa den här informationen, Välj **delegeringar** på vänster sida av den **tjänsteleverantörer** sidan.

Filter överst på sidan kan du sortera och gruppera dina delegering info eller filtrera efter specifika kunder, erbjudanden och nyckelord.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure fyr](../overview.md).
- Lär dig hur tjänstleverantörer kan [visa och hantera kunder](view-manage-customers.md) genom att gå till **mina kunder** i Azure-portalen.