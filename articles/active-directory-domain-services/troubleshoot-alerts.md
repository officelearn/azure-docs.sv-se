---
title: 'Azure Active Directory Domain Services: Felsöka aviseringar | Microsoft Docs'
description: Felsöka aviseringar för Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 5a8f3401de0dc452193efbcf79aef87a19aed081
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617077"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services – felsök aviseringar
Den här artikeln innehåller fel söknings guider för aviseringar som kan uppstå i din hanterade domän.


Välj de fel söknings steg som motsvarar ID eller meddelande i aviseringen.

| **Aviserings-ID** | **Aviserings meddelande** | **Lösning** |
| --- | --- | :--- |
| AADDS001 | *Säkert LDAP över Internet är aktiverat för den hanterade domänen. Åtkomst till port 636 är dock inte låst med en nätverks säkerhets grupp. Detta kan innebära att användar konton på den hanterade domänen används för lösen ords brutet angrepp.* | [Felaktig säker LDAP-konfiguration](alert-ldaps.md) |
| AADDS100 | *Azure AD-katalogen som är kopplad till din hanterade domän kan ha tagits bort. Den hanterade domänen är inte längre i en konfiguration som stöds. Microsoft kan inte övervaka, hantera, korrigera och synkronisera din hanterade domän.* | [Katalog saknas](#aadds100-missing-directory) |
| AADDS101 | *Azure AD Domain Services kan inte aktive ras i en Azure AD B2C katalog.* | [Azure AD B2C körs i den här katalogen](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Ett huvud namn för tjänsten som krävs för att Azure AD Domain Services ska fungera korrekt har tagits bort från Azure AD-katalogen. Den här konfigurationen påverkar Microsofts förmåga att övervaka, hantera, uppdatera och synkronisera din hanterade domän.* | [Tjänstens huvud namn saknas](alert-service-principal.md) |
| AADDS103 | *IP-adressintervallet för det virtuella nätverk där du har aktiverat Azure AD Domain Services finns i ett offentligt IP-adressintervall. Azure AD Domain Services måste vara aktiverat i ett virtuellt nätverk med ett privat IP-adressintervall. Den här konfigurationen påverkar Microsofts förmåga att övervaka, hantera, korrigera och synkronisera din hanterade domän.* | [Adressen finns i ett offentligt IP-adressintervall](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *Microsoft kan inte komma åt domän kontrol Lanterna för den här hanterade domänen. Detta kan inträffa om en nätverks säkerhets grupp (NSG) som kon figurer ATS i ditt virtuella nätverk blockerar åtkomsten till den hanterade domänen. En annan möjlig orsak är om det finns en användardefinierad väg som blockerar inkommande trafik från Internet.* | [Nätverks fel](alert-nsg.md) |
| AADDS105 | *Tjänstens huvud namn med program-ID: t "d87dcbc6-a371-462e-88e3-28ad15ec4e64" togs bort och återskapades. Rekreationet lämnar inkonsekventa behörigheter på Azure AD Domain Services resurser som behövs för att underhålla din hanterade domän. Synkronisering av lösen ord på din hanterade domän kan påverkas.* | [Programmet för Lösenordssynkronisering är inaktuellt](alert-service-principal.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS106 | *Din Azure-prenumeration som är associerad med din hanterade domän har tagits bort.  Azure AD Domain Services kräver en aktiv prenumeration för att fortsätta att fungera korrekt.* | [Det gick inte att hitta Azure-prenumerationen](#aadds106-your-azure-subscription-is-not-found) |
| AADDS107 | *Din Azure-prenumeration som är associerad med din hanterade domän är inte aktiv.  Azure AD Domain Services kräver en aktiv prenumeration för att fortsätta att fungera korrekt.* | [Azure-prenumerationen är inaktive rad](#aadds107-your-azure-subscription-is-disabled) |
| AADDS108 | *Prenumerationen som används av Azure AD Domain Services har flyttats till en annan katalog. Azure AD Domain Services måste ha en aktiv prenumeration i samma katalog för att fungera korrekt.* | [Flyttade prenumerations kataloger](#aadds108-subscription-moved-directories) |
| AADDS109 | *En resurs som används för din hanterade domän har tagits bort. Den här resursen krävs för att Azure AD Domain Services ska fungera korrekt.* | [En resurs har tagits bort](#aadds109-resources-for-your-managed-domain-cannot-be-found) |
| AADDS110 | *Det undernät som valts för distribution av Azure AD Domain Services är fullt och det finns inget utrymme för den ytterligare domänkontrollant som behöver skapas.* | [Under nätet är fullt](#aadds110-the-subnet-associated-with-your-managed-domain-is-full) |
| AADDS111 | *Ett huvud namn för tjänsten som Azure AD Domain Services använder för att betjäna din domän har inte behörighet att hantera resurser i Azure-prenumerationen. Tjänstens huvud namn måste ha behörighet att betjäna din hanterade domän.* | [Tjänstens huvud namn tillåts inte](#aadds111-service-principal-unauthorized) |
| AADDS112 | *Vi har identifierat att under nätet för det virtuella nätverket i den här domänen kanske inte har tillräckligt med IP-adresser. Azure AD Domain Services behöver minst två tillgängliga IP-adresser inom under nätet som den är aktive rad i. Vi rekommenderar att ha minst 3-5 extra IP-adresser i under nätet. Detta kan ha inträffat om andra virtuella datorer distribueras i under nätet, så att antalet tillgängliga IP-adresser förbrukas, eller om det finns en begränsning för antalet tillgängliga IP-adresser i under nätet.* | [Inte tillräckligt med IP-adresser](#aadds112-not-enough-ip-address-in-the-managed-domain) |
| AADDS113 | *De resurser som används av Azure AD Domain Services har upptäckts i ett oväntat tillstånd och kan inte återställas.* | [Resurserna kan inte återställas](#aadds113-resources-are-unrecoverable) |
| AADDS114 | *Det undernät som valts för distribution av Azure AD Domain Services är ogiltigt och kan inte användas.* | [Undernät är ogiltigt](#aadds114-subnet-invalid) |
| AADDS115 | *En eller flera av de nätverks resurser som används av den hanterade domänen kan inte användas eftersom mål omfånget har låsts.* | [Resurserna är låsta](#aadds115-resources-are-locked) |
| AADDS116 | *En eller flera av de nätverks resurser som används av den hanterade domänen kan inte användas på grund av princip begränsningar.* | [Resurserna är oanvändbara](#aadds116-resources-are-unusable) |
| AADDS500 | *Den hanterade domänen synkroniserades senast med Azure AD på [Date]. Användarna kanske inte kan logga in på den hanterade domänen eller grupp medlemskapen kanske inte är synkroniserade med Azure AD.* | [Synkroniseringen har inträffat på ett tag](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *Den hanterade domänen säkerhetskopierades senast [datum].* | [En säkerhets kopiering har inte gjorts på ett tag](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *Det säkra LDAP-certifikatet för den hanterade domänen upphör att gälla [datum].* | [Certifikatet för säker LDAP förfaller](alert-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *Den hanterade domänen har pausats eftersom den Azure-prenumeration som är associerad med domänen inte är aktiv.* | [SUS Pension på grund av inaktive rad prenumeration](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *Den hanterade domänen har pausats på grund av en ogiltig konfiguration. Tjänsten har inte kunnat hantera, korrigera eller uppdatera domän kontrol Lanterna för din hanterade domän under en längre tid.* | [SUS Pension på grund av en ogiltig konfiguration](#aadds504-suspension-due-to-an-invalid-configuration) |



## <a name="aadds100-missing-directory"></a>AADDS100: Katalog saknas
**Aviserings meddelande:**

*Azure AD-katalogen som är kopplad till din hanterade domän kan ha tagits bort. Den hanterade domänen är inte längre i en konfiguration som stöds. Microsoft kan inte övervaka, hantera, korrigera och synkronisera din hanterade domän.*

**Lösning**

Det här felet orsakas vanligt vis av att du flyttar din Azure-prenumeration till en ny Azure AD-katalog på ett felaktigt sätt och tar bort den gamla Azure AD-katalogen som fortfarande är kopplad till Azure AD Domain Services.

Det här felet kan inte återställas. För att lösa problemet måste du [ta bort din befintliga hanterade domän](delete-aadds.md) och återskapa den i din nya katalog. Om du har problem med att ta bort kontaktar du Azure Active Directory Domain Services produkt teamet [för support](contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C körs i den här katalogen
**Aviserings meddelande:**

*Azure AD Domain Services kan inte aktive ras i en Azure AD B2C katalog.*

**Lösning**

>[!NOTE]
>Om du vill fortsätta att använda Azure AD Domain Services måste du återskapa Azure AD Domain Services instansen i en katalog som inte är Azure AD B2C.

Följ dessa steg om du vill återställa tjänsten:

1. [Ta bort din hanterade domän](delete-aadds.md) från din befintliga Azure AD-katalog.
2. Skapa en ny katalog som inte är en Azure AD B2C katalog.
3. Följ [komma igång](tutorial-create-instance.md) -guiden för att återskapa en hanterad domän.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Adressen finns i ett offentligt IP-adressintervall

**Aviserings meddelande:**

*IP-adressintervallet för det virtuella nätverk där du har aktiverat Azure AD Domain Services finns i ett offentligt IP-adressintervall. Azure AD Domain Services måste vara aktiverat i ett virtuellt nätverk med ett privat IP-adressintervall. Den här konfigurationen påverkar Microsofts förmåga att övervaka, hantera, korrigera och synkronisera din hanterade domän.*

**Lösning**

> [!NOTE]
> För att lösa det här problemet måste du ta bort din befintliga hanterade domän och återskapa den i ett virtuellt nätverk med ett privat IP-adressintervall. Den här processen är störande.

Innan du börjar läser du avsnittet **privat IP v4-adress utrymme** i [den här artikeln](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

I det virtuella nätverket kan datorer göra förfrågningar till Azure-resurser som är i samma IP-adressintervall som de som har kon figurer ATS för under nätet. Eftersom det virtuella nätverket har kon figurer ATS för det här intervallet kommer dessa begär Anden att dirigeras inom det virtuella nätverket och kommer inte att uppnå de avsedda webb resurserna. Den här konfigurationen kan leda till oförutsägbara fel med Azure AD Domain Services.

**Om du äger IP-adressintervallet i Internet som har kon figurer ATS i det virtuella nätverket kan den här aviseringen ignoreras. Azure AD Domain Services kan dock inte genomföra [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] med den här konfigurationen eftersom den kan leda till oförutsägbara fel.**


1. [Ta bort din hanterade domän](delete-aadds.md) från katalogen.
2. Åtgärda IP-adressintervallet för under nätet
   1. Gå till [sidan virtuella nätverk på Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
   2. Välj det virtuella nätverk som du planerar att använda för Azure AD Domain Services.
   3. Klicka på **adress utrymme** under Inställningar
   4. Uppdatera adress intervallet genom att klicka på det befintliga adress intervallet och redigera det eller lägga till ytterligare ett adress intervall. Kontrol lera att det nya adress intervallet finns i ett privat IP-adressintervall. Spara ändringarna.
   5. Klicka på **undernät** i det vänstra navigerings fältet.
   6. Klicka på det undernät som du vill redigera i tabellen.
   7. Uppdatera adress intervallet och spara ändringarna.
3. Följ [komma igång med hjälp av Azure AD Domain Services Guide](tutorial-create-instance.md) för att återskapa den hanterade domänen. Se till att du väljer ett virtuellt nätverk med ett privat IP-adressintervall.
4. Följ [den här guiden](join-windows-vm.md)för att ansluta dina virtuella datorer till din nya domän.
8. Kontrol lera din domän hälsa på två timmar för att säkerställa att aviseringen har lösts.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Det gick inte att hitta din Azure-prenumeration

**Aviserings meddelande:**

*Din Azure-prenumeration som är associerad med din hanterade domän har tagits bort.  Azure AD Domain Services kräver en aktiv prenumeration för att fortsätta att fungera korrekt.*

**Lösning**

Azure AD Domain Services kräver en prenumeration för att fungera och kan inte flyttas till en annan prenumeration. Eftersom den Azure-prenumeration som din hanterade domän var associerad med har tagits bort måste du återskapa en Azure-prenumeration och Azure AD Domain Services.

1. Skapa en Azure-prenumeration
2. [Ta bort din hanterade domän](delete-aadds.md) från din befintliga Azure AD-katalog.
3. Följ [komma igång](tutorial-create-instance.md) -guiden för att återskapa en hanterad domän.

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Din Azure-prenumerationen har inaktiverats

**Aviserings meddelande:**

*Din Azure-prenumeration som är associerad med din hanterade domän är inte aktiv.  Azure AD Domain Services kräver en aktiv prenumeration för att fortsätta att fungera korrekt.*

**Lösning**


1. [Förnya din Azure-prenumeration](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. När prenumerationen har förnyats får Azure AD Domain Services ett meddelande från Azure för att återaktivera din hanterade domän.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Flyttade prenumerations kataloger

**Aviserings meddelande:**

*Prenumerationen som används av Azure AD Domain Services har flyttats till en annan katalog. Azure AD Domain Services måste ha en aktiv prenumeration i samma katalog för att fungera korrekt.*

**Lösning**

Du kan antingen flytta prenumerationen som är kopplad till Azure AD Domain Services tillbaka till föregående katalog, eller så måste du [ta bort din hanterade domän](delete-aadds.md) från den befintliga katalogen och skapa den på nytt i den valda katalogen (antingen med en ny prenumeration eller ändra katalogen som Azure AD Domain Services-instansen är i).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Det går inte att hitta resurser för din hanterade domän

**Aviserings meddelande:**

*En resurs som används för din hanterade domän har tagits bort. Den här resursen krävs för att Azure AD Domain Services ska fungera korrekt.*

**Lösning**

Azure AD Domain Services skapar vissa resurser när de distribueras för att fungera korrekt, inklusive offentliga IP-adresser, nätverkskort och en belastningsutjämnare. Om något av de namn som har tagits bort gör det att din hanterade domän är i ett tillstånd som inte stöds och förhindrar att din domän hanteras. Den här aviseringen visas när någon som kan redigera Azure AD Domain Services resurser tar bort en nödvändig resurs. Följande steg beskriver hur du återställer din hanterade domän.

1. Gå till sidan Azure AD Domain Services hälsa
   1.    Gå till [Azure AD Domain Services sidan](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) i Azure Portal.
   2.    I det vänstra navigerings fältet klickar du på **hälsa**
2. Kontrol lera om aviseringen är mindre än 4 timmar gammal
   1.    På sidan hälsa klickar du på aviseringen med ID- **AADDS109**
   2.    Aviseringen kommer att ha en tidsstämpel för första gången den hittades. Om tidsstämpeln är mindre än 4 timmar sedan, finns det en risk att Azure AD Domain Services kan återskapa den borttagna resursen.
3. Om aviseringen är mer än 4 timmar gammal, är den hanterade domänen i ett oåterkalleligt tillstånd. Du måste ta bort och återskapa Azure AD Domain Services.


## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: Det undernät som är associerat med din hanterade domän är fullt

**Aviserings meddelande:**

*Det undernät som valts för distribution av Azure AD Domain Services är fullt och det finns inget utrymme för den ytterligare domänkontrollant som behöver skapas.*

**Lösning**

Det här felet kan inte återställas. För att lösa problemet måste du [ta bort din befintliga hanterade domän](delete-aadds.md) och [återskapa din hanterade domän](tutorial-create-instance.md)

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Tjänstens huvud namn tillåts inte

**Aviserings meddelande:**

*Ett huvud namn för tjänsten som Azure AD Domain Services använder för att betjäna din domän har inte behörighet att hantera resurser i Azure-prenumerationen. Tjänstens huvud namn måste ha behörighet att betjäna din hanterade domän.*

**Lösning**

Tjänstens huvud namn behöver ha åtkomst för att kunna hantera och skapa resurser i din hanterade domän. Någon har nekat åtkomst till tjänstens huvud namn och nu kan den inte hantera resurser. Följ stegen för att bevilja åtkomst till tjänstens huvud namn.

1. Läs om [RBAC-kontroll och hur du beviljar åtkomst till program på Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
2. Granska åtkomsten till tjänstens huvud namn med ID ```abba844e-bc0e-44b0-947a-dc74e5d09022``` och bevilja åtkomst som nekades vid ett tidigare datum.


## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Det finns inte tillräckligt med IP-adress i den hanterade domänen

**Aviserings meddelande:**

*Vi har identifierat att under nätet för det virtuella nätverket i den här domänen kanske inte har tillräckligt med IP-adresser. Azure AD Domain Services behöver minst två tillgängliga IP-adresser inom under nätet som den är aktive rad i. Vi rekommenderar att ha minst 3-5 extra IP-adresser i under nätet. Detta kan ha inträffat om andra virtuella datorer distribueras i under nätet, så att antalet tillgängliga IP-adresser förbrukas, eller om det finns en begränsning för antalet tillgängliga IP-adresser i under nätet.*

**Lösning**

1. Ta bort din hanterade domän från din klient.
2. Åtgärda IP-adressintervallet för under nätet
   1. Gå till [sidan virtuella nätverk på Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
   2. Välj det virtuella nätverk som du planerar att använda för Azure AD Domain Services.
   3. Klicka på **adress utrymme** under Inställningar
   4. Uppdatera adress intervallet genom att klicka på det befintliga adress intervallet och redigera det eller lägga till ytterligare ett adress intervall. Spara ändringarna.
   5. Klicka på **undernät** i det vänstra navigerings fältet.
   6. Klicka på det undernät som du vill redigera i tabellen.
   7. Uppdatera adress intervallet och spara ändringarna.
3. Följ [komma igång med hjälp av Azure AD Domain Services Guide](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) för att återskapa den hanterade domänen. Se till att du väljer ett virtuellt nätverk med ett privat IP-adressintervall.
4. Följ [den här guiden](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal)för att ansluta dina virtuella datorer till din nya domän.
5. Kontrol lera din domän hälsa på två timmar för att se till att du har slutfört stegen korrekt.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Resurserna kan inte återställas

**Aviserings meddelande:**

*De resurser som används av Azure AD Domain Services har upptäckts i ett oväntat tillstånd och kan inte återställas.*

**Lösning**

Det här felet kan inte återställas. För att lösa problemet måste du [ta bort din befintliga hanterade domän](delete-aadds.md) och [återskapa din hanterade domän](tutorial-create-instance.md).

## <a name="aadds114-subnet-invalid"></a>AADDS114: Undernät är ogiltigt

**Aviserings meddelande:**

*Det undernät som valts för distribution av Azure AD Domain Services är ogiltigt och kan inte användas.*

**Lösning**

Det här felet kan inte återställas. För att lösa problemet måste du [ta bort din befintliga hanterade domän](delete-aadds.md) och [återskapa din hanterade domän](tutorial-create-instance.md).

## <a name="aadds115-resources-are-locked"></a>AADDS115: Resurserna är låsta

**Aviserings meddelande:**

*En eller flera av de nätverks resurser som används av den hanterade domänen kan inte användas eftersom mål omfånget har låsts.*

**Lösning**

1.  Granska Resource Managers åtgärds loggar på nätverks resurserna (detta bör ge information om vilket Lås hindrar ändringar).
2.  Ta bort låsen på resurserna så att Azure AD Domain Services tjänstens huvud namn kan användas på dem.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Resurserna är oanvändbara

**Aviserings meddelande:**

*En eller flera av de nätverks resurser som används av den hanterade domänen kan inte användas på grund av princip begränsningar.*

**Lösning**

1.  Granska Resource Manager-åtgärden loggar på nätverks resurserna för din hanterade domän.
2.  Minska princip begränsningarna för resurserna så att AAD-DS-tjänstens huvud namn kan användas på dem.



## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Synkroniseringen har inte slutförts på ett tag

**Aviserings meddelande:**

*Den hanterade domänen synkroniserades senast med Azure AD på [Date]. Användarna kanske inte kan logga in på den hanterade domänen eller grupp medlemskapen kanske inte är synkroniserade med Azure AD.*

**Lösning**

[Kontrol lera din domäns hälsa](check-health.md) för alla aviseringar som kan tyda på problem i din konfiguration av din hanterade domän. Ibland kan problem med konfigurationen blockera Microsofts möjlighet att synkronisera din hanterade domän. Om du kan lösa eventuella aviseringar väntar du två timmar och kontrollerar tillbaka för att se om synkroniseringen har slutförts.

Här följer några vanliga orsaker till varför synkroniseringen stoppas på hanterade domäner:
- Nätverks anslutningen är blockerad på den hanterade domänen. Läs mer om att kontrol lera om nätverket har problem genom att [Felsöka nätverks säkerhets grupper](alert-nsg.md) och [nätverks krav för Azure AD Domain Services](network-considerations.md).
-  Lösenordssynkronisering har inte kon figurer ATS eller slutförts. Läs [den här artikeln](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)om du vill konfigurera Lösenordssynkronisering.

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: En säkerhets kopia har inte gjorts på ett tag

**Aviserings meddelande:**

*Den hanterade domänen säkerhetskopierades senast [datum].*

**Lösning**

[Kontrol lera din domäns hälsa](check-health.md) för alla aviseringar som kan tyda på problem i din konfiguration av din hanterade domän. Ibland kan problem med konfigurationen blockera Microsofts möjlighet att säkerhetskopiera din hanterade domän. Om du kan lösa eventuella aviseringar väntar du två timmar och kontrollerar tillbaka för att se om säkerhets kopieringen har slutförts.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: SUS Pension på grund av inaktive rad prenumeration

**Aviserings meddelande:**

*Den hanterade domänen har pausats eftersom den Azure-prenumeration som är associerad med domänen inte är aktiv.*

**Lösning**

> [!WARNING]
> Om din hanterade domän är inaktive rad under en längre tid är det risk att du tar bort den. Det är bäst att åtgärda uppskjutningen så snart som möjligt. Läs mer i [den här artikeln](suspension.md).

Om du vill återställa tjänsten [förnyar du din Azure-prenumeration](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable) som är kopplad till din hanterade domän.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: SUS Pension på grund av en ogiltig konfiguration

**Aviserings meddelande:**

*Den hanterade domänen har pausats på grund av en ogiltig konfiguration. Tjänsten har inte kunnat hantera, korrigera eller uppdatera domän kontrol Lanterna för din hanterade domän under en längre tid.*

**Lösning**

> [!WARNING]
> Om din hanterade domän är inaktive rad under en längre tid är det risk att du tar bort den. Det är bäst att åtgärda uppskjutningen så snart som möjligt. Läs mer i [den här artikeln](suspension.md).

[Kontrol lera din domäns hälsa](check-health.md) för alla aviseringar som kan tyda på problem i din konfiguration av din hanterade domän. Om du kan lösa dessa aviseringar gör du det. Efter kontaktar du supporten för att återaktivera din prenumeration.


## <a name="contact-us"></a>Kontakta oss
Kontakta Azure Active Directory Domain Services produkt teamet för att [dela feedback eller för support](contact-us.md).
