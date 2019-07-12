---
title: Hantera hot mot resurser och data i Azure Active Directory B2C
description: Läs mer om identifiering och skyddsteknik tekniker för DOS-attacker och lösenord attacker i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7232917df6018c9c8afc7e7edd3730a277b193f4
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798237"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Hantera hot mot resurser och data i Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C har inbyggda funktioner som kan hjälpa dig att skydda mot hot mot resurser och data. Dessa hot är denial of service-attacker och lösenord för attacker. Denial of service-attacker kan göra resurser tillgängliga för avsedda användarna. Lösenord attacker leda till obehörig åtkomst till resurser.

## <a name="denial-of-service-attacks"></a>Denial of service-attacker

Azure AD B2C skyddar mot SYN översvämning angrepp med hjälp av en SYN-cookie. Azure AD B2C skyddar även mot DOS-attacker genom att använda gränser för frekvens och anslutningar.

## <a name="password-attacks"></a>Lösenord attacker

Lösenord som anges av användare krävs för att vara förhållandevis komplexa. Azure AD B2C använder minskning tekniker för lösenord attacker. Minskning innehåller identifiering av lösenord för brute force-attacker och ordlisteattacker för lösenord. Med hjälp av olika signaler analyserar Azure AD B2C integriteten för begäranden. Azure AD B2C har utformats för att skilja smart avsedda användarna från hackare och botnät.

Azure AD B2C använder en avancerad strategi för att låsa konton. Konton som är låsta baserat på IP-Adressen för begäran och det lösenord som anges. Låsningens varaktighet ökar också beroende på sannolikheten att den är en attack. När ett lösenord har misslyckats 10 gånger (försök standardtröskelvärdet), en minuts utelåsning inträffar. Nästa gång en inloggning är misslyckas efter kontot är upplåst (det vill säga när kontot har varit automatiskt låsas upp av tjänsten när den kontoutelåsning perioden går ut), en annan en minut utelåsning inträffar och fortsätter varje kunde inte logga in. Att ange samma lösenord flera gånger, räknas inte som flera misslyckade inloggningar.

De första 10 kontoutelåsning punkter är en minut långa. Därefter 10 kontoutelåsning punkter är något längre och öka varaktigheten efter varje 10 kontoutelåsning perioder. Räknaren för kontoutelåsning återställs till noll efter en genomförd inloggning att kontot inte är låst. Kontoutelåsning perioder kan vara upp till fem timmar.

## <a name="manage-password-protection-settings"></a>Hantera inställningar för skydd av lösenord

Hantera inställningar för skydd av lösenord, inklusive tröskelvärde för kontoutelåsning:

1. Navigera till [Azure-portalen](https://portal.azure.com).
1. Välj den **katalog + prenumeration** filtrera på menyn längst upp till höger i portalen och sedan välja vilken Azure AD B2C-klient.
1. Välj **Azure Active Directory** på den vänstra menyn (eller välj **alla tjänster** i avsnittet längst upp till vänster i portalen och Sök efter och välj *Azure Active Directory*).
1. Under **Security**väljer **autentiseringsmetoder**och välj sedan **lösenordsskydd**.
1. Ange dina önskade lösenord skyddsinställningar och välj sedan **spara**.

    ![Azure portal lösenord protection sidan i Azure AD-inställningar](media/active-directory-b2c-reference-threat-management/portal-02-password-protection.png)
    <br />*Ange tröskelvärde för låsning till 5 i **lösenordsskydd** inställningar*.

## <a name="view-locked-out-accounts"></a>Visa utelåst konton

Om du vill ha information om utelåst konton måste du kontrollera Active Directory [inloggningsaktivitet rapporten](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md). Under **Status**väljer **fel**. Misslyckade försök logga in med en **inloggning felkoden** av `50053` indikera ett låst konto:

![I Azure AD-inloggningen rapport som visar utelåst konto](media/active-directory-b2c-reference-threat-management/portal-01-locked-account.png)

Läs om hur du visar rapporten inloggningsaktiviteter i Azure Active Directory i [inloggning felkoder inloggningsaktivitetsrapporten](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
