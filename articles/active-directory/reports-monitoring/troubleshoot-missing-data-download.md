---
title: 'Felsökning: Saknade data i de nedladdade Azure Active Directory-aktivitetsloggarna | Microsoft Docs'
description: Ger en lösning till saknade data i nedladdade Azure Active Directory-aktivitetsloggar.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 9138da42eeb87e45b86be10aff67792ee6de09b4
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054371"
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Jag kan inte hitta några data i Azure Active Directory-aktivitetsloggarna som jag har hämtat


## <a name="symptoms"></a>Symtom

Jag har hämtat aktivitetsloggarna (granskning eller inloggningar) och kan inte se alla poster för den tid som jag har valt. Varför? 

 ![Rapportering](./media/troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Orsak

När du hämtar aktivitetsloggar i Azure portal begränsar vi omfattningen till 5 000 poster, sorterade efter senaste först. 

## <a name="resolution"></a>Lösning

Du kan använda [rapporterings-API:er för Azure AD](concept-reporting-api.md) att hämta upp till en miljoner poster när som helst. Den rekommenderade metoden är att köra ett skript enligt ett schema som anropar reporting API: er för att hämta poster på ett inkrementellt sätt under en viss tidsperiod (till exempel varje dag eller varje vecka).

## <a name="next-steps"></a>Nästa steg
Se guiden med [vanliga frågor om Azure Active Directory-rapportering](reports-faq.md).

