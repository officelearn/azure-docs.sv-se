---
title: 'Felsökning: Saknade data i aktivitetsloggen för Azure Active Directory | Microsoft Docs'
description: Visar de olika tillgängliga rapporterna i Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 01/15/2018
ms.author: rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: 1568a7c508dbbb42143d8badf39833af90fcc376
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589550"
---
# <a name="i-cant-find-some-actions-that-i-performed-in-the-azure-active-directory-activity-log"></a>Det går inte att hitta vissa åtgärder som jag utförde i aktivitetsloggen för Azure Active Directory


## <a name="symptoms"></a>Symtom

Jag utförde vissa åtgärder i Azure Portal och förväntade att se granskningsloggarna för dessa åtgärder på bladet `Activity logs > Audit Logs`, men det går inte att hitta dem.

 ![Rapportering](./media/active-directory-reporting-troubleshoot-missing-audit-data/01.png)
 

## <a name="cause"></a>Orsak

Åtgärder visas inte direkt i granskningsloggen för aktiviteten. Det kan ta någonstans mellan 15 minuter och en timme att visa granskningsloggarna i portalen från den tidpunkt då åtgärden utförs.

## <a name="resolution"></a>Lösning

Vänta i mellan 15 minuter och en timme och se om åtgärderna visas i loggen. Om du fortfarande inte ser dem skapar du ett supportärende hos oss så tittar vi på det.


## <a name="next-steps"></a>Nästa steg
Se guiden med [vanliga frågor om Azure Active Directory-rapportering](active-directory-reporting-faq.md).

