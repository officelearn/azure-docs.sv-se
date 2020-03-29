---
title: PublicIpAddressCombo UI-element
description: Beskriver användargränssnittselementet Microsoft.Network.PublicIpAddressCombo för Azure-portalen.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 0393673663df8f3ca580ff34e16bee910b955f8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651922"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI-element

En grupp kontroller för att välja en ny eller befintlig offentlig IP-adress.

## <a name="ui-sample"></a>Exempel på användargränssnitt

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Om användaren väljer "Ingen" för offentlig IP-adress döljs textrutan för domännamnsetiketten.
- Om användaren väljer en befintlig offentlig IP-adress inaktiveras textrutan domännamnsetikett. Dess värde är domännamnsetiketten för den valda IP-adressen.
- Suffixet domännamn (till exempel westus.cloudapp.azure.com) uppdateras automatiskt baserat på den valda platsen.

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

Om användaren inte väljer någon offentlig IP-adress returnerar kontrollen följande utdata:

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

- När `options.hideNone` anges som **true**sant `newOrExistingOrNone` har det bara ett värde på **nya** eller **befintliga**.
- När `options.hideDomainNameLabel` anges som **true**sant `domainNameLabel` , är odeklarerat.

## <a name="remarks"></a>Anmärkningar

- Om `constraints.required.domainNameLabel` värdet är **true**måste användaren ange en domännamnsetikett när en ny offentlig IP-adress skapas. Befintliga offentliga IP-adresser utan etikett är inte tillgängliga för val.
- Om `options.hideNone` värdet är **true**döljs alternativet **Ingen** för den offentliga IP-adressen. Standardvärdet är **falskt**.
- Om `options.hideDomainNameLabel` värdet är **true**döljs textrutan för domännamnsetiketten. Standardvärdet är **falskt**.
- Om `options.hideExisting` är sant kan användaren inte välja en befintlig offentlig IP-adress. Standardvärdet är **falskt**.
- För `zone`är endast offentliga IP-adresser för den angivna zonen eller zonens elastiska offentliga IP-adresser tillgängliga.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av vanliga egenskaper i gränssnittselement finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
