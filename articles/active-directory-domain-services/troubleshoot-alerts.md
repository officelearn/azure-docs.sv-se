---
title: Vanliga aviseringar och lösningar i Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du löser vanliga aviseringar som genereras som en del av hälsostatusen för Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: c83caf31e25ae2212ed120e77e017ac3849898e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612910"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Kända problem: Vanliga aviseringar och lösningar i Azure Active Directory Domain Services

Som en central del av identitet och autentisering för program har Azure Active Directory Domain Services (Azure AD DS) ibland problem. Om du stöter på problem finns det några vanliga aviseringar och tillhörande felsökningssteg som hjälper dig att få igång saker igen. Du kan när som helst också [öppna en Azure-supportbegäran för][azure-support] ytterligare felsökningshjälp.

Den här artikeln innehåller felsökningsinformation för vanliga aviseringar i Azure AD DS.

## <a name="aadds100-missing-directory"></a>AADDS100: Katalog saknas

### <a name="alert-message"></a>Varningsmeddelande

*Azure AD-katalogen som är associerad med din hanterade domän kan ha tagits bort. Den hanterade domänen finns inte längre i en konfiguration som stöds. Microsoft kan inte övervaka, hantera, korrigera och synkronisera din hanterade domän.*

### <a name="resolution"></a>Lösning

Det här felet orsakas vanligtvis när en Azure-prenumeration flyttas till en ny Azure AD-katalog och den gamla Azure AD-katalogen som är associerad med Azure AD DS tas bort.

Det här felet kan inte kan återknäckas. Lös aviseringen [genom att ta bort din befintliga Azure AD DS-hanterade domän](delete-aadds.md) och återskapa den i den nya katalogen. Om du har problem med att ta bort den hanterade Azure AD DS-domänen [öppnar du en Azure-supportbegäran för][azure-support] ytterligare felsökningshjälp.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C körs i den här katalogen

### <a name="alert-message"></a>Varningsmeddelande

*Azure AD Domain Services kan inte aktiveras i en Azure AD B2C-katalog.*

### <a name="resolution"></a>Lösning

Azure AD DS synkroniseras automatiskt med en Azure AD-katalog. Om Azure AD-katalogen är konfigurerad för B2C kan Azure AD DS inte distribueras och synkroniseras.

Om du vill använda Azure AD DS måste du återskapa din hanterade domän i en katalog som inte är Azure AD B2C med hjälp av följande steg:

1. [Ta bort den Hanterade Azure AD DS-domänen](delete-aadds.md) från din befintliga Azure AD-katalog.
1. Skapa en ny Azure AD-katalog som inte är en Azure AD B2C-katalog.
1. [Skapa en hanterad Azure AD DS-hanterad domän](tutorial-create-instance.md).

Azure AD DS-hanterade domänens hälsa uppdaterar sig automatiskt inom två timmar och tar bort aviseringen.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Adressen finns i ett offentligt IP-intervall

### <a name="alert-message"></a>Varningsmeddelande

*IP-adressintervallet för det virtuella nätverket där du har aktiverat Azure AD Domain Services finns i ett offentligt IP-intervall. Azure AD Domain Services måste vara aktiverat i ett virtuellt nätverk med ett privat IP-adressintervall. Den här konfigurationen påverkar Microsofts förmåga att övervaka, hantera, korrigera och synkronisera din hanterade domän.*

### <a name="resolution"></a>Lösning

Innan du börjar, se till att du förstår [privata IP v4-adressutrymmen](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

I ett virtuellt nätverk kan virtuella datorer göra begäranden till Azure-resurser i samma IP-adressintervall som konfigurerats för undernätet. Om du konfigurerar ett offentligt IP-adressintervall för ett undernät kanske begäranden som dirigeras inom ett virtuellt nätverk inte når de avsedda webbresurserna. Den här konfigurationen kan leda till oförutsägbara fel med Azure AD DS.

> [!NOTE]
> Om du äger IP-adressintervallet på internet som är konfigurerat i det virtuella nätverket kan den här aviseringen ignoreras. Azure AD Domain Services kan dock inte binda sig till [SLA]](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)med den här konfigurationen eftersom det kan leda till oförutsägbara fel.

Lös den här aviseringen genom att ta bort din befintliga Azure AD DS-hanterade domän och återskapa den i ett virtuellt nätverk med ett privat IP-adressintervall. Den här processen är störande eftersom Azure AD DS-hanterad domän inte är tillgänglig och alla anpassade resurser som du har skapat som us eller tjänstkonton går förlorade.

1. [Ta bort den Hanterade Azure AD DS-domänen](delete-aadds.md) från katalogen.
1. Om du vill uppdatera IP-adressintervallet för virtuellt nätverk söker du efter och väljer *Virtuellt nätverk* i Azure-portalen. Välj det virtuella nätverket för Azure AD DS som felaktigt har angett ett offentligt IP-adressintervall.
1. Under **Inställningar**väljer du *Adressutrymme*.
1. Uppdatera adressintervallet genom att välja det befintliga adressintervallet och redigera det, eller lägga till ytterligare ett adressintervall. Kontrollera att det nya IP-adressintervallet finns i ett privat IP-intervall. Spara ändringarna när du **är** klar.
1. Välj **Undernät** i vänsternavigering.
1. Välj det undernät som du vill redigera eller skapa ytterligare ett undernät.
1. Uppdatera eller ange ett privat IP-adressintervall och **spara** ändringarna.
1. [Skapa en hanterad Azure AD DS-hanterad domän](tutorial-create-instance.md). Se till att du väljer det uppdaterade virtuella nätverksundernätet med ett privat IP-adressintervall.

Azure AD DS-hanterade domänens hälsa uppdaterar sig automatiskt inom två timmar och tar bort aviseringen.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Din Azure-prenumeration hittades inte

### <a name="alert-message"></a>Varningsmeddelande

*Din Azure-prenumeration som är associerad med din hanterade domän har tagits bort.  Azure AD Domain Services kräver en aktiv prenumeration för att fortsätta fungera korrekt.*

### <a name="resolution"></a>Lösning

Azure AD DS kräver en aktiv prenumeration och kan inte flyttas till en annan prenumeration. Om Azure-prenumerationen som azure AD DS-hanterad domän har associerats med tas bort måste du återskapa en Azure-prenumeration och Azure AD DS-hanterad domän.

1. [Skapa en Azure-prenumeration](../cost-management-billing/manage/create-subscription.md).
1. [Ta bort den Hanterade Azure AD DS-domänen](delete-aadds.md) från din befintliga Azure AD-katalog.
1. [Skapa en hanterad Azure AD DS-hanterad domän](tutorial-create-instance.md).

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Din Azure-prenumeration är inaktiverad

### <a name="alert-message"></a>Varningsmeddelande

*Din Azure-prenumeration som är associerad med din hanterade domän är inte aktiv.  Azure AD Domain Services kräver en aktiv prenumeration för att fortsätta fungera korrekt.*

### <a name="resolution"></a>Lösning

Azure AD DS kräver en aktiv prenumeration. Om Azure-prenumerationen som Azure AD DS-hanterad domän var associerad med inte är aktiv måste du förnya den för att återaktivera prenumerationen.

1. [Förnya din Azure-prenumeration](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. När prenumerationen har förnyats kan du aktivera den hanterade domänen igen.

När den hanterade domänen är aktiverad igen uppdateras Azure AD DS-hanterade domänens hälsa automatiskt inom två timmar och tar bort aviseringen.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Prenumeration flyttade kataloger

### <a name="alert-message"></a>Varningsmeddelande

*Prenumerationen som används av Azure AD Domain Services har flyttats till en annan katalog. Azure AD Domain Services måste ha en aktiv prenumeration i samma katalog för att fungera korrekt.*

### <a name="resolution"></a>Lösning

Azure AD DS kräver en aktiv prenumeration och kan inte flyttas till en annan prenumeration. Om Den Azure-prenumeration som Azure AD DS-hanterad domän har associerats med flyttas flyttar du tillbaka prenumerationen till den tidigare katalogen eller [tar bort den hanterade domänen](delete-aadds.md) från den befintliga katalogen och skapar en [ersättnings-Azure AD DS-hanterad domän i den valda prenumerationen](tutorial-create-instance.md).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Det går inte att hitta resurser för den hanterade domänen

### <a name="alert-message"></a>Varningsmeddelande

*En resurs som används för den hanterade domänen har tagits bort. Den här resursen behövs för att Azure AD Domain Services ska fungera korrekt.*

### <a name="resolution"></a>Lösning

Azure AD DS skapar ytterligare resurser för att fungera korrekt, till exempel offentliga IP-adresser, virtuella nätverksgränssnitt och en belastningsutjämnare. Om någon av dessa resurser tas bort är den hanterade domänen i ett tillstånd som inte stöds och förhindrar att domänen hanteras. Mer information om dessa resurser finns i [Nätverksresurser som används av Azure AD DS](network-considerations.md#network-resources-used-by-azure-ad-ds).

Den här aviseringen genereras när en av dessa nödvändiga resurser tas bort. Om resursen togs bort för mindre än 4 timmar sedan finns det en risk att Azure-plattformen automatiskt kan återskapa den borttagna resursen. I följande steg beskrivs hur du kontrollerar hälsostatus och tidsstämpel för borttagning av resurser:

1. Sök efter och välj **Domain Services**i Azure-portalen . Välj din Azure AD DS-hanterade domän, till exempel *aaddscontoso.com*.
1. I den vänstra navigeringen väljer du **Hälsa**.
1. På hälsosidan väljer du aviseringen med ID *AADDS109*.
1. Aviseringen har en tidsstämpel för när den först hittades. Om den tidsstämpeln är mindre än 4 timmar sedan kan Azure-plattformen automatiskt återskapa resursen och lösa aviseringen av sig själv.

    Om aviseringen är mer än 4 timmar gammal är Azure AD DS-hanterad domän i ett oåterkalleligt tillstånd. [Ta bort den Hanterade Azure AD DS-domänen](delete-aadds.md) och skapa sedan [en ersättningshanterad domän](tutorial-create-instance.md).

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: Undernätet som är associerat med din hanterade domän är fullt

### <a name="alert-message"></a>Varningsmeddelande

*Undernätet som valts för distribution av Azure AD Domain Services är fullt och har inte utrymme för den ytterligare domänkontrollant som behöver skapas.*

### <a name="resolution"></a>Lösning

Det virtuella nätverksundernätet för Azure AD DS behöver tillräckligt med IP-adresser för de automatiskt skapade resurserna. Det här IP-adressutrymmet innehåller behovet av att skapa ersättningsresurser om det finns en underhållshändelse. För att minimera risken för att tillgängliga IP-adresser tar ska du inte distribuera ytterligare resurser, till exempel dina egna virtuella datorer, till samma virtuella nätverksundernät som Azure AD DS.

Det här felet kan inte kan återknäckas. Lös aviseringen [genom att ta bort din befintliga Azure AD DS-hanterade domän](delete-aadds.md) och återskapa den. Om du har problem med att ta bort den hanterade Azure AD DS-domänen [öppnar du en Azure-supportbegäran för][azure-support] ytterligare felsökningshjälp.

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Tjänstens huvudnamn obehörig

### <a name="alert-message"></a>Varningsmeddelande

*Ett tjänsthuvudnamn som Azure AD Domain Services använder för att serva din domän har inte behörighet att hantera resurser på Azure-prenumerationen. Tjänstens huvudnamn måste få behörighet att serva din hanterade domän.*

### <a name="resolution"></a>Lösning

Vissa automatiskt genererade tjänsthuvudnamn används för att hantera och skapa resurser för en Azure AD DS-hanterad domän. Om åtkomstbehörigheterna för ett av dessa tjänsthuvudnamn ändras kan domänen inte hantera resurser korrekt. Följande steg visar hur du förstår och sedan beviljar åtkomstbehörigheter till ett tjänsthuvudnamn:

1. Läs om [rollbaserad åtkomstkontroll och hur du beviljar åtkomst till program i Azure-portalen](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
2. Granska åtkomsten som tjänstens huvudnamn med ID *abba844e-bc0e-44b0-947a-dc74e5d09022* har och bevilja den åtkomst som nekades vid ett tidigare datum.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Inte tillräckligt med IP-adress i den hanterade domänen

### <a name="alert-message"></a>Varningsmeddelande

*Vi har identifierat att undernätet för det virtuella nätverket i den här domänen kanske inte har tillräckligt med IP-adresser. Azure AD Domain Services behöver minst två tillgängliga IP-adresser i undernätet som det är aktiverat i. Vi rekommenderar att du har minst 3-5 extra IP-adresser i undernätet. Detta kan ha inträffat om andra virtuella datorer distribueras i undernätet, vilket uttömmer antalet tillgängliga IP-adresser eller om det finns en begränsning av antalet tillgängliga IP-adresser i undernätet.*

### <a name="resolution"></a>Lösning

Det virtuella nätverksundernätet för Azure AD DS behöver tillräckligt med IP-adresser för de automatiskt skapade resurserna. Det här IP-adressutrymmet innehåller behovet av att skapa ersättningsresurser om det finns en underhållshändelse. För att minimera risken för att tillgängliga IP-adresser tar ska du inte distribuera ytterligare resurser, till exempel dina egna virtuella datorer, till samma virtuella nätverksundernät som Azure AD DS.

Lös den här aviseringen genom att ta bort din befintliga Azure AD DS-hanterade domän och återskapa den i ett virtuellt nätverk med ett tillräckligt stort IP-adressintervall. Den här processen är störande eftersom Azure AD DS-hanterad domän inte är tillgänglig och alla anpassade resurser som du har skapat som us eller tjänstkonton går förlorade.

1. [Ta bort den Hanterade Azure AD DS-domänen](delete-aadds.md) från katalogen.
1. Om du vill uppdatera IP-adressintervallet för virtuellt nätverk söker du efter och väljer *Virtuellt nätverk* i Azure-portalen. Välj det virtuella nätverket för Azure AD DS som har det lilla IP-adressintervallet.
1. Under **Inställningar**väljer du *Adressutrymme*.
1. Uppdatera adressintervallet genom att välja det befintliga adressintervallet och redigera det, eller lägga till ytterligare ett adressintervall. Kontrollera att det nya IP-adressintervallet är tillräckligt stort för Azure AD DS-undernätsområdet. Spara ändringarna när du **är** klar.
1. Välj **Undernät** i vänsternavigering.
1. Välj det undernät som du vill redigera eller skapa ytterligare ett undernät.
1. Uppdatera eller ange ett tillräckligt stort IP-adressintervall och **spara** ändringarna.
1. [Skapa en hanterad Azure AD DS-hanterad domän](tutorial-create-instance.md). Se till att du väljer det uppdaterade virtuella nätverksundernätet med tillräckligt stort IP-adressintervall.

Azure AD DS-hanterade domänens hälsa uppdaterar sig automatiskt inom två timmar och tar bort aviseringen.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Resurser kan inte återknäckas

### <a name="alert-message"></a>Varningsmeddelande

*De resurser som används av Azure AD Domain Services upptäcktes i ett oväntat tillstånd och kan inte återställas.*

### <a name="resolution"></a>Lösning

Det här felet kan inte kan återknäckas. Lös aviseringen [genom att ta bort din befintliga Azure AD DS-hanterade domän](delete-aadds.md) och återskapa den. Om du har problem med att ta bort den hanterade Azure AD DS-domänen [öppnar du en Azure-supportbegäran för][azure-support] ytterligare felsökningshjälp.

## <a name="aadds114-subnet-invalid"></a>AADDS114: Undernätet ogiltigt

### <a name="alert-message"></a>Varningsmeddelande

*Det undernät som valts för distribution av Azure AD Domain Services är ogiltigt och kan inte användas.*

### <a name="resolution"></a>Lösning

Det här felet kan inte kan återknäckas. Lös aviseringen [genom att ta bort din befintliga Azure AD DS-hanterade domän](delete-aadds.md) och återskapa den. Om du har problem med att ta bort den hanterade Azure AD DS-domänen [öppnar du en Azure-supportbegäran för][azure-support] ytterligare felsökningshjälp.

## <a name="aadds115-resources-are-locked"></a>AADDS115: Resurserna är låsta

### <a name="alert-message"></a>Varningsmeddelande

*Det går inte att manövrera en eller flera nätverksresurser som används av den hanterade domänen eftersom målomfånget har låsts.*

### <a name="resolution"></a>Lösning

Resurslås kan tillämpas på Azure-resurser för att förhindra ändring eller borttagning. Eftersom Azure AD DS är en hanterad tjänst behöver Azure-plattformen möjligheten att göra konfigurationsändringar. Om ett resurslås tillämpas på några av Azure AD DS-komponenterna kan Azure-plattformen inte utföra sina hanteringsuppgifter.

Så här söker du efter resurslås på Azure AD DS-komponenterna och tar bort dem:

1. Kontrollera åtgärdsloggarna i Azure DS-portalen för var och en av Azure AD DS-nätverkskomponenterna i resursgruppen, till exempel virtuellt nätverk, nätverksgränssnittet eller den offentliga IP-adressen. Dessa åtgärdsloggar bör ange varför en åtgärd misslyckas och var ett resurslås används.
1. Markera den resurs där ett lås används och markera och ta sedan bort låsen under **Lås.**

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Resurserna är oanvändningbara

### <a name="alert-message"></a>Varningsmeddelande

*En eller flera av de nätverksresurser som används av den hanterade domänen kan inte användas på grund av principbegränsningar.*

### <a name="resolution"></a>Lösning

Principer tillämpas på Azure-resurser och resursgrupper som styr vilka konfigurationsåtgärder som är tillåtna. Eftersom Azure AD DS är en hanterad tjänst behöver Azure-plattformen möjligheten att göra konfigurationsändringar. Om en princip tillämpas på några av Azure AD DS-komponenterna kanske Azure-plattformen inte kan utföra sina hanteringsuppgifter.

Om du vill söka efter tillämpade principer för Azure AD DS-komponenterna och uppdatera dem gör du följande:

1. Kontrollera åtgärdsloggarna i Azure DS-portalen för var och en av Azure AD DS-nätverkskomponenterna i resursgruppen, till exempel virtuellt nätverk, nätverkskort eller offentlig IP-adress. Dessa åtgärdsloggar bör ange varför en åtgärd misslyckas och var en restriktiv princip tillämpas.
1. Välj den resurs där en princip tillämpas och välj och redigera principen under **Principer**så att den är mindre restriktiv.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Synkroniseringen har inte slutförts på ett tag

### <a name="alert-message"></a>Varningsmeddelande

*Den hanterade domänen synkroniserades senast med Azure AD på [datum]. Användare kanske inte kan logga in på den hanterade domänen eller gruppmedlemskap kanske inte är synkroniserade med Azure AD.*

### <a name="resolution"></a>Lösning

[Kontrollera Azure AD DS-hälsotillståndet](check-health.md) för alla aviseringar som indikerar problem i konfigurationen av den hanterade domänen. Problem med nätverkskonfigurationen kan blockera synkroniseringen från Azure AD. Om du kan lösa aviseringar som indikerar ett konfigurationsproblem väntar du två timmar och kontrollerar om synkroniseringen har slutförts.

Följande vanliga orsaker gör att synkroniseringen stoppas i en Azure AD DS-hanterade domäner:

* Den nödvändiga nätverksanslutningen är blockerad. Mer information om hur du kontrollerar azure-virtuella nätverket efter problem och vad som krävs finns [i felsöka nätverkssäkerhetsgrupper](alert-nsg.md) och [nätverkskraven för Azure AD Domain Services](network-considerations.md).
*  Lösenordssynkronisering har inte konfigurerats eller slutförts när den Azure AD DS-hanterade domänen har distribuerats. Du kan ställa in lösenordssynkronisering för användare av [endast molnet](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) eller [hybridanvändare från on-prem](tutorial-configure-password-hash-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: En säkerhetskopia har inte tagits på ett tag

### <a name="alert-message"></a>Varningsmeddelande

*Den hanterade domänen säkerhetskopierades senast [datum].*

### <a name="resolution"></a>Lösning

[Kontrollera Azure AD DS-hälsotillståndet](check-health.md) för aviseringar som indikerar problem i konfigurationen av den hanterade domänen. Problem med nätverkskonfigurationen kan blockera Azure-plattformen från att framgångsrikt ta säkerhetskopior. Om du kan lösa aviseringar som indikerar ett konfigurationsproblem väntar du två timmar och kontrollerar om synkroniseringen har slutförts.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Avstängning på grund av inaktiverad prenumeration

### <a name="alert-message"></a>Varningsmeddelande

*Den hanterade domänen pausas eftersom Azure-prenumerationen som är associerad med domänen inte är aktiv.*

### <a name="resolution"></a>Lösning

> [!WARNING]
> Om en Azure AD DS-hanterad domän stängs av under en längre tid finns det risk för att den tas bort. Lös orsaken till avstängning så snabbt som möjligt. Mer information finns i [Förstå de avstängda tillstånden för Azure AD DS](suspension.md).

Azure AD DS kräver en aktiv prenumeration. Om Azure-prenumerationen som Azure AD DS-hanterad domän var associerad med inte är aktiv måste du förnya den för att återaktivera prenumerationen.

1. [Förnya din Azure-prenumeration](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. När prenumerationen har förnyats kan du aktivera den hanterade domänen igen.

När den hanterade domänen är aktiverad igen uppdateras Azure AD DS-hanterade domänens hälsa automatiskt inom två timmar och tar bort aviseringen.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Avstängning på grund av en ogiltig konfiguration

### <a name="alert-message"></a>Varningsmeddelande

*Den hanterade domänen pausas på grund av en ogiltig konfiguration. Tjänsten har inte kunnat hantera, korrigera eller uppdatera domänkontrollanterna för den hanterade domänen under en längre tid.*

### <a name="resolution"></a>Lösning

> [!WARNING]
> Om en Azure AD DS-hanterad domän stängs av under en längre tid finns det risk för att den tas bort. Lös orsaken till avstängning så snabbt som möjligt. Mer information finns i [Förstå de avstängda tillstånden för Azure AD DS](suspension.md).

[Kontrollera Azure AD DS-hälsotillståndet](check-health.md) för aviseringar som indikerar problem i konfigurationen av den hanterade domänen. Om du kan lösa aviseringar som indikerar ett konfigurationsproblem väntar du två timmar och kontrollerar om synkroniseringen har slutförts. När du är klar [öppnar du en Azure-supportbegäran för][azure-support] att återaktivera den Hanterade Azure AD DS-domänen.

## <a name="next-steps"></a>Nästa steg

Om du fortfarande har problem [öppnar du en Azure-supportbegäran för][azure-support] ytterligare felsökningshjälp.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
