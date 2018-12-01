---
title: Lösenord för självbetjäning i Azure Active Directory B2C | Microsoft Docs
description: Visar hur du ställer in lösenord för självbetjäning för dina kunder i Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7dd68f409ad4c8130ed841ddc7ef5910d3b1945c
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52719750"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Konfigurera lösenord för självbetjäning för dina kunder
Med funktionen för återställning av lösenord för självbetjäning kan dina kunder som har registrerat sig för lokala konton återställa sina lösenord på egen hand. Det här minskar avsevärt belastningen på supportpersonal, särskilt om programmet har miljontals kunder som använder den med jämna mellanrum. Med en verifierad e-postadress är för närvarande den enda återställningsmetoden stöds.

> [!NOTE]
> Den här artikeln gäller för lösenord för självbetjäning återställning som används i samband med V1 **logga in** användarflödet som använder **inloggning från lokalt konto** som identitetsleverantör. Om du behöver användarflöden för återställning av helt anpassningsbara lösenord anropas från din app Se [i den här artikeln](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

Som standard din katalog har inte lösenord för självbetjäning återställning aktiverat. Använd följande steg för att aktivera den:

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som administratör för prenumerationen. Det här är samma arbets- eller skolkonto eller samma Microsoft-konto som användes för att skapa din katalog.
2. Öppna **Azure Active Directory** (i navigeringsfältet till vänster).
4. Ange **Self service för lösenordsåterställning aktiverat** till **alla**. 
5. Klicka på **Spara** längst upp på sidan. Du är klar!

Testa genom att använda funktionen ”Kör nu” på Logga in användarflöde som har lokala konton som identitetsprovider. Om inloggningen lokalt konto klickar (där du anger en e-postadress och lösenord, eller ett användarnamn och lösenord), på **kan inte komma åt ditt konto?** att verifiera kundupplevelsen.

> [!NOTE]
> Sidorna för återställning av lösenord för självbetjäning kan anpassas med hjälp av den [funktionen för företagsanpassning](../active-directory/fundamentals/customize-branding.md).
> 
> 

