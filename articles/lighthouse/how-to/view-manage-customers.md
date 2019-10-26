---
title: Visa och hantera kunder och delegerade resurser
description: Som en tjänst leverantör som använder Azure-delegerad resurs hantering kan du Visa alla dina delegerade kund resurser och prenumerationer genom att gå till mina kunder i Azure Portal.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 10/23/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: f6c98a67394db8028901f3f1f8e799af4c7a8c5f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898901"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Visa och hantera kunder och delegerade resurser

Tjänst leverantörer som använder [Azure-delegerad resurs hantering](../concepts/azure-delegated-resource-management.md) kan använda sidan **mina kunder** i [Azure Portal](https://portal.azure.com) för att Visa delegerade kund resurser och prenumerationer. Vi kommer att hänvisa till tjänst leverantörer och kunder här, men företag som hanterar flera klienter kan använda samma process för att konsolidera sin hanterings upplevelse.

Du kommer åt sidan **Mina kunder** på Azure Portal genom att välja **alla tjänster**och sedan söka efter **Mina kunder** och välja den. Du kan också hitta den genom att ange "mina kunder" i sökrutan längst upp i Azure Portal.

Tänk på att den översta delen av sidan **Mina kunder** endast visar information om kunder som har delegerade prenumerationer eller resurs grupper. Om du arbetar med andra kunder (t. ex. via [Cloud Solution Provider-programmet](https://docs.microsoft.com/partner-center/csp-overview)kan du inte se information om dessa kunder här om du inte har publicerat resurserna för delegerad resurs hantering). I avsnittet **Cloud Solution Provider (för hands version)** på sidan visas fakturerings information och resurser för dina CSP-kunder som har [undertecknat Microsofts kund avtal (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) och följer Azure-prenumerationen, oavsett om de har registrerats eller inte för Azure-delegerad resurs hantering. Mer information finns i [Kom igång med ditt fakturerings konto för Microsoft partner avtal](https://docs.microsoft.com/azure/billing/mpa-overview).

> [!NOTE]
> Dina kunder kan visa information om tjänst leverantörer genom att gå till **tjänst leverantörer** i Azure Portal. Mer information finns i [Visa och hantera tjänst leverantörer](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Visa och hantera kund information

Om du vill visa kund information väljer du **kunder** till vänster på sidan **Mina kunder** .

För varje kund ser du kundens namn, kund-ID (klient-ID) och erbjudandet som är associerat med ärendet. I kolumnen **delegeringar** ser du antalet delegerade prenumerationer och/eller antalet delegerade resurs grupper.

Med filter överst på sidan kan du sortera och gruppera kund information eller filtrera efter specifika kunder, erbjudanden eller nyckelord.

Du kan visa följande information från den här sidan:

- Om du vill se alla prenumerationer, erbjudanden och delegeringar som är associerade med en kund väljer du kundens namn.
- Om du vill se mer information om ett erbjudande och dess delegeringar väljer du namnet på erbjudandet.
- Om du vill visa mer information om roll tilldelningar för delegerade prenumerationer eller resurs grupper väljer du posten i kolumnen **delegeringar** .

## <a name="view-delegations"></a>Visa delegeringar

Delegeringarna visar den prenumeration/resurs grupp som har delegerats, tillsammans med de användare och behörigheter som har åtkomst till den. Om du vill visa den här informationen väljer du **delegeringar** på vänster sida av sidan **Mina kunder** .

Med filter överst på sidan kan du sortera och gruppera information om åtkomst tilldelning eller filtrera efter vissa kunder, erbjudanden eller nyckelord.

Användare och behörigheter som är associerade med varje delegering visas i kolumnen **roll tilldelningar** . Du kan välja varje post om du vill visa en fullständig lista över användare, grupper och tjänstens huvud namn som har beviljats åtkomst till prenumerationen eller resurs gruppen. Därifrån kan du välja en viss användare, grupp eller tjänstens huvud namn för att få mer information.

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Arbeta i kontexten för en delegerad prenumeration

Du kan arbeta direkt i kontexten för en delegerad prenumeration i Azure Portal, utan att byta katalog som du arbetar i. Gör så här:

1. Välj ikonen **katalog + prenumeration** längst upp i Azure Portal.
2. I det **globala prenumerations** filtret ser du till att endast rutan för den delegerade prenumerationen är markerad. Du kan använda List rutan **aktuella och delegerade kataloger** om du bara vill visa prenumerationer i en angiven katalog. (Använd inte alternativet **Växla katalog** eftersom det ändrar den katalog som du är inloggad på.)

Om du sedan ansluter till en tjänst som stöder [hantering av flera innehavare](../concepts/cross-tenant-management-experience.md), kommer tjänsten att standard kontexten för den delegerade prenumeration som du har valt. Du kan ändra detta genom att följa stegen ovan och **Markera kryss rutan Markera alla** (eller välja en eller flera prenumerationer som du vill arbeta i i stället).

> [!NOTE]
> Om du har beviljats åtkomst till en eller flera resurs grupper, i stället för att få åtkomst till en hel prenumeration, kan du välja den prenumeration som resurs gruppen tillhör. Sedan arbetar du i samband med den prenumerationen, men kommer bara att kunna komma åt de angivna resurs grupperna.

Du kan också få åtkomst till funktioner relaterade till delegerade prenumerationer eller resurs grupper från tjänster som stöder hantering av flera innehavare genom att välja prenumerationen eller resurs gruppen inifrån den tjänsten.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterings upplevelser mellan flera innehavare](../concepts/cross-tenant-management-experience.md).
- Lär dig hur dina kunder kan [Visa och hantera tjänst leverantörer](view-manage-service-providers.md) genom att gå till **tjänst leverantörer** i Azure Portal.
