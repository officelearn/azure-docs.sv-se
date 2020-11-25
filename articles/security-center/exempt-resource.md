---
title: Undanta en resurs från Azure Security Center säkerhets rekommendationer och säkra Poäng
description: Lär dig hur du undantar en resurs från säkerhets rekommendationer och säkra Poäng
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b7780a0ef70a89a88070d5883cc840319a67fa3d
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122349"
---
# <a name="exempt-a-resource-from-recommendations-and-secure-score"></a>Undanta en resurs från rekommendationer och säkerhetspoäng

En kärn prioritet för alla säkerhets Team försöker säkerställa att analytiker kan fokusera på de uppgifter och incidenter som är viktiga för organisationen. Security Center har många funktioner för att anpassa den information som du prioriterar mest och se till att dina säkra poäng är en giltig reflektion av organisationens säkerhets beslut. Att undanta resurser är en sådan funktion.

När du undersöker en säkerhets rekommendation i Azure Security Center är en av de första delarna av informationen som du granskar listan över berörda resurser.

Ibland visas en resurs som du inte bör ta med. Den kan ha åtgärd ATS av en process som inte spåras av Security Center. Eller så kanske din organisation har valt att godkänna risken för den aktuella resursen. 

I sådana fall kan du skapa en undantags regel och se till att resursen inte visas i listan med felaktiga resurser i framtiden och inte påverkar dina säkra poäng. 

Resursen visas som ej tillämplig och orsaken visas som "undantagen" med den justering du väljer.

## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Förhandsgranskning<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Priset|Det här är en förstklassig Azure policy-funktion som erbjuds för Azure Defender-kunder utan extra kostnad. För andra användare kan avgifter tillkomma i framtiden.|
|Nödvändiga roller och behörigheter:|**Prenumerations ägare** eller **princip deltagare** för att skapa ett undantag<br>Om du vill skapa en regel måste du ha behörighet att redigera principer i Azure Policy.<br>Läs mer i [Azure RBAC-behörigheter i Azure policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Nej](./media/icons/no-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||


## <a name="create-an-exemption-rule"></a>Skapa en undantags regel

1. I listan över resurser som inte är felfria väljer du ellips-menyn ("...") för den resurs som du vill undanta.

    :::image type="content" source="./media/exempt-resource/create-exemption.png" alt-text="Skapa undantags alternativ från snabb menyn":::

    Fönstret Skapa undantag öppnas.

    :::image type="content" source="./media/exempt-resource/exemption-rule-options.png" alt-text="Fönstret Skapa undantag":::

1. Ange kriterier och välj ett villkor för varför den här resursen ska undantas:
    - **Åtgärdat – det** här problemet är inte relevant för resursen eftersom den har hanterats av ett annat verktyg eller en annan process än den som föreslås
    - **Avsägelse** – godkänner risken för den här resursen
1. Välj **Spara**.
1. Efter ett tag (det kan ta upp till 24 timmar):
    - Resursen påverkar inte dina säkra poäng.
    - Resursen visas på fliken **ej tillämpligt** på rekommendations informations Sidan
    - Informations remsan överst på sidan med rekommendations information visar antalet undantagna resurser:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Antal undantagna resurser":::

1. Om du vill granska dina undantagna resurser öppnar du fliken **ej tillämpligt** .

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Ändra ett undantag":::

    Orsaken till varje undantag ingår i tabellen (1).

    Om du vill ändra eller ta bort ett undantag väljer du ellips-menyn ("...") som visas (2).


## <a name="review-all-of-the-exemption-rules-on-your-subscription"></a>Granska alla undantags regler för din prenumeration

Undantags regler använder Azure policy för att skapa ett undantag för resursen i princip tilldelningen.

Du kan använda Azure Policy för att spåra alla dina undantag på sidan **undantag** :

:::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure Policy sidan undantag":::



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du undantar en resurs från en rekommendation så att den inte påverkar dina säkra poäng. Mer information om säkra Poäng finns i:

- [Säkerhetspoäng i Azure Security Center](secure-score-security-controls.md)