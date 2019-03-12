---
title: Resource Manager-Mallfunktioner | Microsoft Docs
description: Beskriver funktionerna du använder i en Azure Resource Manager-mall för att hämta värden, arbeta med strängar och siffror och hämta information om programdistribution.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0644abe1-abaa-443d-820d-1966d7d26bfd
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2019
ms.author: tomfitz
ms.openlocfilehash: eb3435d8c7b10e2de55cb0cf1f3ad2548bf2bcef
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766758"
---
# <a name="azure-resource-manager-template-functions"></a>Azure Resource Manager-Mallfunktioner
Den här artikeln beskrivs de funktioner som du kan använda i en Azure Resource Manager-mall. Information om hur du använder funktioner i mallen finns i [mallsyntaxen](resource-group-authoring-templates.md#syntax).

Om du vill skapa egna funktioner [användardefinierade funktioner](resource-group-authoring-templates.md#functions).

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

## <a name="array-and-object-functions"></a>Funktioner för matris och objekt
Resource Manager tillhandahåller flera funktioner för att arbeta med matriser och -objekt.

* [Matris](resource-group-template-functions-array.md#array)
* [Slå samman](resource-group-template-functions-array.md#coalesce)
* [concat](resource-group-template-functions-array.md#concat)
* [innehåller](resource-group-template-functions-array.md#contains)
* [createArray](resource-group-template-functions-array.md#createarray)
* [tom](resource-group-template-functions-array.md#empty)
* [första](resource-group-template-functions-array.md#first)
* [skärningspunkten](resource-group-template-functions-array.md#intersection)
* [json](resource-group-template-functions-array.md#json)
* [last](resource-group-template-functions-array.md#last)
* [Längd](resource-group-template-functions-array.md#length)
* [min](resource-group-template-functions-array.md#min)
* [max](resource-group-template-functions-array.md#max)
* [Adressintervall](resource-group-template-functions-array.md#range)
* [skip](resource-group-template-functions-array.md#skip)
* [ta](resource-group-template-functions-array.md#take)
* [union](resource-group-template-functions-array.md#union)

<a id="equals" />
<a id="less" />
<a id="lessorequals" />
<a id="greater" />
<a id="greaterorequals" />

## <a name="comparison-functions"></a>Jämförelsefunktioner
Resource Manager tillhandahåller flera funktioner för att göra jämförelser i dina mallar.

* [är lika med](resource-group-template-functions-comparison.md#equals)
* [less](resource-group-template-functions-comparison.md#less)
* [lessOrEquals](resource-group-template-functions-comparison.md#lessorequals)
* [större](resource-group-template-functions-comparison.md#greater)
* [greaterOrEquals](resource-group-template-functions-comparison.md#greaterorequals)

<a id="deployment" />
<a id="parameters" />
<a id="variables" />

## <a name="deployment-value-functions"></a>Värdet distributionsfunktioner
Resource Manager tillhandahåller följande funktioner för att hämta värden från avsnitt i mallen och värden som är relaterade till distributionen:

* [Distribution](resource-group-template-functions-deployment.md#deployment)
* [parameters](resource-group-template-functions-deployment.md#parameters)
* [Variabler](resource-group-template-functions-deployment.md#variables)

<a id="and" />
<a id="bool" />
<a id="if" />
<a id="not" />
<a id="or" />

## <a name="logical-functions"></a>Logiska funktioner
Resource Manager tillhandahåller följande funktioner för att arbeta med logiska villkor:

* [och](resource-group-template-functions-logical.md#and)
* [Bool](resource-group-template-functions-logical.md#bool)
* [if](resource-group-template-functions-logical.md#if)
* [not](resource-group-template-functions-logical.md#not)
* [eller](resource-group-template-functions-logical.md#or)

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
Resource Manager tillhandahåller följande funktioner för att arbeta med heltal:

* [add](resource-group-template-functions-numeric.md#add)
* [copyIndex](resource-group-template-functions-numeric.md#copyindex)
* [div](resource-group-template-functions-numeric.md#div)
* [flyttal](resource-group-template-functions-numeric.md#float)
* [int](resource-group-template-functions-numeric.md#int)
* [min](resource-group-template-functions-numeric.md#min)
* [max](resource-group-template-functions-numeric.md#max)
* [mod](resource-group-template-functions-numeric.md#mod)
* [mul](resource-group-template-functions-numeric.md#mul)
* [sub](resource-group-template-functions-numeric.md#sub)

<a id="listkeys" />
<a id="list" />
<a id="providers" />
<a id="reference" />
<a id="resourcegroup" />
<a id="resourceid" />
<a id="subscription" />

## <a name="resource-functions"></a>Resursfunktioner
Resource Manager tillhandahåller följande funktioner för att hämta resurs-värden:

* [listAccountSas](resource-group-template-functions-resource.md#list)
* [Listnycklar](resource-group-template-functions-resource.md#listkeys)
* [listSecrets](resource-group-template-functions-resource.md#list)
* [lista *](resource-group-template-functions-resource.md#list)
* [Providers](resource-group-template-functions-resource.md#providers)
* [Referens](resource-group-template-functions-resource.md#reference)
* [ResourceGroup](resource-group-template-functions-resource.md#resourcegroup)
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
Resource Manager tillhandahåller följande funktioner för att arbeta med strängar:

* [base64](resource-group-template-functions-string.md#base64)
* [base64ToJson](resource-group-template-functions-string.md#base64tojson)
* [base64ToString](resource-group-template-functions-string.md#base64tostring)
* [concat](resource-group-template-functions-string.md#concat)
* [innehåller](resource-group-template-functions-string.md#contains)
* [dataUri](resource-group-template-functions-string.md#datauri)
* [dataUriToString](resource-group-template-functions-string.md#datauritostring)
* [tom](resource-group-template-functions-string.md#empty)
* [endsWith](resource-group-template-functions-string.md#endswith)
* [första](resource-group-template-functions-string.md#first)
* [GUID](resource-group-template-functions-string.md#guid)
* [indexOf](resource-group-template-functions-string.md#indexof)
* [last](resource-group-template-functions-string.md#last)
* [lastIndexOf](resource-group-template-functions-string.md#lastindexof)
* [Längd](resource-group-template-functions-string.md#length)
* [newGuid](resource-group-template-functions-string.md#newguid)
* [padLeft](resource-group-template-functions-string.md#padleft)
* [Ersätt](resource-group-template-functions-string.md#replace)
* [skip](resource-group-template-functions-string.md#skip)
* [split](resource-group-template-functions-string.md#split)
* [startsWith](resource-group-template-functions-string.md#startswith)
* [sträng](resource-group-template-functions-string.md#string)
* [delsträngen](resource-group-template-functions-string.md#substring)
* [ta](resource-group-template-functions-string.md#take)
* [toLower](resource-group-template-functions-string.md#tolower)
* [toUpper](resource-group-template-functions-string.md#toupper)
* [trim](resource-group-template-functions-string.md#trim)
* [uniqueString](resource-group-template-functions-string.md#uniquestring)
* [uri](resource-group-template-functions-string.md#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)
* [utcNow](resource-group-template-functions-string.md#utcnow)

## <a name="next-steps"></a>Nästa steg
* En beskrivning av avsnitt i en Azure Resource Manager-mall finns i [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md)
* Om du vill slå samman flera mallar, se [med länkade mallar med Azure Resource Manager](resource-group-linked-templates.md)
* Iterera ett angivet antal gånger när du skapar en typ av resurs, finns i [och skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md)
* Om du vill se hur du distribuerar mallen som du har skapat, se [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md)
