---
title: Hitta aktivitetsrapporter för Azure Active Directory-användare i Azure-portalen | Microsoft Docs
description: Lär dig var aktivitetsrapporter för Azure Active Directory-användare finns i Azure-portalen.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65e803ca373fb9853fc23d17f1a27ecadc6a209c
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295223"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Hitta aktivitetsrapporter i Azure portal

I den här artikeln får lära du att hitta aktivitetsrapporter för Azure Active Directory (Azure AD)-användare i Azure-portalen.

## <a name="audit-logs-report"></a>Granskningsloggar

Granskningsloggar kombinerar flera rapporter kring program aktiviteter i en enda vy för kontext-baserade rapporter. Komma åt granskningsloggar:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj din katalog från det övre högra hörnet och välj sedan den **Azure Active Directory** bladet från det vänstra navigeringsfönstret.
3. Välj **granskningsloggar** från den **aktivitet** på bladet Azure Active Directory. 

    ![Granskningsloggar](./media/howto-find-activity-reports/482.png "Granskningsloggar")

Granskningsloggar konsoliderar följande rapporter:

* Granskningsrapport
* Lösenordsåterställningsaktivitet
* Lösenordsåterställningsaktivitet för registrering
* Aktiviteter för självbetjäningsgrupper
* Namnändringar för Office 365-grupp
* Kontot etablering aktivitet
* Status för förnyelse av lösenord
* Kontoetableringsfel

### <a name="filtering-on-audit-logs"></a>Filtrering på granskningsloggar

Du kan använda avancerade filter i granskningsrapporten för att få åtkomst till en särskild kategori av granskningsdata, genom att ange den i den **kategori** filter. Till exempel vill visa alla aktiviteter som rör användare väljer den **UserManagement** kategori. 

Kategorierna är:

- Alla
- AdministrativeUnit
- ApplicationManagement
- Authentication
- Auktorisering
- Kontakt
- Enhet
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Annat
- Princip
- ResourceManagement
- RoleManagement
- UserManagement

Du kan också filtrera på en specifik tjänst med hjälp av den **Service** listrutan filter. Till exempel för att få alla granskningshändelser som rör hantering av lösenord för självbetjäning kan välja den **Self-service lösenordshantering** filter.

Tjänsterna omfattar:

- Alla
- Åtkomstgranskningar
- Kontoetablering 
- Programmet SSO
- Autentiseringsmetoder
- B2C
- Villkorlig åtkomst
- Kärnkatalog
- Berättigandehantering
- Identity Protection
- Inbjudna användare
- PIM
- Självbetjäning, grupphantering
- Hantering av lösenord för självbetjäning
- Användningsvillkor

## <a name="sign-ins-report"></a>Rapport över inloggningar 

Den **inloggningar** vyn innehåller alla användarinloggningar, samt de **programanvändning** rapporten. Du kan också visa information om användningen av program i den **hantera** delen av den **företagsprogram** Översikt.

Öppna rapporten inloggningar:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj din katalog från det övre högra hörnet och välj sedan den **Azure Active Directory** bladet från det vänstra navigeringsfönstret.
3. Välj **inloggningar** från den **aktivitet** på bladet Azure Active Directory. 

    ![Visa inloggningar](./media/howto-find-activity-reports/483.png "visa inloggningar")


### <a name="filtering-on-application-name"></a>Filtrera efter namn

Du kan använda rapporten inloggningar för att visa information om appanvändningen genom att filtrera efter användarnamn eller programnamn.

![Filtrera inloggningshändelser sidan](./media/howto-find-activity-reports/07.png "filtrera inloggningshändelser sidan")

## <a name="security-reports"></a>Säkerhetsrapporter

### <a name="anomalous-activity-reports"></a>Avvikande aktivitetsrapporter

Avvikande aktivitetsrapporter innehåller information om säkerhetsrelaterade riskhändelser som Azure AD kan identifiera och rapportera om.

Följande tabell visar Azure AD avvikande aktivitet säkerhetsrapporter och motsvarande typer av riskhändelser på Azure-portalen. Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](concept-risk-events.md).  


| Azure AD avvikande Aktivitetsrapport |  Identity protection riskhändelsetyp|
| :--- | :--- |
| Användare med läckta autentiseringsuppgifter | Läckta autentiseringsuppgifter |
| Oregelbunden inloggningsaktivitet | Omöjliga resor till ovanliga platser |
| Inloggningar från potentiellt infekterade enheter | Inloggningar från angripna enheter|
| Inloggningar från okända källor | Inloggningar från anonyma IP-adresser |
| Inloggningar från IP-adresser med misstänkt aktivitet | Inloggningar från IP-adresser med misstänkt aktivitet |
| - | Inloggningar från okända platser |

I följande Azure AD-säkerhetsgrupper för avvikande aktivitet rapporterar inte ingår som riskhändelser på Azure-portalen:

* Inloggningar efter flera fel
* Inloggningar från flera geografiska områden


### <a name="detected-risk-events"></a>Identifierade riskhändelserna

Du kan komma åt rapporter om identifierade riskhändelserna i den **Security** delen av den **Azure Active Directory** -bladet i den [Azure-portalen](https://portal.azure.com). Identifierade riskhändelserna spåras i följande rapporter:   

- [Användare i riskzonen](concept-user-at-risk.md)
- [Riskfyllda inloggningar](concept-risky-sign-ins.md)

    ![Säkerhetsrapporter](./media/howto-find-activity-reports/04.png "säkerhetsrapporter")

## <a name="troubleshoot-issues-with-activity-reports"></a>Felsöka problem med aktivitetsrapporter

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Saknade data i nedladdade aktivitetsloggar

#### <a name="symptoms"></a>Symtom 

Jag har hämtat aktivitetsloggarna (granskning eller inloggningar) och kan inte se alla poster för den tid som jag har valt. Varför? 

 ![Rapportering](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Orsak

När du hämtar aktivitetsloggar i Azure portal begränsar vi omfattningen till 250000 poster, sorterade efter senaste först. 

#### <a name="resolution"></a>Lösning

Du kan använda [rapporterings-API:er för Azure AD](concept-reporting-api.md) att hämta upp till en miljoner poster när som helst.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Saknas granskningsdata för senaste åtgärder i Azure portal

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

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Logga saknas loggar för de senaste användarinloggningar i Azure AD-inloggningar aktivitet

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

Jag kan inte se mer än 30 dagars inloggnings- och granskningsdata från Azure-portalen. Varför? 

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

* [Granska loggar översikt](concept-audit-logs.md)
* [Översikt över inloggningar](concept-sign-ins.md)
* [Översikt över riskfyllda händelser](concept-risk-events.md)
