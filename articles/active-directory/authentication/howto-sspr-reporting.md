---
title: Lösenordsåterställning via självbetjäning rapporter – Azure Active Directory
description: Rapportering om Azure AD-självbetjäning lösenord återställa händelser
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 91ed8a073dd95ddf37e4a71bfd7c3ab1dcb94f09
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39161369"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Alternativ för Azure AD-lösenordshantering

Efter distributionen kan många organisationer vill veta hur eller om lösenordsåterställning via självbetjäning (SSPR) som verkligen används. Rapporteringsfunktionen som ger Azure Active Directory (Azure AD) kan du besvara frågor med hjälp av fördefinierade rapporter. Om du har korrekt licens, kan du också skapa egna frågor.

![Rapportering][Reporting]

Följande frågor besvaras av rapporterna som finns i [Azure portal] (https://portal.azure.com/):

> [!NOTE]
> Du måste vara [en global administratör](../users-groups-roles/directory-assign-admin-roles.md), och du måste delta i för dessa data samlas in för din organisations räkning. Om du vill välja i, måste du gå till den **rapportering** fliken eller granskningen loggar minst en gång. Fram till dess data inte samlas in för din organisation.
>

* Hur många personer har registrerat dig för återställning av lösenord?
* Vem som har registrerats för återställning av lösenord?
* Vilka data personer registrerar?
* Hur många personer återställa sina lösenord under de senaste sju dagarna?
* Vilka är de vanligaste metoder som användare eller administratörer använder för att återställa sina lösenord?
* Vad är vanliga problem med användare eller administratörer ansikte när du försöker använda lösenordsåterställning?
* Vilka administratörer återställer sina lösenord ofta?
* Finns det några misstänkt aktivitet som händer med återställning av lösenord?

## <a name="power-bi-content-pack"></a>Power BI-Innehållspaketet

Om du är en Power BI-användare finns ett innehållspaket för Azure AD som innehåller enkel att använda rapporter för SSPR. Läs mer om hur du använder och distribuera Innehållspaketet [hur du använder Azure Active Directory Power BI-Innehållspaketet](../active-directory-reporting-power-bi-content-pack-how-to.md). Med Innehållspaketet kan du skapa dina egna instrumentpaneler och dela dem med andra i din organisation.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Så här visar du lösenordshanteringsrapporter i Azure portal

Vi har förbättrat sättet du kan visa återställning av lösenord och lösenordsåterställningsaktivitet för registrering i Azure portal tidigare. Använd följande steg för att hitta lösenordet återställa och registreringshändelser för lösenordsåterställning:

1. Bläddra till den [Azure-portalen](https://portal.azure.com).
2. Välj **alla tjänster** i den vänstra rutan.
3. Sök efter **Azure Active Directory** i listan över tjänster och markera den.
4. Välj **Användare och grupper**.
5. Välj **granskningsloggar** från den **användare och grupper** menyn. Detta visar alla granskningshändelser som gjorts mot alla användare i din katalog. Du kan filtrera den här vyn om du vill se alla lösenord-relaterade händelser.
6. Om du vill filtrera den här vyn om du vill visa endast de lösenord-återställning-relaterade händelserna, Välj den **Filter** längst upp i fönstret.
7. Från den **Filter** menyn och välj den **kategori** nedrullningsbara listan och ändra det till den **Self-service lösenordshantering** kategorityp.
8. Du kan också filtrera listan ytterligare genom att välja specifikt **aktivitet** du är intresserad av.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Beskrivning av rapportkolumner i Azure portal

I följande lista beskrivs var och en av rapportkolumner i Azure-portalen i detalj:

* **Användaren**: den användare som försökte lösenord återställa registreringsåtgärden.
* **Rollen**: rollen för användare i katalogen.
* **Datum och tid**: datum och tid för försöket.
* **Data registrerad**: autentiseringsdata som du angav under lösenord registrering för lösenordsåterställning.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Beskrivning av rapporten värdena i Azure portal

I följande tabell beskrivs de olika värdena som du kan ange för varje kolumn i Azure portal:

| Kolumn | Tillåtna värden och deras innebörd |
| --- | --- |
| Registrerade |**Alternativ e-postadress**: användaren används en alternativ e-postadress eller e-post för autentisering för att autentisera.<p><p>**Arbetstelefon**: användaren används en telefon på kontoret för att autentisera.<p>**Mobiltelefon**: användaren används en mobiltelefon eller autentiseringstelefon för att autentisera.<p>**Säkerhetsfrågor**: användaren används säkerhetsfrågor för att autentisera.<p>**Valfri kombination av de tidigare metoderna, till exempel alternativ e-post + mobiltelefon**: inträffar när en två-gate-princip har angetts och visar vilka två metoder som användaren använde för att autentisering sin begäran om lösenordsåterställning. |

## <a name="self-service-password-management-activity-types"></a>Självbetjäning aktivitetstyper för lösenordshantering

Följande aktivitetstyper av visas i den **Self-Service lösenordshantering** händelsekategori för granskning:

* [Blockerad från lösenordsåterställning via självbetjäning](#activity-type-blocked-from-self-service-password-reset): Anger att en användare har försökt att återställa ett lösenord, använda en specifik gate eller verifiera ett telefonnummer fler än fem Totalt antal gånger under 24 timmar.
* [Ändra lösenord (självbetjäning)](#activity-type-change-password-self-service): Anger att en användare utförde en frivillig eller tvingad (på grund av utgången) Ändra lösenordet.
* [Återställ lösenord (av administratör)](#activity-type-reset-password-by-admin): Anger att en administratör har genomfört en å en användares från Azure-portalen.
* [Återställ lösenord (självbetjäning)](#activity-type-reset-password-self-service): Anger att en användare har återställa sina lösenord från den [portal för Azure AD-lösenordsåterställning](https://passwordreset.microsoftonline.com).
* [Lösenordsåterställning via självbetjäning aktivitetsförlopp för lösenordsåterställningsflöde](#activity-type-self-serve-password-reset-flow-activity-progress): anger varje specifikt steg som en användare fortsätter genom, t.ex. skicka ett särskilt lösenord återställa autentiseringsgate, som en del av lösenordet återställa lösenordet.
* [Lås upp användarkonto (självbetjäning)](#activity-type-unlock-user-account-self-service): Anger att en användare har låsts upp sina Active Directory-konto utan att återställa sina lösenord från den [portal för Azure AD-lösenordsåterställning](https://passwordreset.microsoftonline.com) med hjälp av aktivt Katalogfunktion för konto låser du upp utan återställning.
* [Användare registrerad för lösenordsåterställning via självbetjäning](#activity-type-user-registered-for-self-service-password-reset): Anger att en användare har registrerat all nödvändig information för att kunna återställa sina lösenord i enlighet med principen för lösenordsåterställning för aktuell klient.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Aktivitetstyp: blockerad från lösenordsåterställning via självbetjäning

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivningen**: Anger att en användare har försökt att återställa ett lösenord, använda en specifik gate eller verifiera ett telefonnummer fler än fem Totalt antal gånger under 24 timmar.
* **Aktivitet aktören**: den användare som har begränsade från att utföra ytterligare återställningsaktiviteter. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: den användare som har begränsade från att utföra ytterligare återställningsaktiviteter. Användaren kan vara en användare eller administratör.
* **Aktivitetsstatus**:
  * _Lyckade_: Anger att en användare begränsades från utföra eventuella ytterligare återställningar, försök eventuella ytterligare autentiseringsmetoder eller verifieringen av eventuella ytterligare telefonnummer för nästkommande 24 timmar.
* **Aktivitetens status felorsak**: ej tillämpligt.

### <a name="activity-type-change-password-self-service"></a>Aktivitetstyp: ändra lösenord (självbetjäning)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivningen**: Anger att en användare utförde en frivillig eller tvingad (på grund av utgången) Ändra lösenordet.
* **Aktivitet aktören**: den användare som har ändrat sitt lösenord. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: den användare som har ändrat sitt lösenord. Användaren kan vara en användare eller administratör.
* **Aktivitet statusar**:
  * _Lyckade_: Anger att en användare har ändrat sitt lösenord.
  * _Fel_: visar att en användare misslyckades med att ändra sina lösenord. Du kan välja raden för att se den **aktivitet statusorsak** kategori om du vill veta mer om varför de inträffade.
* **Aktivitetens status felorsak**: 
  * _FuzzyPolicyViolationInvalidPassword_: användaren har valt ett lösenord som har automatiskt förbjuden eftersom identifieringsfunktioner Microsoft förbjudna lösenord visade sig vara för vanligt eller särskilt svaga.

### <a name="activity-type-reset-password-by-admin"></a>Aktivitetstyp: Återställ lösenord (av administratör)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivningen**: Anger att en administratör har genomfört en å en användares från Azure-portalen.
* **Aktivitet aktören**: administratören som utförs åt en annan användare eller administratör om återställning av lösenord. Måste vara antingen en global administratör, lösenordsadministratör, administratören eller supportavdelningsadministratör.
* **Mål för aktiviteten**: användaren vars lösenord har återställts. Användaren kan vara en användare eller en annan administratör.
* **Aktivitet statusar**:
  * _Lyckade_: Anger att en administratör har återställts en användares lösenord.
  * _Fel_: Anger att en administratör gick inte att ändra en användares lösenord. Du kan välja raden för att se den **aktivitet statusorsak** kategori om du vill veta mer om varför de inträffade.

### <a name="activity-type-reset-password-self-service"></a>Aktivitetstyp: Återställ lösenord (självbetjäning)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivningen**: Anger att en användare har återställa sina lösenord från den [portal för Azure AD-lösenordsåterställning](https://passwordreset.microsoftonline.com).
* **Aktivitet aktören**: användare återställa sina lösenord. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: användare återställa sina lösenord. Användaren kan vara en användare eller administratör.
* **Aktivitet statusar**:
  * _Lyckade_: Anger att en användare har återställts sina egna lösenord.
  * _Fel_: visar att en användare misslyckades med att återställa sina egna lösenord. Du kan välja raden för att se den **aktivitet statusorsak** kategori om du vill veta mer om varför de inträffade.
* **Aktivitetens status felorsak**: 
  * _FuzzyPolicyViolationInvalidPassword_: administratören har valt ett lösenord som har automatiskt förbjuden eftersom identifieringsfunktioner Microsoft förbjudna lösenord visade sig vara för vanligt eller särskilt svaga.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Aktivitetstyp: själv hantera lösenordsåterställning aktivitetsförlopp för lösenordsåterställningsflöde

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivningen**: indikerar varje specifikt steg som en användare fortsätter genom (t.ex. skicka ett särskilt lösenord återställa autentiseringsgate) som en del av lösenordet återställa lösenordet.
* **Aktivitet aktören**: användaren som utförde en del av lösenordet återställa flödet. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: användaren som utförde en del av lösenordet återställa flödet. Användaren kan vara en användare eller administratör.
* **Aktivitet statusar**:
  * _Lyckade_: visar att en användare har slutförts ett specifikt steg till flödet för återställning av lösenord.
  * _Fel_: Anger att ett specifikt steg av lösenordet återställa flöde misslyckades. Du kan välja raden för att se den **aktivitet statusorsak** kategori om du vill veta mer om varför de inträffade.
* **Aktivitetens statusorsaker**: visas i följande tabell för [alla tillåtna återställning aktivitetens statusorsaker](#allowed-values-for-details-column).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Aktivitetstyp: Lås upp användarkonto (självbetjäning)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivningen**: Anger att en användare har låsts upp sina Active Directory-konto utan att återställa sina lösenord från den [portal för Azure AD-lösenordsåterställning](https://passwordreset.microsoftonline.com) med hjälp av Active Directory-funktion i kontoupplåsning utan återställning.
* **Aktivitet aktören**: den användare som låsas upp sitt konto utan att återställa sina lösenord. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: den användare som låsas upp sitt konto utan att återställa sina lösenord. Användaren kan vara en användare eller administratör.
* **Tillåtna aktivitet statusar**:
  * _Lyckade_: Anger att en användare har olåst sitt eget konto.
  * _Fel_: Anger att en användare Det gick inte att låsa upp sitt konto. Du kan välja raden för att se den **aktivitet statusorsak** kategori om du vill veta mer om varför de inträffade.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Aktivitetstyp: användare registrerad för lösenordsåterställning via självbetjäning

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivningen**: Anger att en användare har registrerat all nödvändig information för att kunna återställa sina lösenord i enlighet med principen för lösenordsåterställning för aktuell klient. 
* **Aktivitet aktören**: användaren som registrerade för återställning av lösenord. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: användaren som registrerade för återställning av lösenord. Användaren kan vara en användare eller administratör.
* **Tillåtna aktivitet statusar**:
  * _Lyckade_: Anger att en användare har registrerats för lösenordsåterställning i enlighet med den aktuella principen. 
  * _Fel_: visar att en användare misslyckades med att registrera för återställning av lösenord. Du kan välja raden för att se den **aktivitet statusorsak** kategori om du vill veta mer om varför de inträffade. 

     >[!NOTE]
     >Felet betyder det inte går att återställa sina egna lösenord. Det innebär att de inte slutför registreringen. Om det finns overifierade data på deras konto är korrekt, till exempel ett telefonnummer som inte har verifierats, även om de inte har kontrollerat det här telefonnumret, kan de fortfarande använda för att återställa sina lösenord. Mer information finns i [vad som händer när en användare registrerar?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers).
     >

## <a name="next-steps"></a>Nästa steg

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registrera för återställning av lösenord för självbetjäning](../user-help/active-directory-passwords-reset-register.md).
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Exempel på SSPR-aktivitetsgranskning loggar i Azure AD"
