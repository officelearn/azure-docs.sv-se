---
title: 'Felsökning: Data saknas i de nedladdade aktivitetsloggarna | Microsoft-dokument'
description: Ger en lösning till saknade data i nedladdade Azure Active Directory-aktivitetsloggar.
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee07fdb6f8a4e69600297bdb16b6ad74793f10c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74007724"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Jag kan inte hitta alla data i Azure Active Directory-aktivitetsloggarna som jag hämtade

## <a name="symptoms"></a>Symtom

Jag har hämtat aktivitetsloggarna (granskning eller inloggningar) och kan inte se alla poster för den tid som jag har valt. Varför det? 

 ![Rapportering](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Orsak

När du hämtar aktivitetsloggar i Azure-portalen begränsar vi skalan till 250 000 poster, sorterade efter den senaste först. 

## <a name="resolution"></a>Lösning

Du kan använda [rapporterings-API:er för Azure AD](concept-reporting-api.md) att hämta upp till en miljoner poster när som helst.

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor och svar om Azure Active Directory-rapporter](reports-faq.md)

