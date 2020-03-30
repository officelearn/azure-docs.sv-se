---
title: JavaScript- och sidlayoutversioner
titleSuffix: Azure AD B2C
description: Lär dig hur du aktiverar JavaScript och använder sidlayoutversioner i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 23d345ea9f22be5c4dac20e6e8784a8de079bccb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185852"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>JavaScript- och sidlayoutversioner i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C innehåller en uppsättning paketerade innehåll som innehåller HTML, CSS och JavaScript för användargränssnittselementen i dina användarflöden och anpassade principer.

Så här aktiverar du JavaScript för dina program:

* Aktivera det på användarflödet med hjälp av Azure-portalen
* Välja en [sidlayout](page-layout.md)
* Använda [b2clogin.com](b2clogin.md) i dina begäranden

Om du tänker aktivera [JavaScript-kod](javascript-samples.md) på klientsidan måste de element som du baserar JavaScript på vara oföränderliga. Om de inte är oföränderliga kan eventuella ändringar orsaka oväntat beteende på dina användarsidor. Om du vill förhindra dessa problem kan du framtvinga användningen av en sidlayout och ange en sidlayoutversion för att säkerställa att innehållsdefinitionerna som du har baserat JavaScript på är oföränderliga. Även om du inte tänker aktivera JavaScript kan du ange en sidlayoutversion för dina sidor.

## <a name="enable-javascript"></a>Aktivera JavaScript

I egenskaperna för **användarflödet**kan du aktivera JavaScript. Om du aktiverar JavaScript används även en sidlayout. Du kan sedan ange sidlayoutversionen för användarflödet enligt beskrivningen i nästa avsnitt.

![Sidan Egenskaper för användarflöde med aktivera JavaScript-inställning markerad](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Välj en sidlayoutversion

Oavsett om du aktiverar JavaScript i användarflödets egenskaper eller inte kan du ange en sidlayoutversion för användarnas flödessidor. Öppna användarflödet och välj **Sidlayouter**. Under **LAYOUTNAMN**väljer du en sida för användarflöde och väljer **sidlayoutversion**.

Information om de olika sidlayoutversionerna finns i [ändringsloggen för sidlayoutversion](page-layout.md).

![Sidlayoutinställningar i portalen som visar listrutan sidlayoutversion](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>Nästa steg

Du hittar exempel på JavaScript-användning i [JavaScript-exempel för användning i Azure Active Directory B2C](javascript-samples.md).
