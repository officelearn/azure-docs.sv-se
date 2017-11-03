---
title: Azure Active Directory audit API-referens | Microsoft Docs
description: "Hur du kommer igång med Azure Active Directory granska API"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 44e46be8-09e5-4981-be2b-d474aaa92792
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 242fa094010694d7060b05e5892ce738d5b37a32
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-audit-api-reference"></a>Azure Active Directory audit API-referens
Det här avsnittet är en del av en samling ämnen om Azure Active Directory reporting API.  
Azure AD-rapportering ger dig en API som gör att du kan komma åt granskningsdata via kod eller relaterade verktyg.
Omfånget för det här avsnittet är att ge dig referensinformation om den **granska API**.

Se:

* [Granskningsloggar](active-directory-reporting-azure-portal.md#activity-reports) mer information

* [Komma igång med Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md) för mer information om reporting API.


För:

- Vanliga frågor och svar, Läs vår [vanliga frågor och svar](active-directory-reporting-faq.md) 

- Kontrollera utfärdar [filen ett supportärende](active-directory-troubleshooting-support-howto.md) 


## <a name="who-can-access-the-data"></a>Vem kan komma åt dessa data?
* Användare i rollen säkerhetsadministratör eller säkerhetsläsare
* Globala administratörer
* Alla appar som har behörighet att komma åt API: et (auktorisering i apptjänst kan vara installationen endast baserat på Global administratör behörighet)

## <a name="prerequisites"></a>Krav
Du måste ha för att komma åt den här rapporten via Reporting-API:

* En [Azure Active Directory ledigt eller bättre edition](active-directory-editions.md)
* Slutfört den [krav för att få åtkomst till Azure AD reporting API](active-directory-reporting-api-prerequisites.md). 

## <a name="accessing-the-api"></a>Åtkomst till API: et
Du kan antingen komma åt den här API via den [diagram Explorer](https://graphexplorer2.cloudapp.net) eller programmässigt med, till exempel PowerShell. För PowerShell för att tolka OData-filtersyntaxen som används i AAD diagram REST-anrop, måste du använda backtick (även kallat: allvarligt accent) tecken för att ”avbryta” $-tecken. Tecknet backtick fungerar som [PowerShells escape-tecknet](https://technet.microsoft.com/library/hh847755.aspx), vilket gör att PowerShell för att göra en literal tolkning av $-tecken, och undvika förvirrande som ett PowerShell-variabelnamn (ie: $filter).

Det här avsnittet fokuserar på diagrammet Explorer. Ett PowerShell-exempel finns [PowerShell-skriptet](active-directory-reporting-api-audit-samples.md#powershell-script).

## <a name="api-endpoint"></a>API-slutpunkt
Du kan komma åt den här API: et med följande URI: N:  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Det finns ingen gräns för antalet poster som returneras av Azure AD-granska-API (med OData sidbrytning).
Kvarhållning begränsningar rapportdata, kolla [Reporting bevarandeprinciper](active-directory-reporting-retention.md).

Det här anropet returnerar data i batchar. Varje grupp som har högst 1000 poster.  
Använd nästa länken för att få poster nästa batch. Hämta skiptoken information från den första uppsättningen returnerade poster. Skip-token anges i slutet av resultatet.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Filter som stöds
Du kan begränsa antalet poster som returneras av en API-anrop i form av ett filter.  
Relaterade data, för inloggning API följande filter som stöds:

* **$top =\<antal poster som ska returneras\>**  - om du vill begränsa antalet returnerade poster. Det här är en kostsam åtgärd. Du bör inte använda det här filtret om du vill returnera tusentals objekt.     
* **$filter =\<filter-instruktionen\>**  - om du vill ange vilken typ av poster som intresserar dig på grundval av filter som stöds fält

## <a name="supported-filter-fields-and-operators"></a>Filter som stöds fält och operatorer
Du kan skapa ett filter-uttryck som kan innehålla ett eller flera av följande filterfält om du vill ange vilken typ av poster som du är intresserad:

* [activityDate](#activitydate) -definierar ett datum eller datumintervall
* [kategori](#category) -definierar den kategori som du vill filtrera på.
* [activityStatus](#activitystatus) -definierar status för en aktivitet
* [activityType](#activitytype) -definierar typ av en aktivitet
* [aktiviteten](#activity) -definierar aktiviteten som sträng  
* [namn påskådespelare/](#actorname) -definierar aktören i form av skådespelare, namn
* [aktören/objectid](#actorobjectid) – aktören definierar i form av den aktörs-ID   
* [aktören/upn](#actorupn) – aktören definierar i form av skådespelare, princip (user Principal name) 
* [målnamn/](#targetname) -definierar målet i form av skådespelare, namn
* [mål/objectid](#targetobjectid) -definierar målet i form av den mål-ID  
* [mål/upn](#targetupn) – aktören definierar i form av skådespelare, princip (user Principal name)   

- - -
### <a name="activitydate"></a>activityDate
**Stöd för operatörer**: eq, ge, le, gt, lt

**Exempel**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=activityDate gt ' + $7daysago    

**Anteckningar**:

datetime ska vara i UTC-format

- - -
### <a name="category"></a>category

**Värden som stöds**:

| Kategori                         | Värde     |
| :--                              | ---       |
| Kärnkatalog                   | Katalog |
| Hantering av lösenord för självbetjäning | SSPR      |
| Självbetjäning, grupphantering    | SSGM      |
| Kontoetablering             | Sync      |
| Automatiserad förnyelse av lösenord      | Automatiserad förnyelse av lösenord |
| Identity Protection              | IdentityProtection |
| Inbjudna användare                    | Inbjudna användare |
| MIM-tjänst                      | MIM-tjänst |



**Stöd för operatörer**: eq

**Exempel**:

    $filter=category eq 'SSPR'
- - -
### <a name="activitystatus"></a>ActivityStatus

**Värden som stöds**:

| Aktivitetsstatus | Värde |
| :--             | ---   |
| Lyckades         | 0     |
| Fel         | - 1   |

**Stöd för operatörer**: eq

**Exempel**:

    $filter=activityStatus eq -1    

---
### <a name="activitytype"></a>activityType
**Stöd för operatörer**: eq

**Exempel**:

    $filter=activityType eq 'User'    

**Anteckningar**:

skiftlägeskänsligt

- - -
### <a name="activity"></a>Aktivitet
**Stöd för operatörer**: eq, innehåller startsWith

**Exempel**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')    

**Anteckningar**:

skiftlägeskänsligt

- - -
### <a name="actorname"></a>aktören/namn
**Stöd för operatörer**: eq, innehåller startsWith

**Exempel**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')    

**Anteckningar**:

Icke skiftlägeskänslig

- - -
### <a name="actorobjectid"></a>aktören/objectId
**Stöd för operatörer**: eq

**Exempel**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    


- - -
### <a name="targetname"></a>målnamn /
**Stöd för operatörer**: eq, innehåller startsWith

**Exempel**:

    $filter=targets/any(t: t/name eq 'some name')    

**Anteckningar**:

Icke skiftlägeskänslig

- - -
### <a name="targetupn"></a>mål/upn
**Stöd för operatörer**: eq, startsWith

**Exempel**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc'))    

**Anteckningar**:

* Icke skiftlägeskänslig
* Du måste lägga till det fullständiga namnområdet när du frågar Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity

- - -
### <a name="targetobjectid"></a>mål/objectId
**Stöd för operatörer**: eq

**Exempel**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

- - -
### <a name="actorupn"></a>aktören/upn
**Stöd för operatörer**: eq, startsWith

**Exempel**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')    

**Anteckningar**:

* Icke skiftlägeskänslig 
* Du måste lägga till det fullständiga namnområdet när du frågar Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity

- - -
## <a name="next-steps"></a>Nästa steg
* Vill du se exempel för filtrerade systemaktiviteter? Kolla in den [Azure Active Directory audit API-exempel](active-directory-reporting-api-audit-samples.md).
* Vill du veta mer om Azure AD reporting API? Se [komma igång med Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md).

