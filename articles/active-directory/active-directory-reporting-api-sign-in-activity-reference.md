---
title: Azure Active Directory inloggningsaktivitet rapporten API-referens | Microsoft Docs
description: Referens för Azure Active Directory inloggningsaktivitet rapporten API
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ddcd9ae0-f6b7-4f13-a5e1-6cbf51a25634
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/08/2018
ms.author: dhanyahk;rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: c5d5e11c5178e5b90c2970e52dd1e6f47a78204d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588397"
---
# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Azure Active Directory inloggningsaktivitet rapporten API-referens

> [!TIP] 
> Kolla in nya Microsoft Graph API för [reporting](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit), vilket till slut ersätter detta API. 

Den här artikeln är en del av en samling artiklar om Azure Active Directory (AD Azure) reporting API. Azure AD-rapportering ger dig en API som gör att du kan komma åt granskningsdata via kod eller relaterade verktyg.
Omfånget för den här artikeln är att ge dig referensinformation om den **granska API**.

Se:

* [Logga in aktiviteter](active-directory-reporting-azure-portal.md#activity-reports) mer information
* [Komma igång med Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md) för mer information om reporting API.


## <a name="who-can-access-the-api-data"></a>Vem som har åtkomst till API-data?
* Användare och tjänstens huvudnamn i rollen Administratör säkerhet eller säkerhet läsare
* Globala administratörer
* Alla appar som har behörighet att komma åt API: et (auktorisering i apptjänst kan ställas in endast baserat på Global administratör behörighet)

Använd följande PowerShell att lägga till program tjänstens huvudnamn i rollen Läsare av säkerhet för att konfigurera åtkomst för ett program för att komma åt säkerheten API: er, till exempel inloggning händelser

```PowerShell
Connect-MsolService
$servicePrincipal = Get-MsolServicePrincipal -AppPrincipalId "<app client id>"
$role = Get-MsolRole | ? Name -eq "Security Reader"
Add-MsolRoleMember -RoleObjectId $role.ObjectId -RoleMemberType ServicePrincipal -RoleMemberObjectId $servicePrincipal.ObjectId
```

## <a name="prerequisites"></a>Förutsättningar
Du måste ha för att komma åt den här rapporten via reporting API:

* En [Azure Active Directory Premium P1 eller P2 edition](active-directory-whatis.md)
* Slutfört den [krav för att få åtkomst till Azure AD reporting API](active-directory-reporting-api-prerequisites.md). 

## <a name="accessing-the-api"></a>Åtkomst till API: et
Du kan antingen komma åt den här API via den [diagram Explorer](https://graphexplorer2.cloudapp.net) eller programmässigt med, till exempel PowerShell. Använd backtick (även kallat: allvarligt accent) tecken för att ”avbryta” $-tecken för att säkerställa att PowerShell kan tolka OData-filtersyntaxen som används i AAD diagram REST-anrop. Tecknet backtick fungerar som [PowerShells escape-tecknet](https://technet.microsoft.com/library/hh847755.aspx), vilket gör att PowerShell för att göra en literal tolkning av $-tecken, och undvika förvirrande som ett PowerShell-variabelnamn (till exempel $filter).

Det här avsnittet fokuserar på diagrammet Explorer. Ett PowerShell-exempel finns [PowerShell-skriptet](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script).

## <a name="api-endpoint"></a>API-slutpunkt
Du kan komma åt den här API: et med följande bas-URI:  

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



På grund av mängden data har en gräns på 1 000 000 returnerade poster i detta API. 

Det här anropet returnerar data i batchar. Varje grupp som har högst 1000 poster. Använd nästa länken för att få poster nästa batch. Hämta den [skiptoken](https://msdn.microsoft.com/library/dd942121.aspx) information från den första uppsättningen returnerade poster. Skip-token anges i slutet av resultatet.  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>Filter som stöds
Du kan begränsa antalet poster som returneras av en API-anrop i form av ett filter.  
Logga in API-relaterad data stöder följande filter:

* **$top =\<antal poster som ska returneras\>**  - om du vill begränsa antalet returnerade poster. Det här är en kostsam åtgärd. Använd inte det här filtret om du vill returnera tusentals objekt.  
* **$filter =\<filter-instruktionen\>**  - om du vill ange vilken typ av poster som intresserar dig på grundval av filter som stöds fält

## <a name="supported-filter-fields-and-operators"></a>Filter som stöds fält och operatorer
Du kan skapa ett filter-uttryck som kan innehålla ett eller flera av följande filterfält om du vill ange vilken typ av poster som du är intresserad:

* [signinDateTime](#signindatetime) -definierar ett datum eller datumintervall
* [användar-ID](#userid) -definierar en specifik användare baserat på användar-ID.
* [userPrincipalName](#userprincipalname) -definierar en specifik användare baserat på användarens huvudnamn (UPN)
* [appId](#appid) -definierar en specifik app baserat på app-ID
* [appDisplayName](#appdisplayname) -definierar en specifik app baserat appens namn
* [loginStatus](#loginStatus) -definierar status för inloggningar (lyckade / misslyckade)

> [!NOTE]
> När du använder diagram Explorer, är du behöver ha rätt skiftläge för varje bokstav filterfält.
> 
> 

Du kan skapa kombinationer av fälten stöds filter och filtrera om du vill begränsa omfånget för returnerade data. Till exempel returnerar följande sats de översta 10 posterna mellan juli 1 2016 och juli 6 2016:

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


- - -
### <a name="signindatetime"></a>signinDateTime
**Stöd för operatörer**: eq, ge, le, gt, lt

**Exempel**:

Med hjälp av ett visst datum

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z    



Med hjälp av ett datumintervall    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**Anteckningar**:

Datetime-parametern måste vara i UTC-format 

- - -
### <a name="userid"></a>userId
**Stöd för operatörer**: eq

**Exempel**:

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**Anteckningar**:

Värdet för användar-ID är ett strängvärde

- - -
### <a name="userprincipalname"></a>userPrincipalName
**Stöd för operatörer**: eq

**Exempel**:

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**Anteckningar**:

Värdet för userPrincipalName är ett strängvärde

- - -
### <a name="appid"></a>appId
**Stöd för operatörer**: eq

**Exempel**:

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**Anteckningar**:

Värdet för appId är ett strängvärde

- - -
### <a name="appdisplayname"></a>appDisplayName
**Stöd för operatörer**: eq

**Exempel**:

    $filter=appDisplayName+eq+'Azure+Portal' 


**Anteckningar**:

Värdet för appDisplayName är ett strängvärde

- - -
### <a name="loginstatus"></a>loginStatus
**Stöd för operatörer**: eq

**Exempel**:

    $filter=loginStatus+eq+'1'  


**Anteckningar**:

Det finns två alternativ för loginStatus: 0 - lyckades, 1 – fel

- - -
## <a name="next-steps"></a>Nästa steg
* Vill du se exempel för filtrerade inloggning aktiviteter? Kolla in den [Azure Active Directory inloggningsaktivitet rapporten API-exempel](active-directory-reporting-api-sign-in-activity-samples.md).
* Vill du veta mer om Azure AD reporting API? Se [komma igång med Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md).

