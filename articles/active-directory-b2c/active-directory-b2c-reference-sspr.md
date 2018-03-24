---
title: 'Azure Active Directory B2C: Självbetjäning för lösenordsåterställning | Microsoft Docs'
description: Ett avsnitt som visar hur du ställer in Självbetjäning för återställning av lösenord för dina användare i Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: f38473989f90bfe6d35bffb17a02a892ad08cf5e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure Active Directory B2C: Konfigurera Självbetjäning för återställning av lösenord för dina användare
Med funktionen för återställning av självbetjäning lösenord kan dina användare (som har registrerat sig för lokala konton) återställa sina lösenord på egen hand. Detta minskar avsevärt belastningen på supportpersonal, särskilt om programmet har miljontals konsumenter med hjälp av den regelbundet. För närvarande stöder vi bara med en verifierad e-postadress som en återställningsmetod för. Vi lägger till ytterligare återställningsmetoder (verifierade telefonnummer, säkerhetsfrågor osv.) i framtiden.

> [!NOTE]
> Den här artikeln gäller självbetjäning lösenord återställning som används i samband med en princip för inloggning. Om du behöver helt anpassningsbar Återställ lösenordsprinciper anropas från din app Se [i den här artikeln](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

Som standard i katalogen inte självbetjäning lösenord återställning aktiverat. Använd följande steg för att aktivera den:

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som administratör för prenumerationen. Detta är samma arbets- eller skolkonto eller samma Microsoft-konto som du använde för att skapa katalogen.
2. Öppna Active Directory (i navigeringsfältet till vänster).
3. Välj **egenskaper**.
4. Rulla ned till den **lösenordsåterställning via självbetjäning aktiverat** avsnittet och växla mellan att **alla**. 
5. Klicka på **spara** överst på sidan. Du är klar!

Om du vill testa, funktionen ”Kör nu” på valfri inloggning princip som har lokala konton som en identitetsleverantör. Vid inloggning lokalt konto sidan (där du anger en e-postadress och lösenord, eller ett användarnamn och lösenord), klicka på **kan inte komma åt ditt konto?** att verifiera användarfunktioner.

> [!NOTE]
> Sidorna självbetjäning lösenordet för återställning kan anpassas med hjälp av den [funktionen för företagsanpassning](../active-directory/customize-branding.md).
> 
> 

