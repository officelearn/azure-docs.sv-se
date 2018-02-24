---
title: "Felsöka enterprise kostnad vyer - Azure | Microsoft Docs"
description: "Lär dig hur du åtgärdar problem med organisationens kostnader vyer i Azure-portalen."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/22/2017
ms.author: rithorn
ms.openlocfilehash: 54c7610f1a0d3de2503ef471ca9adc0db423f530
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
# <a name="troubleshoot-enterprise-cost-views"></a>Felsöka enterprise kostnad vyer 

Det finns flera inställningar som kan orsaka användare inom registreringen ska inte kunna visa kostnader i enterprise-registreringar.  De här inställningarna hanteras av registrering administratör eller av partner om registreringen inte har köpt direkt med Microsoft.  Den här artikeln hjälper dig att förstå vilka inställningar som används och hur de påverkar registreringen. De här inställningarna är oberoende av den [Azure RBAC-roller](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure). 


## <a name="enabling-access-to-costs"></a>Aktivera åtkomst till kostnader

Du ser ett meddelande obehörig, eller *”kostnaden vyer inaktiverade i din registrering”.* När du söker efter kostnadsinformation? ![obehörig](media/billing-enterprise-mgmt-groups/unauthorized.png)

Det kan bero på något av följande skäl:

1. Du har köpt Azure via en enterprise-partner och partnern har inte släppts priser ännu. Om du vill frigöra prissättning, kontakta din partner om du vill uppdatera inställningen i den [Enterprise portal](https://ea.azure.com).
2. Alternativt, om du är en kund med EA direkt, det finns ett par möjligheter:
    * Du äger konto och din registrering administratör har inaktiverat ”AO visa avgifterna” inställningen.  
    * Du är administratör för avdelning och administratören registrering har inaktiverat ”DA visa avgifterna” inställningen.
    * Administratören registrering för att få åtkomst. Registrering-administratören kan besöka den [Enterprise portal](https://ea.azure.com/manage/enrollment) och uppdatera inställningen som visas här:

![Inställningar för företag](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)


## <a name="asset-is-unavailable"></a>Tillgångsinformation är inte tillgängligt? 
Om du får ett felmeddelande ”den här tillgången är inte tillgänglig” när försöker få åtkomst till en prenumeration eller hantering av grupp och du har inte rätt roll för att visa det här objektet.  

![asset-not-found](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Kontakta administrera av prenumerationen eller hantering av grupper som ska få åtkomst.  
* Prenumerationer, referera [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) dokument för att få hjälp med rollen krävs.
