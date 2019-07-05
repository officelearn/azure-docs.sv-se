---
title: Felsöka Azure enterprise kostnaden vyer
description: Lär dig hur du åtgärdar problem med organisationens kostnaden vyer i Azure-portalen.
author: bandersmsft
manager: amberb
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 83f7f424b265582a3830c02973cbbb9962ddfbfb
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491263"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Felsöka enterprise kostnad vyer

Det finns flera inställningar som kan orsaka användare inom registreringen kan inte se kostnaderna i enterprise-registreringar.  De här inställningarna hanteras av registreringsadministratören. Eller, om registrering inte är köpt direkt via Microsoft inställningarna hanteras av partnern.  Den här artikeln hjälper dig att förstå vilka inställningar som används och hur de påverkar registreringen. De här inställningarna är oberoende av rollerna Azure rollbaserad åtkomstkontroll (RBAC).

## <a name="enable-access-to-costs"></a>Aktivera åtkomst till kostnader

Är du ser ett meddelande obehörig, eller *”kostnaden vyer är inaktiverade i din registrering”.* När du letar efter kostnadsinformation?
![Skärmbild som visar ”obehörig” i aktuell kostnad fältet för prenumerationen.](media/billing-enterprise-mgmt-groups/unauthorized.png)

Det kan bero på något av följande orsaker:

1. Du har köpt Azure via ett enterprise-partner och partnern släppa inte priser ännu. Kontakta din partner för att uppdatera priser ställa in inom den [Enterprise portal](https://ea.azure.com).
2. Om du är en direkta EA-kund, finns det några av möjligheterna:
    * Är du Kontoägare och administratören registrering har inaktiverats i **AO visa debiteringar** inställningen.  
    * Du är en avdelning-administratör och administratören registrering har inaktiverats i **DA visa debiteringar** inställningen.
    * Kontakta din administratör för registreringen för att få åtkomst. Registrering-administratörer kan uppdatera inställningarna i den [Enterprise portal](https://ea.azure.com/manage/enrollment).

      ![Skärmbild som visar Enterprise Portal inställningarna för Visa debiteringar.](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>Tillgången är inte tillgänglig

Om du får ett felmeddelande om **den här tillgången är inte tillgänglig** vid åtkomst till en prenumeration eller hanteringsgrupp, sedan du inte har rätt roll att visa det här objektet.  

![Skärmbild som visar ”tillgången är inte tillgänglig” visas.](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Fråga Azure prenumerations- eller grupp administratören för åtkomst. Mer information finns i [hantera åtkomst med RBAC och Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg
- Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
