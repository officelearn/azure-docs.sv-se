---
title: Hanteraren för filserverresurser Mallfunktioner | Microsoft Docs
description: Beskriver funktionerna du använder i en Azure Resource Manager-mall för att hämta värden, arbeta med strängar och siffror, och hämta information om distribution.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0644abe1-abaa-443d-820d-1966d7d26bfd
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/09/2018
ms.author: tomfitz
ms.openlocfilehash: 834488e259caf60ae96450fcf7c8188c5ffb0bc5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-resource-manager-template-functions"></a>Azure Resource Manager Mallfunktioner
Den här artikeln beskrivs de funktioner som du kan använda i en Azure Resource Manager-mall.

Du lägger till funktioner i dina mallar genom att skriva dem inom hakparenteser: `[` och `]`respektive. Uttrycket utvärderas under distributionen. Medan skrivs som en teckensträng kan resultat av utvärderingen av uttrycket vara av en annan JSON-typ, till exempel en matris, objekt eller heltal. Precis som i JavaScript-funktionsanrop som är formaterade som `functionName(arg1,arg2,arg3)`. Du kan referera egenskaper genom att använda operatorerna punkt och [index].

Ett malluttryck får inte överskrida 24,576 tecken.

Mallfunktioner och deras parametrar är skiftlägeskänsliga. Till exempel Resource Manager matchar **variables('var1')** och **VARIABLES('VAR1')** samma. När utvärderas om funktionen ändrar uttryckligen skiftläge (till exempel toUpper eller toLower), funktionen bevarar skiftläge. Vissa typer av resurser kan ha case krav oavsett hur funktioner utvärderas.

<a id="array" />
<a id="coalesce" />
<a id="concatarray" />
<a id="contains" />
<a id="createarray" />
<a id="empty" />
<a id="first" />
<a id="intersection" />
<a id="json" />
<a id="last" />
<a id="length" />
<a id="min" />
<a id="max" />
<a id="range" />
<a id="skip" />
<a id="take" />
<a id="union" />

## <a name="array-and-object-functions"></a>Array- och funktioner
Resource Manager innehåller flera funktioner för att arbeta med matriser och -objekt.

* [matris](resource-group-template-functions-array.md#array)
* [Slå samman](resource-group-template-functions-array.md#coalesce)
* [concat](resource-group-template-functions-array.md#concat)
* [Innehåller](resource-group-template-functions-array.md#contains)
* [createArray](resource-group-template-functions-array.md#createarray)
* [tom](resource-group-template-functions-array.md#empty)
* [första](resource-group-template-functions-array.md#first)
* [skärningspunkten](resource-group-template-functions-array.md#intersection)
* [JSON](resource-group-template-functions-array.md#json)
* [senaste](resource-group-template-functions-array.md#last)
* [Längd](resource-group-template-functions-array.md#length)
* [Min](resource-group-template-functions-array.md#min)
* [max](resource-group-template-functions-array.md#max)
* [intervallet](resource-group-template-functions-array.md#range)
* [skip](resource-group-template-functions-array.md#skip)
* [ta](resource-group-template-functions-array.md#take)
* [Union](resource-group-template-functions-array.md#union)

<a id="equals" />
<a id="less" />
<a id="lessorequals" />
<a id="greater" />
<a id="greaterorequals" />

## <a name="comparison-functions"></a>Jämförelse funktioner
Resource Manager innehåller flera funktioner för att göra jämförelser i dina mallar.

* [är lika med](resource-group-template-functions-comparison.md#equals)
* [mindre](resource-group-template-functions-comparison.md#less)
* [lessOrEquals](resource-group-template-functions-comparison.md#lessorequals)
* [större](resource-group-template-functions-comparison.md#greater)
* [greaterOrEquals](resource-group-template-functions-comparison.md#greaterorequals)

<a id="deployment" />
<a id="parameters" />
<a id="variables" />

## <a name="deployment-value-functions"></a>Funktioner för distribution av värdet
Hanteraren för filserverresurser innehåller följande funktioner för att hämta värden från avsnitt i mallen och värden som rör distributionen:

* [Distribution](resource-group-template-functions-deployment.md#deployment)
* [parameters](resource-group-template-functions-deployment.md#parameters)
* [variabler](resource-group-template-functions-deployment.md#variables)

<a id="and" />
<a id="bool" />
<a id="if" />
<a id="not" />
<a id="or" />

## <a name="logical-functions"></a>Logiska funktioner
Hanteraren för filserverresurser innehåller följande funktioner för att arbeta med logiska villkor:

* [Och](resource-group-template-functions-logical.md#and)
* [bool](resource-group-template-functions-logical.md#bool)
* [Om](resource-group-template-functions-logical.md#if)
* [inte](resource-group-template-functions-logical.md#not)
* [Eller](resource-group-template-functions-logical.md#or)

<a id="add" />
<a id="copyindex" />
<a id="div" />
<a id="float" />
<a id="int" />
<a id="minint" />
<a id="maxint" />
<a id="mod" />
<a id="mul" />
<a id="sub" />

## <a name="numeric-functions"></a>Numeriska funktioner
Hanteraren för filserverresurser innehåller följande funktioner för att arbeta med heltal:

* [Lägg till](resource-group-template-functions-numeric.md#add)
* [copyIndex](resource-group-template-functions-numeric.md#copyindex)
* [div](resource-group-template-functions-numeric.md#div)
* [flyttal](resource-group-template-functions-numeric.md#float)
* [int](resource-group-template-functions-numeric.md#int)
* [Min](resource-group-template-functions-numeric.md#min)
* [max](resource-group-template-functions-numeric.md#max)
* [MOD](resource-group-template-functions-numeric.md#mod)
* [mul](resource-group-template-functions-numeric.md#mul)
* [Sub](resource-group-template-functions-numeric.md#sub)

<a id="listkeys" />
<a id="list" />
<a id="providers" />
<a id="reference" />
<a id="resourcegroup" />
<a id="resourceid" />
<a id="subscription" />

## <a name="resource-functions"></a>Resursfunktioner
Hanteraren för filserverresurser innehåller följande funktioner för att hämta resurs värden:

* [listKeys](resource-group-template-functions-resource.md#listkeys)
* [listSecrets](resource-group-template-functions-resource.md#list)
* [lista *](resource-group-template-functions-resource.md#list)
* [providers](resource-group-template-functions-resource.md#providers)
* [Referens](resource-group-template-functions-resource.md#reference)
* [resourceGroup](resource-group-template-functions-resource.md#resourcegroup)
* [Resurs-ID](resource-group-template-functions-resource.md#resourceid)
* [prenumeration](resource-group-template-functions-resource.md#subscription)

<a id="base64" />
<a id="base64tojson" />
<a id="base64tostring" />
<a id="concat" />
<a id="containsstring" />
<a id="datauri" />
<a id="datauritostring" />
<a id="emptystring" />
<a id="endswith" />
<a id="firststring" />
<a id="guid" />
<a id="indexof" />
<a id="laststring" />
<a id="lastindexof" />
<a id="lengthstring" />
<a id="padleft" />
<a id="replace" />
<a id="skipstring" />
<a id="split" />
<a id="startswith" />
<a id="string" />
<a id="substring" />
<a id="takestring" />
<a id="tolower" />
<a id="toupper" />
<a id="trim" />
<a id="uniquestring" />
<a id="uri" />
<a id="uricomponent" />
<a id="uricomponenttostring" />

## <a name="string-functions"></a>Strängfunktioner
Hanteraren för filserverresurser innehåller följande funktioner för att arbeta med strängar:

* [Base64](resource-group-template-functions-string.md#base64)
* [base64ToJson](resource-group-template-functions-string.md#base64tojson)
* [base64ToString](resource-group-template-functions-string.md#base64tostring)
* [concat](resource-group-template-functions-string.md#concat)
* [Innehåller](resource-group-template-functions-string.md#contains)
* [dataUri](resource-group-template-functions-string.md#datauri)
* [dataUriToString](resource-group-template-functions-string.md#datauritostring)
* [tom](resource-group-template-functions-string.md#empty)
* [endsWith](resource-group-template-functions-string.md#endswith)
* [första](resource-group-template-functions-string.md#first)
* [GUID](resource-group-template-functions-string.md#guid)
* [indexOf](resource-group-template-functions-string.md#indexof)
* [senaste](resource-group-template-functions-string.md#last)
* [lastIndexOf](resource-group-template-functions-string.md#lastindexof)
* [Längd](resource-group-template-functions-string.md#length)
* [padLeft](resource-group-template-functions-string.md#padleft)
* [Ersätt](resource-group-template-functions-string.md#replace)
* [skip](resource-group-template-functions-string.md#skip)
* [split](resource-group-template-functions-string.md#split)
* [StartsWith](resource-group-template-functions-string.md#startswith)
* [Sträng](resource-group-template-functions-string.md#string)
* [delsträngen](resource-group-template-functions-string.md#substring)
* [ta](resource-group-template-functions-string.md#take)
* [toLower](resource-group-template-functions-string.md#tolower)
* [toUpper](resource-group-template-functions-string.md#toupper)
* [Rensa](resource-group-template-functions-string.md#trim)
* [uniqueString](resource-group-template-functions-string.md#uniquestring)
* [URI: N](resource-group-template-functions-string.md#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Nästa steg
* En beskrivning av avsnitt i en Azure Resource Manager-mallen finns [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md)
* Om du vill slå samman flera mallar finns [använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md)
* Iterera ett angivet antal gånger när du skapar en typ av resurs finns [skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md)
* Information om hur du distribuerar mallen som du har skapat finns [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md)
