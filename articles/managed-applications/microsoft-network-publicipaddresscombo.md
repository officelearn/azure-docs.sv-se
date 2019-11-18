---
title: Användar gränssnitts element för Azure PublicIpAddressCombo | Microsoft Docs
description: Beskriver elementet Microsoft. Network. PublicIpAddressCombo UI för Azure Portal.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 069a8ee1f019d1b21be996084e4902f94076fbf7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151498"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>GRÄNSSNITTs element för Microsoft. Network. PublicIpAddressCombo

En grupp kontroller för att välja en ny eller befintlig offentlig IP-adress.

## <a name="ui-sample"></a>UI-exempel

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Om användaren väljer Ingen för offentlig IP-adress är text rutan domän namns etikett dold.
- Om användaren väljer en befintlig offentlig IP-adress är text rutan domän namns etikett inaktive rad. Dess värde är domän namns etiketten för den valda IP-adressen.
- Domän namnets suffix (till exempel westus.cloudapp.azure.com) uppdateras automatiskt baserat på den valda platsen.

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="sample-output"></a>Exempel på utdata

Om användaren väljer ingen offentlig IP-adress, returnerar kontrollen följande utdata:

```json
{
  "newOrExistingOrNone": "none"
}
```

Om användaren väljer en ny eller befintlig IP-adress returnerar kontrollen följande utdata:

```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "sku": "Basic",
  "newOrExistingOrNone": "new"
}
```

- När `options.hideNone` anges som **Sant**, kommer `newOrExistingOrNone` bara att ha värdet **ny** eller **befintlig**.
- När `options.hideDomainNameLabel` anges som **Sant**är `domainNameLabel` inte deklarerat.

## <a name="remarks"></a>Kommentarer

- Om `constraints.required.domainNameLabel` har angetts till **True**måste användaren ange en domän namns etikett när du skapar en ny offentlig IP-adress. Befintliga offentliga IP-adresser utan en etikett kan inte väljas.
- Om `options.hideNone` har värdet **True**är alternativet för att välja **ingen** för den offentliga IP-adressen dold. Standardvärdet är **FALSKT**.
- Om `options.hideDomainNameLabel` har värdet **True**är text rutan för domän namns etiketten dold. Standardvärdet är **FALSKT**.
- Om `options.hideExisting` är sant kan användaren inte välja en befintlig offentlig IP-adress. Standardvärdet är **FALSKT**.
- För `zone`är bara offentliga IP-adresser för den angivna zonen eller zonens elastiska offentliga IP-adresser tillgängliga.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
