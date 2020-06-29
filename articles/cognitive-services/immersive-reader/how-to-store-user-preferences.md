---
title: Lagra användar inställningar
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas hur du lagrar användarens inställningar.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: beb053551dc1fa28672c488b31dfb29ca3b53651
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486926"
---
# <a name="how-to-store-user-preferences"></a>Så här lagrar du användar inställningar

Den här artikeln visar hur du lagrar användarens inställningar. Den här funktionen är avsedd som ett annat sätt att lagra användarens inställningar om cookies inte är önskvärda eller genomförbara.

## <a name="how-to-enable-storing-user-preferences"></a>Så här aktiverar du lagring av användar inställningar

- `options` Parametern innehåller `onPreferencesChanged` återanropet. Den här funktionen kommer att anropas när användaren ändrar sina inställningar (till exempel ändra text storlek, temafärg, teckensnitt och så vidare). `value`Parametern innehåller en sträng som representerar användarens aktuella inställningar. Den här strängen kan lagras med vilken mekanism som helst.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Så här läser du in användar inställningar i den fördjupade läsaren

Skicka in användarens inställningar till den fördjupade läsaren med hjälp av `preferences` parametern. Ett enkelt exempel på att lagra och läsa in användar inställningarna är följande:

```typescript
let userPreferences = null;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
    }
};
```

## <a name="next-steps"></a>Nästa steg

* Utforska [SDK-referensen för avancerad läsare](./reference.md)