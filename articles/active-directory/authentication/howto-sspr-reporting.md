---
title: Lösenordsåterställning via självbetjäning rapporter – Azure Active Directory
description: Rapportering om Azure AD-självbetjäning lösenord återställa händelser
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bc2acaa24637c3297af8e91f01b67e5d30f2931
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59577975"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Alternativ för Azure AD-lösenordshantering

Efter distributionen kan många organisationer vill veta hur eller om lösenordsåterställning via självbetjäning (SSPR) som verkligen används. Rapporteringsfunktionen som ger Azure Active Directory (Azure AD) kan du besvara frågor med hjälp av fördefinierade rapporter. Om du har korrekt licens, kan du också skapa egna frågor.

![Rapportering på SSPR loggar med granskningen i Azure AD][Reporting]

Följande frågor besvaras av rapporterna som finns i den [Azure-portalen](https://portal.azure.com/):

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

Om du är en Power BI-användare finns ett innehållspaket för Azure AD som innehåller enkel att använda rapporter för SSPR. Läs mer om hur du använder och distribuera Innehållspaketet [hur du använder Azure Active Directory Power BI-Innehållspaketet](../reports-monitoring/howto-power-bi-content-pack.md). Med Innehållspaketet kan du skapa dina egna instrumentpaneler och dela dem med andra i din organisation.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Så här visar du lösenordshanteringsrapporter i Azure portal

Vi har förbättrat sättet du kan visa återställning av lösenord och lösenordsåterställningsaktivitet för registrering i Azure portal tidigare. Använd följande steg för att hitta lösenordet återställa och registreringshändelser för lösenordsåterställning:

1. Bläddra till [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** i den vänstra rutan.
3. Sök efter **Azure Active Directory** i listan över tjänster och markera den.
4. Välj **användare** från avsnittet Hantera.
5. Välj **granskningsloggar** från den **användare** bladet. Detta visar alla granskningshändelser som gjorts mot alla användare i din katalog. Du kan filtrera den här vyn om du vill se alla lösenord-relaterade händelser.
6. Från den **Filter** menyn längst upp i fönstret, väljer den **Service** nedrullningsbara listan och ändra det till den **Self-service lösenordshantering** typ av tjänst.
7. Du kan också filtrera listan ytterligare genom att välja specifikt **aktivitet** du är intresserad av.

### <a name="converged-registration-preview"></a>Konvergerade registrering (förhandsversion)

Om ditt deltagande i den offentliga förhandsversionen av konvergerade registrering, information om användaraktivitet i granskningsloggarna finns i tjänsten **autentiseringsmetoder**.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Beskrivning av rapportkolumner i Azure portal

I följande lista beskrivs var och en av rapportkolumner i Azure-portalen i detalj:

* **Användaren**: Den användare som försökte lösenord återställa registreringsåtgärden.
* **Roll**: Rollen för användaren i katalogen.
* **Datum och tid**: Datum och tid för försöket.
* **Registrerade**: Autentiseringsdata som användaren har angett under registrering av återställning.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Beskrivning av rapporten värdena i Azure portal

I följande tabell beskrivs de olika värdena som du kan ange för varje kolumn i Azure portal:

| Kolumn | Tillåtna värden och deras innebörd |
| --- | --- |
| Registrerade |**Alternativ e-postadress**: Användaren används en alternativ e-postadress eller e-post för autentisering för att autentisera.<p><p>**Arbetstelefon**: Användaren används en telefon på kontoret för att autentisera.<p>**Mobiltelefon**: Användaren används en mobiltelefon eller autentiseringstelefon för att autentisera.<p>**Säkerhetsfrågor**: Användaren används säkerhetsfrågor för att autentisera.<p>**Valfri kombination av de tidigare metoderna, till exempel alternativ e-post + mobiltelefon**: Inträffar när en två-gate-princip har angetts och visar vilka två metoder som användaren använde för att autentisering sin begäran om lösenordsåterställning. |

## <a name="self-service-password-management-activity-types"></a>Självbetjäning aktivitetstyper för lösenordshantering

Följande aktivitetstyper av visas i den **Self-Service lösenordshantering** händelsekategori för granskning:

* [Blockerad från lösenordsåterställning via självbetjäning](#activity-type-blocked-from-self-service-password-reset): Anger att en användare har försökt att återställa ett lösenord, använda en specifik gate eller verifiera ett telefonnummer fler än fem Totalt antal gånger under 24 timmar.
* [Ändra lösenord (självbetjäning)](#activity-type-change-password-self-service): Anger att en användare utförde en frivillig eller tvingad (på grund av utgången) Ändra lösenordet.
* [Återställ lösenord (av administratör)](#activity-type-reset-password-by-admin): Anger att en administratör har genomfört en å en användares från Azure-portalen.
* [Återställ lösenord (självbetjäning)](#activity-type-reset-password-self-service): Anger att en användare har återställa sina lösenord från den [portal för Azure AD-lösenordsåterställning](https://passwordreset.microsoftonline.com).
* [Lösenordsåterställning via självbetjäning aktivitetsförlopp för lösenordsåterställningsflöde](#activity-type-self-serve-password-reset-flow-activity-progress): Anger varje specifikt steg som en användare fortsätter genom, t.ex. skicka ett särskilt lösenord återställa autentiseringsgate, som en del av lösenordet återställa lösenordet.
* [Lås upp användarkonto (självbetjäning)](#activity-type-unlock-a-user-account-self-service)): Anger att en användare har låsts upp sina Active Directory-konto utan att återställa sina lösenord från den [portal för Azure AD-lösenordsåterställning](https://passwordreset.microsoftonline.com) låsa upp utan återställning med hjälp av funktionen Active Directory för kontot.
* [Användare registrerad för lösenordsåterställning via självbetjäning](#activity-type-user-registered-for-self-service-password-reset): Anger att en användare har registrerat all nödvändig information för att kunna återställa sina lösenord i enlighet med principen för lösenordsåterställning för aktuell klient.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Aktivitetstyp: Blockerad från lösenordsåterställning via självbetjäning

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivningen**: Anger att en användare har försökt att återställa ett lösenord, använda en specifik gate eller verifiera ett telefonnummer fler än fem Totalt antal gånger under 24 timmar.
* **Aktivitet aktören**: Den användare som har begränsade från att utföra ytterligare återställningsaktiviteter. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: Den användare som har begränsade från att utföra ytterligare återställningsaktiviteter. Användaren kan vara en användare eller administratör.
* **Aktivitetsstatus**:
  * _Success_: Anger att en användare begränsades från utföra eventuella ytterligare återställningar, försök eventuella ytterligare autentiseringsmetoder eller verifieringen av eventuella ytterligare telefonnummer för nästkommande 24 timmar.
* **Aktivitetens status felorsak**: Inte tillämpligt.

### <a name="activity-type-change-password-self-service"></a>Aktivitetstyp: Ändra lösenord (självbetjäning)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivningen**: Anger att en användare utförde en frivillig eller tvingad (på grund av utgången) Ändra lösenordet.
* **Aktivitet aktören**: Den användare som har ändrat sitt lösenord. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: Den användare som har ändrat sitt lösenord. Användaren kan vara en användare eller administratör.
* **Aktivitet statusar**:
  * _Success_: Anger att en användare har ändrat sitt lösenord.
  * _Fel_: Anger att en användare Det gick inte att ändra sina lösenord. Du kan välja raden för att se den **aktivitet statusorsak** kategori om du vill veta mer om varför de inträffade.
* **Aktivitetens status felorsak**:
  * _FuzzyPolicyViolationInvalidPassword_: Användaren har valt ett lösenord som har automatiskt förbjuden eftersom identifieringsfunktioner Microsoft förbjudna lösenord visade sig vara för vanligt eller särskilt svaga.

### <a name="activity-type-reset-password-by-admin"></a>Aktivitetstyp: Återställ lösenord (av administratör)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivningen**: Anger att en administratör har genomfört en å en användares från Azure-portalen.
* **Aktivitet aktören**: Administratören som utförs åt en annan användare eller administratör om återställning av lösenord. Måste vara en lösenordsadministratör eller Användaradministratör supportavdelningens administratör.
* **Mål för aktiviteten**: Den användare vars lösenord har återställts. Användaren kan vara en användare eller en annan administratör.
* **Aktivitet statusar**:
  * _Success_: Anger att en administratör har återställts en användares lösenord.
  * _Fel_: Anger att en administratör gick inte att ändra en användares lösenord. Du kan välja raden för att se den **aktivitet statusorsak** kategori om du vill veta mer om varför de inträffade.

### <a name="activity-type-reset-password-self-service"></a>Aktivitetstyp: Återställ lösenord (självbetjäning)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivningen**: Anger att en användare har återställa sina lösenord från den [portal för Azure AD-lösenordsåterställning](https://passwordreset.microsoftonline.com).
* **Aktivitet aktören**: Den användare som kan återställa sina lösenord. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: Den användare som kan återställa sina lösenord. Användaren kan vara en användare eller administratör.
* **Aktivitet statusar**:
  * _Success_: Anger att en användare har återställts sina egna lösenord.
  * _Fel_: Visar att en användare misslyckades med att återställa sina egna lösenord. Du kan välja raden för att se den **aktivitet statusorsak** kategori om du vill veta mer om varför de inträffade.
* **Aktivitetens status felorsak**:
  * _FuzzyPolicyViolationInvalidPassword_: Administratören har valt ett lösenord som har automatiskt förbjuden eftersom identifieringsfunktioner Microsoft förbjudna lösenord visade sig vara för vanligt eller särskilt svaga.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Aktivitetstyp: Självsignerat fungera aktivitetsförlopp för lösenordsåterställningsflöde för lösenordsåterställning

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivningen**: Indikerar varje specifikt steg som en användare fortsätter genom (t.ex. skicka ett särskilt lösenord återställa autentiseringsgate) som en del av lösenordet återställa lösenordet.
* **Aktivitet aktören**: Användaren som utförde en del av lösenordet återställa flödet. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: Användaren som utförde en del av lösenordet återställa flödet. Användaren kan vara en användare eller administratör.
* **Aktivitet statusar**:
  * _Success_: Anger att en användare har slutfört ett specifikt steg till flödet för återställning av lösenord.
  * _Fel_: Anger att ett specifikt steg av lösenordet återställa flöde misslyckades. Du kan välja raden för att se den **aktivitet statusorsak** kategori om du vill veta mer om varför de inträffade.
* **Aktivitetens statusorsaker**:   Se följande tabell för [alla tillåtna återställning aktivitetens statusorsaker](#description-of-the-report-columns-in-the-azure-portal).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Aktivitetstyp: Lås upp användarkonto (självbetjäning)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivningen**: Anger att en användare har låsts upp sina Active Directory-konto utan att återställa sina lösenord från den [portal för Azure AD-lösenordsåterställning](https://passwordreset.microsoftonline.com) låsa upp utan återställning med hjälp av funktionen Active Directory för kontot.
* **Aktivitet aktören**: Den användare som låsas upp sitt konto utan att återställa sina lösenord. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: Den användare som låsas upp sitt konto utan att återställa sina lösenord. Användaren kan vara en användare eller administratör.
* **Tillåtna aktivitet statusar**:
  * _Success_: Anger att en användare har olåst sitt eget konto.
  * _Fel_: Anger att en användare Det gick inte att låsa upp sitt konto. Du kan välja raden för att se den **aktivitet statusorsak** kategori om du vill veta mer om varför de inträffade.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Aktivitetstyp: Användare registrerad för lösenordsåterställning via självbetjäning

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivningen**: Anger att en användare har registrerat all nödvändig information för att kunna återställa sina lösenord i enlighet med principen för lösenordsåterställning för aktuell klient. 
* **Aktivitet aktören**: Användaren som registrerad för lösenordsåterställning. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: Användaren som registrerad för lösenordsåterställning. Användaren kan vara en användare eller administratör.
* **Tillåtna aktivitet statusar**:
  * _Success_: Anger att en användare har registrerats för lösenordsåterställning i enlighet med den aktuella principen. 
  * _Fel_: Visar att en användare misslyckades med att registrera för återställning av lösenord. Du kan välja raden för att se den **aktivitet statusorsak** kategori om du vill veta mer om varför de inträffade.

     >[!NOTE]
     >Felet betyder det inte går att återställa sina egna lösenord. Det innebär att de inte slutför registreringen. Om det finns overifierade data på deras konto är korrekt, till exempel ett telefonnummer som inte har verifierats, även om de inte har kontrollerat det här telefonnumret, kan de fortfarande använda för att återställa sina lösenord.

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
