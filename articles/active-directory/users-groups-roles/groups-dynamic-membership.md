---
title: Dynamiska regler för automatisk gruppmedlemskap refererar till i Azure Active Directory | Microsoft Docs
description: Hur du skapar regler för att automatiskt fylla i grupper och en Regelreferens för medlemskap.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 11/07/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: c2e50362de48991c818017b59632be3b0e74cb0b
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282075"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Regler för dynamiskt medlemskap för grupper i Azure Active Directory

I Azure Active Directory (AD Azure), kan du skapa komplexa attributbaserade regler för att aktivera dynamiskt medlemskap för grupper. Dynamiskt gruppmedlemskap minskar det administrativa arbetet med att lägga till och ta bort användare. Den här artikeln beskriver egenskaper och syntaxen för att skapa regler för dynamiskt medlemskap för användare eller enheter. Du kan skapa en regel för dynamiskt medlemskap för säkerhetsgrupper eller Office 365-grupper.

När ett attribut för en användare eller enhet ändras utvärderar systemet alla dynamiska gruppregler i en katalog för att se om ändringen ska utlösa någon grupp lägger till eller tar bort. Om en användare eller enhet uppfyller en regel i en grupp, läggs de som en medlem i gruppen. Om användaren inte längre uppfyller regeln tas bort. Du kan manuellt lägga till eller ta bort en medlem i en dynamisk grupp.

* Du kan skapa en dynamisk grupp för enheter eller användare, men du kan inte skapa en regel som innehåller både användare och enheter.
* Du kan inte skapa en enhetsgrupp baserat på enhetens ägare attribut. Medlemskapsregler för enheten kan bara referera enhetsattribut.

> [!NOTE]
> Den här funktionen kräver en Azure AD Premium P1-licens för varje unika användare som är medlem i en eller flera dynamiska grupper. Du behöver inte tilldela licenser till användare för dem att vara medlemmar i dynamiska grupper, men du måste ha det minsta antalet licenser i klientorganisationen för att täcka alla användare. Om du hade en totalsumma på 1 000 unika användare i alla dynamiska grupper i din klient måste till exempel minst 1 000 licenser för Azure AD Premium P1 att uppfylla kravet på licens.
>

## <a name="constructing-the-body-of-a-membership-rule"></a>Konstruera brödtexten i en medlemskapsregel

En medlemskapsregel som automatiskt fyller på en grupp med användare eller enheter är en binär uttryck som resulterar i ett sant eller FALSKT resultat. De tre delarna i en enkel regel är:

* Egenskap 
* Operator
* Värde

Ordningen på delarna i ett uttryck är viktiga för att undvika syntaxfel.

### <a name="rules-with-a-single-expression"></a>Regler med ett enda uttryck

Ett enda uttryck är den enklaste formen av en medlemskapsregel och har bara tre delar som nämns ovan. En regel med ett enda uttryck ser ut ungefär så här: `Property Operator Value`, där syntaxen för egenskapen är namnet på object.property.

Följande är ett exempel på ett korrekt angivet medlemskapsregel med ett enda uttryck:

```
user.department -eq "Sales"
```

Parenteser är valfria för ett enda uttryck. Den totala längden på innehållet i din medlemskapsregel får inte överskrida 2048 tecken.

## <a name="supported-properties"></a>Egenskaper som stöds

Det finns tre typer av egenskaper som kan användas för att konstruera en medlemskapsregel.

* Boolesk
* Sträng
* Sträng-samling

Här följer användaregenskaper som du kan använda för att skapa ett enkelt uttryck.

### <a name="properties-of-type-boolean"></a>Egenskaper för skriver booleskt värde

| Egenskaper | Tillåtna värden | Användning |
| --- | --- | --- |
| accountEnabled |SANT FALSKT |user.accountEnabled - eq SANT |
| dirSyncEnabled |SANT FALSKT |user.dirSyncEnabled - eq SANT |

### <a name="properties-of-type-string"></a>Egenskaper av typen sträng

| Egenskaper | Tillåtna värden | Användning |
| --- | --- | --- |
| city |Någon strängvärde eller *null* |(user.city - eq ”value”) |
| Land |Någon strängvärde eller *null* |(user.country - eq ”value”) |
| Företagsnamn | Någon strängvärde eller *null* | (user.companyName - eq ”value”) |
| Avdelning |Någon strängvärde eller *null* |(user.department - eq ”value”) |
| displayName |ett värde |(user.displayName - eq ”value”) |
| employeeId |ett värde |(user.employeeId - eq ”value”)<br>(user.employeeId - ne *null*) |
| facsimileTelephoneNumber |Någon strängvärde eller *null* |(user.facsimileTelephoneNumber - eq ”value”) |
| givenName |Någon strängvärde eller *null* |(user.givenName - eq ”value”) |
| jobTitle |Någon strängvärde eller *null* |(user.jobTitle - eq ”value”) |
| e-post |Någon strängvärde eller *null* (SMTP-adress för användaren) |(user.mail - eq ”value”) |
| mailNickName |Ett värde (e-postalias för användaren) |(user.mailNickName - eq ”value”) |
| mobila |Någon strängvärde eller *null* |(user.mobile - eq ”value”) |
| objekt-ID |GUID för användarobjektet |(user.objectId - eq ”11111111-1111-1111-1111-111111111111”) |
| onPremisesSecurityIdentifier | Lokala säkerhetsidentifierare (SID) för användare som har synkroniserats från lokalt till molnet. |(user.onPremisesSecurityIdentifier - eq ”S-1-1-11-1111111111-1111111111-1111111111-1111111”) |
| passwordPolicies |Ingen DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies - eq ”DisableStrongPassword”) |
| physicalDeliveryOfficeName |Någon strängvärde eller *null* |(user.physicalDeliveryOfficeName - eq ”value”) |
| Postnummer |Någon strängvärde eller *null* |(user.postalCode - eq ”value”) |
| preferredLanguage |ISO 639-1-kod |(user.preferredLanguage - eq ”en-US”) |
| sipProxyAddress |Någon strängvärde eller *null* |(user.sipProxyAddress - eq ”value”) |
| state |Någon strängvärde eller *null* |(user.state - eq ”value”) |
| streetAddress |Någon strängvärde eller *null* |(user.streetAddress - eq ”value”) |
| Efternamn |Någon strängvärde eller *null* |(user.surname - eq ”value”) |
| telephoneNumber |Någon strängvärde eller *null* |(user.telephoneNumber - eq ”value”) |
| usageLocation |Två bokstäver landskod |(user.usageLocation - eq ”US”) |
| userPrincipalName |ett värde |(user.userPrincipalName - eq ”alias@domain”) |
| UserType |medlem gäst *null* |(user.userType - eq ”medlem”) |

### <a name="properties-of-type-string-collection"></a>Egenskaper av typen sträng samling

| Egenskaper | Tillåtna värden | Användning |
| --- | --- | --- |
| otherMails |ett värde |(user.otherMails-innehåller ”alias@domain”) |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses-innehåller ”SMTP: alias@domain”) |

Egenskaper som används för regler för enhet, se [regler för enheters](#rules-for-devices).

## <a name="supported-operators"></a>Operatorer som stöds

I följande tabell visas alla operatorer som stöds och deras syntax för ett enda uttryck. Operatörer kan användas med eller utan prefixet bindestreck (-).

| Operator | Syntax |
| --- | --- |
| Inte lika med |-ne |
| Lika med |-eq |
| Börjar inte med |-notStartsWith |
| Börjar med |startsWith- |
| Innehåller inte |-notContains |
| Contains |-innehåller |
| Matchar inte |-notMatch |
| Matchning |-matchar |
| I | -i |
| Inte i | -notIn |

### <a name="using-the--in-and--notin-operators"></a>Med hjälp av-i och - notIn operatörer

Om du vill jämföra värdet för ett användarattribut mot ett antal olika värden kan du använda-i eller - notIn operatörer. Använda hakparentes symbolerna ”[” och ”]” börja och sluta i listan med värden.

 I följande exempel utvärderas uttrycket till true om värdet för user.department är lika med något av värdena i listan:

```
   user.department -in ["50001","50002","50003",“50005”,“50006”,“50007”,“50008”,“50016”,“50020”,“50024”,“50038”,“50039”,“51100”]
```


### <a name="using-the--match-operator"></a>Med hjälp av operatorn - matchning 
Den **-matchar** operator används för att matcha ett reguljärt uttryck. Exempel:

```
user.displayName -match "Da.*"   
```
DA, Dav, David utvärderas till SANT, aDa utvärderas till false.

```
user.displayName -match ".*vid"
```
David utvärderas till SANT, Da utvärderas till false.

## <a name="supported-values"></a>Värden som stöds

De värden som används i ett uttryck kan bestå av flera typer, inklusive:

* Strängar
* Booleska – SANT, FALSKT
* Siffror
* Matriser – antal array, strängmatris

Det är viktigt att du använder rätt syntax för att undvika fel när du anger ett värde i ett uttryck. Tips syntax är:

* Dubbla citattecken är valfria, såvida inte värdet är en sträng.
* Sträng-och regex är inte skiftlägeskänsliga.
* När ett strängvärde innehåller dubbla citattecken, både citattecken ska gås ur med den \` tecken, till exempel user.department - eq \`”försäljning\`” är korrekt syntax när ”försäljning” är värdet.
* Du kan också utföra Null kontroller med null som ett värde, till exempel `user.department -eq null`.

### <a name="use-of-null-values"></a>Användning av Null-värden

Om du vill ange ett null-värde i en regel kan du använda den *null* värde. 

* Använd - eq eller -ne när jämföra den *null* värde i ett uttryck.
* Använd citattecken runt ordet *null* endast om du vill att den ska tolkas som en teckensträng-värde.
* -Inte operatör kan inte användas som en jämförande operator för null. Om du använder det, får du ett felmeddelande om du använder null eller $null.

Det korrekta sättet att referera till null-värdet är följande:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Regler med flera uttryck

En gruppmedlemskapsregel kan bestå av fler än en enda uttryck med- och, - eller, och - inte logiska operatorer. Logiska operatorer kan också användas tillsammans. 

Här följer några exempel på korrekt angivet medlemskapsregler med flera uttryck:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Operatorer

Alla operatörer listas nedan i ordning och prioritet från högsta till lägsta. Operatörer på samma rad har samma prioritet:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Följande är ett exempel för operatorer där två uttryck utvärderas för användaren:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Parenteser behövs bara om prioritet inte uppfyller dina krav. Till exempel om du vill att avdelningen som ska utvärderas först visas nedan hur parenteser kan användas för att fastställa ordningen:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Regler med komplexa uttryck

En medlemskapsregel kan bestå av komplexa uttryck där egenskaper, operatorer och värden ta på mer komplexa formulär. Uttryck anses komplexa när något av följande villkor uppfylls:

* Egenskapen som består av en uppsättning värden. mer specifikt med flera värden egenskaper
* Använda uttrycken-alla och -alla operatörer
* Värdet för uttrycket kan vara en eller flera uttryck

## <a name="multi-value-properties"></a>Egenskaper för flera värden

Egenskaper för flera värden är samlingar av objekt av samma typ. De kan användas för att skapa regler för medlemskap med hjälp av-alla och -alla logiska operatorer.

| Egenskaper | Värden | Användning |
| --- | --- | --- |
| assignedPlans | Varje objekt i samlingen visar egenskaperna för följande sträng: capabilityStatus, tjänst, servicePlanId |user.assignedPlans-alla (assignedPlan.servicePlanId - eq ”efb87545-963c-4e0d-99df-69c6916d9eb0”- och assignedPlan.capabilityStatus - eq ”Enabled”) |
| proxyAddresses| SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses-alla (\_ -innehåller ”contoso”)) |

### <a name="using-the--any-and--all-operators"></a>Med hjälp av-alla och -alla operatörer

Du kan använda - alla och -alla operatörer att tillämpa ett villkor för en eller alla objekt i samlingen, respektive.

* -alla (uppfyllt när minst ett objekt i samlingen matchar villkoret)
* -alla (uppfyllt när alla objekt i samlingen matchar villkoret)

#### <a name="example-1"></a>Exempel 1

assignedPlans är en egenskap för flera värden som visar en lista över alla tjänstplaner som tilldelats användaren. Följande uttryck väljer användare som har den Exchange Online (Plan 2) service-planen (som ett GUID-värde) som också är tillståndet aktiverad:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

En regel som den här kan användas till att gruppen Alla användare som en Office 365 (eller andra Microsoft-onlinetjänst) funktionen är aktiverad. Du kan använda med en uppsättning principer i gruppen.

#### <a name="example-2"></a>Exempel 2

Följande uttryck väljer alla användare som har alla service-plan som är associerad med Intune-tjänsten (som identifieras av tjänstnamn ”SCO”):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore--syntax"></a>Med understreck (\_) syntax

Understreck (\_) syntax matchar förekomster av ett specifikt värde i ett flervärdesattribut strängegenskaper samling att lägga till användare eller enheter i en dynamisk grupp. Den används med-ansvariga för några eller -alla.

Här är ett exempel på hur du använder understreck (\_) i en regel för att lägga till medlemmar baserat på user.proxyAddress (det fungerar på samma sätt för user.otherMails). Den här regeln lägger till alla användare med proxyadress som innehåller ”contoso” i gruppen.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Andra egenskaper och vanliga regler

### <a name="create-a-direct-reports-rule"></a>Skapa en regel för ”direktrapporter”

Du kan skapa en grupp som innehåller alla direktrapporter av en hanterare. När den chefens rapporter ändras i framtiden, justeras automatiskt gruppens medlemskap.

Direktrapporter regeln skapas med följande syntax:

```
Direct Reports for "{objectID_of_manager}"
```

Här är ett exempel på en giltig regel där ”62e19b97-8b3d-4d4a-a106-4ce66896a863” är objectID för hanteraren för:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

Följande tips kan hjälpa dig att använda regeln korrekt.

* Den **ID Manager** är objekt-ID för chefen. Det finns i chefens **profil**.
* Kontrollera att regeln ska fungera för den **Manager** egenskapen är korrekt inställda för användare i din klient. Du kan kontrollera det aktuella värdet i användarens **profil**.
* Den här regeln stöder endast chefens direktrapporter. Med andra ord, du kan inte skapa en grupp med chefens direktrapporter *och* sina rapporter.
* Den här regeln kan inte kombineras med andra medlemskapsregler som.

### <a name="create-an-all-users-rule"></a>Skapa en regel för ”alla användare”

Du kan skapa en grupp som innehåller alla användare i en klient med hjälp av en medlemskapsregel. När användare läggs till eller tas bort från klientorganisationen i framtiden, justeras automatiskt gruppens medlemskap.

”Alla användare”-regel skapas med hjälp av enda uttryck med hjälp av operatorn - ne och null-värde. Den här regeln lägger till B2B-gästanvändare samt användare i gruppen.

```
user.objectid -ne null
```

### <a name="create-an-all-devices-rule"></a>Skapa en regel för ”alla enheter”

Du kan skapa en grupp som innehåller alla enheter inom en klient med hjälp av en medlemskapsregel. När enheter läggs till eller tas bort från klientorganisationen i framtiden, justeras automatiskt gruppens medlemskap.

Regeln ”alla enheter” konstrueras med enda uttryck med hjälp av operatorn - ne och null-värde:

```
device.objectid -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Tilläggsegenskaper och anpassade tilläggsegenskaper

Tilläggsattribut och anpassade extenson egenskaper stöds som egenskaper för anslutningssträngar i regler för dynamiskt medlemskap. Tilläggsattribut synkroniseras från den lokala Windows Server AD och vidta formatet för ”ExtensionAttributeX”, där X är lika med 1 – 15. Här är ett exempel på en regel som använder ett tilläggsattribut som en egenskap:

```
(user.extensionAttribute15 -eq "Marketing")
```

Anpassade tilläggsegenskaper synkroniseras från en lokal Windows Server AD eller från ett anslutna SaaS-program och är i formatet `user.extension_[GUID]__[Attribute]`, där:

* [GUID] är den unika identifieraren i Azure AD för det program som skapade egenskapen i Azure AD
* [Attribut] är namnet på egenskapen som den skapades

Ett exempel på en regel som använder ett anpassat tilläggs-egenskapen är:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber -eq "123"
```

Anpassade egenskapsnamnet kan hittas i katalogen genom att fråga en egenskap med hjälp av Graph-testaren och söker efter egenskapsnamnet. Dessutom kan du nu välja **hämta anpassade tilläggsegenskaper** länken i dynamisk användare grupp regeln builder för att ange ett unikt app-ID och få en fullständig lista över anpassat tilläggs-egenskaper som ska användas när du skapar en regel för dynamiskt medlemskap. Den här listan kan också uppdateras för att hämta alla nya anpassade tilläggsegenskaper för appen.

## <a name="rules-for-devices"></a>Regler för enheter

Du kan också skapa en regel som väljer enhetsobjekt för medlemskap i en grupp. Du kan inte ha både användare och enheter som medlemmar i gruppen. Den **organizationalUnit** attribut visas inte längre och ska inte användas. Den här strängen ställs av Intune i vissa fall, men kan identifieras inte av Azure AD, så inga enheter har lagts till i grupper baserat på det här attributet.

Du kan använda följande enhetsattribut.

 Enhetsattributet  | Värden | Exempel
 ----- | ----- | ----------------
 accountEnabled | SANT FALSKT | (device.accountEnabled - eq SANT)
 displayName | ett värde |(device.displayName - eq ”Rob Iphone”)
 deviceOSType | ett värde | (device.deviceOSType - eq ”iPad”)- eller (device.deviceOSType - eq ”iPhone”)
 deviceOSVersion | ett värde | (enhet. OSVersion - eq ”9.1”)
 deviceCategory | ett giltigt enhetsnamn för kategori | (device.deviceCategory - eq ”BYOD”)
 deviceManufacturer | ett värde | (device.deviceManufacturer - eq ”Samsung”)
 deviceModel | ett värde | (device.deviceModel - eq ”iPad luften”)
 deviceOwnership | Privat, företag, okänt | (device.deviceOwnership - eq ”Company”)
 Domännamn | ett värde | (device.domainName - eq ”contoso.com”)
 enrollmentProfileName | Profil för registrering av Apple-enheter eller Windows Autopilot-profilnamn | (device.enrollmentProfileName - eq ”DEP iPhone”)
 isRooted | SANT FALSKT | (device.isRooted - eq SANT)
 managementType | MDM (för mobila enheter)<br>Datorn (för datorer som hanteras av Intune PC-agenten) | (device.managementType - eq ”MDM”)
 deviceId | en giltig enhets-ID för Azure AD | (device.deviceId - eq ”d4fe7726-5966-431c-b3b8-cddc8fdb717d”)
 objekt-ID | en giltig Azure AD objekt-ID |  (device.objectId - eq 76ad43c9-32c5-45e8-a272-7b58b58f596d ”)
 systemLabels | valfri sträng som matchar Intune enhetsegenskap för taggar Modern arbetsplats enheter | (device.systemLabels-innehåller ”M365Managed”)

## <a name="next-steps"></a>Nästa steg

Dessa artiklar innehåller ytterligare information om grupper i Azure Active Directory.

* [Visa befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Skapa en ny grupp och lägga till medlemmar](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
