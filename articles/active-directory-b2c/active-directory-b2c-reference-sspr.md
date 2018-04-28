---
title: Självbetjäning för lösenordsåterställning | Microsoft Docs
description: Visar hur du ställer in Självbetjäning för återställning av lösenord för dina kunder i Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/17/2018
ms.author: davidmu
ms.openlocfilehash: 5b75455ad604b594a5f85fea8299d35a7d02c848
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Konfigurera Självbetjäning för återställning av lösenord för dina kunder
Med funktionen självbetjäning lösenord återställa kan dina kunder som har registrerat dig för lokala konton återställa sina lösenord på egen hand. Detta minskar avsevärt belastningen på supportpersonal, särskilt om programmet har miljontals kunder som använder den regelbundet. Med en verifierad e-postadress är för närvarande endast stöds återställningsmetod.

> [!NOTE]
> Den här artikeln gäller självbetjäning lösenord återställning som används i samband med en princip för inloggning. Om du behöver helt anpassningsbar Återställ lösenordsprinciper anropas från din app Se [i den här artikeln](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

Som standard inte din katalog har självbetjäning lösenord återställning aktiverat. Använd följande steg för att aktivera den:

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som administratör för prenumerationen. Detta är samma arbets- eller skolkonto eller samma Microsoft-konto som du använde för att skapa katalogen.
2. Öppna **Azure Active Directory** (i navigeringsfältet till vänster).
4. Ange **Self service lösenordsåterställning aktiverat** till **alla**. 
5. Klicka på **spara** överst på sidan. Du är klar!

Om du vill testa, funktionen ”Kör nu” på valfri inloggning princip som har lokala konton som en identitetsleverantör. Vid inloggning lokalt konto sidan (där du anger en e-postadress och lösenord, eller ett användarnamn och lösenord), klicka på **kan inte komma åt ditt konto?** att verifiera kundupplevelsen.

> [!NOTE]
> Sidorna självbetjäning lösenordet för återställning kan anpassas med hjälp av den [funktionen för företagsanpassning](../active-directory/customize-branding.md).
> 
> 

