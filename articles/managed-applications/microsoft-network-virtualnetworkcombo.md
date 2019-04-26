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
ms.openlocfilehash: b0437338b403ff19761173d08be3938d07f13f55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60252525"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkCombo UI-element
En grupp av kontroller för att välja ett nytt eller befintligt virtuellt nätverk.

## <a name="ui-sample"></a>UI-exempel
När användaren väljer ett nytt virtuellt nätverk, kan du anpassa namn på varje undernät och adressprefix. Det är valfritt att konfigurera undernät.

![Microsoft.Network.VirtualNetworkCombo new](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

När användaren väljer ett befintligt virtuellt nätverk, måste användaren mappas varje undernät som kräver att mallen för distribution till ett befintligt undernät. Konfigurera undernät krävs i det här fallet.

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
- Om det första icke-överlappande adressprefix i storleken `defaultValue.addressPrefixSize` fastställs automatiskt baserat på befintliga virtuella nätverk i användarens prenumeration.
- Standardvärdet för `defaultValue.name` och `defaultValue.addressPrefixSize` är **null**.
- `constraints.minAddressPrefixSize` måste anges. Alla befintliga virtuella nätverk med ett adressutrymme som är mindre än det angivna värdet är inte tillgängliga för val av.
- `subnets` måste anges och `constraints.minAddressPrefixSize` måste anges för varje undernät.
- När du skapar ett nytt virtuellt nätverk, adressprefix för varje undernät beräknas automatiskt baserat på det virtuella nätverket adressprefix och till respektive `addressPrefixSize`.
- När du använder ett befintligt virtuellt nätverk, alla undernät som är mindre än till respektive `constraints.minAddressPrefixSize` är otillgängliga för val av. Även om anges undernät som inte har minst `minAddressCount` tillgängliga adresser är otillgängliga för val av. Standardvärdet är **0**. För att säkerställa att de tillgängliga adresserna är sammanhängande, ange **SANT** för `requireContiguousAddresses`. Standardvärdet är **SANT**.
- Skapa undernät i ett befintligt virtuellt nätverk stöds inte.
- Om `options.hideExisting` är **SANT**, användaren kan inte välja ett befintligt virtuellt nätverk. Standardvärdet är **FALSKT**.

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
* En introduktion till att skapa UI-definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element som finns i [CreateUiDefinition element](create-uidefinition-elements.md).
