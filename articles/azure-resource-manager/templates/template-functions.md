---
title: Mallfunktioner
description: Beskriver de funktioner som ska användas i en Azure Resource Manager-mall för att hämta värden, arbeta med strängar och numeriska enheter och hämta distributionsinformation.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 1d2789e59c091b4e6c39be48b83fe610a592abe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156267"
---
# <a name="arm-template-functions"></a>ARM-mallfunktioner

I den här artikeln beskrivs alla funktioner som du kan använda i en ARM-mall (Azure Resource Manager). Information om hur du använder funktioner i mallen finns i [mallsyntax .](template-expressions.md)

Information om hur du skapar egna funktioner finns i [Användardefinierade funktioner](template-syntax.md#functions).

De flesta funktioner fungerar på samma sätt när de distribueras till en resursgrupp, prenumeration, hanteringsgrupp eller klient. Några funktioner kan inte användas i alla scope. De är noterade i listorna nedan.

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

* [Array](template-functions-array.md#array)
* [smälter samman](template-functions-array.md#coalesce)
* [Concat](template-functions-array.md#concat)
* [Innehåller](template-functions-array.md#contains)
* [skapaArray](template-functions-array.md#createarray)
* [Tom](template-functions-array.md#empty)
* [Första](template-functions-array.md#first)
* [Korsningen](template-functions-array.md#intersection)
* [Json](template-functions-array.md#json)
* [Senaste](template-functions-array.md#last)
* [Längd](template-functions-array.md#length)
* [Min](template-functions-array.md#min)
* [Max](template-functions-array.md#max)
* [Utbud](template-functions-array.md#range)
* [Hoppa över](template-functions-array.md#skip)
* [Ta](template-functions-array.md#take)
* [Unionen](template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Jämförelsefunktioner

Resource Manager innehåller flera funktioner för att göra jämförelser i mallarna.

* [lika med](template-functions-comparison.md#equals)
* [mindre än](template-functions-comparison.md#less)
* [mindre än eller lika med](template-functions-comparison.md#lessorequals)
* [större än](template-functions-comparison.md#greater)
* [större än eller lika med](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="deployment-value-functions"></a>Funktionerna för distributionsvärde

Resource Manager innehåller följande funktioner för att hämta värden från avsnitt i mallen och värden som är relaterade till distributionen:

* [Distribution](template-functions-deployment.md#deployment)
* [Miljö](template-functions-deployment.md#environment)
* [Parametrar](template-functions-deployment.md#parameters)
* [Variabler](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Logiska funktioner

Resource Manager innehåller följande funktioner för att arbeta med logiska villkor:

* [och](template-functions-logical.md#and)
* [Bool](template-functions-logical.md#bool)
* [Om](template-functions-logical.md#if)
* [Inte](template-functions-logical.md#not)
* [Eller](template-functions-logical.md#or)

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

* [Add](template-functions-numeric.md#add)
* [copyIndex (på)](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [float](template-functions-numeric.md#float)
* [int](template-functions-numeric.md#int)
* [Min](template-functions-numeric.md#min)
* [Max](template-functions-numeric.md#max)
* [Mod](template-functions-numeric.md#mod)
* [mul (mul)](template-functions-numeric.md#mul)
* [Sub](template-functions-numeric.md#sub)

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

Resource Manager innehåller följande funktioner för att hämta resursvärden:

* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [listaAS](template-functions-resource.md#list)
* [listTangenter](template-functions-resource.md#listkeys)
* [listaSekreterare](template-functions-resource.md#list)
* [lista*](template-functions-resource.md#list)
* [Leverantörer](template-functions-resource.md#providers)
* [Referens](template-functions-resource.md#reference)
* [resourceGroup](template-functions-resource.md#resourcegroup) - kan endast användas i distributioner till en resursgrupp.
* [resourceId](template-functions-resource.md#resourceid) - kan användas i alla scope, men de giltiga parametrarna ändras beroende på omfånget.
* [prenumeration](template-functions-resource.md#subscription) - kan endast användas i distributioner till en resursgrupp eller prenumeration.
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

* [base64](template-functions-string.md#base64)
* [bas64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [Concat](template-functions-string.md#concat)
* [Innehåller](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [Tom](template-functions-string.md#empty)
* [slutarMed](template-functions-string.md#endswith)
* [Första](template-functions-string.md#first)
* [Format](template-functions-string.md#format)
* [Guid](template-functions-string.md#guid)
* [indexOf](template-functions-string.md#indexof)
* [Senaste](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [Längd](template-functions-string.md#length)
* [nyaGuid](template-functions-string.md#newguid)
* [padVänd](template-functions-string.md#padleft)
* [Ersätta](template-functions-string.md#replace)
* [Hoppa över](template-functions-string.md#skip)
* [split](template-functions-string.md#split)
* [börjarMed](template-functions-string.md#startswith)
* [sträng](template-functions-string.md#string)
* [Delsträng](template-functions-string.md#substring)
* [Ta](template-functions-string.md#take)
* [toLower (TillLågare)](template-functions-string.md#tolower)
* [tillUpper](template-functions-string.md#toupper)
* [Trimma](template-functions-string.md#trim)
* [uniqueString (unikSträngning)](template-functions-string.md#uniquestring)
* [Uri](template-functions-string.md#uri)
* [uriKompent](template-functions-string.md#uricomponent)
* [uriKompent TillString](template-functions-string.md#uricomponenttostring)
* [utcNow (olikartade)](template-functions-string.md#utcnow)

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitten i en ARM-mall finns i [Skapa ARM-mallar](template-syntax.md)
* Information om hur du sammanfogar flera mallar finns [i Använda länkade mallar med Azure Resource Manager](linked-templates.md)
* Information om hur du itererar ett angivet antal gånger när du skapar en typ av resurs finns [i Skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md).
* Information om hur du distribuerar mallen som du har skapat finns i [Distribuera ett program med ARM-mallar](deploy-powershell.md)
