---
title: JavaScript och sidan kontrakt versioner för användarflöden i Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du aktiverar JavaScript och använda sidan kontrakt versioner för att anpassa ett användarflöde i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5102755c9e830f43fa92e8546e5125960e0a2f9a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401562"
---
# <a name="about-using-javascript-and-page-contract-versions-in-a-user-flow"></a>Hur du använder JavaScript och sidan kontrakt-versioner i ett användarflöde

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C tillhandahåller en uppsättning paketinnehåll som innehåller HTML, CSS och JavaScript för gränssnittselement i din användarflöden. Om du tänker aktivera [JavaScript](javascript-samples.md) klientkod i din användare flöden, ska du noga de element som du baserar din JavaScript på är inte kan ändras. I annat fall kan ändringar orsaka oväntade resultat för dina användare flow sidor. Du kan framtvinga användningen av ett sida-kontrakt för ett användarflöde och ange en sidan kontrakt version för att förhindra dessa problem. Detta säkerställer att alla innehållsdefinitioner som du har utifrån din JavaScript är inte kan ändras. Även om du inte planerar att aktivera JavaScript för ett användarflöde, kan du ange en sidan kontrakt-version för användaren flow sidorna.

> [!NOTE]
> Den här artikeln beskrivs JavaScript för användarflöden, men du kan också använda JavaScript och väljer sidan kontrakt versioner när du använder [anpassade principer](page-contract.md).

## <a name="enable-javascript"></a>Aktivera JavaScript

Du kan aktivera JavaScript, som också framtvingar användningen av ett sida-kontrakt i flow Användaregenskaper. Du kan konfigurera sidan kontrakt version enligt beskrivningen i nästa avsnitt.

![Aktivera inställningen för JavaScript](media/user-flow-javascript-overview/javascript-settings.PNG)

## <a name="specify-a-page-contract-version"></a>Ange en sidan kontrakt-version

Oavsett om du aktiverar JavaScript i egenskaperna för ditt användarflöde, kan du ange en sidan kontrakt-version för användaren flow sidorna. Öppna användarflödet och välj **sidan layouter**. Under **Layoutnamn**markerar du en användarsidan för flödet och väljer den **sidan kontrakt Version**.

![Aktivera inställningen för JavaScript](media/user-flow-javascript-overview/page-contract-version.PNG)

## <a name="next-steps"></a>Nästa steg
Se den [JavaScript-exempel för användning i Azure Active Directory B2C](javascript-samples.md).
