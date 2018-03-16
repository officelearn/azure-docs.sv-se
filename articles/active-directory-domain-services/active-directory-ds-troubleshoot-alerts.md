---
title: "Azure Active Directory Domain Services: Felsöka aviseringar | Microsoft Docs"
description: "Felsökning av aviseringar i Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: ergreenl
ms.openlocfilehash: e4b8f31fe3eb79f9b38ae01af598290582a2cde3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services - felsökning av aviseringar
Den här artikeln innehåller felsökning guider för alla aviseringar som kan uppstå på din hanterade domän.


Välj felsökningsstegen som är kopplade till ID eller meddelande i aviseringen.

| **Varnings-ID.** | **Aviseringsmeddelande** | **Lösning** |
| --- | --- | :--- |
| AADDS001 | *Säker LDAP via internet har aktiverats för den hanterade domänen. Åtkomst till port 636 är inte låst nedåt med hjälp av en nätverkssäkerhetsgrupp. Det kan exponera användarkonton på den hanterade domänen att lösenord brute force-attacker.* | [Felaktig säker LDAP-konfiguration](active-directory-ds-troubleshoot-ldaps.md) |
| AADDS100 | *Azure AD-katalog som är associerade med din hanterade domän kan ha tagits bort. Den hanterade domänen inte längre i en konfiguration som stöds. Microsoft kan övervaka, hantera, korrigera och synkronisera din hanterade domän.* | [Saknas katalog](#aadds100-missing-directory) |
| AADDS101 | *Azure AD Domain Services kan inte aktiveras i en Azure AD B2C-katalog.* | [Azure AD B2C körs i den här katalogen](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Ett huvudnamn för tjänsten som krävs för Azure AD Domain Services ska fungera korrekt har tagits bort från Azure AD-katalogen. Den här konfigurationen påverkar Microsofts möjlighet att övervaka, hantera, korrigering, och synkronisera din hanterade domän.* | [Tjänstens huvudnamn saknas](active-directory-ds-troubleshoot-service-principals.md) |
| AADDS103 | *IP-adressintervall för det virtuella nätverket som du har aktiverat Azure AD Domain Services är i en offentlig IP-adressintervallet. Azure AD Domain Services måste aktiveras i ett virtuellt nätverk med en privat IP-adressintervall. Den här konfigurationen påverkar Microsofts möjlighet att övervaka, hantera, korrigeringsfil och synkronisera din hanterade domän.* | [Adressen är i en offentlig IP-adressintervall](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *Microsoft kan inte nå domänkontrollanterna för den här hanterade domänen. Detta kan inträffa om en nätverkssäkerhetsgrupp (NSG) som har konfigurerats på din virtuella nätverk blockerar åtkomst till den hanterade domänen. En annan möjlig orsak är att om det finns en användardefinierad väg som blockerar inkommande trafik från internet.* | [Nätverksfel](active-directory-ds-troubleshoot-nsg.md) |
| AADDS105 | *Tjänstens huvudnamn med program-ID ”d87dcbc6-a371-462e-88e3-28ad15ec4e64” har tagits bort och Microsoft gick att skapa den på nytt. Den här tjänstens huvudnamn hanterar en annan tjänstens huvudnamn och ett program som används för synkronisering av lösenord. Den hanterade tjänstens huvudnamn program tillåts inte under nyligen skapade tjänstens huvudnamn och kommer att bli inaktuell när synkroniseringen certifikatet upphör att gälla. Detta innebär att nyligen skapade tjänstens huvudnamn inte kommer att uppdatera gamla hanterade program och synkronisering av objekt från AAD kommer att påverkas.* | [Programmet för synkronisering av lösenord är inaktuell](active-directory-ds-troubleshoot-service-principals.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS500 | *Den hanterade domänen senast synkroniserades med Azure AD [dag]. Användare kan inte logga in på den hanterade domänen eller gruppmedlemskap kanske inte är synkroniserad med Azure AD.* | [Synkronisering har inte utförts på ett tag](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *Den hanterade domänen senast säkerhetskopierades [dag].* | [En säkerhetskopiering har inte utförts på ett tag](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *Säkert LDAP-certifikatet för den hanterade domänen upphör XX.* | [Att säkra LDAP-certifikat](active-directory-ds-troubleshoot-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *Den hanterade domänen har pausats eftersom Azure-prenumerationen som är kopplade till domänen inte är aktiv.* | [Upphävande på grund av inaktiverad prenumeration](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *Den hanterade domänen har pausats på grund av en ogiltig konfiguration. Tjänsten har inte kan hantera, korrigering, eller uppdatera domänkontrollanterna för din hanterade domän under en längre tid.* | [Upphävande på grund av en ogiltig konfiguration](#aadds504-suspension-due-to-an-invalid-configuration) |


## <a name="aadds100-missing-directory"></a>AADDS100: Saknas directory
**Varningsmeddelande:**

*Azure AD-katalog som är associerade med din hanterade domän kan ha tagits bort. Den hanterade domänen inte längre i en konfiguration som stöds. Microsoft kan övervaka, hantera, korrigera och synkronisera din hanterade domän.*

**Lösning:**

Det här felet beror vanligtvis på felaktigt flyttar din Azure-prenumeration till en ny Azure AD-katalog och ta bort gammalt Azure AD-katalog som fortfarande är associerat med Azure AD Domain Services.

Det här felet är oåterkalleligt. Lös, måste du [ta bort den befintliga Hantera domänen](active-directory-ds-disable-aadds.md) och återskapa den i den nya katalogen. Kontakta Produktteamet för Azure Active Directory Domain Services om du har problem med att ta bort [för support](active-directory-ds-contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C körs i den här katalogen
**Varningsmeddelande:**

*Azure AD Domain Services kan inte aktiveras i en Azure AD B2C-katalog.*

**Lösning:**

>[!NOTE]
>För att fortsätta använda Azure AD Domain Services, måste du återskapa din Azure AD Domain Services-instans i en icke - Azure AD B2C-katalog.

Följ dessa steg om du vill återställa din tjänst:

1. [Ta bort den Hantera domänen](active-directory-ds-disable-aadds.md) från befintliga Azure AD-katalogen.
2. Skapa en ny katalog som inte är en Azure AD B2C-katalog.
3. Följ den [komma igång](active-directory-ds-getting-started.md) guide för att skapa en hanterad domän.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Adressen är i en offentlig IP-adressintervall

**Varningsmeddelande:**

*IP-adressintervall för det virtuella nätverket som du har aktiverat Azure AD Domain Services är i en offentlig IP-adressintervallet. Azure AD Domain Services måste aktiveras i ett virtuellt nätverk med en privat IP-adressintervall. Den här konfigurationen påverkar Microsofts möjlighet att övervaka, hantera, korrigeringsfil och synkronisera din hanterade domän.*

**Lösning:**

> [!NOTE]
> Om du vill åtgärda det här problemet måste du ta bort den befintliga Hantera domänen och skapa den igen i ett virtuellt nätverk med en privat IP-adressintervall. Den här processen är störande.

Innan du börjar läsa den **privat IP v4-adressutrymmet** i avsnittet [i den här artikeln](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

I det virtuella nätverket kan datorer gör förfrågningar till Azure-resurser som finns i samma IP-adressintervall som de som konfigurerats för undernätet. Men eftersom det virtuella nätverket har konfigurerats för det här intervallet kan dessa begäranden skickas vidare i det virtuella nätverket och kommer inte att nå avsedda webbresurser. Den här konfigurationen kan leda till oväntade fel med Azure AD Domain Services.

**Om du äger IP-adressintervall i internet som konfigureras i det virtuella nätverket kan den här aviseringen ignoreras. Dock Azure AD Domain Services kan inte användas i [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] med den här konfigurationen eftersom det kan leda till oväntade fel.**


1. [Ta bort den Hantera domänen](active-directory-ds-disable-aadds.md) från din katalog.
2. Åtgärda IP-adressintervall för undernätet
  1. Navigera till den [sidan för virtuella nätverk på Azure portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
  2. Välj det virtuella nätverket som du planerar att använda för Azure AD Domain Services.
  3. Klicka på **adressutrymmet** under inställningar
  4. Uppdatera adressintervallet genom att klicka på det befintliga adressintervallet och redigerar den eller lägga till ett ytterligare adressintervall. Kontrollera att det nya adressintervallet är i ett privat IP-adressintervall. Spara ändringarna.
  5. Klicka på **undernät** i det vänstra navigeringsfönstret.
  6. Klicka på det undernät som du vill redigera i tabellen.
  7. Uppdatera adressintervallet och spara ändringarna.
3. Följ [guiden komma igång med Azure AD Domain Services](active-directory-ds-getting-started.md) att återskapa din hanterade domän. Se till att du väljer ett virtuellt nätverk med en privat IP-adressintervall.
4. Domänanslutning dina virtuella datorer till den nya domänen, så [handboken](active-directory-ds-admin-guide-join-windows-vm-portal.md).
8. Kontrollera hälsotillståndet för din domän på två timmar för att säkerställa aviseringen har lösts.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Synkronisering har inte slutförts i en stund

**Varningsmeddelande:**

*Den hanterade domänen senast synkroniserades med Azure AD [dag]. Användare kan inte logga in på den hanterade domänen eller gruppmedlemskap kanske inte är synkroniserad med Azure AD.*

**Lösning:**

[Kontrollera din domän hälsa](active-directory-ds-check-health.md) för eventuella aviseringar som kan tyda på problem i konfigurationen för din hanterade domän. Problem med konfigurationen kan ibland blockera Microsofts möjlighet att synkronisera din hanterade domän. Om du ska kunna lösa alla aviseringar, vänta tillbaka två timmar och kontrollera om du vill se om synkroniseringen har slutförts.


## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: En säkerhetskopiering inte har utförts i en stund

**Varningsmeddelande:**

*Den hanterade domänen senast säkerhetskopierades [dag].*

**Lösning:**

[Kontrollera din domän hälsa](active-directory-ds-check-health.md) för eventuella aviseringar som kan tyda på problem i konfigurationen för din hanterade domän. Problem med konfigurationen kan ibland blockera Microsofts möjlighet att synkronisera din hanterade domän. Om du ska kunna lösa alla aviseringar, vänta tillbaka två timmar och kontrollera om du vill se om synkroniseringen har slutförts.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Tillfälligt på grund av inaktiverad prenumeration

**Varningsmeddelande:**

*Den hanterade domänen har pausats eftersom Azure-prenumerationen som är kopplade till domänen inte är aktiv.*

**Lösning:**

Att återställa din tjänst [förnya prenumerationen Azure](https://docs.microsoft.com/en-us/azure/billing/billing-subscription-become-disable) som är associerade med din hanterade domän.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Tillfälligt på grund av en ogiltig konfiguration

**Varningsmeddelande:**

*Den hanterade domänen har pausats på grund av en ogiltig konfiguration. Tjänsten har inte kan hantera, korrigering, eller uppdatera domänkontrollanterna för din hanterade domän under en längre tid.*

**Lösning:**

[Kontrollera din domän hälsa](active-directory-ds-check-health.md) för eventuella aviseringar som kan tyda på problem i konfigurationen för din hanterade domän. Om du kan lösa några av dessa varningar, göra det. Kontakta supporten om du vill aktivera prenumerationen igen efter.

## <a name="contact-us"></a>Kontakta oss
Kontakta produktteamet Azure Active Directory Domain Services för att [dela feedback eller support](active-directory-ds-contact-us.md).
