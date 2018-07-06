---
title: Attributbaserade dynamiska regler för gruppmedlemskap i Azure Active Directory | Microsoft Docs
description: Skapa avancerade regler för dynamiskt medlemskap, inklusive stöd för uttrycket operatorer och parametrar.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/05/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: a48dcff6eedc2aa6e8bb6cd5b0668af72259493b
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869107"
---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>Skapa attributbaserade regler för dynamiskt gruppmedlemskap i Azure Active Directory
Du kan skapa anpassade regler för att aktivera komplexa attributbaserade dynamiska medlemskap för grupper i Azure Active Directory (AD Azure). Den här artikeln beskriver de attribut och syntaxen för att skapa regler för dynamiskt medlemskap för användare eller enheter. Du kan skapa en regel för dynamiskt medlemskap för säkerhetsgrupper eller Office 365-grupper.

När ett attribut för en användare eller enhet ändras utvärderar systemet alla dynamiska gruppregler i en katalog för att se om ändringen ska utlösa någon grupp lägger till eller tar bort. Om en användare eller enhet uppfyller en regel i en grupp, läggs de som en medlem i gruppen. Om användaren inte längre uppfyller regeln tas bort.

> [!NOTE]
> Den här funktionen kräver en Azure AD Premium P1-licens för varje unika användare som är medlem i en eller flera dynamiska grupper. Du behöver inte tilldela licenser till användare för dem att vara medlemmar i dynamiska grupper, men du måste ha det minsta antalet licenser i klientorganisationen för att täcka alla användare. Om du hade en totalsumma på 1 000 unika användare i alla dynamiska grupper i din klient måste till exempel minst 1 000 licenser för Azure AD Premium P1 att uppfylla kravet på licens.
>
> Du kan skapa en dynamisk grupp för enheter eller användare, men du kan inte skapa en regel som innehåller både användare och enheter.
> 
> För tillfället går inte att skapa en enhetsgrupp baserat på den ägande användaren attribut. Medlemskapsregler för enheten kan bara referera omedelbar attribut för enhetsobjekt i katalogen.

## <a name="to-create-an-advanced-rule"></a>Skapa en avancerad regel
1. Logga in på den [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som är en global administratör eller en Användaradministratör för kontot.
2. Välj **användare och grupper**.
3. Välj **alla grupper**, och välj **ny grupp**.

   ![Lägg till ny grupp](./media/groups-dynamic-membership/new-group-creation.png)

4. På den **grupp** bladet anger du ett namn och beskrivning för den nya gruppen. Välj en **Medlemskapstyp** antingen **dynamisk användare** eller **dynamisk enhet**, beroende på om du vill skapa en regel för användare eller enheter och välj sedan **Lägg till dynamisk fråga**. Du kan använda regeln builder för att skapa en enkel regel eller skapa en avancerad regel själv. Den här artikeln innehåller mer information om tillgängliga attribut för användare och enhet samt exempel på avancerade regler.

   ![Lägg till dynamisk medlemsregel](./media/groups-dynamic-membership/add-dynamic-group-rule.png)

5. När du har skapat regeln, Välj **Lägg till fråga** längst ned på bladet.
6. Välj **skapa** på den **grupp** bladet för att skapa gruppen.

> [!TIP]
> Det går inte att skapa en grupp om regeln som du angav har felaktigt format eller är inte giltig. Ett meddelande visas i det övre högra hörnet i portalen, som innehåller en förklaring av varför regeln inte kunde bearbetas. Läs den noggrant för att förstå hur du behöva justera regeln så att den blir giltigt.

## <a name="status-of-the-dynamic-rule"></a>Status för den dynamiska regeln

Du kan se medlemskapet bearbetning av status och datum för senaste uppdatering på översiktssidan för den dynamiska gruppen.
  
  ![skärm för dynamisk grupp](./media/groups-dynamic-membership/group-status.png)


Följande statusmeddelanden kan visas för **medlemskap bearbetning** status:
* **Utvärdera**: gruppändringen har tagits emot och uppdateringarna utvärderas.
* **Bearbetning av**: uppdateringar som bearbetas.
* **Uppdateringen är klar**: bearbetning har slutförts och alla tillämpliga uppdateringar har gjorts.
* **Fel vid bearbetning av**: ett fel uppstod vid utvärdering av medlemskapsregel och bearbetning kunde inte slutföras.
* **Uppdatera pausats**: dynamisk medlemsregel har pausats av administratören. MembershipRuleProcessingState anges som ”pausad”.

Följande statusmeddelanden kan visas för **medlemskap senast uppdaterad** status:
* &lt;**Datum och tid**&gt;: senast medlemskap har uppdaterats.
* **Pågår**: uppdateringar pågår för tillfället.
* **Okänd**: Det går inte att hämta den senaste uppdateringstiden. Det kan bero på gruppen som nyligen skapas.

Om det uppstår ett fel vid bearbetning av medlemskapsregeln för en viss grupp, visas en avisering i den **översiktssidan** för gruppen. Om du inte väntar på dynamiskt medlemskap uppdateringar kan bearbetas för alla grupper i klienten för sedan 24 timmar, visas en avisering i **alla grupper**.

![felmeddelande för bearbetning](./media/groups-dynamic-membership/processing-error.png)

## <a name="constructing-the-body-of-an-advanced-rule"></a>Konstruera brödtexten i en avancerad regel
Den avancerade regeln som du kan skapa för dynamiskt medlemskap för grupper är i stort sett en binär uttryck som består av tre delar och resulterar i ett sant eller FALSKT resultat. Det finns tre delar:

* Vänstra parametern
* Binär operator
* Rätt konstant

En fullständig avancerad regel ser ut ungefär så här: (leftParameter binaryOperator ”RightConstant”), där inledande och avslutande parentes är valfria för hela binära uttrycket, dubbla citattecken är valfria, endast nödvändiga för rätt konstanten När det är sträng och syntaxen för parametern vänstra är user.property. En avancerad regel kan bestå av fler än en binär uttryck avgränsade med- och, - eller, och - inte logiska operatorer.

Här följer några exempel på ett korrekt angivet avancerad regel:
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
En fullständig lista över parametrar som stöds och uttryck operatorer, finns i avsnitten nedan. Attribut som används för regler för enhet, se [använda attribut för att skapa regler för enhetsobjekt](#using-attributes-to-create-rules-for-device-objects).

Den totala längden på texten i avancerade regeln får inte överskrida 2048 tecken.

> [!NOTE]
> Sträng-och regex är inte skiftlägeskänsliga. Du kan också utföra Null-kontroller med hjälp av *null* som en konstant, till exempel user.department - eq *null*.
> Strängar som innehåller citattecken ”ska gås ur med-tecken, till exempel user.department - eq \`” försäljning ”.

## <a name="supported-expression-rule-operators"></a>Uttryck för stöds operatorer
I följande tabell visas alla operatorer för stöds uttryck-regeln och deras syntax som ska användas i brödtexten i den avancerade regeln:

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

## <a name="operator-precedence"></a>Operatorer

Alla operatörer listas nedan per prioritet från lägre till högre. Operatorer på samma rad är i samma prioritet:
````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````
Alla operatörer kan användas med eller utan prefixet bindestreck. Parenteser behövs bara om prioritet inte uppfyller dina krav.
Exempel:
```
   user.department –eq "Marketing" –and user.country –eq "US"
```
motsvarar att:
```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```
## <a name="using-the--in-and--notin-operators"></a>Med hjälp av-i och - notIn operatörer

Om du vill jämföra värdet för ett användarattribut mot ett antal olika värden kan du använda-i eller - notIn operatörer. Här är ett exempel med hjälp av-i operatorn:
```
   user.department -In ["50001","50002","50003",“50005”,“50006”,“50007”,“50008”,“50016”,“50020”,“50024”,“50038”,“50039”,“51100”]
```
Observera användningen av den ”[” och ”]” i början och slutet av listan med värden. Det här villkoret utvärderas till True för värdet user.department är lika med ett av värdena i listan.


## <a name="query-error-remediation"></a>Fråga fel reparation
I följande tabell visas vanliga fel och hur du åtgärdar dem

| Frågan parsas | Fel vid användning | Korrigerad användning |
| --- | --- | --- |
| Fel: Attributet stöds inte. |(user.invalidProperty - eq ”Value”) |(user.department - eq ”value”)<br/><br/>Kontrollera att attributet är på den [stöds egenskapslistan](#supported-properties). |
| Fel: Operatorn stöds inte på attribut. |(user.accountEnabled-innehåller SANT) |(user.accountEnabled - eq SANT)<br/><br/>Den operator som används stöds inte för egenskapstypen (i det här exemplet-innehåller kan inte användas på Skriv booleskt värde). Använd rätt operatorer för egenskapstypen. |
| Fel: Frågekompileringsfel. |1. (user.department - eq ”försäljning”) (user.department - eq ”marknadsföring”)<br/><br/>2. (user.userPrincipalName-matchar ”*@domain.ext”) |1. Operator saknas. Använd - och eller - eller två gå med i predikat<br/><br/>(user.department - eq ”försäljning”)- eller (user.department - eq ”marknadsföring”)<br/><br/>2. fel i reguljärt uttryck som används med - matcha<br/><br/>(user.userPrincipalName-matchar ”. *@domain.ext”), alternativt: (user.userPrincipalName-matchar ”\@domain.ext$”)|

## <a name="supported-properties"></a>Egenskaper som stöds
Här följer alla användaregenskaper som du kan använda i dina avancerade regeln:

### <a name="properties-of-type-boolean"></a>Egenskaper för skriver booleskt värde
Tillåtna operatörer

* -eq
* -ne

| Egenskaper | Tillåtna värden | Användning |
| --- | --- | --- |
| accountEnabled |SANT FALSKT |user.accountEnabled - eq SANT |
| dirSyncEnabled |SANT FALSKT |user.dirSyncEnabled - eq SANT |

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
| city |Någon strängvärde eller *null* |(user.city - eq ”value”) |
| land |Någon strängvärde eller *null* |(user.country - eq ”value”) |
| Företagsnamn | Någon strängvärde eller *null* | (user.companyName - eq ”value”) |
| avdelning |Någon strängvärde eller *null* |(user.department - eq ”value”) |
| displayName |Ett värde |(user.displayName - eq ”value”) |
| employeeId |Ett värde |(user.employeeId - eq ”value”)<br>(user.employeeId - ne *null*) |
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
| userPrincipalName |Ett värde |(user.userPrincipalName - eq ”alias@domain”) |
| userType |medlem gäst *null* |(user.userType - eq ”medlem”) |

### <a name="properties-of-type-string-collection"></a>Egenskaper av typen sträng samling
Tillåtna operatörer

* -innehåller
* -notContains

| Egenskaper | Tillåtna värden | Användning |
| --- | --- | --- |
| otherMails |Ett värde |(user.otherMails-innehåller ”alias@domain”) |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses-innehåller ”SMTP: alias@domain”) |

## <a name="multi-value-properties"></a>Egenskaper för flera värden
Tillåtna operatörer

* -alla (uppfyllt när minst ett objekt i samlingen matchar villkoret)
* -alla (uppfyllt när alla objekt i samlingen matchar villkoret)

| Egenskaper | Värden | Användning |
| --- | --- | --- |
| assignedPlans |Varje objekt i samlingen visar egenskaperna för följande sträng: capabilityStatus, tjänst, servicePlanId |user.assignedPlans-alla (assignedPlan.servicePlanId - eq ”efb87545-963c-4e0d-99df-69c6916d9eb0”- och assignedPlan.capabilityStatus - eq ”Enabled”) |

Egenskaper för flera värden är samlingar av objekt av samma typ. Du kan använda - alla och -alla operatörer att tillämpa ett villkor för en eller alla objekt i samlingen, respektive. Exempel:

assignedPlans är en egenskap för flera värden som visar en lista över alla tjänstplaner som tilldelats användaren. Den nedan uttrycket väljer användare som har den Exchange Online (Plan 2) service-planen som också är tillståndet aktiverad:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(Guid-identifierare identifierar tjänstprenumerationen Exchange Online (Plan 2).)

> [!NOTE]
> Detta är användbart om du vill identifiera alla användare som en Office 365 (eller andra Microsoft-onlinetjänst) funktionen har aktiverats, till exempel för att rikta dem med en viss uppsättning principer.

Följande uttryck väljer alla användare som har alla service-plan som är associerad med Intune-tjänsten (som identifieras av tjänstnamn ”SCO”):
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Användning av Null-värden

Om du vill ange ett null-värde i en regel kan du använda den *null* värde. Var noga med att inte använda citattecken runt ordet *null* -om du gör det tolkas som en teckensträng-värde. -Inte operatör kan inte användas som en jämförande operator för null. Om du använder det, får du ett felmeddelande om du använder null eller $null. Använd istället - eq eller -ne. Det korrekta sättet att referera till null-värdet är följande:
```
   user.mail –ne $null
```

## <a name="extension-attributes-and-custom-attributes"></a>Tilläggsattribut och anpassade attribut
Tilläggsattribut och anpassade attribut som stöds i regler för dynamiskt medlemskap.

Tilläggsattribut synkroniseras från den lokala Windows Server AD och vidta formatet för ”ExtensionAttributeX”, där X är lika med 1 – 15.
Ett exempel på en regel som använder ett tilläggsattribut är
```
(user.extensionAttribute15 -eq "Marketing")
```
Anpassade attribut som synkroniseras från en lokal Windows Server AD eller från ett anslutna SaaS-program och format för ”user.extension_[GUID]\__ [attribut]”, där [GUID] är den unika identifieraren i AAD för det program som skapats i attributet i AAD och [attribut] är namnet på attributet eftersom den har skapats.
Ett exempel på en regel som använder ett anpassat attribut är
```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```
Anpassade attributets namn finns i katalogen genom att fråga en användare är attributet med hjälp av Graph-testaren och söka efter attributets namn.

## <a name="direct-reports-rule"></a>”Direktrapporter” regel
Du kan skapa en grupp som innehåller alla direktrapporter av en hanterare. När den chefens rapporter ändras i framtiden, justeras gruppens medlemskap automatiskt.

> [!NOTE]
> 1. Kontrollera att regeln ska fungera för den **hanterar-ID** egenskapen är korrekt inställda på användare i din klient. Du kan kontrollera det aktuella värdet för en användare på deras **fliken profil**.
> 2. Den här regeln stöder endast **direkt** rapporter. Det är för närvarande inte möjligt att skapa en grupp för en kapslad hierarki; till exempel en grupp som innehåller direktrapporter och sina rapporter.
> 3. Den här regeln kan inte kombineras med andra avancerade regler.

**Konfigurera gruppen**

1. Följ steg 1-5 från avsnittet [att skapa avancerade regeln](#to-create-the-advanced-rule), och välj en **Medlemskapstyp** av **dynamisk användare**.
2. På den **regler för dynamiskt medlemskap** bladet ange regeln med följande syntax:

    *Direktrapporterna för ”{objectID_of_manager}”*

    Ett exempel på en giltig regel:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. När du har sparat regeln, läggs alla användare med det angivna värdet för hanterar-ID till i gruppen.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Använda attribut för att skapa regler för enhetsobjekt
Du kan också skapa en regel som väljer enhetsobjekt för medlemskap i en grupp. Du kan använda följande enhetsattribut.

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
 organizationalUnit | valfritt strängvärde som matchar namnet på den organisationsenhet som anges av en lokal Active Directory | (device.organizationalUnit - eq ”USA datorer”)
 deviceId | en giltig enhets-ID för Azure AD | (device.deviceId - eq ”d4fe7726-5966-431c-b3b8-cddc8fdb717d”)
 objekt-ID | en giltig Azure AD objekt-ID |  (device.objectId - eq 76ad43c9-32c5-45e8-a272-7b58b58f596d ”)



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>Ändra dynamiskt medlemskap till statiskt, och vice versa
Det är möjligt att ändra hur medlemskap hanteras i en grupp. Detta är användbart när du vill behålla samma gruppnamn och ID i systemet, så att alla befintliga referenser i gruppen är fortfarande giltiga. Skapa en ny grupp kräver uppdatering av dessa referenser.

Vi har uppdaterat Azure AD administrationscentret för att lägga till stöd för den här funktionen. Kunder kan nu kan konvertera befintliga grupper från dynamiskt medlemskap till tilldelat medlemskap och vice versa via Azure AD-administrationscentret eller PowerShell-cmdletar som visas nedan.

> [!WARNING]
> När du ändrar en befintlig statisk grupp till en dynamisk grupp, alla befintliga medlemmar tas bort från gruppen och sedan medlemskapsregel bearbetas för att lägga till nya medlemmar. Om gruppen används för att styra åtkomsten till appar eller resurser, kan de ursprungliga medlemmarna förlora åtkomsten tills medlemskapsregel bearbetas fullständigt.
>
> Vi rekommenderar att du testar den nya medlemskapsregeln i förväg för att se till att det nya medlemskapet i gruppen är som förväntat.

### <a name="using-azure-ad-admin-center-to-change-membership-management-on-a-group"></a>Med hjälp av Azure AD administratörscenter för ändringshantering medlemskap i en grupp 

1. Logga in på den [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som är en global administratör eller Användaradministratör konto i din klient.
2. Välj **grupper**.
3. Från den **alla grupper** öppna den grupp som du vill ändra.
4. Välj **egenskaper**.
5. På den **egenskaper** för gruppen, Välj en **Medlemskapstyp** tilldelad (statisk), dynamisk användare eller dynamisk enheten, beroende på din önskade Medlemskapstyp. Du kan använda regeln builder för dynamiskt medlemskap för att välja alternativ för en enkel regel eller skapa en avancerad regel själv. 

Följande är ett exempel på hur en grupp från statisk till dynamiskt medlemskap för en grupp användare. 

1. På den **egenskaper** sida på den valda gruppen, Välj en **Medlemskapstyp** av **dynamisk användare**, Välj Ja i dialogrutan förklarar ändringarna i gruppen medlemskap att fortsätta. 
  
   ![Välj medlemskapstypen för dynamisk användare](./media/groups-dynamic-membership/select-group-to-convert.png)
  
2. Välj **Lägg till dynamisk fråga**, och ange sedan regeln.
  
   ![Ange regeln](./media/groups-dynamic-membership/enter-rule.png)
  
3. När du har skapat regeln, Välj **Lägg till fråga** längst ned på sidan.
4. Välj **spara** på den **egenskaper** för gruppen för att spara dina ändringar. Den **Medlemskapstyp** i gruppen uppdateras direkt i listan.

> [!TIP]
> Konverteringen kan misslyckas om den avancerade regeln som du har angett var felaktig. Ett meddelande visas i det övre högra hörnet i portalen som den innehåller en förklaring av varför regeln kan inte accepteras av systemet. Läs den noggrant för att förstå hur du kan justera regeln så att den blir giltigt.

### <a name="using-powershell-to-change-membership-management-on-a-group"></a>Ändringshantering medlemskap i en grupp med hjälp av PowerShell

> [!NOTE]
> Att ändra egenskaperna för dynamisk grupp behöver du använda cmdlet: ar från **förhandsversionen av** [Azure AD PowerShell Version 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Du kan installera förhandsversionen från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureADPreview).

Här är ett exempel på funktioner som växla hantering av medlemskap i en befintlig grupp. I det här exemplet är noggrant att ändra egenskapen GroupTypes och bevara alla värden som inte är relaterade till dynamiskt medlemskap korrekt.

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
Så här gör en grupp statiska:
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
Du gör en grupp dynamisk:
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om grupper i Azure Active Directory.

* [Se befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Skapa en ny grupp och lägga till medlemmar](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Hantera inställningarna för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
