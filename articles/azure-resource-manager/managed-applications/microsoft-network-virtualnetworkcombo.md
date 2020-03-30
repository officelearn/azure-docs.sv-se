---
title: VirtualNetworkCombo UI-element
description: I artikeln beskrivs användargränssnittselementet Microsoft.Network.VirtualNetworkCombo för Azure portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 53c9653b44a6c9d26d49d37b351cf6000676e2d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651974"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkCombo UI-element

En grupp kontroller för att välja ett nytt eller befintligt virtuellt nätverk.

## <a name="ui-sample"></a>Exempel på användargränssnitt

När användaren väljer ett nytt virtuellt nätverk kan användaren anpassa varje undernäts namn och adressprefix. Det är valfritt att konfigurera undernät.

![Microsoft.Network.VirtualNetworkCombo ny](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

När användaren väljer ett befintligt virtuellt nätverk måste användaren mappa varje undernät som distributionsmallen kräver till ett befintligt undernät. Det krävs att undernät konfigureras i det här fallet.

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

## <a name="remarks"></a>Anmärkningar

- Om det anges bestäms det första icke-överlappande adressprefixet av storlek `defaultValue.addressPrefixSize` automatiskt baserat på befintliga virtuella nätverk i användarens prenumeration.
- Standardvärdet för `defaultValue.name` `defaultValue.addressPrefixSize` och är **null**.
- `constraints.minAddressPrefixSize`måste anges. Alla befintliga virtuella nätverk med ett adressutrymme som är mindre än det angivna värdet är inte tillgängliga för markering.
- `subnets`måste anges och `constraints.minAddressPrefixSize` måste anges för varje undernät.
- När du skapar ett nytt virtuellt nätverk beräknas varje undernäts adressprefix automatiskt baserat på `addressPrefixSize`det virtuella nätverkets adressprefix och respektive .
- När du använder ett befintligt virtuellt nätverk `constraints.minAddressPrefixSize` är alla undernät som är mindre än respektive inte tillgängliga för val. Om det anges är dessutom undernät som inte `minAddressCount` har åtminstone tillgängliga adresser tillgängliga för val. Standardvärdet är **0**. Om du vill vara säkra på **true** att `requireContiguousAddresses`de tillgängliga adresserna är sammanhängande anger du true för . Standardvärdet är **sant**.
- Det går inte att skapa undernät i ett befintligt virtuellt nätverk.
- Om `options.hideExisting` är **sant**kan användaren inte välja ett befintligt virtuellt nätverk. Standardvärdet är **falskt**.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av vanliga egenskaper i gränssnittselement finns i [CreateUiDefinition-element](create-uidefinition-elements.md).
