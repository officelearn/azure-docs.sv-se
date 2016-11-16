---
title: "Azure Active Directory Reporting: Komma igång | Microsoft Docs"
description: "Visar de olika tillgängliga rapporterna i Azure Active Directory Reporting"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 7ac99919-8df5-4424-9298-fc7c025ba949
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/07/2016
ms.author: dhanyahk
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6ce0e0ce9004e1b331328fca5830f01b6ce6af6c


---
# <a name="getting-started-with-azure-active-directory-reporting"></a>Komma igång med Azure Active Directory Reporting
## <a name="what-it-is"></a>Vad det är
Azure AD (Active Directory Azure) tillhandahåller säkerhets-, aktivitets- och granskningsrapporter för din katalog. Här är en lista över de rapporter som ingår:

### <a name="security-reports"></a>Säkerhetsrapporter
* Inloggningar från okända källor
* Inloggningar efter flera fel
* Inloggningar från flera geografiska områden
* Inloggningar från IP-adresser med misstänkt aktivitet
* Oregelbunden inloggningsaktivitet
* Inloggningar från potentiellt infekterade enheter
* Användare med avvikande inloggningsaktivitet

### <a name="activity-reports"></a>Aktivitetsrapporter
* Programanvändning: sammanfattning
* Programanvändning: detaljerad
* Instrumentpanel för program
* Kontoetableringsfel
* Enskilda användarenheter
* Aktivitet för enskilda användare
* Aktivitetsrapport för grupper
* Aktivitetsrapport över registrering av lösenordsåterställning
* Lösenordsåterställningsaktivitet

### <a name="audit-reports"></a>Granskningsrapporter
* Kataloggranskningsrapport

> [!TIP]
> Mer dokumentation om Azure AD Reporting finns i [Visa åtkomst- och användningsrapporter](active-directory-view-access-usage-reports.md).
> 
> 

## <a name="how-it-works"></a>Hur det fungerar
### <a name="reporting-pipeline"></a>Rapporteringsflöde
Rapporteringsflödet består av tre steg. Varje gång en användare loggar in, eller varje gång en autentisering görs, händer följande:

* Först autentiseras användaren (åtgärden lyckas eller misslyckas) och resultatet lagras i Azure Active Directory-tjänstdatabaser.
* De senaste inloggningarna bearbetas med jämna mellanrum. I detta läge söker våra säkerhetsalgoritmer och algoritmer för avvikande aktivitet igenom de senaste inloggningarna och letar efter misstänkt aktivitet.
* Efter bearbetningen skrivs, cachelagras och hanteras rapporterna på den klassiska Azure-portalen.

### <a name="report-generation-times"></a>Rapportera genereringstider
På grund av den stora mängden autentiseringar och inloggningar som bearbetas av Azure AD-plattformen är de senaste inloggningarna som behandlas i genomsnitt en timme gamla. I sällsynta fall kan det ta upp till åtta timmar att bearbeta de senaste inloggningarna.

Du hittar den senast bearbetade inloggningen genom att granska hjälptexten längst upp i varje rapport.

![Hjälptext längst upp i varje rapport](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [!TIP]
> Mer dokumentation om Azure AD Reporting finns i [Visa åtkomst- och användningsrapporter](active-directory-view-access-usage-reports.md).
> 
> 

## <a name="getting-started"></a>Komma igång
### <a name="sign-into-the-azure-classic-portal"></a>Logga in på den klassiska Azure-portalen.
Först måste du logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) som global administratör eller efterlevnadsadministratör. Du måste också vara administratör eller medadministratör för Azure-prenumerationstjänster eller använda Azure-prenumerationen ”Åtkomst till Azure AD”.

### <a name="navigate-to-reports"></a>Gå till Rapporter
Du visar rapporterna genom att gå till fliken Rapporter längst upp i din katalog.

Om det här är första gången du visar rapporterna måste du accepterar en dialogruta innan du kan visa rapporterna. Avsikten med detta är att bekräfta att administratörer i organisationen får se dessa data, som kan betraktas som privat information i vissa länder.

![Dialogruta](./media/active-directory-reporting-getting-started/dialogBox.png)

### <a name="explore-each-report"></a>Utforska varje rapport
Navigera in i varje rapport för att se de data som samlas in och de inloggningar som bearbetas. Du hittar en [lista över alla rapporter här](active-directory-reporting-guide.md).

![Alla rapporter](./media/active-directory-reporting-getting-started/reportsMain.png)

### <a name="download-the-reports-as-csv"></a>Ladda ned rapporterna som CSV
Alla rapporter kan laddas ned som CSV-filer (kommaavgränsade). Du kan använda de här filerna i Excel, PowerBI eller i analysprogram från tredje part för att ytterligare analysera dina data.

Om du vill ladda ned en rapport som en CSV-fil går du till rapporten och klickar på ”Ladda ned” längst ned.

![Knappen Ladda ned](./media/active-directory-reporting-getting-started/downloadButton.png)

> [!TIP]
> Mer dokumentation om Azure AD Reporting finns i [Visa åtkomst- och användningsrapporter](active-directory-view-access-usage-reports.md).
> 
> 

## <a name="next-steps"></a>Nästa steg
### <a name="customize-alerts-for-anomalous-sign-in-activity"></a>Anpassa aviseringar om avvikande inloggningsaktivitet
Gå till fliken ”Konfigurera” i din katalog.

Rulla ned till avsnittet ”Meddelanden”.

Aktivera eller inaktivera avsnittet ”Skicka e-postmeddelanden om avvikande inloggningar”.

![Avsnittet Meddelanden](./media/active-directory-reporting-getting-started/notificationsSection.png)

### <a name="integrate-with-the-azure-ad-reporting-api"></a>Integrera med Azure AD Reporting-API:et
Läs [Komma igång med Reporting-API:et](active-directory-reporting-api-getting-started.md).

### <a name="engage-multifactor-authentication-on-users"></a>Aktivera Multi-Factor Authentication för användare
Välj en användare i en rapport.

Klicka på knappen ”Aktivera MFA” längst ned på skärmen.

![Knappen Multi-Factor Authentication längst ned på skärmen](./media/active-directory-reporting-getting-started/mfaButton.png)

> [!TIP]
> Mer dokumentation om Azure AD Reporting finns i [Visa åtkomst- och användningsrapporter](active-directory-view-access-usage-reports.md).
> 
> 

## <a name="learn-more"></a>Läs mer
### <a name="audit-events"></a>Granska händelser
Lär dig mer om vilka händelser som granskas i katalogen i [Granskningshändelser i Azure Active Directory Reporting](active-directory-reporting-audit-events.md).

### <a name="api-integration"></a>API-integrering
Läs [Komma igång med Reporting-API:et](active-directory-reporting-api-getting-started.md) och [API-referensdokumentationen](https://msdn.microsoft.com/library/azure/mt126081.aspx).

### <a name="get-in-touch"></a>Kontakta oss
E-post [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) för feedback, hjälp eller eventuella frågor.

> [!TIP]
> Mer dokumentation om Azure AD Reporting finns i [Visa åtkomst- och användningsrapporter](active-directory-view-access-usage-reports.md).
> 
> 




<!--HONumber=Nov16_HO2-->


