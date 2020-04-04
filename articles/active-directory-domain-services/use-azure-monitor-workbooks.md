---
title: Använda Azure Monitor-arbetsböcker med Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du använder Azure Monitor-arbetsböcker för att granska säkerhetsgranskningar och förstå problem i en hanterad Azure Active Directory Domain Services-domän.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/18/2020
ms.author: iainfou
ms.openlocfilehash: bdfc7d37d99dc5511f47e33d1848c3f142a9693e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654460"
---
# <a name="review-security-audit-events-in-azure-ad-domain-services-using-azure-monitor-workbooks"></a>Granska säkerhetsgranskningshändelser i Azure AD Domain Services med Hjälp av Azure Monitor-arbetsböcker

För att hjälpa dig att förstå tillståndet för din Azure Active Directory Domain Services (Azure AD DS) hanterad domän kan du aktivera säkerhetsgranskningshändelser. Dessa säkerhetsgranskningshändelser kan sedan granskas med hjälp av Azure Monitor-arbetsböcker som kombinerar text, analysfrågor och parametrar i omfattande interaktiva rapporter. Azure AD DS innehåller arbetsboksmallar för säkerhetsöversikt och kontoaktivitet som gör att du kan gräva i granskningshändelser och hantera din miljö.

Den här artikeln visar hur du använder Azure Monitor-arbetsböcker för att granska säkerhetsgranskningshändelser i Azure AD DS.

## <a name="before-you-begin"></a>Innan du börjar

För att kunna slutföra den här artikeln behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs slutför du självstudien för att [skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* Säkerhetsgranskningshändelser som är aktiverade för din hanterade Azure Active Directory Domain Services-domän som strömmar data till en Log Analytics-arbetsyta.
    * Aktivera vid behov [säkerhetsgranskningar för Azure Active Directory Domain Services][enable-security-audits].

## <a name="azure-monitor-workbooks-overview"></a>Översikt över Azure Monitor-arbetsböcker

När säkerhetsgranskningshändelser aktiveras i Azure AD DS kan det vara svårt att analysera och identifiera problem i den hanterade domänen. Med Azure Monitor kan du aggregera dessa säkerhetsgranskningshändelser och fråga efter data. Med Azure Monitor-arbetsböcker kan du visualisera dessa data för att göra det snabbare och enklare att identifiera problem.

Arbetsboksmallar är kurerade rapporter som är utformade för flexibel återanvändning av flera användare och team. När du öppnar en arbetsboksmall läses data från din Azure Monitor-miljö in. Du kan använda mallar utan påverkan på andra användare i organisationen och spara egna arbetsböcker baserat på mallen.

Azure AD DS innehåller följande två arbetsboksmallar:

* Översiktsrapport för säkerhet
* Rapport över kontoaktivitet

Mer information om hur du redigerar och hanterar arbetsböcker finns i [översikt över Azure Monitor-arbetsböcker](../azure-monitor/platform/workbooks-overview.md).

## <a name="use-the-security-overview-report-workbook"></a>Använda arbetsboken för säkerhetsöversiktsrapport

För att hjälpa dig att bättre förstå användning och identifiera potentiella säkerhetshot sammanfattar säkerhetsöversiktsrapporten inloggningsdata och identifierar konton som du kanske vill kontrollera. Du kan visa händelser i ett visst datumintervall och öka detaljnivån i specifika inloggningshändelser, till exempel felaktiga lösenordsförsök eller var kontot har inaktiverats.

Så här öppnar du arbetsboksmallen för säkerhetsöversiktsrapporten:

1. Sök efter och välj **Azure Active Directory Domain Services** i Azure-portalen.
1. Välj din hanterade domän, till exempel *aaddscontoso.com*
1. Välj **Övervaka > arbetsböcker** på menyn till vänster

    ![Välj menyalternativet Arbetsböcker i Azure-portalen](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. Välj **rapporten Säkerhetsöversikt**.
1. Välj din Azure-prenumeration och sedan Azure Monitor-arbetsytan på de rullgardinsmenyerna högst upp i arbetsboken. Välj ett **tidsintervall,** till exempel *Senaste 7 dagarna*.

    ![Välj menyalternativet Arbetsböcker i Azure-portalen](./media/use-azure-monitor-workbooks/select-query-filters.png)

    Alternativen **för panelvyn** och **diagramvyn** kan också ändras för att analysera och visualisera data efter behov

1. Om du vill öka detaljnivån i en viss händelsetyp väljer du ett av **inloggningsresultatkorten,** till exempel *Konto utelåst,* vilket visas i följande exempel:

    ![Exempel på säkerhetsöversiktsrapportdata som visualiseras i Azure Monitor-arbetsböcker](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. Den nedre delen av säkerhetsöversiktsrapporten under diagrammet delar sedan upp den valda aktivitetstypen. Du kan filtrera efter användarnamn som är inblandade på höger sida, vilket visas i följande exempelrapport:

    [![](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png "Details of account lockouts in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>Använda kontoaktivitetsrapportarbetsboken

För att hjälpa dig att felsöka problem för ett visst användarkonto bryter kontoaktivitetsrapporten upp detaljerad information om granskningshändelseloggen. Du kan granska när ett felaktigt användarnamn eller lösenord angavs under inloggningen och källan till inloggningsförsöket.

Så här öppnar du arbetsboksmallen för kontoaktivitetsrapporten:

1. Sök efter och välj **Azure Active Directory Domain Services** i Azure-portalen.
1. Välj din hanterade domän, till exempel *aaddscontoso.com*
1. Välj **Övervaka > arbetsböcker** på menyn till vänster
1. Välj **kontoaktivitetsrapport**.
1. Välj din Azure-prenumeration och sedan Azure Monitor-arbetsytan på de rullgardinsmenyerna högst upp i arbetsboken. Välj ett **tidsintervall,** till exempel *Senaste 30 dagarna,* och sedan hur du vill att **panelen** ska representera data. Du kan filtrera efter **kontoanvändarnamn**, till exempel *felix*, som visas i följande exempelrapport:

    [![](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png "Account activity report in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    Området under diagrammet visar enskilda inloggningshändelser tillsammans med information som aktivitetsresultat och källarbetsstation. Den här informationen kan hjälpa till att fastställa upprepade källor till inloggningshändelser som kan orsaka kontoutelåsningar eller indikera en potentiell attack.

Precis som med säkerhetsöversiktsrapporten kan du öka detaljnivån i de olika panelerna högst upp i rapporten för att visualisera och analysera data efter behov.

## <a name="save-and-edit-workbooks"></a>Spara och redigera arbetsböcker

De två mallarbetsböckerna som tillhandahålls av Azure AD DS är ett bra ställe att börja med din egen dataanalys. Om du behöver få mer detaljerade i datafrågor och undersökningar kan du spara dina egna arbetsböcker och redigera frågorna.

1. Om du vill spara en kopia av en av arbetsboksmallarna väljer du **Redigera > Spara som > Delade rapporter**och anger sedan ett namn och sparar det.
1. Välj **Redigera** i redigeringsläget i din egen kopia av mallen. Du kan välja den blå **redigera-knappen** bredvid någon del av rapporten och ändra den.

Alla diagram och tabeller i Azure Monitor-arbetsböcker genereras med Kusto-frågor. Mer information om hur du skapar dina egna frågor finns i [Azure Monitor-loggfrågor][azure-monitor-queries] och [självstudiekursen Kusto-frågor][kusto-queries].

## <a name="next-steps"></a>Nästa steg

Om du behöver justera principer för lösenord och utelåsning läser du [principer för lösenords- och kontoutelåsning på hanterade domäner][password-policy].

Om du har problem med användare kan du läsa om felsÃ¶kning [av problem med kontoinformation][troubleshoot-sign-in] eller [problem med kontoutelåsning][troubleshoot-account-lockout].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: ../azure-monitor/log-query/query-language.md
[kusto-queries]: https://docs.microsoft.com/azure/kusto/query/tutorial?pivots=azuredataexplorer
