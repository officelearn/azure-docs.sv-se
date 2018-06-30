---
title: Azure VirtualNetworkCombo UI-element | Microsoft Docs
description: Beskriver Microsoft.Network.VirtualNetworkCombo UI-element för Azure-portalen.
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
ms.openlocfilehash: 2c2553d9ffb1dfbe032385fb77e234a8b96cb239
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110073"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkCombo UI-element
En grupp av kontroller för att välja ett nytt eller befintligt virtuellt nätverk.

## <a name="ui-sample"></a>UI-exempel
När användaren hämtar ett nytt virtuellt nätverk, kan användaren anpassa varje undernät namn och adressprefix. Konfigurera undernät är valfritt.

![Ny Microsoft.Network.VirtualNetworkCombo](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

När du hämtar ett befintligt virtuellt nätverk, måste användaren mappa varje undernät som krävs för mallen för distribution till ett befintligt undernät. Konfiguration av undernät krävs i det här fallet.

![Microsoft.Network.VirtualNetworkCombo befintliga](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-existing.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="remarks"></a>Kommentarer
- Om anges den första icke-överlappande-prefixet för storlek `defaultValue.addressPrefixSize` bestäms automatiskt baserat på de befintliga virtuella nätverk i användarens prenumeration.
- Standardvärdet för `defaultValue.name` och `defaultValue.addressPrefixSize` är **null**.
- `constraints.minAddressPrefixSize` måste anges. Alla befintliga virtuella nätverk med ett adressutrymme som är mindre än det angivna värdet är inte tillgängliga för val.
- `subnets` måste anges, och `constraints.minAddressPrefixSize` måste anges för varje undernät.
- När du skapar ett nytt virtuellt nätverk, varje undernät adressprefixet beräknas automatiskt utifrån på det virtuella nätverket adressprefixet och motsvarande `addressPrefixSize`.
- När du använder ett befintligt virtuellt nätverk, alla undernät som är mindre än respektive `constraints.minAddressPrefixSize` är inte tillgängliga för val. Dessutom, om angivna undernät som inte har minst `minAddressCount` tillgängliga adresser är inte tillgängliga för val. Standardvärdet är **0**. För att säkerställa att tillgängliga adresser är sammanhängande, ange **SANT** för `requireContiguousAddresses`. Standardvärdet är **SANT**.
- Skapa undernät i ett befintligt virtuellt nätverk stöds inte.
- Om `options.hideExisting` är **SANT**, kan användaren välja ett befintligt virtuellt nätverk. Standardvärdet är **FALSKT**.

## <a name="sample-output"></a>Exempel på utdata

```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element, se [CreateUiDefinition element](create-uidefinition-elements.md).
