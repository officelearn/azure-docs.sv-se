---
title: Felsöka Azure enterprise kostnaden vyer | Microsoft Docs
description: Lär dig hur du åtgärdar problem med organisationens kostnaden vyer i Azure-portalen.
author: rthorn17
manager: adpick
editor: ''
ms.assetid: ''
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2017
ms.author: cwatson
ms.custom: seodec18
ms.openlocfilehash: 434f717d9590eff39df161a74931477bdf71bb67
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309574"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Felsöka enterprise kostnad vyer

Det finns flera inställningar som kan orsaka användare inom registreringen kan inte se kostnaderna i enterprise-registreringar.  De här inställningarna hanteras av registreringsadministratören. Eller, om registrering inte är köpt direkt via Microsoft inställningarna hanteras av partnern.  Den här artikeln hjälper dig att förstå vilka inställningar som används och hur de påverkar registreringen. De här inställningarna är oberoende av rollerna Azure rollbaserad åtkomstkontroll (RBAC).

## <a name="enabling-access-to-costs"></a>Aktivera åtkomst till kostnader

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

Om du får ett felmeddelande ”den här tillgången är inte tillgänglig” när försöker få åtkomst till en prenumerations- eller -grupp och sedan du har inte rätt roll att visa det här objektet.  

![Skärmbild som visar ”tillgången är inte tillgänglig” visas.](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Fråga Azure prenumerations- eller grupp administratören för åtkomst. Mer information finns i [hantera åtkomst med RBAC och Azure portal](../role-based-access-control/role-assignments-portal.md).
