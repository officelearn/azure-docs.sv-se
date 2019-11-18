---
title: Användar gränssnitts element för Azure VirtualNetworkCombo | Microsoft Docs
description: Beskriver elementet Microsoft. Network. VirtualNetworkCombo UI för Azure Portal.
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
ms.openlocfilehash: d325230f603be4ccfe4fe42f1b58c6ad892fdb2c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151474"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>GRÄNSSNITTs element för Microsoft. Network. VirtualNetworkCombo

En grupp kontroller för att välja ett nytt eller befintligt virtuellt nätverk.

## <a name="ui-sample"></a>UI-exempel

När användaren väljer ett nytt virtuellt nätverk kan användaren anpassa varje undernäts namn och adressprefix. Konfiguration av undernät är valfritt.

![Microsoft. Network. VirtualNetworkCombo New](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

När användaren väljer ett befintligt virtuellt nätverk måste användaren mappa varje undernät som distributions mal len kräver till ett befintligt undernät. Du måste konfigurera undernät i det här fallet.

![Microsoft. Network. VirtualNetworkCombo befintlig](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-existing.png)

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

## <a name="remarks"></a>Kommentarer

- Om det här alternativet anges bestäms det första icke överlappande adressprefixet för storlek `defaultValue.addressPrefixSize` automatiskt utifrån de befintliga virtuella nätverken i användarens prenumeration.
- Standardvärdet för `defaultValue.name` och `defaultValue.addressPrefixSize` är **Null**.
- `constraints.minAddressPrefixSize` måste anges. Alla befintliga virtuella nätverk med ett adress utrymme som är mindre än det angivna värdet är inte tillgängliga för markering.
- `subnets` måste anges, och `constraints.minAddressPrefixSize` måste anges för varje undernät.
- När du skapar ett nytt virtuellt nätverk beräknas varje undernäts adressprefix automatiskt baserat på det virtuella nätverkets adressprefix och respektive `addressPrefixSize`.
- När du använder ett befintligt virtuellt nätverk är alla undernät som är mindre än respektive `constraints.minAddressPrefixSize` inte tillgängliga för markering. Dessutom är undernät som inte har minst `minAddressCount` tillgängliga adresser inte tillgängliga för markering. Standardvärdet är **0**. Om du vill se till att de tillgängliga adresserna är sammanhängande anger du **Sant** för `requireContiguousAddresses`. Standardvärdet är **True**.
- Det finns inte stöd för att skapa undernät i ett befintligt virtuellt nätverk.
- Om `options.hideExisting` är **Sant**kan användaren inte välja ett befintligt virtuellt nätverk. Standardvärdet är **FALSKT**.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
