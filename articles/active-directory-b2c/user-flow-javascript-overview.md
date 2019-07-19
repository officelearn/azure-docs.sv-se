---
title: Versioner av Java Script och sidlayout – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du aktiverar Java Script och använder versioner av sidlayouten i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0eb5c89387d8bdcf0e0b72c669c42f716ff5fbb3
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227109"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Java Script och versioner av sidlayouten i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C innehåller en uppsättning paketerat innehåll som innehåller HTML, CSS och Java Script för användar gränssnitts elementen i dina användar flöden och anpassade principer. Om du vill aktivera Java Script för dina program måste du lägga till ett element i din [anpassade princip](active-directory-b2c-overview-custom.md) eller aktivera det i portalen för användar flöden, välja en sidlayout och använda [b2clogin.com](b2clogin.md) i dina begär Anden.

Om du vill aktivera kod för [Java Script](javascript-samples.md) på klient sidan vill du vara säker på att de element som du baserar ditt Java Script på inte är oföränderliga. Annars kan alla ändringar orsaka oväntade beteenden på dina användar sidor. För att förhindra dessa problem kan du använda en sidlayout och ange en version för sidlayouten. På så sätt ser du till att alla innehålls definitioner som du har baserat på Java Script är oföränderliga. Även om du inte planerar att aktivera Java Script kan du ange en version för sidlayouten för dina sidor.

## <a name="user-flows"></a>Användarflöden

I **egenskaperna**för användar flödet kan du aktivera Java Script, vilket även tvingar användningen av en sidlayout. Du kan sedan ange versionen för sid layouten för användar flödet enligt beskrivningen i nästa avsnitt.

![Sidan Egenskaper för användar flöde med alternativet Aktivera Java Script markerat](media/user-flow-javascript-overview/javascript-settings.png)

### <a name="select-a-page-layout-version"></a>Välj en version av sidlayouten

Oavsett om du aktiverar java script i ditt användar flödes egenskaper kan du ange en version för sidlayouten för dina användar flödes sidor. Öppna användar flödet och välj **sidlayouter**. Under **Kolumnlayoutnamn**väljer du sidan användar flöde och väljer **versionen för sidlayouten**.

Information om olika versioner av sidlayouten finns i [versions ändrings loggen](page-layout.md#version-change-log).

![Inställningar för sidlayout i portalen med listruta för sidlayout](media/user-flow-javascript-overview/page-layout-version.png)

## <a name="custom-policies"></a>Anpassade principer

Om du vill aktivera Java Script i anpassade principer lägger du till elementet **ScriptExecution** i **RelyingParty** -elementet i din anpassade princip fil. Mer information finns i [JavaScript-exempel för användning i Azure Active Directory B2C](javascript-samples.md).

Oavsett om du aktiverar java script i dina anpassade principer kan du ange en version för sidlayouten för dina sidor. Mer information om hur du anger en sidlayout finns [i Välj en sidlayout i Azure Active Directory B2C att använda anpassade principer](page-layout.md).

## <a name="next-steps"></a>Nästa steg

Information om olika versioner av sidlayouten finns i avsnittet versions **ändrings logg** i [Välj en sidlayout i Azure Active Directory B2C med anpassade principer](page-layout.md#version-change-log).

Du kan hitta exempel på JavaScript-användning i [JavaScript-exempel för användning i Azure Active Directory B2C](javascript-samples.md).
