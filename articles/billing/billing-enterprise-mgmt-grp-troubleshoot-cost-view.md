---
title: Felsöka Azure enterprise kostnaden vyer | Microsoft Docs
description: Lär dig hur du åtgärdar problem med organisationens kostnaden vyer i Azure-portalen.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/22/2017
ms.author: cwatson
ms.openlocfilehash: 853307f24574e6cea5841bcace815fea2bbcf2c8
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47390672"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Felsöka enterprise kostnad vyer 

Det finns flera inställningar som kan orsaka användare i registreringen inte att kunna visa kostnader i enterprise-registreringar.  De här inställningarna hanteras av registreringsadministratören för eller av partnern om registreringen inte har köpt direkt med Microsoft.  Den här artikeln hjälper dig att förstå vilka inställningar som används och hur de påverkar registreringen. De här inställningarna är oberoende av den [Azure RBAC-roller](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). 


## <a name="enabling-access-to-costs"></a>Aktivera åtkomst till kostnader

Är du ser ett meddelande obehörig, eller *”kostnaden vyer är inaktiverade i din registrering”.* När du letar efter kostnadsinformation? ![obehörig](media/billing-enterprise-mgmt-groups/unauthorized.png)

Det kan bero på något av följande orsaker:

1. Du har köpt Azure via ett enterprise-partner och partnern inte har släppt priser ännu. Om du vill frigöra priser, kontakta din partner om du vill uppdatera inställningen i den [Enterprise portal](https://ea.azure.com).
2. Du kan också om du är en direkta EA-kund, finns det några av möjligheterna:
    * Du är en kontoinnehavare och din registrering-administratör har inaktiverat de ”AO visa debiteringar” ställa in.  
    * Du är administratör avdelning och din registrering-administratör har inaktiverat de ”DA visa debiteringar” ställa in.
    * Kontakta din administratör för registreringen för att få åtkomst. Registrering-administratören kan besöka den [Enterprise portal](https://ea.azure.com/manage/enrollment) och uppdatera inställningen som visas här:

![Enterprise portalinställningar](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)


## <a name="asset-is-unavailable"></a>Tillgången är inte tillgänglig? 
Om du får ett felmeddelande ”den här tillgången är inte tillgänglig” när försöker få åtkomst till en prenumerations- eller -grupp och sedan du har inte rätt roll att visa det här objektet.  

![tillgången kan inte hittas](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Kontakta administrera prenumerations- eller grupper som ska få åtkomst.  
* För prenumerationer, referera till [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) dokumentet om du behöver hjälp där rollen krävs.
