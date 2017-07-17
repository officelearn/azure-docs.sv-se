---
title: "Azure Active Directory Reporting: Komma igång | Microsoft Docs"
description: "Visar de olika tillgängliga rapporterna i Azure Active Directory Reporting"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 7ac99919-8df5-4424-9298-fc7c025ba949
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: dhanyahk;markvi
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: eec9b73cbaccfa50eec6f237e4d1d810c6efa1d9
ms.openlocfilehash: e5b8ac91914203156bd395d7f462385e9f6dbcb4
ms.contentlocale: sv-se
ms.lasthandoff: 02/24/2017

---
# Komma igång med Azure Active Directory Reporting
<a id="getting-started-with-azure-active-directory-reporting" class="xliff"></a>
## Vad det är
<a id="what-it-is" class="xliff"></a>
Azure AD (Active Directory Azure) tillhandahåller säkerhets-, aktivitets- och granskningsrapporter för din katalog. Här är en lista över de rapporter som ingår:

### Säkerhetsrapporter
<a id="security-reports" class="xliff"></a>
* Inloggningar från okända källor
* Inloggningar efter flera fel
* Inloggningar från flera geografiska områden
* Inloggningar från IP-adresser med misstänkt aktivitet
* Oregelbunden inloggningsaktivitet
* Inloggningar från potentiellt infekterade enheter
* Användare med avvikande inloggningsaktivitet

### Aktivitetsrapporter
<a id="activity-reports" class="xliff"></a>
* Programanvändning: sammanfattning
* Programanvändning: detaljerad
* Instrumentpanel för program
* Kontoetableringsfel
* Enskilda användarenheter
* Aktivitet för enskilda användare
* Aktivitetsrapport för grupper
* Aktivitetsrapport över registrering av lösenordsåterställning
* Lösenordsåterställningsaktivitet

### Granskningsrapporter
<a id="audit-reports" class="xliff"></a>
* Kataloggranskningsrapport

> [!TIP]
> Mer dokumentation om Azure AD Reporting finns i [Visa åtkomst- och användningsrapporter](active-directory-view-access-usage-reports.md).
> 
> 

## Hur det fungerar
<a id="how-it-works" class="xliff"></a>
### Rapporteringsflöde
<a id="reporting-pipeline" class="xliff"></a>
Rapporteringsflödet består av tre steg. Varje gång en användare loggar in, eller varje gång en autentisering görs, händer följande:

* Först autentiseras användaren (åtgärden lyckas eller misslyckas) och resultatet lagras i Azure Active Directory-tjänstdatabaser.
* De senaste inloggningarna bearbetas med jämna mellanrum. I detta läge söker våra säkerhetsalgoritmer och algoritmer för avvikande aktivitet igenom de senaste inloggningarna och letar efter misstänkt aktivitet.
* Efter bearbetningen skrivs, cachelagras och hanteras rapporterna på den klassiska Azure-portalen.

### Rapportera genereringstider
<a id="report-generation-times" class="xliff"></a>
På grund av den stora mängden autentiseringar och inloggningar som bearbetas av Azure AD-plattformen är de senaste inloggningarna som behandlas i genomsnitt en timme gamla. I sällsynta fall kan det ta upp till åtta timmar att bearbeta de senaste inloggningarna.

Du hittar den senast bearbetade inloggningen genom att granska hjälptexten längst upp i varje rapport.

![Hjälptext längst upp i varje rapport](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [!TIP]
> Mer dokumentation om Azure AD Reporting finns i [Visa åtkomst- och användningsrapporter](active-directory-view-access-usage-reports.md).
> 
> 

## Komma igång
<a id="getting-started" class="xliff"></a>
### Logga in på den klassiska Azure-portalen.
<a id="sign-into-the-azure-classic-portal" class="xliff"></a>
Först måste du logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) som global administratör eller efterlevnadsadministratör. Du måste också vara administratör eller medadministratör för Azure-prenumerationstjänster eller använda Azure-prenumerationen ”Åtkomst till Azure AD”.

### Gå till Rapporter
<a id="navigate-to-reports" class="xliff"></a>
Du visar rapporterna genom att gå till fliken Rapporter längst upp i din katalog.

Om det här är första gången du visar rapporterna måste du accepterar en dialogruta innan du kan visa rapporterna. Avsikten med detta är att bekräfta att administratörer i organisationen får se dessa data, som kan betraktas som privat information i vissa länder/regioner.

![Dialogruta](./media/active-directory-reporting-getting-started/dialogBox.png)

### Utforska varje rapport
<a id="explore-each-report" class="xliff"></a>
Navigera in i varje rapport för att se de data som samlas in och de inloggningar som bearbetas. Du hittar en [lista över alla rapporter här](active-directory-reporting-guide.md).

![Alla rapporter](./media/active-directory-reporting-getting-started/reportsMain.png)

### Ladda ned rapporterna som CSV
<a id="download-the-reports-as-csv" class="xliff"></a>
Alla rapporter kan laddas ned som CSV-filer (kommaavgränsade). Du kan använda de här filerna i Excel, PowerBI eller i analysprogram från tredje part för att ytterligare analysera dina data.

Om du vill ladda ned en rapport som en CSV-fil går du till rapporten och klickar på ”Ladda ned” längst ned.

![Knappen Ladda ned](./media/active-directory-reporting-getting-started/downloadButton.png)

> [!TIP]
> Mer dokumentation om Azure AD Reporting finns i [Visa åtkomst- och användningsrapporter](active-directory-view-access-usage-reports.md).
> 
> 

## Nästa steg
<a id="next-steps" class="xliff"></a>
### Anpassa aviseringar om avvikande inloggningsaktivitet
<a id="customize-alerts-for-anomalous-sign-in-activity" class="xliff"></a>
Gå till fliken ”Konfigurera” i din katalog.

Rulla ned till avsnittet ”Meddelanden”.

Aktivera eller inaktivera avsnittet ”Skicka e-postmeddelanden om avvikande inloggningar”.

![Avsnittet Meddelanden](./media/active-directory-reporting-getting-started/notificationsSection.png)

### Integrera med Azure AD Reporting-API:et
<a id="integrate-with-the-azure-ad-reporting-api" class="xliff"></a>
Läs [Komma igång med Reporting-API:et](active-directory-reporting-api-getting-started.md).

### Aktivera Multi-Factor Authentication för användare
<a id="engage-multi-factor-authentication-on-users" class="xliff"></a>
Välj en användare i en rapport.

Klicka på knappen ”Aktivera MFA” längst ned på skärmen.

![Knappen Multi-Factor Authentication längst ned på skärmen](./media/active-directory-reporting-getting-started/mfaButton.png)

> [!TIP]
> Mer dokumentation om Azure AD Reporting finns i [Visa åtkomst- och användningsrapporter](active-directory-view-access-usage-reports.md).
> 
> 

## Läs mer
<a id="learn-more" class="xliff"></a>
### Granska händelser
<a id="audit-events" class="xliff"></a>
Lär dig mer om vilka händelser som granskas i katalogen i [Granskningshändelser i Azure Active Directory Reporting](active-directory-reporting-audit-events.md).

### API-integrering
<a id="api-integration" class="xliff"></a>
Läs [Komma igång med Reporting-API:et](active-directory-reporting-api-getting-started.md) och [API-referensdokumentationen](https://msdn.microsoft.com/library/azure/mt126081.aspx).

### Kontakta oss
<a id="get-in-touch" class="xliff"></a>
E-post [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) för feedback, hjälp eller eventuella frågor.

> [!TIP]
> Mer dokumentation om Azure AD Reporting finns i [Visa åtkomst- och användningsrapporter](active-directory-view-access-usage-reports.md).
> 
> 


