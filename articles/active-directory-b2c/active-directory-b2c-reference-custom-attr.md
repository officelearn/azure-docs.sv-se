---
title: 'Azure Active Directory B2C: Anpassade attribut som | Microsoft Docs'
description: Hur du använder anpassade attribut i Azure Active Directory B2C för att samla in information om dina användare
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
ms.openlocfilehash: 6f285c10b7d8ff92c8568c42b6a78dc4ea9bcc74
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Azure Active Directory B2C: Använd anpassade attribut för att samla in information om dina användare
Din Azure Active Directory (AD Azure) B2C-katalog som levereras med en inbyggd uppsättning information (attribut): Förnamn, efternamn, ort, postnummer och andra attribut. Men har alla konsumentinriktade program unika krav på vad attribut för att samla in från konsumenterna. Du kan utöka uppsättningen attribut som lagras på varje konsumentkonto med Azure AD B2C. Du kan skapa anpassade attribut på den [Azure-portalen](https://portal.azure.com/) och använda den i din anmälan principer som visas nedan. Du kan också läsa och skriva dessa attribut med hjälp av den [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [!NOTE]
> Använd för anpassade attribut [Azure AD Graph API Directory-schemautökningar](https://msdn.microsoft.com/library/azure/dn720459.aspx).
> 
> 

## <a name="create-a-custom-attribute"></a>Skapa ett anpassat attribut
1. [Följ dessa steg för att gå till B2C-funktionsbladet på Azure portal](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klicka på **användarattribut**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange en **namn** för det anpassade attributet (till exempel ”ShoeSize”) och eventuellt en **beskrivning**. Klicka på **Skapa**.
   
   > [!NOTE]
   > Endast ”sträng”, ”Boolean” och ”Int” **datatyper** är tillgängliga.
   > 
   > 

Det anpassade attributet är nu tillgänglig i listan över **användarattribut**, och för användning i dina principer för registrering.

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>Använd ett anpassat attribut i principen för registrering
1. [Följ dessa steg för att gå till B2C-funktionsbladet på Azure portal](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klicka på **Registreringsprinciper**.
3. Klicka på principen för registrering (till exempel ”B2C_1_SiUp”) för att öppna den. Klicka på **redigera** längst upp på bladet.
4. Klicka på **registreringsattribut** och välj det anpassade attributet (till exempel ”ShoeSize”). Klicka på **OK**.
5. Klicka på **Programanspråk** och välj det anpassade attributet. Klicka på **OK**.
6. Klicka på **spara** längst upp på bladet.

Du kan använda funktionen ”Kör nu” för principen för att verifiera användarfunktioner. Du bör nu se ”ShoeSize” i listan över attribut som samlas in under registreringen konsumenten och finns i den token som skickas tillbaka till ditt program.

## <a name="notes"></a>Anteckningar
* Tillsammans med principer för registrering kan anpassade attribut också användas i principer för registrering eller inloggning och redigera principer profiler.
* Det finns en känd begränsning för anpassade attribut. Det är endast skapas första gången den används i princip och inte när du lägger till den i listan över **användarattribut**.

