---
title: "Felsökning: Saknade data i aktivitetsloggen för Azure Active Directory | Microsoft Docs"
description: "Visar de olika tillgängliga rapporterna i Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 18af51e95a283a5cd33688484a0d7477eb4b957d
ms.contentlocale: sv-se
ms.lasthandoff: 05/08/2017

---

# Det går inte att hitta vissa åtgärder som jag utförde i aktivitetsloggen för Azure Active Directory
<a id="i-cant-find-some-actions-that-i-performed-in-the-azure-active-directory-activity-log" class="xliff"></a>


## Symtom
<a id="symptoms" class="xliff"></a>

Jag utförde vissa åtgärder i Azure Portal och förväntade att se granskningsloggarna för dessa åtgärder på bladet `Activity logs > Audit Logs`, men det går inte att hitta dem.

 ![Rapportering](./media/active-directory-reporting-troubleshoot-missing-audit-data/01.png)
 

## Orsak
<a id="cause" class="xliff"></a>

Åtgärder visas inte direkt i granskningsloggen för aktiviteten. Det kan ta någonstans mellan 15 minuter och en timme att visa granskningsloggarna i portalen från den tidpunkt då åtgärden utförs.

## Lösning
<a id="resolution" class="xliff"></a>

Vänta i mellan 15 minuter och en timme och se om åtgärderna visas i loggen. Om du fortfarande inte ser dem skapar du ett supportärende hos oss så tittar vi på det.


## Nästa steg
<a id="next-steps" class="xliff"></a>
Se guiden med [vanliga frågor om Azure Active Directory-rapportering](active-directory-reporting-faq.md).


