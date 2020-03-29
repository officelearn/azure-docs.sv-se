---
title: Azure AD Connect-synkroniseringstjänsthanterarens åtgärder | Microsoft-dokument
description: Förstå fliken Åtgärder i Synkroniseringstjänsthanteraren för Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 474000d1d4d7e1358682d1421125d482e3782049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381423"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Använda fliken Synkroniseringstjänsthanterarens verksamhet

![Synkronisera tjänsthanteraren](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

På fliken Åtgärder visas resultaten från de senaste operationerna. Den här fliken är nyckeln för att förstå och felsöka problem.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Förstå informationen som visas på fliken Åtgärder
Den övre halvan visar alla körningar i kronologisk ordning. Som standard behåller verksamhetsloggen information om de senaste sju dagarna, men den här inställningen kan ändras med [schemaläggaren](how-to-connect-sync-feature-scheduler.md). Du vill leta efter en körning som inte visar en framgångsstatus. Du kan ändra sorteringen genom att klicka på rubrikerna.

Kolumnen **Status** är den viktigaste informationen och visar det allvarligaste problemet för en körning. Här är en snabb sammanfattning av de vanligaste statusarna i prioritetsordning för att undersöka (där * indikerar flera möjliga felsträngar).

| Status | Kommentar |
| --- | --- |
| stoppas-\* |Körningen kunde inte slutföras. Om fjärrsystemet till exempel är nere och inte kan kontaktas. |
| stoppad-fel-gräns |Det finns mer än 5000 fel. Körningen stoppades automatiskt på grund av det stora antalet fel. |
| slutfört-\*-fel |Körningen har slutförts, men det finns fel (färre än 5 000) som ska undersökas. |
| -varningar\* |Körningen har slutförts, men vissa data är inte i förväntat tillstånd. Om du har fel är det här meddelandet vanligtvis bara ett symptom. Tills du har åtgärdat fel bör du inte undersöka varningar. |
| lyckades |Inga problem. |

När du väljer en rad uppdateras den nedersta raden för att visa information om körningen. Längst till vänster i botten kan du ha en lista som säger **steg #**. Den här listan visas bara om du har flera domäner i skogen där varje domän representeras av ett steg. Domännamnet finns under rubriken **Partition**. Under **Synkroniseringsstatistik**hittar du mer information om antalet ändringar som har bearbetats. Du kan klicka på länkarna för att få en lista över de ändrade objekten. Om du har objekt med fel visas dessa fel under **Synkroniseringsfel**.

Mer information finns i [felsöka ett objekt som inte synkroniserar](tshoot-connect-object-not-syncing.md)

## <a name="next-steps"></a>Nästa steg
Läs mer om synkroniseringskonfigurationen för [Azure AD Connect.](how-to-connect-sync-whatis.md)

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
