---
title: 'Fel sökning: saknade data i de hämtade aktivitets loggarna | Microsoft Docs'
description: Ger en lösning till saknade data i nedladdade Azure Active Directory-aktivitetsloggar.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: db96bd99a25ca7803dbdfe412e2cddbbcb57c0e4
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817521"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Jag kan inte hitta alla data i Azure Active Directory aktivitets loggar som jag har hämtat

## <a name="symptoms"></a>Symtom

Jag har hämtat aktivitetsloggarna (granskning eller inloggningar) och kan inte se alla poster för den tid som jag har valt. Varför? 

 ![Rapportering](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Orsak

När du hämtar aktivitets loggar i Azure Portal begränsar vi skalan till 250 000 poster, sorterade efter senaste första. 

## <a name="resolution"></a>Upplösning

Du kan använda [rapporterings-API:er för Azure AD](concept-reporting-api.md) att hämta upp till en miljoner poster när som helst.

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor och svar om Azure Active Directory-rapporter](reports-faq.md)

