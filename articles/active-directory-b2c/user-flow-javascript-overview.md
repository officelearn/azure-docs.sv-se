---
title: Versioner av Java Script och sidlayout
titleSuffix: Azure AD B2C
description: Lär dig hur du aktiverar Java Script och använder versioner av sidlayouten i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 50644ad3be226648accba6a2f43d4ea068ff977c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258854"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Java Script och versioner av sidlayouten i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C innehåller en uppsättning paketerat innehåll som innehåller HTML, CSS och Java Script för användar gränssnitts elementen i dina användar flöden och anpassade principer.

Så här aktiverar du Java Script för dina program:

* Aktivera det i användar flödet med hjälp av Azure Portal
* Välj en [Sidlayout](page-layout.md)
* Använd [b2clogin.com](b2clogin.md) i dina begär Anden

Om du tänker aktivera [Java Script](javascript-samples.md) på klient sidan måste de element som du baserar Java Script på vara oföränderliga. Om de inte är oföränderliga kan eventuella ändringar orsaka oväntade beteenden på dina användar sidor. Du kan förhindra dessa problem genom att använda en sidlayout och ange en version för sidlayouten för att se till att de innehålls definitioner som du har baserat java script är oföränderliga. Även om du inte planerar att aktivera Java Script kan du ange en version för sidlayouten för dina sidor.

## <a name="enable-javascript"></a>Aktivera JavaScript

I **egenskaperna**för användar flödet kan du aktivera Java Script. Genom att aktivera Java Script används även en sidlayout. Du kan sedan ange versionen för sid layouten för användar flödet enligt beskrivningen i nästa avsnitt.

![Sidan Egenskaper för användar flöde med alternativet Aktivera Java Script markerat](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Välj en version av sidlayouten

Oavsett om du aktiverar java script i ditt användar flödes egenskaper kan du ange en version för sidlayouten för dina användar flödes sidor. Öppna användar flödet och välj **sidlayouter**. Under **Kolumnlayoutnamn**väljer du sidan användar flöde och väljer sedan sidlayouten **(för hands version)**.

Information om olika versioner av sidlayouten finns i [versions ändrings loggen för sidlayouten](page-layout.md).

![Inställningar för sidlayout i portalen med listruta för sidlayout](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>Nästa steg

Du kan hitta exempel på JavaScript-användning i [JavaScript-exempel för användning i Azure Active Directory B2C](javascript-samples.md).
