---
title: "Självbetjäning för lösenordsåterställning rapporter - Azure Active Directory"
description: "Rapportering av Azure AD-lösenordet för självbetjäning återställa händelser"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: ae9475218141f4fdf08ef6b39fb85f3b18711e61
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Alternativ för Azure AD-lösenordshantering

Efter distributionen många organisationer vill veta hur eller om Självbetjäning för lösenordsåterställning (SSPR) som verkligen används. Rapporteringsfunktionen som ger Azure Active Directory (AD Azure) hjälper dig att besvara frågor med hjälp av fördefinierade rapporter. Om du är licensierad korrekt, kan du också skapa egna frågor.

![Rapportering][Reporting]

Följande frågor kan besvaras av rapporterna som finns i [Azure-portalen] (https://portal.azure.com/):

> [!NOTE]
> Du måste vara [en global administratör](active-directory-assign-admin-roles-azure-portal.md), och du måste välja i dessa data samlas in för din organisation. Om du vill välja, måste du besöka den **rapportering** fliken eller revisionen loggar minst en gång. Fram till dess kommer data inte samlas in för din organisation.
>

* Hur många som har registrerats för återställning av lösenord?
* Vem som har registrerats för återställning av lösenord?
* Vilka data personer registrerar?
* Hur många personer återställa sina lösenord under de senaste sju dagarna?
* Vilka är de vanligaste metoder med användare eller administratörer kan återställa sina lösenord?
* Vad är vanliga problem med användare eller administratörer står inför när de försöker använda lösenordsåterställning?
* Vilka administratörer ofta återställa sina lösenord?
* Finns det några misstänkt aktivitet fortsätter med återställning av lösenord?

## <a name="power-bi-content-pack"></a>Innehållspaketet för Power BI

Om du använder Power BI, finns det en Innehållspaketet för Azure AD som innehåller enkla att använda rapporter för SSPR. Mer information om hur du använder och distribuera Innehållspaketet finns [hur du använder Azure Active Directory Power BI-Innehållspaketet](active-directory-reporting-power-bi-content-pack-how-to.md). Med Innehållspaketet kan du skapa dina egna instrumentpaneler och dela dem med andra i din organisation.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Så här visar du lösenordshantering rapporter i Azure-portalen

Vi har förbättrat sättet att du kan visa lösenordsåterställning och lösenordsåterställningsaktivitet för registrering i Azure portal upplevelsen. Använd följande steg för att hitta lösenordet återställning och registrering händelser för återställning av lösenord:

1. Bläddra till den [Azure-portalen](https://portal.azure.com).
2. Välj **alla tjänster** i den vänstra rutan.
3. Sök efter **Azure Active Directory** i listan över tjänster och markera den.
4. Välj **användare och grupper**.
5. Välj **granskningsloggar** från den **användare och grupper** menyn. Detta visar alla granskningshändelser som inträffat mot alla användare i din katalog. Du kan filtrera den här vyn om du vill se alla lösenord-relaterade händelser.
6. Du kan filtrera den här vyn om du vill visa bara de lösenord-återställning relaterade händelserna, välja den **Filter** längst upp i fönstret.
7. Från den **Filter** väljer du den **kategori** nedrullningsbara listan, och ändra den till den **Self-service lösenordshantering** kategorityp.
8. Du kan också filtrera listan ytterligare genom att välja specifikt **aktiviteten** du är intresserad av.

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Hur du hämtar lösenord management händelser från Azure AD-rapporter och händelser API

Azure AD-rapporter och händelser API stöder hämtning av all information som ingår i lösenordsåterställning och rapporter för registrering för lösenordsåterställning. Du kan hämta enskilda lösenord Återställ lösenord Återställ registrering händelser och och integrera dem med reporting tekniken önskat genom att använda detta API.

> [!IMPORTANT]
> För närvarande Azure AD-rapporter och händelser API hämtar upp till *75 000 enskilda händelser* av den [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) och [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent) typer. API: et omfattar de senaste 30 dagarna.
> 
> Om du behöver hämta eller lagra data utöver det här fönstret föreslår vi spara det i en extern databas med API för att fråga går som uppstår. Vi rekommenderar att du börjar hämta dessa data när du börjar använda SSPR i din organisation. Spara den externt och fortsätt sedan att spåra går från den tidpunkten och framåt.
>

### <a name="how-to-get-started-with-the-reporting-api"></a>Hur du kommer igång med reporting API

Om du vill komma åt dessa data, måste du skriva ett litet program eller skript för att hämta från våra servrar. Mer information finns i [Kom igång med Azure AD reporting API](active-directory-reporting-api-getting-started.md).

När du har ett fungerande skript vill du undersöka lösenord återställning och registrering händelser som du kan hämta för att uppfylla dina scenarier:

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): Visar en lista över tillgängliga kolumner för händelser för återställning av lösenord.
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): Visar en lista över tillgängliga kolumner för registrering händelser för återställning av lösenord.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Beskrivning av rapportkolumner i Azure-portalen

I följande lista beskrivs var och en av rapportkolumner i Azure-portalen i detalj:

* **Användaren**: användaren som försökte lösenord återställa registrering igen.
* **Rollen**: rollen för användare i katalogen.
* **Datum och tid**: datum och tid för försöket.
* **Data registrerad**: autentiseringsdata som användaren angav under lösenord återställa registrering.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Beskrivning av rapporten värden i Azure-portalen

I följande tabell beskrivs de olika värdena som du kan ange för varje kolumn i Azure-portalen:

| Kolumn | Tillåtna värden och deras innebörd |
| --- | --- |
| Data som registrerats |**Alternativ e-postadress**: användaren används en alternativ e-post eller e-autentisering för att autentisera.<p><p>**Arbetstelefon**: användaren används en Arbetstelefon för att autentisera.<p>**Mobiltelefon**: användaren används en mobiltelefon eller telefon för autentisering för att autentisera.<p>**Säkerhetsfrågor**: användaren används säkerhetsfrågor för att autentisera.<p>**Valfri kombination av föregående metoder, exempelvis alternativa e-post + mobiltelefon**: inträffar när en två-gate-princip har angetts och visar vilka två metoder den användare som används för autentisering deras begäran om lösenordsåterställning. |

## <a name="self-service-password-management-activity-types"></a>Självbetjäning lösenordshantering aktivitetstyper

Följande aktivitetstyper som visas i den **Self-Service lösenordshantering** audit händelsekategori:

* [Blockeras från lösenordsåterställning via självbetjäning](#activity-type-blocked-from-self-service-password-reset): Anger att en användare har försökt att återställa ett lösenord, använda en specifik gate eller verifiera ett telefonnummer fler än fem Totalt antal gånger under 24 timmar.
* [Ändra lösenord (Self Service)](#activity-type-change-password-self-service): Anger att en användare utföra en frivillig eller tvingas (på grund av utgången) Ändra lösenordet.
* [Återställ lösenord (som admin)](#activity-type-reset-password-by-admin): Anger att en administratör utföra en lösenordsåterställning för en användare från Azure-portalen.
* [Återställ lösenord (Self Service)](#activity-type-reset-password-self-service): Anger att en användare har återställa sina lösenord från den [portalen för återställning av lösenord i Azure AD](https://passwordreset.microsoftonline.com).
* [Självbetjäning för lösenordsåterställning flödet aktivitet pågår](#activity-type-self-serve-password-reset-flow-activity-progress): anger varje specifikt steg som en användare fortsätter via, såsom att skicka ett särskilt lösenord återställa autentiseringsgate, som en del av lösenordet återställa processen.
* [Låsa upp användarkonto (Self Service)](#activity-type-unlock-user-account-self-service): Anger att en användare har låsts upp deras Active Directory-konto utan att återställa sina lösenord från den [portalen för återställning av lösenord i Azure AD](https://passwordreset.microsoftonline.com) med hjälp av aktivt Directory funktionen för konto upplåsning utan återställning.
* [Användare som har registrerats för lösenordsåterställning via självbetjäning](#activity-type-user-registered-for-self-service-password-reset): Anger att en användare har registrerat all nödvändig information för att kunna återställa sina lösenord i enlighet med den angivna innehavaren principen för lösenordsåterställning.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Aktivitetstypen: blockeras från lösenordsåterställning via självbetjäning

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivning av**: Anger att en användare har försökt att återställa ett lösenord, använda en specifik gate eller verifiera ett telefonnummer fler än fem Totalt antal gånger under 24 timmar.
* **Aktiviteten aktören**: återställning av den användare som har begränsats från att utföra ytterligare åtgärder. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: återställning av den användare som har begränsats från att utföra ytterligare åtgärder. Användaren kan vara en användare eller administratör.
* **Aktivitetsstatus**:
  * _Lyckade_: Anger att en användare har begränsats från utföra några ytterligare återställning, försöker du utföra några ytterligare autentiseringsmetoder eller verifiera eventuella ytterligare telefonnummer för nästkommande 24 timmar.
* **Aktivitetens status felorsak**: ej tillämpligt.

### <a name="activity-type-change-password-self-service"></a>Aktivitetstypen: ändra lösenord (Self Service)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivning av**: Anger att en användare utföra en frivillig eller tvingas (på grund av utgången) Ändra lösenordet.
* **Aktiviteten aktören**: den användare som har ändrat sitt lösenord. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: den användare som har ändrat sitt lösenord. Användaren kan vara en användare eller administratör.
* **Aktiviteten statuslägen**:
  * _Lyckade_: Anger att en användare har ändrat sitt lösenord.
  * _Fel_: visar att en användare misslyckades med att ändra sina lösenord. Du kan välja att raden ska se i **Statusanledning för aktiviteten** kategori som du vill veta mer om varför de inträffade.
* **Aktivitetens status felorsak**: 
  * _FuzzyPolicyViolationInvalidPassword_: användaren har valt ett lösenord som var förbjuden eftersom identifieringsfunktionerna Microsoft förbjudna lösenord finns det ska vara för vanligt eller särskilt svaga automatiskt.

### <a name="activity-type-reset-password-by-admin"></a>Aktivitetstypen: Återställ lösenord (som admin)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivning av**: Anger att en administratör utföra en lösenordsåterställning för en användare från Azure-portalen.
* **Aktiviteten aktören**: administratören som utförs på uppdrag av en annan användare eller administratör för återställning av lösenord. Måste vara antingen en global administratör, lösenordsadministratör, Användaradministratör eller supportavdelning administratör.
* **Mål för aktiviteten**: användaren vars lösenord har återställts. Användaren kan vara en slutanvändare eller en annan administratör.
* **Aktiviteten statuslägen**:
  * _Lyckade_: Anger att en administratör har återställts en användares lösenord.
  * _Fel_: Anger att en administratör gick inte att ändra en användares lösenord. Du kan välja att raden ska se i **Statusanledning för aktiviteten** kategori som du vill veta mer om varför de inträffade.

### <a name="activity-type-reset-password-self-service"></a>Aktivitetstypen: Återställ lösenord (Self Service)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivning av**: Anger att en användare har återställa sina lösenord från den [portalen för återställning av lösenord i Azure AD](https://passwordreset.microsoftonline.com).
* **Aktiviteten aktören**: användare återställa sina lösenord. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: användare återställa sina lösenord. Användaren kan vara en användare eller administratör.
* **Aktiviteten statuslägen**:
  * _Lyckade_: Anger att en användare har återställts sina egna lösenord.
  * _Fel_: visar att en användare misslyckades med att återställa sina egna lösenord. Du kan välja att raden ska se i **Statusanledning för aktiviteten** kategori som du vill veta mer om varför de inträffade.
* **Aktivitetens status felorsak**: 
  * _FuzzyPolicyViolationInvalidPassword_: administratören har valt ett lösenord som var förbjuden eftersom identifieringsfunktionerna Microsoft förbjudna lösenord finns det ska vara för vanligt eller särskilt svaga automatiskt.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Aktivitetstypen: Self hantera lösenord återställa flödet aktivitet pågår

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivning av**: indikerar varje specifikt steg som en användare fortsätter via (t.ex. skicka ett särskilt lösenord återställa autentiseringsgaten) som en del av lösenordet att återställa lösenordet.
* **Aktiviteten aktören**: användaren som utförde en del av lösenordet återställa flödet. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: användaren som utförde en del av lösenordet återställa flödet. Användaren kan vara en användare eller administratör.
* **Aktiviteten statuslägen**:
  * _Lyckade_: visar att en användare har slutförts specifikt steg för återställning av lösenord.
  * _Fel_: Anger att ett specifikt steg av lösenordet återställa flödet misslyckades. Du kan välja att raden ska se i **Statusanledning för aktiviteten** kategori som du vill veta mer om varför de inträffade.
* **Aktivitetens statusorsaker**: Se följande tabell för [alla tillåtna Återställ aktivitetens status skälen](#allowed-values-for-details-column).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Aktivitetstypen: låsa upp ett användarkonto (Self Service)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivning av**: Anger att en användare har låsts upp deras Active Directory-konto utan att återställa sina lösenord från den [portalen för återställning av lösenord i Azure AD](https://passwordreset.microsoftonline.com) med hjälp av Active Directory-funktionen för kontoupplåsning utan återställning.
* **Aktiviteten aktören**: den användare som ska låsas upp sitt konto utan att återställa sina lösenord. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: den användare som ska låsas upp sitt konto utan att återställa sina lösenord. Användaren kan vara en användare eller administratör.
* **Tillåtna aktiviteten statuslägen**:
  * _Lyckade_: Anger att en användare har låst sitt eget konto.
  * _Fel_: Anger att en användare kunde inte låsa upp sitt konto. Du kan välja att raden ska se i **Statusanledning för aktiviteten** kategori som du vill veta mer om varför de inträffade.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Aktivitetstypen: användare som har registrerats för lösenordsåterställning via självbetjäning

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitetsbeskrivning av**: Anger att en användare har registrerat all nödvändig information för att kunna återställa sina lösenord i enlighet med den angivna innehavaren principen för lösenordsåterställning. 
* **Aktiviteten aktören**: den användare som registrerats för återställning av lösenord. Användaren kan vara en användare eller administratör.
* **Mål för aktiviteten**: den användare som registrerats för återställning av lösenord. Användaren kan vara en användare eller administratör.
* **Tillåtna aktiviteten statuslägen**:
  * _Lyckade_: Anger att en användare har registrerats för lösenordsåterställning i enlighet med den aktuella principen. 
  * _Fel_: Anger att en användare kunde inte registreras för återställning av lösenord. Du kan välja att raden ska se i **Statusanledning för aktiviteten** kategori som du vill veta mer om varför de inträffade. 

     >[!NOTE]
     >Felet innebär inte går att återställa sina egna lösenord. Det innebär att de inte slutföra registreringen. Om overifierade data på sitt konto är korrekt, till exempel ett telefonnummer inte har verifierats, även om de inte har kontrollerat telefonnumret använda de fortfarande den för att återställa sina lösenord. Mer information finns i [vad som händer när en användare som registrerar?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers).
     >

## <a name="next-steps"></a>Nästa steg

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](active-directory-passwords-best-practices.md)
* [Återställ eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md).
* [Registrera för återställning av lösenord för självbetjäning](active-directory-passwords-reset-register.md).
* [Har du en fråga om licensiering?](active-directory-passwords-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](active-directory-passwords-data.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](active-directory-passwords-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](active-directory-passwords-writeback.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](active-directory-passwords-how-it-works.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Reporting]: ./media/active-directory-passwords-reporting/sspr-reporting.png "Exempel på SSPR aktivitet audit loggar i Azure AD"
