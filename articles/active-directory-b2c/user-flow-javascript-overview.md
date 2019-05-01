---
title: JavaScript och sidan kontrakt versioner – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du aktiverar JavaScript och använda sidan kontrakt versioner i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 91b4b621fc3dcedb52f88372fbfac222a744dbd1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570624"
---
# <a name="javascript-and-page-contract-versions-in-azure-active-directory-b2c"></a>JavaScript och sidan kontrakt-versioner i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C tillhandahåller en uppsättning paketinnehåll som innehåller HTML, CSS och JavaScript för användargränssnittselement i användarflöden och anpassade principer. Om du vill aktivera JavaScript för dina program, måste du lägga till ett element till din [anpassad princip](active-directory-b2c-overview-custom.md) eller aktivera det i portalen för användarflöden, Välj ett sida-kontrakt och använda [b2clogin.com](b2clogin.md) i dina önskemål.

Om du tänker aktivera [JavaScript](javascript-samples.md) klientkod, ska du noga de element som du baserar din JavaScript på är inte kan ändras. Annars kan ändringar orsaka oväntade resultat på sidorna användare. Du kan framtvinga användningen av ett sida-kontrakt och ange en sidan kontrakt-version för att förhindra dessa problem. Detta garanterar att alla innehållsdefinitioner som du har utifrån din JavaScript är inte kan ändras. Även om du inte planerar att aktivera JavaScript, kan du ange en sidan kontrakt-version för sidorna.

## <a name="user-flows"></a>Användarflöden

Du kan aktivera JavaScript, som också framtvingar användningen av ett sida-kontrakt i flow Användaregenskaper. Du kan konfigurera sidan kontrakt version enligt beskrivningen i nästa avsnitt.

![Aktivera inställningen för JavaScript](media/user-flow-javascript-overview/javascript-settings.png)

Oavsett om du aktiverar JavaScript i egenskaperna för ditt användarflöde, kan du ange en sidan kontrakt-version för användaren flow sidorna. Öppna användarflödet och välj **sidan layouter**. Under **Layoutnamn**markerar du en användarsidan för flödet och väljer den **sidan kontrakt Version**.

![Aktivera inställningen för JavaScript](media/user-flow-javascript-overview/page-contract-version.png)

## <a name="custom-policies"></a>Anpassade principer

För att aktivera JavaScript i anpassade principer måste du lägga till den **ScriptExecution** elementet så att den **RelyingParty** elementet i filen anpassad princip. Mer information finns i [JavaScript-exempel för användning i Azure Active Directory B2C](javascript-samples.md).

Oavsett om du aktiverar JavaScript i dina anpassade principer, kan du ange en sidan kontrakt-version för sidorna. Läs mer om hur du anger ett sida-kontrakt, [Markera ett sidan kontrakt i Azure Active Directory B2C med anpassade principer](page-contract.md).

## <a name="next-steps"></a>Nästa steg

Se den [JavaScript-exempel för användning i Azure Active Directory B2C](javascript-samples.md).
