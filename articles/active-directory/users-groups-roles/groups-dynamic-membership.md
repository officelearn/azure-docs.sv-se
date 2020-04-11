---
title: Regler för dynamiskt fyllnadsmedlemskap - Azure AD | Microsoft-dokument
description: Så här skapar du medlemskapsregler för att automatiskt fylla i grupper och en regelreferens.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/27/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6f8237ac13744e56baa8551f8cced12b2785a48
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114728"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Dynamiska medlemskapsregler för grupper i Azure Active Directory

I Azure Active Directory (Azure AD) kan du skapa komplexa attributbaserade regler för att aktivera dynamiska medlemskap för grupper. Dynamiskt gruppmedlemskap minskar de administrativa kostnaderna för att lägga till och ta bort användare. I den här artikeln beskrivs egenskaperna och syntaxen för att skapa dynamiska medlemskapsregler för användare eller enheter. Du kan skapa en regel för dynamiskt medlemskap för säkerhetsgrupper eller Office 365-grupper.

När attribut för en användare eller enhet ändras utvärderar systemet alla dynamiska gruppregler i en katalog för att se om ändringen skulle utlösa någon grupp som lägger till eller tar bort. Om en användare eller enhet uppfyller en regel för en grupp läggs de till som medlem i den gruppen. Om de inte längre uppfyller regeln tas de bort. Du kan inte lägga till eller ta bort en medlem i en dynamisk grupp manuellt.

- Du kan skapa en dynamisk grupp för enheter eller användare, men du kan inte skapa en regel som innehåller både användare och enheter.
- Du kan inte skapa en enhetsgrupp baserat på enhetsägarnas attribut. Regler för enhetsmedlemskap kan bara referera till enhetsattribut.

> [!NOTE]
> Den här funktionen kräver en Azure AD Premium P1-licens för varje unik användare som är medlem i en eller flera dynamiska grupper. Du behöver inte tilldela licenser till användare för att de ska vara medlemmar i dynamiska grupper, men du måste ha det minsta antalet licenser i klienten för att täcka alla sådana användare. Om du till exempel hade totalt 1 000 unika användare i alla dynamiska grupper i din klientorganisation behöver du minst 1 000 licenser för Azure AD Premium P1 för att uppfylla licenskravet.
> Ingen licens krävs för enheter som är medlemmar i en dynamisk enhetsgrupp.

## <a name="rule-builder-in-the-azure-portal"></a>Regelbyggare i Azure-portalen

Azure AD tillhandahåller en regelbyggare för att skapa och uppdatera dina viktiga regler snabbare. Regelbyggaren stöder konstruktionen upp till fem uttryck. Regelverktyget gör det enklare att skapa en regel med några enkla uttryck, men den kan inte användas för att återskapa alla regler. Om regelverktyget inte stöder den regel som du vill skapa kan du använda textrutan.

Här är några exempel på avancerade regler eller syntax som vi rekommenderar att du konstruerar med hjälp av textrutan:

- Regel med fler än fem uttryck
- Regeln om direktrapporter
- Ange [operatörsprioritet](groups-dynamic-membership.md#operator-precedence)
- [Regler med komplexa uttryck](groups-dynamic-membership.md#rules-with-complex-expressions); till exempel`(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Regelbyggaren kanske inte kan visa vissa regler som har skapats i textrutan. Du kan se ett meddelande när regelverktyget inte kan visa regeln. Regelverktyget ändrar inte den syntax, validering eller bearbetning som stöds av dynamiska gruppregler på något sätt.

Mer steg-för-steg-instruktioner finns i [Skapa eller uppdatera en dynamisk grupp](groups-create-rule.md).

![Lägga till medlemskapsregel för en dynamisk grupp](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>Regelsyntax för ett enskilt uttryck

Ett enda uttryck är den enklaste formen av en medlemskap regel och bara har de tre delar som nämns ovan. En regel med ett enda uttryck `Property Operator Value`ser ut ungefär så här: , där syntaxen för egenskapen är namnet på object.property.

Följande är ett exempel på en korrekt konstruerad medlemskapsregel med ett enda uttryck:

```
user.department -eq "Sales"
```

Parenteser är valfria för ett enskilt uttryck. Den totala längden på brödtexten i medlemskapsregeln får inte överstiga 2048 tecken.

## <a name="constructing-the-body-of-a-membership-rule"></a>Konstruera en medlemsregel

En medlemskapsregel som automatiskt fyller en grupp med användare eller enheter är ett binärt uttryck som resulterar i ett sant eller falskt resultat. De tre delarna av en enkel regel är:

- Egenskap
- Operator
- Värde

Ordningen på delarna i ett uttryck är viktiga för att undvika syntaxfel.

## <a name="supported-properties"></a>Egenskaper som stöds

Det finns tre typer av egenskaper som kan användas för att skapa en medlemskapsregel.

- Boolesk
- Sträng
- Strängsamling

Följande är de användaregenskaper som du kan använda för att skapa ett enda uttryck.

### <a name="properties-of-type-boolean"></a>Egenskaper för typ boolesk

| Egenskaper | Tillåtna värden | Användning |
| --- | --- | --- |
| accountEnabled |sant falskt |user.accountEnabled -eq sant |
| dirSyncEnabled |sant falskt |user.dirSyncEnabled -eq sant |

### <a name="properties-of-type-string"></a>Egenskaper för typsträng

| Egenskaper | Tillåtna värden | Användning |
| --- | --- | --- |
| city |Alla strängvärde eller *null* |(user.city -eq "värde") |
| land |Alla strängvärde eller *null* |(user.country -eq "värde") |
| företagsNamn | Alla strängvärde eller *null* | (user.companyName -eq "värde") |
| avdelning |Alla strängvärde eller *null* |(user.avdelning -eq "värde") |
| displayName |Alla strängvärde |(user.displayName -eq "värde") |
| Employeeid |Alla strängvärde |(user.employeeId -eq "värde")<br>(user.employeeId -ne *null*) |
| faksimilTelephoneNumber |Alla strängvärde eller *null* |(user.facsimileTelephoneNumber -eq "värde") |
| förnamn |Alla strängvärde eller *null* |(user.givenName -eq "värde") |
| jobbTitle |Alla strängvärde eller *null* |(user.jobTitle -eq "värde") |
| e-post |Alla strängvärde eller *null* (SMTP-adress för användaren) |(user.mail -eq "värde") |
| smeknamn för e-post |Alla strängvärde (användarens e-postalias) |(user.mailNickName -eq "värde") |
| mobil |Alla strängvärde eller *null* |(user.mobile -eq "värde") |
| Objectid |GUID för användarobjektet |(user.objectId -eq "1111111-1111-1111-1111-1111111111111111") |
| påPremisesSecurityIdentifier | Lokalt säkerhetsidentifierare (SID) för användare som synkroniserades från lokalt till molnet. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-111111111-11111111111-1111111111-1111111") |
| lösenordPolicies |Ingen DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| fysisktDeliveryOfficeName |Alla strängvärde eller *null* |(user.physicalDeliveryOfficeName -eq "värde") |
| Postnummer |Alla strängvärde eller *null* |(user.postalCode -eq "värde") |
| preferredLanguage |ISO 639-1-kod |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Alla strängvärde eller *null* |(user.sipProxyAddress -eq "värde") |
| state |Alla strängvärde eller *null* |(user.state -eq "värde") |
| streetAddress (streetAddress) |Alla strängvärde eller *null* |(user.streetAddress -eq "värde") |
| surname |Alla strängvärde eller *null* |(user.efternamn -eq "värde") |
| telefonAntal |Alla strängvärde eller *null* |(user.telephoneNumber -eq "värde") |
| användningLokalisering |Landskod med två bokstäver |(user.usageLocation -eq "US") |
| userPrincipalName |Alla strängvärde |(user.userPrincipalName -eqalias@domain" ") |
| userType (användare) |medlem gäst *null* |(user.userType -eq "Medlem") |

### <a name="properties-of-type-string-collection"></a>Egenskaper för typsträngsamling

| Egenskaper | Tillåtna värden | Användning |
| --- | --- | --- |
| otherMails |Alla strängvärde |(user.otherMails -innehålleralias@domain" ") |
| proxyAddresses |SMTP: alias@domain smtp:alias@domain |(user.proxyAddresses -innehåller "SMTP: alias@domain") |

För de egenskaper som används för enhetsregler finns i [Regler för enheter](#rules-for-devices).

## <a name="supported-expression-operators"></a>Uttrycksoperatorer som stöds

I följande tabell visas alla operatorer som stöds och deras syntax för ett enda uttryck. Operatorer kan användas med eller utan bindestreck (-) prefix.

| Operator | Syntax |
| --- | --- |
| Inte lika |-ne ( ne ) |
| Är lika med |-eq (på samma sätt som) |
| Inte börjar med |-notStartsWith |
| Börjar med |-börjarmed |
| Innehåller inte |-inteContains |
| Innehåller |-innehåller |
| Matchar inte |-notMatch |
| Matchning |-match |
| I | -i |
| Inte i | -notIn |

### <a name="using-the--in-and--notin-operators"></a>Använda operatorerna -in och -notIn

Om du vill jämföra värdet för ett användarattribut mot ett antal olika värden kan du använda operatorerna -in eller -notIn. Använd hakparentessymbolerna "[" och "]" för att börja och avsluta listan med värden.

 I följande exempel utvärderas uttrycket till true om värdet för user.department är lika med något av värdena i listan:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>Använda operatorn -match 
**Operatorn -match** används för att matcha alla reguljära uttryck. Exempel:

```
user.displayName -match "Da.*"   
```
Da, Dav, David utvärdera till sant, aDa utvärderar till falska.

```
user.displayName -match ".*vid"
```
David utvärderar till sant, utvärderar Da till falska.

## <a name="supported-values"></a>Värden som stöds

De värden som används i ett uttryck kan bestå av flera typer, bland annat:

* Strängar
* Boolesk – sant, falskt
* Tal
* Matriser – talmatris, strängmatris

När du anger ett värde i ett uttryck är det viktigt att använda rätt syntax för att undvika fel. Några syntaxtips är:

* Dubbla citattecken är valfria om inte värdet är en sträng.
* Sträng- och regex-åtgärder är inte skiftlägeskänsliga.
* När ett strängvärde innehåller dubbla citattecken bör båda citaten flytas med \` tecknet, \`till\`exempel user.department -eq "Försäljning" är rätt syntax när "Försäljning" är värdet.
* Du kan också utföra Null-kontroller med null `user.department -eq null`som ett värde, till exempel .

### <a name="use-of-null-values"></a>Användning av Null-värden

Om du vill ange ett null-värde i en regel kan du använda *null-värdet.* 

* Använd -eq eller -ne när du jämför *null-värdet* i ett uttryck.
* Använd citattecken runt ordet *null* endast om du vill att det ska tolkas som ett litteralt strängvärde.
* Operatorn -inte kan inte användas som jämförande operator för null. Om du använder den får du ett felmeddelande om du använder null eller $null.

Det korrekta sättet att referera till null-värdet är följande:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Regler med flera uttryck

En gruppmedlemskapsregel kan bestå av mer än ett enskilt uttryck som är kopplat till operatorerna -, -eller - eller och inte. Logiska operatorer kan också användas i kombination. 

Följande är exempel på korrekt konstruerade medlemskapsregler med flera uttryck:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Prioritet för operator

Alla operatörer listas nedan i prioritetsordning från högsta till lägsta. Operatorer på samma linje har samma prioritet:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Följande är ett exempel på operatorprioritet där två uttryck utvärderas för användaren:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Parenteser behövs bara när prioriteten inte uppfyller dina krav. Om du till exempel vill att avdelningen ska utvärderas först visar följande hur parenteser kan användas för att bestämma ordning:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Regler med komplexa uttryck

En medlemskapsregel kan bestå av komplexa uttryck där egenskaper, operatorer och värden får mer komplexa former. Uttryck anses komplexa när något av följande är sant:

* Egenskapen består av en samling värden; specifikt egenskaper med flera värden
* Uttrycken använder operatorerna -any och -all
* Värdet för uttrycket kan i sig vara ett eller flera uttryck

## <a name="multi-value-properties"></a>Egenskaper för flera värden

Egenskaper för flera värden är samlingar av objekt av samma typ. De kan användas för att skapa medlemskapsregler med alla logiska operatorer.

| Egenskaper | Värden | Användning |
| --- | --- | --- |
| tilldeladePlaner | Varje objekt i samlingen visar följande strängegenskaper: capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -och assignedPlan.capabilityStatus -eq "Enabled") |
| proxyAddresses| SMTP: alias@domain smtp:alias@domain | (user.proxyAddresses -any\_ ( -contains "contoso")) |

### <a name="using-the--any-and--all-operators"></a>Använda -alla och -alla operatörer

Du kan använda -alla och -alla operatorer för att tillämpa ett villkor på en eller alla objekt i samlingen, respektive.

* -alla (tillfredsställda när minst ett objekt i samlingen matchar villkoret)
* -alla (nöjd när alla objekt i samlingen matchar villkoret)

#### <a name="example-1"></a>Exempel 1

assignedPlans är en egenskap med flera värden som visar alla serviceplaner som tilldelats användaren. Följande uttryck väljer användare som har serviceplanen för Exchange Online (Plan 2) (som guid-värde) som också är i aktiverat tillstånd:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

En regel som den här kan användas för att gruppera alla användare för vilka en Office 365-funktion (eller annan Microsoft Online Service) är aktiverad. Du kan sedan använda med en uppsättning principer för gruppen.

#### <a name="example-2"></a>Exempel 2

Följande uttryck väljer alla användare som har en serviceplan som är associerad med Intune-tjänsten (identifieras med tjänstnamnet "SCO"):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore-_-syntax"></a>Använda syntaxen\_för understreck ( )

Syntaxen\_för understreck ( ) matchar förekomster av ett visst värde i en av egenskaperna för flervärdessträngsamling för att lägga till användare eller enheter i en dynamisk grupp. Den används med -någon eller -alla operatörer.

Här är ett exempel på\_hur du använder understrecket ( ) i en regel för att lägga till medlemmar baserat på user.proxyAddress (det fungerar på samma sätt för user.otherMails). Den här regeln lägger till alla användare med proxyadress som innehåller "contoso" i gruppen.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Andra egenskaper och gemensamma regler

### <a name="create-a-direct-reports-rule"></a>Skapa en regel för "direktrapporter"

Du kan skapa en grupp som innehåller alla direkta rapporter från en chef. När chefens direkta rapporter ändras i framtiden justeras gruppens medlemskap automatiskt.

Regeln för direktrapporter konstrueras med följande syntax:

```
Direct Reports for "{objectID_of_manager}"
```

Här är ett exempel på en giltig regel där "62e19b97-8b3d-4d4a-a106-4ce66896a863" är objektID för chefen:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

Följande tips kan hjälpa dig att använda regeln på rätt sätt.

- **Chefens ID** är projektledarens objekt-ID. Den finns i chefens **profil**.
- Kontrollera att egenskapen **Manager** är korrekt inställd för användare i klienten för regeln. Du kan kontrollera det aktuella värdet i användarens **profil**.
- Den här regeln stöder endast chefens direkta rapporter. Med andra ord kan du inte skapa en grupp med chefens direktrapporter *och* deras rapporter.
- Den här regeln kan inte kombineras med några andra medlemskapsregler.

### <a name="create-an-all-users-rule"></a>Skapa en regel "Alla användare"

Du kan skapa en grupp som innehåller alla användare i en klient med hjälp av en medlemskapsregel. När användare läggs till eller tas bort från klienten i framtiden justeras gruppens medlemskap automatiskt.

Regeln "Alla användare" skapas med hjälp av ett uttryck med operatorn -ne och null-värdet. Den här regeln lägger till B2B-gästanvändare samt medlemsanvändare i gruppen.

```
user.objectId -ne null
```
Om du vill att gruppen ska utesluta gästanvändare och endast inkludera medlemmar i din klientorganisation kan du använda följande syntax:

```
(user.objectId -ne null) -and (user.userType -eq "Member")
```

### <a name="create-an-all-devices-rule"></a>Skapa en regel "Alla enheter"

Du kan skapa en grupp som innehåller alla enheter i en klient med hjälp av en medlemskapsregel. När enheter läggs till eller tas bort från klienten i framtiden justeras gruppens medlemskap automatiskt.

Regeln "Alla enheter" konstrueras med hjälp av ett uttryck med operatorn -ne och null-värdet:

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Tilläggsegenskaper och anpassade tilläggsegenskaper

Tilläggsattribut och anpassade tilläggsegenskaper stöds som strängegenskaper i dynamiska medlemskapsregler. [Tilläggsattribut](https://docs.microsoft.com/graph/api/resources/onpremisesextensionattributes?view=graph-rest-1.0) synkroniseras från lokala Window Server AD och tar formatet "ExtensionAttributeX", där X är lika med 1 - 15. Här är ett exempel på en regel som använder ett tilläggsattribut som egenskap:

```
(user.extensionAttribute15 -eq "Marketing")
```

[Anpassade tilläggsegenskaper](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-directory-extensions) synkroniseras från lokala Windows Server AD eller från ett anslutet `user.extension_[GUID]_[Attribute]`SaaS-program och är av formatet för , där:

* [GUID] är den unika identifieraren i Azure AD för programmet som skapade egenskapen i Azure AD
* [Attribut] är namnet på egenskapen när den skapades

Ett exempel på en regel som använder en anpassad tilläggsegenskap är:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

Det anpassade egenskapsnamnet finns i katalogen genom att fråga en användares egenskap med Graph Explorer och söka efter egenskapsnamnet. Nu kan du också välja Länken **Hämta anpassade tilläggsegenskaper** i den dynamiska användargruppsregelverktyget för att ange ett unikt app-ID och få en fullständig lista över anpassade tilläggsegenskaper som ska användas när du skapar en dynamisk medlemskapsregel. Den här listan kan också uppdateras för att få nya anpassade tilläggsegenskaper för den appen.

## <a name="rules-for-devices"></a>Regler för enheter

Du kan också skapa en regel som väljer enhetsobjekt för medlemskap i en grupp. Du kan inte ha både användare och enheter som gruppmedlemmar. 

> [!NOTE]
> **Attributet organizationalUnit** visas inte längre och ska inte användas. Den här strängen anges av Intune i specifika fall men känns inte igen av Azure AD, så inga enheter läggs till i grupper baserat på det här attributet.

> [!NOTE]
> systemlabels är ett skrivskyddat attribut som inte kan ställas in med Intune.
>
> För Windows 10 är rätt format för attributet deviceOSVersion följande: (device.deviceOSVersion -eq "10.0.17763"). Formateringen kan valideras med Get-MsolDevice PowerShell-cmdleten.

Följande enhetsattribut kan användas.

 Enhetsattribut  | Värden | Exempel
 ----- | ----- | ----------------
 accountEnabled | sant falskt | (device.accountEnabled -eq sant)
 displayName | vilket strängvärde som helst |(device.displayName -eq "Rob iPhone")
 deviceOSType | vilket strängvärde som helst | (device.deviceOSType -eq "iPad") -eller (device.deviceOSType -eq "iPhone")<br>(device.deviceOSType -innehåller "AndroidEnterprise")<br>(device.deviceOSType -eq "AndroidForWork")
 deviceOSVersion | vilket strängvärde som helst | (device.deviceOSVersion -eq "9.1")
 deviceKaegory | ett giltigt enhetskategorinamn | (device.deviceKategori -eq "BYOD")
 deviceManufacturer | vilket strängvärde som helst | (device.deviceManufacturer -eq "Samsung")
 deviceModel | vilket strängvärde som helst | (device.deviceModel -eq "iPad Air")
 enhetÄgare | Personligt, Företag, Okänd | (device.deviceOwnership -eq "Företag")
 registreringProfileName | Registreringsprofil för Apple-enhet, Enhetsregistrering – Enhetsidentifierare (Android – Kiosk) eller Windows Autopilot-profilnamn | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | sant falskt | (device.isRooted -eq sant)
 managementType (hanteringstyp) | MDM (för mobila enheter)<br>PC (för datorer som hanteras av Intune PC-agenten) | (device.managementType -eq "MDM")
 deviceId | ett giltigt Azure AD-enhets-ID | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 Objectid | ett giltigt Azure AD-objekt-ID |  (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d")
 devicePhysicalIds | alla strängvärde som används av Autopilot, till exempel alla Autopilot-enheter, OrderID eller PurchaseOrderID  | (device.devicePhysicalIDs -any _ -contains "[ZTDId]") (device.devicePhysicalIds -any _ -eq "[OrderID]:179887111881") (device.devicePhysicalIds -any _ -eq "[PurchaseOrderId]:76222342342")
 systemLabels | en sträng som matchar egenskapen Intune-enhet för taggning av enheter på modern arbetsplats | (device.systemLabels -innehåller "M365Hantaged")

> [!Note]  
> För enhetenÄgare när du skapar dynamiska grupper för enheter måste du ange värdet lika med "Företag". På Intune representeras enhetsägarskapet i stället som Företag. Mer information finns i [OwnerTypes.](https://docs.microsoft.com/intune/reports-ref-devices#ownertypes) 

## <a name="next-steps"></a>Nästa steg

De här artiklarna innehåller ytterligare information om grupper i Azure Active Directory.

- [Se befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Skapa en ny grupp och lägga till medlemmar](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Hantera dynamiska regler för användare i en grupp](groups-create-rule.md)
