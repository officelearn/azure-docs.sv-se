---
title: Azure PublicIpAddressCombo UI-element | Microsoft Docs
description: Beskriver Microsoft.Network.PublicIpAddressCombo UI-element för Azure-portalen.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/30/2018
ms.author: tomfitz
ms.openlocfilehash: c308b6626f9c37b3928107c4c03e9e0a5da12e6f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI-element
En grupp av kontroller för att välja en ny eller befintlig offentlig IP-adress.

## <a name="ui-sample"></a>UI-exempel
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Om användaren väljer ”Ingen” för den offentliga IP-adress, döljs textrutan domän etikett.
- Om användaren väljer en befintlig offentlig IP-adress, inaktiveras domän etikett namnrutan. Dess värde är domännamnet för den valda IP-adressen.
- De domain name suffix (till exempel westus.cloudapp.azure.com) uppdateringar automatiskt baserat på den valda platsen.

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

## <a name="remarks"></a>Kommentarer
- Om `constraints.required.domainNameLabel` är inställd på **SANT**, måste användaren ange en domännamnet när du skapar en ny offentlig IP-adress. Befintliga offentliga IP-adresser utan en etikett inte är tillgängliga för val.
- Om `options.hideNone` är inställd på **SANT**, sedan alternativet **ingen** för den offentliga IP-adress är dolt. Standardvärdet är **FALSKT**.
- Om `options.hideDomainNameLabel` är inställd på **SANT**, och sedan textrutan för domännamnet är dolt. Standardvärdet är **FALSKT**.
- Om `options.hideExisting` är true tas användaren inte har möjlighet att välja en befintlig offentlig IP-adress. Standardvärdet är **FALSKT**.
- För `zone`, endast offentliga IP-adresser för den angivna zonen eller zonen flexibel offentliga IP-adresser som är tillgängliga.

## <a name="sample-output"></a>Exempel på utdata
Om användaren väljer Ingen offentlig IP-adress, förväntat i följande utdata:
```json
{
  "newOrExistingOrNone": "none"
}
```

Om användaren väljer en ny eller befintlig IP-adress, förväntat i följande utdata:
```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "newOrExistingOrNone": "new"
}
```
- När `options.hideNone` har angetts som **SANT**, `newOrExistingOrNone` har bara ett värde av **nya** eller **befintliga**.
- När `options.hideDomainNameLabel` har angetts som **SANT**, `domainNameLabel` har inte deklarerats.

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element, se [CreateUiDefinition element](create-uidefinition-elements.md).
