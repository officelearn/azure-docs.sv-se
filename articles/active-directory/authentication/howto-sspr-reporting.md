---
title: Rapporter om återställning av lösenord med självbetjäning – Azure Active Directory
description: Rapportering om azure ad-händelser för återställning av lösenord för självbetjäning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/01/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 348082ad96a5efa4d8f866c3675044edf7b6f8a8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652159"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Rapporteringsalternativ för Azure AD-lösenordshantering

Efter distributionen vill många organisationer veta hur eller om självbetjäningslösenordsåterställning (SSPR) verkligen används. Rapporteringsfunktionen som Azure Active Directory (Azure AD) tillhandahåller hjälper dig att svara på frågor med hjälp av färdiga rapporter. Om du är licensierad på rätt sätt kan du också skapa anpassade frågor.

![Rapportering om SSPR med hjälp av granskningsloggarna i Azure AD][Reporting]

Följande frågor kan besvaras av de rapporter som finns i [Azure-portalen:](https://portal.azure.com/)

> [!NOTE]
> Du måste vara [en global administratör](../users-groups-roles/directory-assign-admin-roles.md)och du måste välja att dessa data ska samlas in för din organisations räkning. Om du vill anmäla dig måste du besöka fliken **Rapportering** eller granskningsloggarna minst en gång. Fram till dess samlas inte data in för din organisation.
>

* Hur många personer har registrerat sig för återställning av lösenord?
* Vem har registrerat sig för återställning av lösenord?
* Vilka uppgifter registrerar sig för personer?
* Hur många har återställt sina lösenord de senaste sju dagarna?
* Vilka är de vanligaste metoderna som användare eller administratörer använder för att återställa sina lösenord?
* Vilka är vanliga problem som användare eller administratörer ställs inför när de försöker använda återställning av lösenord?
* Vilka administratörer återställer sina egna lösenord ofta?
* Finns det någon misstänkt aktivitet som pågår med återställning av lösenord?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Så här visar du lösenordshanteringsrapporter i Azure Portal

I Azure-portalen har vi förbättrat sättet som du kan visa registreringsaktivitet för lösenordsåterställning och registrering av lösenordsåterställning. Använd följande steg för att hitta lösenordsåterställning och registreringshändelser för återställning av lösenord:

1. Bläddra till [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** i den vänstra rutan.
3. Sök efter **Azure Active Directory** i listan över tjänster och välj den.
4. Välj **Användare** i avsnittet Hantera.
5. Välj **Granskningsloggar** från bladet **Användare.** Detta visar alla granskningshändelser som inträffade mot alla användare i katalogen. Du kan filtrera den här vyn om du vill visa alla lösenordsrelaterade händelser.
6. Välj listrutan **Tjänst** på **filtermenyn** högst upp i fönstret och ändra den till tjänsttypen **Lösenordshantering.**
7. Du kan också filtrera listan ytterligare genom att välja den specifika **aktivitet** du är intresserad av.

### <a name="converged-registration-preview"></a>Konvergerad registrering (förhandsgranskning)

Om du deltar i den offentliga förhandsversionen av konvergerad registrering, finns information om användaraktivitet i granskningsloggarna under > **Säkerhetsautentiseringsmetoder**. **Security**

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Beskrivning av rapportkolumnerna i Azure-portalen

I följande lista beskrivs var och en av rapportkolumnerna i Azure-portalen i detalj:

* **Användare**: Användaren som försökte sig på en registreringsåtgärd för återställning av lösenord.
* **Roll**: Användarens roll i katalogen.
* **Datum och tid**: Datum och tid för försöket.
* **Registrerade data:** De autentiseringsdata som användaren angav vid registrering av återställning av lösenord.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Beskrivning av rapportvärdena i Azure-portalen

I följande tabell beskrivs de olika värden som du kan ange för varje kolumn i Azure-portalen:

| Kolumn | Tillåtna värden och deras betydelser |
| --- | --- |
| Registrerade uppgifter |**Alternativ e-post:** Användaren använde ett alternativt e-postmeddelande eller ett autentiseringsmeddelande för att autentisera.<p><p>**Office-telefon:** Användaren använde en kontorstelefon för att autentisera.<p>**Mobiltelefon**: Användaren använde en mobiltelefon eller autentiseringstelefon för att autentisera.<p>**Säkerhetsfrågor**: Användaren använde säkerhetsfrågor för att autentisera.<p>**En kombination av tidigare metoder, till exempel alternativa e-post + mobiltelefon:** Inträffar när en två-gate princip anges och visar vilka två metoder användaren använde för att autentisering deras begäran om återställning av lösenord. |

## <a name="self-service-password-management-activity-types"></a>Aktivitetstyper för lösenordshantering med självbetjäning

Följande aktivitetstyper visas i granskningshändelsekategorin **Självbetjäningsgranskning:**

* [Blockerad från återställning av lösenord för självbetjäning](#activity-type-blocked-from-self-service-password-reset): Anger att en användare försökte återställa ett lösenord, använda en viss grind eller validera ett telefonnummer som är mer än fem gånger totalt på 24 timmar.
* [Ändra lösenord (självbetjäning):](#activity-type-change-password-self-service)Anger att en användare har utfört en frivillig eller tvingad (på grund av att lösenordsändringen har upphört att gälla).
* [Återställ lösenord (av administratör):](#activity-type-reset-password-by-admin)Anger att en administratör har utfört en återställning av lösenord för en användares räkning från Azure-portalen.
* [Återställ lösenord (självbetjäning):](#activity-type-reset-password-self-service)Anger att en användare har återställt sitt lösenord från [Azure AD-portalen](https://passwordreset.microsoftonline.com)för återställning av lösenord .
* [Självbetjäningsflödesförlopp](#activity-type-self-serve-password-reset-flow-activity-progress)för återställning av lösenordsaktivitet: Anger varje specifikt steg som en användare fortsätter genom, till exempel att skicka en specifik autentiseringsport för återställning av lösenord, som en del av processen för återställning av lösenord.
* [Lås upp användarkonto (självbetjäning)](#activity-type-unlock-a-user-account-self-service)): Anger att en användare har låst upp sitt Active Directory-konto utan att återställa sitt lösenord från [Azure AD-portalen](https://passwordreset.microsoftonline.com) för återställning av lösenord med hjälp av Active Directory-funktionen för kontoupplåsning utan återställning.
* [Användare registrerad för återställning av lösenord för självbetjäning](#activity-type-user-registered-for-self-service-password-reset): Anger att en användare har registrerat all nödvändig information för att kunna återställa sitt lösenord i enlighet med den för närvarande angivna klientens lösenordsåterställningsprincip.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Aktivitetstyp: Blockerad från återställning av lösenord för självbetjäning

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivning**: Anger att en användare försökte återställa ett lösenord, använda en viss grind eller validera ett telefonnummer som är mer än fem gånger totalt på 24 timmar.
* **Aktivitetsaktör**: Användaren som begränsades från att utföra ytterligare återställningsåtgärder. Användaren kan vara en slutanvändare eller administratör.
* **Aktivitetsmål**: Användaren som begränsades från att utföra ytterligare återställningsåtgärder. Användaren kan vara en slutanvändare eller administratör.
* **Aktivitetsstatus**:
  * _Framgång_: Anger att en användare har begränsats från att utföra ytterligare återställningar, försöka sig på ytterligare autentiseringsmetoder eller validera ytterligare telefonnummer under de närmaste 24 timmarna.
* **Orsak till aktivitetsstatusfel**: Ej tillämpligt.

### <a name="activity-type-change-password-self-service"></a>Aktivitetstyp: Ändra lösenord (självbetjäning)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivning**: Anger att en användare utförde en frivillig eller påtvingad (på grund av att det har upphört att gälla) lösenordsändring.
* **Aktivitetsskådespelare**: Användaren som ändrade sitt lösenord. Användaren kan vara en slutanvändare eller administratör.
* **Aktivitetsmål**: Användaren som ändrade sitt lösenord. Användaren kan vara en slutanvändare eller administratör.
* **Aktivitetsstatus:**
  * _Framgång_: Anger att en användare har ändrat sitt lösenord.
  * _Fel_: Anger att en användare inte kunde ändra sitt lösenord. Du kan välja raden om du vill visa kategorin **Aktivitetsstatusorsak** om du vill veta mer om varför felet uppstod.
* **Orsak till aktivitetsstatusfel:**
  * _FuzzyPolicyViolationInvalidPassword_: Användaren valde ett lösenord som automatiskt förbjöds eftersom Microsoft Banned Password Detection-funktionerna ansåg att det var för vanligt eller särskilt svagt.

### <a name="activity-type-reset-password-by-admin"></a>Aktivitetstyp: Återställ lösenord (efter administratör)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivning**: Anger att en administratör har utfört en lösenordsåterställning för en användares räkning från Azure-portalen.
* **Aktivitetsaktör**: Administratören som utförde återställningen av lösenordet för en annan slutanvändares eller administratörs räkning. Måste vara en lösenordsadministratör, användaradministratör eller helpdesk administratör.
* **Aktivitetsmål**: Den användare vars lösenord återställdes. Användaren kan vara en slutanvändare eller en annan administratör.
* **Aktivitetsstatus:**
  * _Framgång_: Anger att en administratör har återställt en användares lösenord.
  * _Fel_: Anger att en administratör inte kunde ändra en användares lösenord. Du kan välja raden om du vill visa kategorin **Aktivitetsstatusorsak** om du vill veta mer om varför felet uppstod.

### <a name="activity-type-reset-password-self-service"></a>Aktivitetstyp: Återställ lösenord (självbetjäning)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivning**: Anger att en användare har återställt sitt lösenord från [Azure AD-portalen](https://passwordreset.microsoftonline.com)för återställning av lösenord .
* **Aktivitetsskådespelare**: Användaren som återställde sitt lösenord. Användaren kan vara en slutanvändare eller administratör.
* **Aktivitetsmål**: Användaren som återställde sitt lösenord. Användaren kan vara en slutanvändare eller administratör.
* **Aktivitetsstatus:**
  * _Framgång_: Anger att en användare har återställt sitt eget lösenord.
  * _Fel_: Anger att en användare inte kunde återställa sitt eget lösenord. Du kan välja raden om du vill visa kategorin **Aktivitetsstatusorsak** om du vill veta mer om varför felet uppstod.
* **Orsak till aktivitetsstatusfel:**
  * _FuzzyPolicyViolationInvalidPassword_: Admin valde ett lösenord som automatiskt förbjöds eftersom Microsoft Banned Password Detection funktioner fann det vara för vanligt eller särskilt svag.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Aktivitetstyp: Självbetjäning av flödesaktivitet för återställning av lösenord

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivning**: Anger varje specifikt steg som en användare fortsätter igenom (t.ex. passerar en specifik autentiseringsport för lösenordsåterställning) som en del av processen för återställning av lösenord.
* **Aktivitetsskådespelare**: Användaren som utförde en del av flödet för återställning av lösenord. Användaren kan vara en slutanvändare eller administratör.
* **Aktivitetsmål**: Användaren som utförde en del av flödet för återställning av lösenord. Användaren kan vara en slutanvändare eller administratör.
* **Aktivitetsstatus:**
  * _Framgång_: Anger att en användare har slutfört ett visst steg i flödet för återställning av lösenord.
  * _Fel_: Anger att ett visst steg i flödet för återställning av lösenord misslyckades. Du kan välja raden om du vill visa kategorin **Aktivitetsstatusorsak** om du vill veta mer om varför felet uppstod.
* **Aktivitetsstatusorsaker**: Se följande tabell för [alla tillåtna statusorsaker för återställningsaktivitet](#description-of-the-report-columns-in-the-azure-portal).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Aktivitetstyp: Lås upp ett användarkonto (självbetjäning)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivning**: Anger att en användare har låst upp sitt Active Directory-konto utan att återställa sitt lösenord från [Azure AD-portalen](https://passwordreset.microsoftonline.com) för återställning av lösenord med hjälp av Active Directory-funktionen för kontoupplåsning utan återställning.
* **Aktivitetsskådespelare**: Användaren som låste upp sitt konto utan att återställa sitt lösenord. Användaren kan vara en slutanvändare eller administratör.
* **Aktivitetsmål:** Användaren som låste upp sitt konto utan att återställa sitt lösenord. Användaren kan vara en slutanvändare eller administratör.
* **Tillåtna aktivitetsstatus:**
  * _Framgång_: Anger att en användare har låst upp sitt eget konto.
  * _Fel_: Anger att en användare inte kunde låsa upp sitt konto. Du kan välja raden om du vill visa kategorin **Aktivitetsstatusorsak** om du vill veta mer om varför felet uppstod.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Aktivitetstyp: Användare registrerad för återställning av lösenord för självbetjäning

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivning**: Anger att en användare har registrerat all nödvändig information för att kunna återställa sitt lösenord i enlighet med den för närvarande angivna klientens lösenordsåterställningsprincip. 
* **Aktivitetsskådespelare**: Användaren som registrerade sig för återställning av lösenord. Användaren kan vara en slutanvändare eller administratör.
* **Aktivitetsmål**: Användaren som registrerade sig för återställning av lösenord. Användaren kan vara en slutanvändare eller administratör.
* **Tillåtna aktivitetsstatus:**
  * _Framgång_: Anger att en användare har registrerats för återställning av lösenord i enlighet med den aktuella principen. 
  * _Fel_: Anger att en användare inte kunde registrera sig för återställning av lösenord. Du kan välja raden om du vill visa kategorin **Aktivitetsstatusorsak** om du vill veta mer om varför felet uppstod.

     >[!NOTE]
     >Ett fel betyder inte att en användare inte kan återställa sitt eget lösenord. Det betyder att de inte avslutade registreringsprocessen. Om det finns obekräftade data på deras konto som är korrekta, till exempel ett telefonnummer som inte är validerat, även om de inte har verifierat det här telefonnumret, kan de fortfarande använda det för att återställa sitt lösenord.

## <a name="next-steps"></a>Nästa steg

* [SSPR och MFA användning och insikter rapportering](howto-authentication-methods-usage-insights.md)
* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registrera dig för återställning av lösenord för självbetjäning](../user-help/active-directory-passwords-reset-register.md).
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något är trasigt. Hur felsöker jag SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Exempel på SSPR-aktivitetsgranskningsloggar i Azure AD"
