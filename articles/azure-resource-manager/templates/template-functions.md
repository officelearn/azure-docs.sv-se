---
title: Mallfunktioner
description: Beskriver de funktioner som används i en Azure Resource Manager mall för att hämta värden, arbeta med strängar och siffror och hämta distributions information.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: a9d10ad4899f35acd45069cb3d351a60632fed3a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207050"
---
# <a name="azure-resource-manager-template-functions"></a>Funktioner för Azure Resource Manager mallar

I den här artikeln beskrivs alla funktioner som du kan använda i en Azure Resource Manager-mall. Information om hur du använder funktioner i din mall finns i [syntax för mallar](template-expressions.md).

Information om hur du skapar dina egna funktioner finns i [användardefinierade funktioner](template-syntax.md#functions).

De flesta funktioner fungerar på samma sätt när de distribueras till en resurs grupp, prenumeration, hanterings grupp eller klient organisation. Några funktioner kan inte användas i alla omfång. De anges i listorna nedan.

<a id="array" aria-hidden="true" />
<a id="coalesce" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="json" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-and-object-functions"></a>Funktioner för matris och objekt

Resource Manager innehåller flera funktioner för att arbeta med matriser och objekt.

* [lagringsmatriser](template-functions-array.md#array)
* [coalesce](template-functions-array.md#coalesce)
* [concat](template-functions-array.md#concat)
* [ingår](template-functions-array.md#contains)
* [createArray](template-functions-array.md#createarray)
* [saknas](template-functions-array.md#empty)
* [förstagångskörningen](template-functions-array.md#first)
* [överlappning](template-functions-array.md#intersection)
* [utgör](template-functions-array.md#json)
* [pågå](template-functions-array.md#last)
* [krävande](template-functions-array.md#length)
* [minimum](template-functions-array.md#min)
* [bekräftat](template-functions-array.md#max)
* [intervall](template-functions-array.md#range)
* [Ignorera](template-functions-array.md#skip)
* [gå](template-functions-array.md#take)
* [Union](template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Jämförelsefunktioner

Resource Manager innehåller flera funktioner för att göra jämförelser i dina mallar.

* [är lika med](template-functions-comparison.md#equals)
* [minskad](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [störst](template-functions-comparison.md#greater)
* [Större](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="deployment-value-functions"></a>Funktioner för distributions värde

Resource Manager innehåller följande funktioner för att hämta värden från avsnitt i mallen och värden som är relaterade till distributionen:

* [spridningen](template-functions-deployment.md#deployment)
* [miljö](template-functions-deployment.md#environment)
* [parameters](template-functions-deployment.md#parameters)
* [användarvariabler](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Logiska funktioner

Resource Manager innehåller följande funktioner för att arbeta med logiska villkor:

* [särskilt](template-functions-logical.md#and)
* [booleska](template-functions-logical.md#bool)
* [eventuella](template-functions-logical.md#if)
* [Ogiltigt](template-functions-logical.md#not)
* [eller](template-functions-logical.md#or)

<a id="add" aria-hidden="true" />
<a id="copyindex" aria-hidden="true" />
<a id="div" aria-hidden="true" />
<a id="float" aria-hidden="true" />
<a id="int" aria-hidden="true" />
<a id="minint" aria-hidden="true" />
<a id="maxint" aria-hidden="true" />
<a id="mod" aria-hidden="true" />
<a id="mul" aria-hidden="true" />
<a id="sub" aria-hidden="true" />

## <a name="numeric-functions"></a>Numeriska funktioner

Resource Manager innehåller följande funktioner för att arbeta med heltal:

* [skapa](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [tagg](template-functions-numeric.md#div)
* [flyta](template-functions-numeric.md#float)
* [int](template-functions-numeric.md#int)
* [minimum](template-functions-numeric.md#min)
* [bekräftat](template-functions-numeric.md#max)
* [rest](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [Build](template-functions-numeric.md#sub)

<a id="extensionResourceId" aria-hidden="true" />
<a id="listkeys" aria-hidden="true" />
<a id="list" aria-hidden="true" />
<a id="providers" aria-hidden="true" />
<a id="reference" aria-hidden="true" />
<a id="resourcegroup" aria-hidden="true" />
<a id="resourceid" aria-hidden="true" />
<a id="subscription" aria-hidden="true" />
<a id="subscriptionResourceId" aria-hidden="true" />
<a id="tenantResourceId" aria-hidden="true" />

## <a name="resource-functions"></a>Resursfunktioner

Resource Manager tillhandahåller följande funktioner för att hämta resurs-värden:

* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [Listnycklar](template-functions-resource.md#listkeys)
* [listSecrets](template-functions-resource.md#list)
* [lista](template-functions-resource.md#list)
* [finansiär](template-functions-resource.md#providers)
* [förhållande](template-functions-resource.md#reference)
* [resourceGroup](template-functions-resource.md#resourcegroup) -kan bara användas i distributioner till en resurs grupp.
* [resourceId](template-functions-resource.md#resourceid) -kan användas i valfri omfattning, men giltiga parametrar ändras beroende på omfattningen.
* [prenumeration](template-functions-resource.md#subscription) – kan bara användas i distributioner till en resurs grupp eller prenumeration.
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true" />
<a id="base64tojson" aria-hidden="true" />
<a id="base64tostring" aria-hidden="true" />
<a id="concat" aria-hidden="true" />
<a id="containsstring" aria-hidden="true" />
<a id="datauri" aria-hidden="true" />
<a id="datauritostring" aria-hidden="true" />
<a id="emptystring" aria-hidden="true" />
<a id="endswith" aria-hidden="true" />
<a id="firststring" aria-hidden="true" />
<a id="guid" aria-hidden="true" />
<a id="indexof" aria-hidden="true" />
<a id="laststring" aria-hidden="true" />
<a id="lastindexof" aria-hidden="true" />
<a id="lengthstring" aria-hidden="true" />
<a id="padleft" aria-hidden="true" />
<a id="replace" aria-hidden="true" />
<a id="skipstring" aria-hidden="true" />
<a id="split" aria-hidden="true" />
<a id="startswith" aria-hidden="true" />
<a id="string" aria-hidden="true" />
<a id="substring" aria-hidden="true" />
<a id="takestring" aria-hidden="true" />
<a id="tolower" aria-hidden="true" />
<a id="toupper" aria-hidden="true" />
<a id="trim" aria-hidden="true" />
<a id="uniquestring" aria-hidden="true" />
<a id="uri" aria-hidden="true" />
<a id="uricomponent" aria-hidden="true" />
<a id="uricomponenttostring" aria-hidden="true" />

## <a name="string-functions"></a>Strängfunktioner

Resource Manager innehåller följande funktioner för att arbeta med strängar:

* [Base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [concat](template-functions-string.md#concat)
* [ingår](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [saknas](template-functions-string.md#empty)
* [endsWith](template-functions-string.md#endswith)
* [förstagångskörningen](template-functions-string.md#first)
* [formatering](template-functions-string.md#format)
* [LED](template-functions-string.md#guid)
* [indexOf](template-functions-string.md#indexof)
* [pågå](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [krävande](template-functions-string.md#length)
* [newGuid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [bytt](template-functions-string.md#replace)
* [Ignorera](template-functions-string.md#skip)
* [split](template-functions-string.md#split)
* [startsWith](template-functions-string.md#startswith)
* [nollängd](template-functions-string.md#string)
* [under sträng](template-functions-string.md#substring)
* [gå](template-functions-string.md#take)
* [toLower](template-functions-string.md#tolower)
* [toUpper](template-functions-string.md#toupper)
* [reducera](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [URI](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)
* [utcNow](template-functions-string.md#utcnow)

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitten i en Azure Resource Manager mall finns i [redigera Azure Resource Manager mallar](template-syntax.md)
* Om du vill slå samman flera mallar, se [använda länkade mallar med Azure Resource Manager](linked-templates.md)
* Om du vill iterera ett visst antal gånger när du skapar en typ av resurs, se [skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md).
* Information om hur du distribuerar mallen som du har skapat finns i [distribuera ett program med Azure Resource Manager mall](deploy-powershell.md)
