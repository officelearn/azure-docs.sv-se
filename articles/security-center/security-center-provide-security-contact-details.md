---
title: Ange information om säkerhets kontakt i Azure Security Center | Microsoft Docs
description: Det här dokumentet visar hur du anger säkerhets kontakt uppgifter i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: c09dca8b5927e417714ef89393950cfb58c89020
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042283"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>Konfigurera e-postaviseringar för säkerhets aviseringar 

För att se till att rätt personer i din organisation meddelas om säkerhets aviseringar i din miljö, ange deras e-postadresser på sidan Inställningar för **e-postaviseringar** .

När du konfigurerar dina aviseringar kan du konfigurera e-postmeddelanden som ska skickas till vissa individer eller till någon med en speciell Azure-roll för en prenumeration. 

För att undvika aviserings utmattning begränsar Security Center volymen utgående e-post. Security Center skickar för varje prenumeration:

- högst **fyra** e-postmeddelanden per dag för aviseringar med **hög allvarlighets grad**
- högst **två** e-postmeddelanden per dag för aviseringar med **medelhög allvarlighets grad**
- högst **ett** e-postmeddelande per dag för aviseringar med **låg allvarlighets grad**

## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmänt tillgänglig|
|Priset|Kostnadsfri nivå|
|Nödvändiga roller och behörigheter:|**Säkerhets administratör**<br>**Prenumerations ägare** |
|Moln|![Yes](./media/icons/yes-icon.png) Kommersiella moln<br>![Yes](./media/icons/yes-icon.png) US Gov (delvis)<br>![No](./media/icons/no-icon.png) Kina gov, andra gov|
|||




## <a name="set-up-email-notifications-for-alerts"></a>Konfigurera e-postmeddelanden för aviseringar<a name="email"></a>

Du kan skicka e-postmeddelanden till enskilda användare eller till alla användare med vissa Azure-roller.

1. Från Security Center **pris & inställningar för prissättning** , relevant prenumeration och välj **e-postaviseringar**.

1. Definiera mottagarna för dina meddelanden:

    - Välj från de tillgängliga rollerna i list rutan.
    - Och/eller ange vissa e-postadresser avgränsade med kommatecken. Det finns ingen gräns för antalet e-postadresser som du kan ange.

1. Om du vill använda säkerhets kontakt information för din prenumeration väljer du **Spara**.


## <a name="see-also"></a>Se även
Mer information om säkerhets aviseringar finns i följande avsnitt:

* [Säkerhets aviseringar – en referens guide](alerts-reference.md) – lär dig mer om säkerhets aviseringar som du kan se i Azure Security Center skydds modul för hot
* [Hantera och åtgärda säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – lär dig hantera och reagera på säkerhets aviseringar