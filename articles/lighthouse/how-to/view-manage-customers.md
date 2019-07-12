---
title: Visa och hantera kunder och delegerad resurser i Azure portal
description: Som en tjänsteleverantör som använder Azure delegerat resurshantering, kan du visa alla dina delegerade kundresurser och prenumerationer genom att gå till mina kunder i Azure-portalen.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: acc90afa258fa7140cd7dfa8711dd64b554df45d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809857"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Visa och hantera kunder och delegerad resurser

Leverantörer av tjänster med hjälp av [Azure delegerad resurshantering](../concepts/azure-delegated-resource-management.md) kan använda den **mina kunder** sidan i den [Azure-portalen](https://portal.azure.com) att visa delegerade kundresurser och prenumerationer. Vi ska referera till leverantörer och kunder här, kan företag hantera flera klienter använda samma process för att konsolidera sina hanteringsmiljö.

Åtkomst till den **mina kunder** sida i Azure portal, Välj **alla tjänster**, Sök sedan efter **mina kunder** och markera den. Du kan också hitta den genom att ange ”mina kunder” i sökrutan längst upp i Azure-portalen.

Tänk på att den **mina kunder** sidan visar bara information om kunder som har delegerade prenumerationer eller resursgrupper. Om du arbetar med andra kunder (till exempel via den [programmet Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview), information om dessa kunder här visas inte om inte du publicera sina resurser för delegerat resurshantering.

> [!NOTE]
> Kunderna kan visa information om leverantörer genom att gå till **tjänsteleverantörer** i Azure-portalen. Mer information finns i [visa och hantera leverantörer](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Visa och hantera kundinformation

Om du vill visa information om kunden väljer **kunder** på vänster sida av den **mina kunder** sidan.

För varje kund visas kundens namn, kund-ID (klient-ID) och erbjudandet som är associerade med samarbetet. I den **delegeringar** kolumnen ser du antalet delegerade prenumerationer och/eller antalet delegerade resursgrupper.

Filter överst på sidan kan du sortera och gruppera din kundinformation eller filtrera efter specifika kunder, erbjudanden och nyckelord.

Du kan visa följande information från den här sidan:

- Om du vill se alla prenumerationer, erbjuder och delegeringar som är associerade med en kund, Välj kundens namn.
- Om du vill se mer information om ett erbjudande och dess delegeringar, väljer du erbjudandenamn.
- Om du vill visa mer information om acrolecess tilldelningar för delegerade prenumerationer eller resursgrupper, Välj posten i den **delegeringar** kolumn.

## <a name="view-delegations"></a>Visa delegeringar

Delegeringar visar prenumeration/resursgruppen som har delegerats tillsammans med användare och behörigheter som har åtkomst till den. Om du vill visa den här informationen, Välj **delegeringar** på vänster sida av den **mina kunder** sidan.

Filter överst på sidan kan du sortera och gruppera ditt åtkomstinformation för tilldelning eller filtrera efter specifika kunder, erbjudanden och nyckelord.

De användare och behörigheter som är associerade med varje delegering visas i den **rolltilldelningar** kolumn. Du kan välja varje post att visa en fullständig lista över användare, grupper och tjänstens huvudnamn som har beviljats åtkomst till en prenumeration eller resursgrupp. Därifrån kan välja du en viss användare, grupp eller tjänstens huvudnamn som vill ha mer information.

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Fungera i kontexten för en delegerad prenumeration

Du kan arbeta direkt i samband med en delegerad prenumeration i Azure-portalen utan att växla den katalog som du arbetar i. Gör så här:

1. Välj den **katalog + prenumeration** ikonen högt upp på Azure-portalen.
2. I den **globala** filtrera, se till att endast i rutan för den delegerade prenumerationen har valts. Du kan använda den **aktuella + delegerade kataloger** listrutan för att visa endast prenumerationer i en viss katalog. (Använd inte den **växla katalog** alternativ, eftersom som ändrar den katalog som du är inloggad.)

Om du sedan ansluter till en tjänst som stöder [mellan klientorganisationer hanteringsupplevelser](../concepts/cross-tenant-management-experience.md), tjänsten som standard den delegerade prenumerationen som du har valt. Du kan ändra detta genom att följa stegen ovan och kontrollera den **Markera alla** box (eller välja en eller flera prenumerationer i stället arbeta).

> [!NOTE]
> Om du har beviljats åtkomst till en eller flera resursgrupper i stället för åtkomst till en hela prenumerationen, kan du välja den prenumeration som resursgruppen tillhör. Du kommer sedan att arbeta i kontexten för den prenumerationen, men kommer bara att kunna komma åt de avsedda resursgrupperna.

Du kan också använda funktioner som är relaterade till delegerade prenumerationer eller resursgrupper i tjänster som har stöd för flera klienter hanteringsupplevelser genom att välja den prenumeration eller resursgrupp-gruppen från tjänsten.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [mellan klientorganisationer hanteringsupplevelser](../concepts/cross-tenant-management-experience.md).
- Lär dig hur dina kunder kan [visa och hantera leverantörer](view-manage-service-providers.md) genom att gå till **tjänsteleverantörer** i Azure-portalen.
