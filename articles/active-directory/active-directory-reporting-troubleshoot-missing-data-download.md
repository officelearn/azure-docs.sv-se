---
title: "Felsökning: Saknade data i de nedladdade Azure Active Directory-aktivitetsloggarna | Microsoft Docs"
description: "Ger en lösning till saknade data i nedladdade Azure Active Directory-aktivitetsloggar."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
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
ms.openlocfilehash: 9109c698e4e8b43eeb7534c338adc99476012a3f
ms.contentlocale: sv-se
ms.lasthandoff: 05/08/2017

---

# Jag kan inte hitta några data i Azure Active Directory-aktivitetsloggar som jag har hämtat
<a id="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-have-downloaded" class="xliff"></a>


## Symtom
<a id="symptoms" class="xliff"></a>

Jag har hämtat aktivitetsloggarna (granskning eller inloggningar) och kan inte se alla poster för den tid som jag har valt. Varför? 

 ![Rapportering](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## Orsak
<a id="cause" class="xliff"></a>

När du hämtar aktivitetsloggar i Azure Portal begränsar vi omfattningen till 120 000 poster, sorterade efter de senaste. 

## Lösning
<a id="resolution" class="xliff"></a>

Du kan använda [rapporterings-API:er för Azure AD](active-directory-reporting-api-getting-started.md) att hämta upp till en miljoner poster när som helst. Vår rekommenderade metod är att köra ett skript enligt ett schema som anropar rapporterings-API: er för att hämta poster på ett inkrementellt sätt under en viss tidsperiod (t.ex. varje dag eller varje vecka).

## Nästa steg
<a id="next-steps" class="xliff"></a>
Se guiden med [vanliga frågor om Azure Active Directory-rapportering](active-directory-reporting-faq.md).


