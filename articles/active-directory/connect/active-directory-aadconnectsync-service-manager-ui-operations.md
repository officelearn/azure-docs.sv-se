---
title: "Azure AD Connect Synchronization Service Manager-åtgärder | Microsoft Docs"
description: "Förstå fliken åtgärder i hanteraren för synkroniseringstjänsten för Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 91210edc3306b834cbd68f0f028845a7f36dd0b5
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="using-the-sync-service-manager-operations-tab"></a>På fliken synkronisering Service Manager-åtgärder

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

Fliken åtgärder visar resultaten från de senaste åtgärderna. Den här fliken är för att förstå och felsöka problem.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Förstå informationen som visas på fliken åtgärder
Den övre delen visas alla körs i kronologisk ordning. Som standard åtgärderna logga behåller information om de senaste sju dagarna, men den här inställningen kan ändras med den [scheduler](active-directory-aadconnectsync-feature-scheduler.md). Du vill söka efter alla kör som inte visar statusen lyckades. Du kan ändra sortering genom att klicka på rubrikerna.

Den **Status** kolumnen är den viktigaste informationen och visar de svåraste problemet för en körning. Här är en kort sammanfattning av de vanligaste status i prioritetsordning att undersöka (där * ange flera möjliga felsträngar).

| Status | Kommentera |
| --- | --- |
| stopped-* |Kör kunde inte slutföras. Till exempel om fjärrdatorn är igång och kan inte kontaktas. |
| stopped-error-limit |Det finns fler än 5 000 fel. Kör har automatiskt stoppats på grund av det stora antalet fel. |
| slutförda -\*-fel |Kör slutfördes, men det finns fel (färre än 5 000) som bör undersökas. |
| slutförda -\*-varningar |Kör slutfördes, men vissa data är inte i det förväntade tillståndet. Om du har fel sedan är det här meddelandet vanligtvis bara ett symtom. Du bör inte undersöka varningar förrän du har åtgärdat felen. |
| lyckades |Inga problem. |

När du har valt en rad uppdateras längst ned om du vill visa detaljer för som kör. Du kanske har en lista som säger att längst till vänster i längst ned, **steg #**. Den här listan visas bara om du har flera domäner i skogen där varje domän som representeras av ett steg. Domännamnet finns under rubriken **Partition**. Under **Synkroniseringsstatistik**, du kan hitta mer information om antalet ändringar som har bearbetats. Du kan klicka på länkarna för att hämta en lista över de ändrade objekt. Om du har objekt med fel felen visas **synkroniseringsfel**.

Mer information finns i [felsökning av ett objekt som inte synkroniseras](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)

## <a name="next-steps"></a>Nästa steg
Lär dig mer om den [Azure AD Connect-synkronisering](active-directory-aadconnectsync-whatis.md) konfiguration.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
