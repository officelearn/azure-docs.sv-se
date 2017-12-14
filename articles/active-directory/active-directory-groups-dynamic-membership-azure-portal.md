---
title: Attributbaserad dynamiskt medlemskap i Azure Active Directory | Microsoft Docs
description: "Så att skapa avancerade regler för dynamisk gruppmedlemskap inklusive stöds uttryck regeln operatorer och parametrar."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: fb434cc2-9a91-4ebf-9753-dd81e289787e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: b8aa841cca63c0c4eb45105e3ccff91920ad35e3
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>Skapa attributbaserade regler för dynamiska gruppmedlemskap i Azure Active Directory
Du kan skapa avancerade regler för att aktivera avancerade attributbaserad dynamiskt medlemskap för grupper i Azure Active Directory (AD Azure). Den här artikeln beskrivs de attribut och syntax för att skapa regler för dynamiskt medlemskap för användare eller enheter.

När alla attribut för en användare eller enhet ändrar utvärderar systemet alla dynamisk gruppregler i en katalog att se om ändringen skulle leda till valfri grupp lägger till eller tar bort. Om en användare eller enhet uppfyller en regel för en grupp, läggs de som medlem i gruppen. Om de inte längre uppfyller regeln tas bort.

> [!NOTE]
> - Du kan skapa en regel för dynamiskt medlemskap för säkerhetsgrupper eller Office 365-grupper.
>
> - Den här funktionen kräver en Azure AD Premium P1-licens för varje användare som ingår i minst en dynamisk grupp. Det är inte obligatoriskt att faktiskt tilldela licenser till användare för att de ska vara medlemmar i dynamiska grupper, men du behöver det minsta antalet licenser i klienten att täcka alla användare. Exempel: Om du har 1 000 unika användare totalt i alla dynamiska grupper i din klient, måste du ha minst 1 000 licenser för Azure AD Premium P1 eller ovan, att uppfylla kravet på licens.
>
> - Du kan skapa en dynamisk grupp för enheter eller användare, men du kan inte skapa en regel som innehåller både användare och enhetsobjekt.

> - För tillfället går inte att skapa en enhetsgrupp baserat på det ägande användarens attribut. Medlemskapsregler för enheten kan bara referera omedelbar attribut för enhetsobjekt i katalogen.

## <a name="to-create-an-advanced-rule"></a>Skapa en avancerad regel
1. Logga in på den [administrationscentret för Azure AD](https://aad.portal.azure.com) med ett konto som är en global administratör eller en användare kontoadministratör.
2. Välj **användare och grupper**.
3. Välj **alla grupper**, och välj **ny grupp**.

   ![Lägg till ny grupp](./media/active-directory-groups-dynamic-membership-azure-portal/new-group-creation.png)

4. På den **grupp** bladet, ange ett namn och beskrivning för den nya gruppen. Välj en **medlemskapstypen** antingen **dynamiska användaren** eller **dynamisk enhet**, beroende på om du vill skapa en regel för användare eller enheter, och välj sedan **Lägg till dynamiska frågan**. Du kan använda verktyget regeln för att skapa en enkel regel eller skapa en avancerad regel själv. Den här artikeln innehåller mer information om tillgängliga attribut för användare och enhet samt exempel på avancerade regler.

   ![Lägg till regel för dynamiskt medlemskap](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

5. När du skapar regeln markerar **Lägg till frågan** längst ned på bladet.
6. Välj **skapa** på den **grupp** bladet för att skapa gruppen.

> [!TIP]
> Skapa en grupp kan misslyckas om den avancerade regeln som du angav är felaktigt. Ett meddelande visas i det övre högra hörnet av portalen. den innehåller en förklaring till varför regeln kan accepteras inte av systemet. Läsa den noggrant för att förstå hur du behöva justera regeln så att den blir ogiltig.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Hur du skapar innehållet i en avancerad regel
Avancerade regeln som du kan skapa för dynamiskt medlemskap för grupper är i grunden en binär uttryck som består av tre delar och resulterar i ett true eller false resultat. Det finns tre delar:

* Vänster parametern
* Binär operator
* Höger konstant

En fullständig avancerade regeln ser ut ungefär så här: (leftParameter binaryOperator ”RightConstant”), där inledande och avslutande parentes är valfria för hela binära uttrycket, dubbla citattecken är valfria, krävs för den högra konstanten bara När det är sträng och syntaxen för parametern vänstra är user.property. En avancerad regel kan bestå av fler än en binär uttryck avgränsade med- och- eller, och - inte logiska operatorer.

Följande är exempel på ett korrekt angivet avancerad regel:
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
Fullständig lista över parametrar som stöds och uttryck regeln operatorer, finns i avsnitten nedan. Attribut som används för enheten regler, se [använda attribut för att skapa regler för enhetsobjekt](#using-attributes-to-create-rules-for-device-objects).

Den totala längden på innehållet i avancerade regeln får inte överskrida 2048 tecken.

> [!NOTE]
> Sträng och regex åtgärder är inte skiftlägeskänsliga. Du kan också utföra Null-kontroller med hjälp av *null* som en konstant, till exempel user.department - eq *null*.
> Strängar som innehåller citattecken ”ska avgränsas med-tecken, till exempel user.department - eq \`” försäljning ”.

## <a name="supported-expression-rule-operators"></a>Stöds uttryck regeln operatorer
I följande tabell visas alla operatorer för stöds uttryck-regeln och deras syntax som ska användas i brödtexten för avancerad regel:

| Operator | Syntax |
| --- | --- |
| Är inte lika med |-ne |
| Lika med |-eq |
| Börjar inte med |-notStartsWith |
| Börjar med |startsWith- |
| Innehåller inte |-notContains |
| Contains |-innehåller |
| Matchar inte |-notMatch |
| Matcha |-matchar |
| i | -i |
| Inte i | -notIn |

## <a name="operator-precedence"></a>Operatorer

Nedan visas alla operatorer per prioritet från lägre till högre. Operatorer på samma rad har samma prioritet:
````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````
Alla operatorer kan användas med eller utan prefixet bindestreck. Parenteser krävs bara när prioritet inte uppfyller dina krav.
Exempel:
```
   user.department –eq "Marketing" –and user.country –eq "US"
```
motsvarar:
```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```
## <a name="using-the--in-and--notin-operators"></a>Med-i och - notIn operatorer

Om du vill jämföra värdet för ett användarattribut mot ett antal olika värden kan du använda-i eller notIn - operatorer. Här är ett exempel med-i-operatorn:
```
    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]
```
Observera användningen av den ”[” och ”]” i början och slutet av listan med värden. Det här villkoret utvärderas till True för värdet user.department är lika med ett av värdena i listan.


## <a name="query-error-remediation"></a>Frågan fel reparation
I följande tabell listas vanliga fel och åtgärda dem

| Parsningsfel i frågan | Fel-användning | Korrigerade användning |
| --- | --- | --- |
| Fel: Attribut stöds inte. |(user.invalidProperty - eq ”värde”) |(user.department - eq ”värde”)<br/><br/>Se till att attributet är på den [lista över egenskaper som stöds](#supported-properties). |
| Fel: Operatorn stöds inte i attributet. |(user.accountEnabled-innehåller true) |(user.accountEnabled - eq SANT)<br/><br/>Operatorn som används stöds inte för egenskapstypen (i det här exemplet-innehåller kan inte användas på typen boolean). Använd rätt operatorer för egenskapstypen. |
| Fel: Frågekompileringsfel. |1. (user.department - eq ”försäljning”) (user.department - eq ”marknadsföring”)<br/><br/>2. (user.userPrincipalName-matchar ”*@domain.ext”) |1. En operator saknas. Använd - och eller - eller två Anslut predikat<br/><br/>(user.department - eq ”försäljning”)- eller (user.department - eq ”marknadsföring”)<br/><br/>2. fel i reguljärt uttryck som används med - matcha<br/><br/>(user.userPrincipalName-matchar ”. *@domain.ext”), alternativt: (user.userPrincipalName-matchar ”@domain.ext$”)|

## <a name="supported-properties"></a>Egenskaper som stöds
Följande är alla användaregenskaper som du kan använda i din avancerade regel:

### <a name="properties-of-type-boolean"></a>Egenskaper av typen boolean
Tillåtna operatörer

* -eq
* -ne

| Egenskaper | Tillåtna värden | Användning |
| --- | --- | --- |
| accountEnabled |SANT FALSKT |user.accountEnabled - eq true |
| DirSyncEnabled |SANT FALSKT |user.dirSyncEnabled - eq true |

### <a name="properties-of-type-string"></a>Egenskaper av typen sträng
Tillåtna operatörer

* -eq
* -ne
* -notStartsWith
* StartsWith-
* -innehåller
* -notContains
* -matchar
* -notMatch
* -i
* -notIn

| Egenskaper | Tillåtna värden | Användning |
| --- | --- | --- |
| city |Någon strängvärde eller *null* |(user.city - eq ”värde”) |
| Land |Någon strängvärde eller *null* |(User.Country. - eq ”värde”) |
| Företagsnamn | Någon strängvärde eller *null* | (user.companyName - eq ”värde”) |
| Avdelning |Någon strängvärde eller *null* |(user.department - eq ”värde”) |
| Visningsnamn |Ett värde |(user.displayName - eq ”värde”) |
| employeeId |Ett värde |(user.employeeId - eq ”värde”)<br>(user.employeeId - ne *null*) |
| facsimileTelephoneNumber |Någon strängvärde eller *null* |(user.facsimileTelephoneNumber - eq ”värde”) |
| givenName |Någon strängvärde eller *null* |(user.givenName - eq ”värde”) |
| Befattning |Någon strängvärde eller *null* |(user.jobTitle - eq ”värde”) |
| E-post |Någon strängvärde eller *null* (SMTP-adressen för användaren) |(user.mail - eq ”värde”) |
| mailNickName |Ett värde (e postalias för användaren) |(user.mailNickName - eq ”värde”) |
| mobila |Någon strängvärde eller *null* |(user.mobile - eq ”värde”) |
| objekt-ID |GUID för användarobjektet |(user.objectId - eq ”1111111-1111-1111-1111-111111111111”) |
| onPremisesSecurityIdentifier | Lokalt säkerhetsidentifierare (SID) för användare som har synkroniserats från lokalt till molnet. |(user.onPremisesSecurityIdentifier - eq ”S-1-1-11-1111111111-1111111111-1111111111-1111111”) |
| passwordPolicies |Ingen DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies - eq ”DisableStrongPassword”) |
| physicalDeliveryOfficeName |Någon strängvärde eller *null* |(user.physicalDeliveryOfficeName - eq ”värde”) |
| Postnummer |Någon strängvärde eller *null* |(user.postalCode - eq ”värde”) |
| preferredLanguage |ISO 639-1-kod |(user.preferredLanguage - eq ”sv-se”) |
| sipProxyAddress |Någon strängvärde eller *null* |(user.sipProxyAddress - eq ”värde”) |
| state |Någon strängvärde eller *null* |(user.state - eq ”värde”) |
| StreetAddress |Någon strängvärde eller *null* |(user.streetAddress - eq ”värde”) |
| Efternamn |Någon strängvärde eller *null* |(user.surname - eq ”värde”) |
| telephoneNumber |Någon strängvärde eller *null* |(user.telephoneNumber - eq ”värde”) |
| usageLocation |Två bokstäver landskod |(user.usageLocation - eq ”USA”) |
| UserPrincipalName |Ett värde |(user.userPrincipalName - eq ”alias@domain”) |
| UserType |medlemmen gäst *null* |(user.userType - eq ”medlem”) |

### <a name="properties-of-type-string-collection"></a>Egenskaper av typen sträng samling
Tillåtna operatörer

* -innehåller
* -notContains

| Egenskaper | Tillåtna värden | Användning |
| --- | --- | --- |
| otherMails |Ett värde |(user.otherMails-innehåller ”alias@domain”) |
| proxyAddresses |SMTP: alias@domain smtp:alias@domain |(user.proxyAddresses-innehåller ”SMTP: alias@domain”) |

## <a name="multi-value-properties"></a>Egenskaper för flera värden
Tillåtna operatörer

* -alla (uppfyllt när minst ett objekt i samlingen matchar villkoret)
* -alla (uppfyllt när alla objekt i samlingen matchar villkoret)

| Egenskaper | Värden | Användning |
| --- | --- | --- |
| assignedPlans |Varje objekt i samlingen visar egenskaperna för följande sträng: capabilityStatus, tjänst, servicePlanId |user.assignedPlans-alla (assignedPlan.servicePlanId - eq ”efb87545-963c-4e0d-99df-69c6916d9eb0”- och assignedPlan.capabilityStatus - eq ”aktiverad”) |

Egenskaper för flera värden är samlingar av objekt av samma typ. Du kan använda - alla och -alla operatörer att tillämpa ett villkor på en eller alla objekt i samlingen, respektive. Exempel:

assignedPlans är en egenskap för flera värden med en lista över alla serviceplaner för användaren. Den nedan uttryck Välj användare med Exchange Online (Plan 2) service-plan som även är i aktiverat läge:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(Guid-identifierare identifierar serviceplan för Exchange Online (Plan 2).)

> [!NOTE]
> Detta är användbart om du vill identifiera alla användare som en Office 365 (eller andra Microsoft-onlinetjänst) kapaciteten har aktiverats, till exempel för att rikta dem med en viss uppsättning principer.

Följande uttryck väljer alla användare som har alla service-plan som är associerad med Intune-tjänsten (som identifieras av tjänstnamn ”SCO”):
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Null-värden

Om du vill ange ett null-värde i en regel som du kan använda den *null* värde. Var noga med att inte använda citattecken runt ordet *null* -om du gör det tolkas som en teckensträng. Beskriver hur du refererar till det null-värdet är:
```
   user.mail –ne null
```

## <a name="extension-attributes-and-custom-attributes"></a>Tilläggsattribut och anpassade attribut
Tilläggsattribut och anpassade attribut stöds i regler för dynamiskt medlemskap.

Tilläggsattribut synkroniseras från lokala Windows Server AD och vidta formatet ”ExtensionAttributeX”, där X är lika med 1 och 15.
Ett exempel på en regel som använder ett tillägg-attribut är
```
(user.extensionAttribute15 -eq "Marketing")
```
Anpassade attribut som synkroniseras från lokala Windows Server AD eller från ett anslutet SaaS-program och format ”user.extension_[GUID]\__ [attribut]”, där [GUID] är den unika identifieraren i AAD för det program som skapats i attributet i AAD och [attribut] är namnet på attributet som det skapades.
Är ett exempel på en regel som använder ett anpassat attribut
```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```
Anpassade attributets namn finns i katalogen genom att fråga användaren attributet diagrammet Utforskaren och söker efter attributets namn.

## <a name="direct-reports-rule"></a>Regeln ”direktrapporter”
Du kan skapa en grupp som innehåller alla direktrapporter för en chef. När den manager direktrapporter ändras i framtiden, justeras gruppens medlemskap automatiskt.

> [!NOTE]
> 1. Kontrollera att regeln ska fungera för den **ID Manager** egenskapen är korrekt inställda på användare i din klient. Du kan kontrollera det aktuella värdet för en användare på deras **fliken profil**.
> 2. Den här regeln har bara stöd för **direkt** rapporter. Det går för närvarande inte att skapa en grupp för en kapslad hierarki, t.ex. en grupp som innehåller direktrapporter och deras rapporter.

**Konfigurera gruppen**

1. Följ steg 1-5 från avsnittet [att skapa avancerade regeln](#to-create-the-advanced-rule), och välj en **medlemskapstypen** av **dynamiska användaren**.
2. På den **dynamiskt medlemskapsregler** bladet ange regeln med följande syntax:

    *Direktrapporter för ”{obectID_of_manager}”*

    Ett exempel på en giltig regel:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. När du har sparat regeln läggs alla användare med det angivna värdet för ID Manager till i gruppen.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Använda attribut för att skapa regler för enhetsobjekt
Du kan också skapa en regel som väljer enhetsobjekten för medlemskap i en grupp. Du kan använda följande attribut för enheten.

 Attribut för enheten  | Värden | Exempel
 ----- | ----- | ----------------
 accountEnabled | SANT FALSKT | (device.accountEnabled - eq SANT)
 Visningsnamn | Ett värde |(device.displayName - eq ”Anders Iphone”)
 DeviceOSType | Ett värde | (device.deviceOSType - eq ”iPad”)- eller (device.deviceOSType - eq ”iPhone”)
 DeviceOSVersion | Ett värde | (enhet. OSVersion - eq ”9.1”)
 deviceCategory | ett giltigt enhetsnamn kategori | (device.deviceCategory - eq ”BYOD”)
 DeviceManufacturer | Ett värde | (device.deviceManufacturer - eq ”Samsung”)
 DeviceModel | Ett värde | (device.deviceModel - eq ”iPad luften”)
 deviceOwnership | Privat, företag, okänt | (device.deviceOwnership - eq ”företagets”)
 Domännamn | Ett värde | (device.domainName - eq ”contoso.com”)
 enrollmentProfileName | Profilnamn för Apple enheten registreringen | (device.enrollmentProfileName - eq ”DEP iPhone”)
 isRooted | SANT FALSKT | (device.isRooted - eq SANT)
 managementType | MDM (för mobila enheter)<br>PC (för datorer som hanteras av Intune PC-agent) | (device.managementType - eq ”MDM”)
 OrganizationalUnit | valfritt strängvärde som matchar namnet på den organisationsenhet som angetts av en lokal Active Directory | (device.organizationalUnit - eq ”USA datorer”)
 deviceId | en giltig enhets-ID för Azure AD | (device.deviceId - eq ”d4fe7726-5966-431c-b3b8-cddc8fdb717d”)
 objekt-ID | en giltig Azure AD-objekt-ID |  (device.objectId - eq 76ad43c9-32c5-45e8-a272-7b58b58f596d ”)



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>Ändra dynamiskt medlemskap till statisk, och vice versa
Det är möjligt att ändra hur hanteras medlemskap i en grupp. Detta är användbart när du vill behålla samma namn och ID i systemet, så att alla befintliga referenser i gruppen är fortfarande giltiga. Skapa en ny grupp krävs uppdaterar du dessa referenser.

Vi håller på att uppdatera Azure-portalen för att stödja den här funktionen. Under tiden kan du använda den [klassiska Azure-portalen](https://manage.windowsazure.com) (Följ instruktionerna [här](active-directory-groups-dynamic-membership-azure-portal.md)) eller använda PowerShell-cmdlets som visas nedan.

> [!WARNING]
> När du ändrar en befintlig statisk grupp till en dynamisk grupp alla befintliga medlemmar tas bort från gruppen och sedan bearbetas medlemskapsregeln för att lägga till nya medlemmar. Om gruppen används för att styra åtkomsten till appar eller resurser, kan de ursprungliga medlemmarna förlora åtkomsten tills medlemskapsregeln bearbetats helt.
>
> Det rekommenderas att testa den nya medlemskapsregeln i förväg för att se till att det nya medlemskapet i gruppen som förväntat.

**Använda PowerShell för att ändra medlemskapshantering på en grupp**

> [!NOTE]
> Ändra egenskaper för dynamisk grupp behöver du använda cmdlet: ar från **förhandsversionen av** [Azure AD PowerShell Version 2](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Du kan installera preview från [här](https://www.powershellgallery.com/packages/AzureADPreview).

Här är ett exempel på funktioner som växla medlemskapshantering på en befintlig grupp. Tänk noggrant att ändra egenskapen GroupTypes och bevara alla värden som kan finnas där, korrekt orelaterade till dynamiskt medlemskap.

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Så här gör du en grupp statiska:
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
Så här gör du en grupp dynamiskt:
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om grupper i Azure Active Directory.

* [Se befintliga grupper](active-directory-groups-view-azure-portal.md)
* [Skapa en ny grupp och lägga till medlemmar](active-directory-groups-create-azure-portal.md)
* [Hantera inställningar för en grupp](active-directory-groups-settings-azure-portal.md)
* [Hantera medlemskap i en grupp](active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](active-directory-groups-dynamic-membership-azure-portal.md)
