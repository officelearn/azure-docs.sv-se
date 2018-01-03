---
title: "Azure hanterade program PublicIpAddressCombo gränssnittselement | Microsoft Docs"
description: "Beskriver Microsoft.Network.PublicIpAddressCombo UI-element för hanterade program i Azure"
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
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 990b84cf0df74b7b08a5fa105c2c2213aa251f9d
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2017
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI-element
En grupp av kontroller för att välja en ny eller befintlig offentlig IP-adress. Du använder det här elementet när [att skapa ett Azure hanterade program](publish-service-catalog-app.md).

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
    "domainNameLabel": "foobar"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Kommentarer
- Om `constraints.required.domainNameLabel` är inställd på **SANT**, måste användaren ange en domännamnet när du skapar en ny offentlig IP-adress. Befintliga offentliga IP-adresser utan en etikett inte är tillgängliga för val.
- Om `options.hideNone` är inställd på **SANT**, sedan alternativet **ingen** för den offentliga IP-adress är dolt. Standardvärdet är **FALSKT**.
- Om `options.hideDomainNameLabel` är inställd på **SANT**, och sedan textrutan för domännamnet är dolt. Standardvärdet är **FALSKT**.
- Om `options.hideExisting` är true tas användaren inte har möjlighet att välja en befintlig offentlig IP-adress. Standardvärdet är **FALSKT**.

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
  "domainNameLabel": "foobar",
  "publicIPAllocationMethod": "Dynamic",
  "newOrExistingOrNone": "new"
}
```
- När `options.hideNone` har angetts som **SANT**, `newOrExistingOrNone` har bara ett värde av **nya** eller **befintliga**.
- När `options.hideDomainNameLabel` har angetts som **SANT**, `domainNameLabel` har inte deklarerats.

## <a name="next-steps"></a>Nästa steg
* En introduktion till hanterade program, se [översikt över Azure Managed Application](overview.md).
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element, se [CreateUiDefinition element](create-uidefinition-elements.md).
