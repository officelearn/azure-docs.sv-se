---
title: Återställning av lösenord för självbetjäning i Azure Active Directory B2C | Microsoft-dokument
description: Visar hur du konfigurerar återställning av lösenord för självbetjäning för dina kunder i Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d6dad52c8a3e63c64bb8e0e0030e8c50b5bab42c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183116"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Konfigurera återställning av lösenord för självbetjäning för dina kunder

Med funktionen för återställning av lösenord med självbetjäning kan dina kunder som har registrerat sig för lokala konton återställa sina lösenord på egen hand. Detta minskar avsevärt bördan för din supportpersonal, särskilt om din ansökan har miljontals kunder som använder den regelbundet. För närvarande är det den enda återställningsmetod som stöds med hjälp av en verifierad e-postadress.

> [!NOTE]
> Den här artikeln gäller återställning av lösenord för självbetjäning som används i samband med användarflödet V1 **Sign in,** som använder **Lokal kontoinloggning** som identitetsprovider. Om du behöver helt anpassningsbara användarflöden för återställning av lösenord som anropas från din app läser du [den här artikeln](user-flow-overview.md).
>
>

Som standard har katalogen inte självbetjäningslösenordsåterställning aktiverat. Gör så här för att aktivera den:

1. Logga in på [Azure-portalen](https://portal.azure.com/) som prenumerationsadministratör. Det här är samma arbets- eller skolkonto eller samma Microsoft-konto som du använde för att skapa katalogen.
2. Öppna **Azure Active Directory** (i navigeringsfältet till vänster).
3. Bläddra nedåt på alternativbladet och välj **Återställning av lösenord**.
4. Ange **återställning av lösenord för självbetjäning aktiverad** för **alla**.
5. Klicka på **Spara** längst upp på sidan. Du är klar!

Testa genom att använda funktionen Kör nu på alla inloggningsanvändare som har lokala konton som identitetsprovider. Klicka på Kan inte komma åt ditt konto på inloggningssidan för lokala konton (där du anger en e-postadress och ett lösenord eller ett användarnamn och **lösenord?**

> [!NOTE]
> Självbetjäningssidorna för återställning av lösenord kan anpassas med hjälp av [företagets varumärkesfunktion](../active-directory/fundamentals/customize-branding.md).
>
>

