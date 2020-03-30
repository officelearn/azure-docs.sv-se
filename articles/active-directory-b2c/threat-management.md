---
title: Hantera hot mot resurser och data
titleSuffix: Azure AD B2C
description: Lär dig mer om identifierings- och begränsningstekniker för överbelastningsattacker och lösenordsattacker i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a438363b054361420222804dffac7973470e82e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183609"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Hantera hot mot resurser och data i Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) har inbyggda funktioner som kan hjälpa dig att skydda mot hot mot dina resurser och data. Dessa hot inkluderar dos-attacker och lösenordsattacker. Denial-of-service-attacker kan göra resurser otillgängliga för avsedda användare. Lösenordsattacker leder till obehörig åtkomst till resurser.

## <a name="denial-of-service-attacks"></a>Överbelastningsattacker

Azure AD B2C skyddar mot SYN-översvämningsattacker med hjälp av en SYN-cookie. Azure AD B2C skyddar också mot dos-attacker med hjälp av begränsningar för priser och anslutningar.

## <a name="password-attacks"></a>Lösenordsattacker

Lösenord som anges av användare måste vara någorlunda komplexa. Azure AD B2C har begränsningstekniker för lösenordsattacker. Begränsning inkluderar identifiering av brute-force lösenordsattacker och ordliste lösenordsattacker. Genom att använda olika signaler analyserar Azure AD B2C integriteten för begäranden. Azure AD B2C är utformad för att på ett intelligent sätt skilja avsedda användare från hackare och botnät.

Azure AD B2C använder en sofistikerad strategi för att låsa konton. Kontona är låsta baserat på IP-adressen för begäran och de angivna lösenorden. Längden på lockouten ökar också baserat på sannolikheten för att det är en attack. När ett lösenord har provats 10 gånger utan framgång (standardgränsen för försök) inträffar en enminutsspärr. Nästa gång en inloggning misslyckas efter att kontot har låsts upp (det vill än, efter att kontot automatiskt har låsts upp av tjänsten när lockoutperioden löper ut), inträffar ytterligare en minuts utelåsning och fortsätter för varje misslyckad inloggning. Att ange samma lösenord upprepade gånger räknas inte som flera misslyckade inloggningar.

De första 10 lockoutperioderna är en minut långa. De kommande tio lockoutperioderna är något längre och tidshöjning efter var tionde lockoutperiod. Utelåsningsräknaren återställs till noll efter en lyckad inloggning när kontot inte är låst. Lockoutperioder kan pågå i upp till fem timmar.

## <a name="manage-password-protection-settings"></a>Hantera inställningar för lösenordsskydd

Så här hanterar du inställningar för lösenordsskydd, inklusive tröskelvärdet för utelåsning:

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Använd **katalog +** prenumerationsfiltret på den övre menyn för att välja den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Under **Säkerhet**väljer du **Autentiseringsmetoder (förhandsgranskning)** och väljer sedan **Lösenordsskydd**.
1. Ange önskade inställningar för lösenordsskydd och välj sedan **Spara**.

    ![Lösenordsskyddssida för Azure Portal i Azure AD-inställningar](./media/threat-management/portal-02-password-protection.png)
    <br />*Ange tröskelvärdet för utelåsning till 5 i **inställningar för lösenordsskydd** *.

## <a name="view-locked-out-accounts"></a>Visa utlåsta konton

Om du vill ha information om utelåst konton kan du kontrollera [aktivitetsrapporten för Active Directory-inloggning](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md). Under **Status**väljer du **Fel**. Misslyckade inloggningsförsök med en **inloggningsfelkod** `50053` för ange ett låst konto:

![Avsnitt i Azure AD-inloggningsrapport som visar utlåst konto](./media/threat-management/portal-01-locked-account.png)

Mer information om hur du visar inloggningsaktivitetsrapporten i Azure Active Directory finns [i Felkoder för inloggningsaktivitetsrapport](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
