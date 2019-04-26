---
title: Azure AD Connect åtgärder för hanteraren för synkroniseringstjänsten | Microsoft Docs
description: Förstå fliken åtgärder i hanteraren för synkroniseringstjänsten för Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 07/13/2017
ms.date: 11/12/2018
ms.component: hybrid
ms.author: v-junlch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 474000d1d4d7e1358682d1421125d482e3782049
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381423"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>På fliken synkronisering Service Manager-åtgärder

![Sync Service Manager](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

Fliken åtgärder visas resultatet från de senaste åtgärderna. Den här fliken är att förstå och felsöka problem.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Förstå informationen som visas på fliken åtgärder
Den övre delen visas alla körningar i kronologisk ordning. Som standard logga åtgärderna är fortfarande information om de senaste sju dagarna, men den här inställningen kan ändras med den [scheduler](how-to-connect-sync-feature-scheduler.md). Du vill söka efter alla körningar som inte visar statusen lyckades. Du kan ändra sorteringen genom att klicka på rubrikerna.

Den **Status** kolumnen är den viktigaste informationen och visar det mest allvarliga problemet för en körning. Här är en snabb sammanfattning av de vanligaste statusvärdena i prioritetsordning att undersöka (där * indikera flera möjliga felsträngar).

| Status | Kommentar |
| --- | --- |
| Stoppad-\* |Det gick inte att slutföra körningen. Exempel: om fjärrdatorn är igång och kan inte kontaktas. |
| stopped-error-limit |Det finns fler än 5 000 fel. Körningen stoppades automatiskt på grund av det stora antalet fel. |
| slutförda -\*-fel |Körningen slutfördes, men det finns fel (färre än 5 000) som bör undersökas. |
| completed-\*-warnings |Körningen har slutförts, men vissa data är inte i förväntat tillstånd. Om du har fel sedan är det här meddelandet vanligtvis bara ett tecken. Du bör inte undersöka varningar förrän du har åtgärdat felen. |
| lyckades |Inga problem. |

När du har valt en rad uppdaterar längst ned för att visa information om som körs. Du kan ha en lista som säger att längst till vänster på nedersta, **steg #**. Den här listan visas bara om du har flera domäner i skogen där varje domän representeras av ett steg. Domännamnet finns under rubriken **Partition**. Under **Synkroniseringsstatistik**, du kan hitta mer information om antalet ändringar som har bearbetats. Du kan klicka på länkarna för att få en lista med ändrade objekt. Om du har objekt med fel felen som visas **synkroniseringsfel**.

Mer information finns i [Felsök ett objekt som inte synkroniseras](tshoot-connect-object-not-syncing.md)

## <a name="next-steps"></a>Nästa steg
Läs mer om den [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md) konfiguration.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

