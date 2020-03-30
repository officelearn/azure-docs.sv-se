---
title: Hitta användaraktivitetsrapporter i Azure Portal | Microsoft-dokument
description: Lär dig var Azure Active Directory-användaraktivitetsrapporterna finns i Azure-portalen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d356f8c10ae3770d9f4ade100ab0496ee58d772f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008219"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Hitta aktivitetsrapporter på Azure-portalen

I den här artikeln får du lära dig hur du hittar Azure Active Directory (Azure AD) användaraktivitetsrapporter i Azure-portalen.

## <a name="audit-logs-report"></a>Granskningsloggar

Granskningsloggrapporten kombinerar flera rapporter kring programaktiviteter i en enda vy för kontextbaserad rapportering. Så här öppnar du granskningsloggrapporten:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj din katalog i det övre högra hörnet och välj sedan **Azure Active Directory-bladet** i det vänstra navigeringsfönstret.
3. Välj **Granskningsloggar** från avsnittet **Aktivitet** i Azure Active Directory-bladet. 

    ![Granskningsloggar](./media/howto-find-activity-reports/482.png "Granskningsloggar")

Granskningsloggrapporten konsoliderar följande rapporter:

* Granskningsrapport
* Lösenordsåterställningsaktivitet
* Registreringsaktivitet för återställning av lösenord
* Aktivitet för självbetjäningsgrupper
* Ändringar av office365-gruppnamn
* Kontotableringsaktivitet
* Status för överrullning av lösenord
* Kontoetableringsfel

### <a name="filtering-on-audit-logs"></a>Filtrering i granskningsloggar

Du kan använda avancerad filtrering i granskningsrapporten för att komma åt en viss kategori av granskningsdata genom att ange den i **filtret Kategori.** Om du till exempel vill visa alla aktiviteter som är relaterade till användare väljer du kategorin **UserManagement.** 

Kategorier inkluderar:

- Alla
- AdministrativeUnit
- ProgramManagement
- Autentisering
- Auktorisering
- Kontakt
- Enhet
- DeviceConfiguration
- KatalogHanagement
- Rättshantagement
- GruppHantverk
- Annat
- Princip
- ResursHantering
- RollHanagement
- UserManagement Användare

Du kan också filtrera på en viss tjänst med hjälp av listrutan **Tjänst.** Om du till exempel vill hämta alla granskningshändelser relaterade till lösenordshantering med självbetjäning väljer du filtret **Lösenordshantering för självbetjäning.**

Tjänsterna omfattar:

- Alla
- Åtkomstgranskningar
- Kontoetablering 
- Ansökan SSO
- Autentiseringsmetoder
- B2C
- Villkorlig åtkomst
- Kärnkatalog
- Hantering av rättigheter
- Identity Protection
- Inbjudna användare
- PIM
- Självbetjäning, grupphantering
- Hantering av lösenord för självbetjäning
- Användningsvillkor

## <a name="sign-ins-report"></a>Rapport över inloggningar 

Vyn **Inloggningar** innehåller alla inloggningar för användare samt rapporten **Programanvändning.** Du kan också visa programanvändningsinformation i avsnittet **Hantera** i översikten **Företagsprogram.**

Så här öppnar du inloggningsrapporten:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj din katalog i det övre högra hörnet och välj sedan **Azure Active Directory-bladet** i det vänstra navigeringsfönstret.
3. Välj **Inloggningar i** avsnittet **Aktivitet** i Azure Active Directory-bladet. 

    ![Inloggningsvyn](./media/howto-find-activity-reports/483.png "Inloggningsvyn")


### <a name="filtering-on-application-name"></a>Filtrering på programnamn

Du kan använda inloggningsrapporten för att visa information om programanvändning genom att filtrera efter användarnamn eller programnamn.

![Sidan Filtrera inloggningshändelser](./media/howto-find-activity-reports/07.png "Sidan Filtrera inloggningshändelser")

## <a name="security-reports"></a>Säkerhetsrapporter

### <a name="anomalous-activity-reports"></a>Avvikande aktivitetsrapporter

Avvikande aktivitetsrapporter innehåller information om säkerhetsrelaterade riskidentifieringar som Azure AD kan identifiera och rapportera om.

I följande tabell visas de avvikande aktivitetssäkerhetsrapporterna för Azure AD och motsvarande riskidentifieringstyper i Azure-portalen. Mer information finns i [Azure Active Directory-riskidentifieringar](concept-risk-events.md).  


| Avvikande aktivitetsrapport för Azure AD |  Typ av identifiering av identitetsskydd|
| :--- | :--- |
| Användare med läckta autentiseringsuppgifter | Läckta autentiseringsuppgifter |
| Oregelbunden inloggningsaktivitet | Omöjliga resor till ovanliga platser |
| Inloggningar från potentiellt infekterade enheter | Inloggningar från angripna enheter|
| Inloggningar från okända källor | Inloggningar från anonyma IP-adresser |
| Inloggningar från IP-adresser med misstänkt aktivitet | Inloggningar från IP-adresser med misstänkt aktivitet |
| - | Inloggningar från okända platser |

Följande Azure AD-avvikande aktivitetssäkerhetsrapporter inkluderas inte som riskidentifieringar i Azure-portalen:

* Inloggningar efter flera fel
* Inloggningar från flera geografiska områden


### <a name="detected-risk-detections"></a>Upptäckta riskidentifieringar

Du kan komma åt rapporter om identifierade riskidentifieringar i avsnittet **Säkerhet** i **Azure Active Directory-bladet** i [Azure-portalen](https://portal.azure.com). Identifierade riskidentifieringar spåras i följande rapporter:   

- [Användare i riskzonen](concept-user-at-risk.md)
- [Riskfyllda inloggningar](concept-risky-sign-ins.md)

    ![Säkerhetsrapporter](./media/howto-find-activity-reports/04.png "Säkerhetsrapporter")

## <a name="troubleshoot-issues-with-activity-reports"></a>Felsöka problem med aktivitetsrapporter

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Data saknas i de nedladdade aktivitetsloggarna

#### <a name="symptoms"></a>Symtom 

Jag har hämtat aktivitetsloggarna (granskning eller inloggningar) och kan inte se alla poster för den tid som jag har valt. Varför det? 

 ![Rapportering](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Orsak

När du hämtar aktivitetsloggar i Azure-portalen begränsar vi skalan till 250000 poster, sorterade efter den senaste först. 

#### <a name="resolution"></a>Lösning

Du kan använda [rapporterings-API:er för Azure AD](concept-reporting-api.md) att hämta upp till en miljoner poster när som helst.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Granskningsdata saknas för de senaste åtgärderna i Azure-portalen

#### <a name="symptoms"></a>Symtom

Jag utförde vissa åtgärder i Azure Portal och förväntade att se granskningsloggarna för dessa åtgärder på bladet `Activity logs > Audit Logs`, men det går inte att hitta dem.

 ![Rapportering](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>Orsak

Åtgärder visas inte omedelbart i aktivitetsloggarna. Tabellen nedan räknar upp våra svarstidsvärden för aktivitetsloggar. 

| Rapport | &nbsp; | Svarstid (P95) | Svarstid (P99) |
|--------|--------|---------------|---------------|
| Kataloggranskning | &nbsp; | 2 min | 5 min |
| Inloggningsaktivitet | &nbsp; | 2 min | 5 min | 

#### <a name="resolution"></a>Lösning

Vänta i mellan 15 minuter och 2 timmar och se om åtgärderna visas i loggen. Om du inte ser loggarna även efter två timmar [skapar du en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) så undersöker vi problemet.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Loggar saknas för senaste användarloggningar i aktivitetsloggen för Azure AD-inloggningar

#### <a name="symptoms"></a>Symtom

Jag loggade nyligen in på Azure-portalen och förväntade mig att se inloggningsloggarna för de åtgärderna på bladet `Activity logs > Sign-ins`, men det går inte att hitta dem.

 ![Rapportering](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>Orsak

Åtgärder visas inte omedelbart i aktivitetsloggarna. Tabellen nedan räknar upp våra svarstidsvärden för aktivitetsloggar. 

| Rapport | &nbsp; | Svarstid (P95) | Svarstid (P99) |
|--------|--------|---------------|---------------|
| Kataloggranskning | &nbsp; | 2 min | 5 min |
| Inloggningsaktivitet | &nbsp; | 2 min | 5 min | 

#### <a name="resolution"></a>Lösning

Vänta i mellan 15 minuter och 2 timmar och se om åtgärderna visas i loggen. Om du inte ser loggarna även efter två timmar [skapar du en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) så undersöker vi problemet.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Jag kan inte se mer än 30 dagars rapportdata i Azure-portalen

#### <a name="symptoms"></a>Symtom

Jag kan inte se mer än 30 dagars inloggnings- och granskningsdata från Azure-portalen. Varför det? 

 ![Rapportering](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>Orsak

Beroende på din licens lagrar Azure Active Directory aktivitetsrapporter under så här lång tid:

| Rapport           | &nbsp; |  Azure AD Kostnadsfri | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| Kataloggranskning  | &nbsp; |   7 dagar     | 30 dagar             | 30 dagar             |
| Inloggningsaktivitet | &nbsp; | Inte tillgängligt. Du kan komma åt dina egna inloggningar i 7 dagar från det bladet för enskild användare | 30 dagar | 30 dagar             |

Mer information finns i [Kvarhållningsprinciper för rapporter i Azure Active Directory](reference-reports-data-retention.md).  

#### <a name="resolution"></a>Lösning

Du har två alternativ för att behålla data längre än 30 dagar. Du kan använda [Azure AD Reporting-API:er](concept-reporting-api.md) för att hämta data programmatiskt och lagra dem i en databas. Du kan även integrera spårningsloggar i ett SIEM-system från tredje part, till exempel Splunk eller SumoLogic.

## <a name="next-steps"></a>Nästa steg

* [Översikt över granskningsloggar](concept-audit-logs.md)
* [Översikt över inloggningar](concept-sign-ins.md)
* [Översikt över riskfyllda händelser](concept-risk-events.md)
