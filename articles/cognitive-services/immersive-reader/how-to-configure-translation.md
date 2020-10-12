---
title: Konfigurera översättning
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas hur du konfigurerar olika alternativ för översättning.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: ea066fab713100309103a040d309bac5b984fb99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85486938"
---
# <a name="how-to-configure-translation"></a>Konfigurera Översättning

Den här artikeln visar hur du konfigurerar olika alternativ för översättning i den fördjupade läsaren.

## <a name="configure-translation-language"></a>Konfigurera översättnings språk

`options`Parametern innehåller alla flaggor som kan användas för att konfigurera översättning. Ange `language` parametern till det språk som du vill översätta till. Se [språk stöd](./language-support.md) för en fullständig lista över språk som stöds.

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>Översätt automatiskt dokumentet vid inläsning

Ange `autoEnableDocumentTranslation` till `true` om du vill aktivera automatiskt översättning av hela dokumentet när den fördjupade läsaren läser in.

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>Aktivera ord översättning automatiskt

Ange `autoEnableWordTranslation` till `true` om du vill aktivera en enstaka ord översättning.

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>Nästa steg

* Utforska [SDK-referensen för avancerad läsare](./reference.md)