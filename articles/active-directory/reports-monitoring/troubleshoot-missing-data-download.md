---
title: 'Felsökning: Saknade data i nedladdade Azure Active Directory-aktivitetsloggarna | Microsoft Docs'
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
ms.openlocfilehash: 2200a9c75b371ed72ffefe6900367e698101e0fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60437114"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Jag kan inte hitta alla data i Azure Active Directory-aktivitetsloggarna som jag har hämtat

## <a name="symptoms"></a>Symtom

Jag har hämtat aktivitetsloggarna (granskning eller inloggningar) och kan inte se alla poster för den tid som jag har valt. Varför? 

 ![Rapportering](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Orsak

När du hämtar aktivitetsloggar i Azure portal begränsar vi omfattningen till 250 000 poster, sorterade efter senaste först. 

## <a name="resolution"></a>Lösning

Du kan använda [rapporterings-API:er för Azure AD](concept-reporting-api.md) att hämta upp till en miljoner poster när som helst.

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor och svar för Azure Active Directory-rapporter](reports-faq.md)

