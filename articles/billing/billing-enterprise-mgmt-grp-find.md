---
title: Hitta en Azure-prenumeration eller management-grupp - Azure | Microsoft Docs
description: "Hur du navigera mellan flera kataloger för att se dina hanteringsgrupper och prenumerationer"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.openlocfilehash: df4d85d0556a62311c112f24431b54d042333c7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="find-an-azure-subscription-or-management-group"></a>Hitta en Azure-prenumeration eller management-grupp

Om du har svårt att hitta en prenumeration eller hanteringsgruppen i Azure, kan du titta i fel katalog. Den här situationen kan inträffa när ditt konto finns i flera aktiva Azure-kataloger. Varje [active directory är oberoende](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-directory-independence) och åtkomst ärvs inte i kataloger.      

![Växeln Directory menyn](media/billing-enterprise-mgmt-groups/mgempty.png)



## <a name="switch-directories"></a>Växla kataloger 
Du kan enkelt växla kataloger i Azure-portalen.
1.  Logga in på [Azure-portalen](https://portal.azure.com).
2.  Välj ditt namn i övre höger på skärmen. 
3.  Längst ned på menyn visar en lista över alla kataloger som du har åtkomst till.
4.  Välj en katalog att komma åt. 

![Växeln Directory menyn](media/billing-enterprise-mgmt-groups/switch-directory.png)

## <a name="asset-is-unavailable"></a>Tillgångsinformation är inte tillgängligt? 
Om du får ett felmeddelande ”den här tillgången är inte tillgänglig” när försöker få åtkomst till en prenumeration eller hantering av grupp och du har inte rätt roll för att visa det här objektet.  

![tillgångsinformation inte hittades](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Kontakta administrera av prenumerationen eller hantering av grupper som ska få åtkomst.  
* Prenumerationer, referera [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure) dokument för att få hjälp med rollen krävs.
* För hanteringsgrupper, RBAC åtkomst är inte tillgänglig och kommer snart. Kontakta din enterprise portal administrera för åtkomst som tilldelats.   

## <a name="improve-your-experience-with-management-groups-and-subscriptions-in-the-same-directory"></a>Förbättra din upplevelse med hanteringsgrupper och prenumerationer i samma katalog 
Du kan överföra dina prenumerationer eller av hanteringsgrupper i katalogen du väljer så att allt finns på samma plats.  Konsolidera prenumerationer och hanteringsgrupper i samma katalog hjälper till att minska behovet av att växla kataloger och Tillåt principer för att ärvas.  


### <a name="transfer-your-subscriptions"></a>Överföra dina prenumerationer 
Du kan flytta en prenumeration till den katalog som är kopplade till dina hanteringsgrupper. Flyttningen kan uppnås genom att överföra din prenumeration till ett konto i målkatalogen registrering av administratören. Mer information, logga in på den [Enterprise portal](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription).


 






