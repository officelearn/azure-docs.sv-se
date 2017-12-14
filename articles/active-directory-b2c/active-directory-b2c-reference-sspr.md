---
title: "Azure Active Directory B2C: Självbetjäning för lösenordsåterställning | Microsoft Docs"
description: "Ett avsnitt som visar hur du ställer in Självbetjäning för återställning av lösenord för dina användare i Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mtillman
editor: curtand
ms.assetid: c87ed86e-1520-42b1-8c31-46cd44ed5310
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 27bc191c66b195692e324d1a10ab276b561ef129
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure Active Directory B2C: Konfigurera Självbetjäning för återställning av lösenord för dina användare
Med funktionen för återställning av självbetjäning lösenord kan dina användare (som har registrerat sig för lokala konton) återställa sina lösenord på egen hand. Detta minskar avsevärt belastningen på supportpersonal, särskilt om programmet har miljontals konsumenter med hjälp av den regelbundet. För närvarande stöder vi bara med en verifierad e-postadress som en återställningsmetod för. Vi lägger till ytterligare återställningsmetoder (verifierade telefonnummer, säkerhetsfrågor osv.) i framtiden.

> [!NOTE]
> Den här artikeln gäller självbetjäning lösenord återställning som används i samband med en princip för inloggning. Om du behöver helt anpassningsbar Återställ lösenordsprinciper anropas från din app Se [i den här artikeln](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

Som standard i katalogen inte självbetjäning lösenord återställning aktiverat. Använd följande steg för att aktivera den:

1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com/) som administratör för prenumerationen. Detta är samma arbets- eller skolkonto eller samma Microsoft-konto som du använde för att skapa katalogen.
2. Gå till Active Directory-tillägget i navigeringsfältet till vänster.
3. Hitta din katalog under den **Directory** och klicka på den.
4. Klicka på fliken **Konfigurera**.
5. Rulla ned till den **princip för lösenordsåterställning för användare** avsnittet och växla den **användare som har aktiverats för lösenordsåterställning** att **Ja**. Observera att den **alternativ e-postadress** alternativet är markerat; lämna den som den är.
   
    ![Återställning av lösenord för självbetjäning](./media/active-directory-b2c-reference-sspr/sspr.png)
6. Klicka på **Spara** längst ned på sidan. Du är klar!

Om du vill testa, funktionen ”Kör nu” på valfri inloggning princip som har lokala konton som en identitetsleverantör. Vid inloggning lokalt konto sidan (där du anger en e-postadress och lösenord, eller ett användarnamn och lösenord), klicka på **kan inte komma åt ditt konto?** att verifiera användarfunktioner.

> [!NOTE]
> Sidorna självbetjäning lösenordet för återställning kan anpassas med hjälp av den [funktionen för företagsanpassning](../active-directory/customize-branding.md).
> 
> 

