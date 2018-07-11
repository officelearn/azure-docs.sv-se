---
title: 'Azure Active Directory Domain Services: Felsöka aviseringar | Microsoft Docs'
description: Felsöka aviseringar för Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: ergreenl
ms.openlocfilehash: 360c6c98227e52f0540b00ef136888d3d143b9fb
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951082"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services - felsöka aviseringar
Den här artikeln innehåller felsökningsguider för alla aviseringar som kan uppstå på din hanterade domän.


Välj felsökningsstegen som relaterar till ID eller meddelande i aviseringen.

| **Aviserings-ID** | **Varningsmeddelande** | **Lösning** |
| --- | --- | :--- |
| AADDS001 | *Säkert LDAP över internet har aktiverats för den hanterade domänen. Åtkomst till port 636 är dock inte låst nedåt med hjälp av en nätverkssäkerhetsgrupp. Detta kan medföra att användarkonton på den hanterade domänen för lösenord brute force-attacker.* | [Felaktig säker LDAP-konfiguration](active-directory-ds-troubleshoot-ldaps.md) |
| AADDS100 | *Azure AD-katalog som är associerade med den hanterade domänen kan ha tagits bort. Den hanterade domänen är inte längre i en konfiguration som stöds. Microsoft kan inte övervaka, hantera, korrigera och synkronisera din hanterade domän.* | [Saknas directory](#aadds100-missing-directory) |
| AADDS101 | *Azure AD Domain Services kan inte aktiveras i en Azure AD B2C-katalog.* | [Azure AD B2C körs i den här katalogen](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Ett huvudnamn för tjänsten som krävs för Azure AD Domain Services ska fungera korrekt har tagits bort från Azure AD-katalogen. Den här konfigurationen påverkar Microsofts förmåga att övervaka, hantera, korrigera och synkronisera din hanterade domän.* | [Tjänstens huvudnamn som saknas](active-directory-ds-troubleshoot-service-principals.md) |
| AADDS103 | *IP-adressintervallet för det virtuella nätverket där du har aktiverat Azure AD Domain Services är i en offentlig IP-adressintervallet. Azure AD Domain Services måste aktiveras i ett virtuellt nätverk med en privat IP-adressintervall. Den här konfigurationen påverkar Microsofts förmåga att övervaka, hantera, korrigera och synkronisera din hanterade domän.* | [Adressen är i en offentlig IP-adressintervall](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *Microsoft kan inte nå domänkontrollanterna för den här hanterade domänen. Detta kan inträffa om en nätverkssäkerhetsgrupp (NSG) som har konfigurerats på ditt virtuella nätverk blockerar åtkomsten till den hanterade domänen. En annan möjlig orsak är att om det finns en användardefinierad väg som blockerar inkommande trafik från internet.* | [Nätverksfel](active-directory-ds-troubleshoot-nsg.md) |
| AADDS105 | *Tjänstens huvudnamn med program-ID ”d87dcbc6-a371-462e-88e3-28ad15ec4e64” har tagits bort och sedan återskapas. Återskapning lämnas bakom inkonsekvent behörigheter på Azure AD Domain Services-resurser som behövs för att underhålla din hanterade domän. Synkronisering av lösenord på den hanterade domänen kan påverkas.* | [Programmet för synkronisering av lösenord är inaktuell](active-directory-ds-troubleshoot-service-principals.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS500 | *Den hanterade domänen senast synkroniserades med Azure AD på [date]. Användare kan inte logga in på den hanterade domänen eller gruppmedlemskap kanske inte är synkroniserad med Azure AD.* | [Synkronisering inte har utförts på ett tag](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *Den hanterade domänen senast säkerhetskopierades på [date].* | [En säkerhetskopia som inte har vidtagits på ett tag](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *Certifikatet för säkert LDAP för den hanterade domänen upphör att gälla [date]].* | [Upphör att gälla certifikatet för säkert LDAP](active-directory-ds-troubleshoot-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *Den hanterade domänen har inaktiverats eftersom den Azure-prenumeration som är associerade med domänen inte är aktiv.* | [Inaktiveringen på grund av inaktiverad prenumeration](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *Den hanterade domänen har pausats på grund av en ogiltig konfiguration. Tjänsten har inte kan hantera, korrigera eller uppdatera domänkontrollanterna för den hanterade domänen under en längre tid.* | [Inaktiveringen på grund av en ogiltig konfiguration](#aadds504-suspension-due-to-an-invalid-configuration) |


## <a name="aadds100-missing-directory"></a>AADDS100: Saknas directory
**Varningsmeddelande:**

*Azure AD-katalog som är associerade med den hanterade domänen kan ha tagits bort. Den hanterade domänen är inte längre i en konfiguration som stöds. Microsoft kan inte övervaka, hantera, korrigera och synkronisera din hanterade domän.*

**Lösning:**

Det här felet beror vanligtvis på felaktigt flyttar din Azure-prenumeration till en ny Azure AD-katalog och ta bort gammalt Azure AD-katalog som är fortfarande associerat med Azure AD Domain Services.

Det här felet är ett oåterkalleligt. För att lösa, måste du [ta bort den befintliga Hantera domänen](active-directory-ds-disable-aadds.md) och återskapar den i den nya katalogen. Om du har problem med att ta bort, kontakta produktteamet Azure Active Directory Domain Services [för support](active-directory-ds-contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C körs i den här katalogen
**Varningsmeddelande:**

*Azure AD Domain Services kan inte aktiveras i en Azure AD B2C-katalog.*

**Lösning:**

>[!NOTE]
>För att fortsätta använda Azure AD Domain Services, måste du återskapa din Azure AD Domain Services-instans i en icke - Azure AD B2C-katalog.

Följ dessa steg om du vill återställa din tjänst:

1. [Ta bort den Hantera domänen](active-directory-ds-disable-aadds.md) från din befintliga Azure AD-katalog.
2. Skapa en ny katalog som inte är en Azure AD B2C-katalog.
3. Följ den [komma igång](active-directory-ds-getting-started.md) guide för att återskapa en hanterad domän.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Adressen är i en offentlig IP-adressintervall

**Varningsmeddelande:**

*IP-adressintervallet för det virtuella nätverket där du har aktiverat Azure AD Domain Services är i en offentlig IP-adressintervallet. Azure AD Domain Services måste aktiveras i ett virtuellt nätverk med en privat IP-adressintervall. Den här konfigurationen påverkar Microsofts förmåga att övervaka, hantera, korrigera och synkronisera din hanterade domän.*

**Lösning:**

> [!NOTE]
> För att åtgärda problemet måste du ta bort den befintliga Hantera domänen och återskapa det i ett virtuellt nätverk med en privat IP-adressintervall. Den här processen är störande.

Innan du kan läsa den **privat IP v4-adressutrymme** i avsnittet [i den här artikeln](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

I det virtuella nätverket, kan datorer gör förfrågningar till Azure-resurser som finns i samma IP-adressintervall som de som konfigurerats för undernätet. Eftersom det virtuella nätverket har konfigurerats för det här intervallet, kan dessa begäranden dirigeras inom det virtuella nätverket och kommer inte att nå de avsedda webbresurser Den här konfigurationen kan leda till oväntade fel med Azure AD Domain Services.

**Om du äger IP-adressintervall i internet som konfigurerats i ditt virtuella nätverk kan kan den här varningen ignoreras. Dock Azure AD Domain Services kan inte bekräfta att den [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] med den här konfigurationen eftersom det kan leda till oväntade fel.**


1. [Ta bort den Hantera domänen](active-directory-ds-disable-aadds.md) från din katalog.
2. Åtgärda IP-adressintervall för undernätet
  1. Navigera till den [virtuella nätverk-sidan på Azure portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
  2. Välj det virtuella nätverket som du planerar att använda för Azure AD Domain Services.
  3. Klicka på **adressutrymme** under inställningar
  4. Uppdatera adressintervallet genom att klicka på befintliga adressintervallet och redigera eller lägga till ett ytterligare adressintervall. Kontrollera att nya adressintervallet är i ett privat IP-adressintervall. Spara ändringarna.
  5. Klicka på **undernät** i det vänstra navigeringsfältet.
  6. Klicka på det undernät som du vill redigera i tabellen.
  7. Uppdatera adressintervallet och spara dina ändringar.
3. Följ [guiden komma igång med Azure AD Domain Services](active-directory-ds-getting-started.md) att återskapa din hanterade domän. Se till att du väljer ett virtuellt nätverk med en privat IP-adressintervall.
4. Domänanslutning dina virtuella datorer till den nya domänen, så [den här guiden](active-directory-ds-admin-guide-join-windows-vm-portal.md).
8. Säkerställ att aviseringen har lösts genom att kontrollera hälsan för domänen i två timmar.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Synkronisering har inte slutförts på ett tag

**Varningsmeddelande:**

*Den hanterade domänen senast synkroniserades med Azure AD på [date]. Användare kan inte logga in på den hanterade domänen eller gruppmedlemskap kanske inte är synkroniserad med Azure AD.*

**Lösning:**

[Kontrollera hälsan för domänen](active-directory-ds-check-health.md) för alla aviseringar som kan tyda på problem i konfigurationen för den hanterade domänen. Ibland kan problem med din konfiguration blockera Microsofts förmåga att synkronisera din hanterade domän. Om du vill lösa alla aviseringar, vänta igen två timmar och kontrollera för att se om synkroniseringen har slutförts.

Här följer några vanliga orsaker till varför synkronisering stoppar på hanterade domäner:
- Nätverksanslutningen är blockerad på den hanterade domänen. Mer information om kontroll av nätverket för problem, Läs om hur du [felsöka Nätverkssäkerhetsgrupper](active-directory-ds-troubleshoot-nsg.md) och [krav för Azure AD Domain Services](active-directory-ds-networking.md).
-  Lösenordssynkronisering har aldrig konfigurera eller har slutförts. Om du vill konfigurera Lösenordssynkronisering läsa [i den här artikeln](active-directory-ds-getting-started-password-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: En säkerhetskopiering inte har utförts på ett tag

**Varningsmeddelande:**

*Den hanterade domänen senast säkerhetskopierades på [date].*

**Lösning:**

[Kontrollera hälsan för domänen](active-directory-ds-check-health.md) för alla aviseringar som kan tyda på problem i konfigurationen för den hanterade domänen. Ibland kan problem med din konfiguration blockera Microsofts förmåga att synkronisera din hanterade domän. Om du vill lösa alla aviseringar, vänta igen två timmar och kontrollera för att se om synkroniseringen har slutförts.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Inaktivering på grund av inaktiverad prenumeration

**Varningsmeddelande:**

*Den hanterade domänen har inaktiverats eftersom den Azure-prenumeration som är associerade med domänen inte är aktiv.*

**Lösning:**

> [!WARNING]
> Om din hanterade domän pausas under en längre tid, är det riskerar att tas bort. Det är bäst att åtgärda inaktiveringen så snabbt som möjligt. Om du vill läsa mer, besök [i den här artikeln](active-directory-ds-suspension.md).

Att återställa din tjänst, [förnya din Azure-prenumeration](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable) som är associerade med din hanterade domän.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Inaktivering på grund av en ogiltig konfiguration

**Varningsmeddelande:**

*Den hanterade domänen har pausats på grund av en ogiltig konfiguration. Tjänsten har inte kan hantera, korrigera eller uppdatera domänkontrollanterna för den hanterade domänen under en längre tid.*

**Lösning:**

> [!WARNING]
> Om din hanterade domän pausas under en längre tid, är det riskerar att tas bort. Det är bäst att åtgärda inaktiveringen så snabbt som möjligt. Om du vill läsa mer, besök [i den här artikeln](active-directory-ds-suspension.md).

[Kontrollera hälsan för domänen](active-directory-ds-check-health.md) för alla aviseringar som kan tyda på problem i konfigurationen för den hanterade domänen. Om du kan matcha någon av dessa aviseringar, göra det. Kontakta supporten om du vill återaktivera din prenumeration efter.


## <a name="contact-us"></a>Kontakta oss
Kontakta Azure Active Directory Domain Services-produktteamet att [dela feedback eller support](active-directory-ds-contact-us.md).
