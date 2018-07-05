---
title: Anpassade attribut för Azure Active Directory B2C | Microsoft Docs
description: Hur du använder anpassade attribut i Azure Active Directory B2C för att samla in information om dina användare.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 893dfbae96d2cfea01b1f281f888e9281bf582f9
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441924"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Azure Active Directory B2C: Använd anpassade attribut för att samla in information om dina användare
Din Azure Active Directory (Azure AD) B2C-katalog som levereras med en inbyggd uppsättning information (attribut): Förnamn, efternamn, stad, postnummer och andra attribut. Varje konsumentinriktade program har dock unika krav på vad attribut för att samla in från användare. Med Azure AD B2C kan du utöka uppsättningen attribut som lagras på varje konsumentkonto. Du kan skapa anpassade attribut på den [Azure-portalen](https://portal.azure.com/) och använda den i din registreringsprinciper enligt nedan. Du kan också läsa och skriva dessa attribut med hjälp av den [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [!NOTE]
> Anpassade attribut Använd [Azure AD Graph API Directory-schemautökningar](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).
> 
> 

## <a name="create-a-custom-attribute"></a>Skapa ett anpassat attribut
1. [Följ dessa steg för att gå till B2C-funktionsbladet på Azure portal](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klicka på **användarattribut**.
3. Klicka på **+Lägg till** överst på bladet.
4. Ange en **namn** för det anpassade attributet (till exempel ”ShoeSize”) och eventuellt en **beskrivning**. Klicka på **Skapa**.
   
   > [!NOTE]
   > Endast ”String”, ”Boolean” och ”Int” **datatyper** finns för närvarande.
   > 
   > 

Det anpassade attributet är nu tillgänglig i listan över **användarattribut**, och för användning i dina principer för registrering.

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>Använd ett anpassat attribut i principen för registrering
1. [Följ dessa steg för att gå till B2C-funktionsbladet på Azure portal](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klicka på **Registreringsprinciper**.
3. Klicka på registreringsprincipen (till exempel ”B2C_1_SiUp”) för att öppna den. Klicka på **redigera** överst på bladet.
4. Klicka på **registreringsattribut** och välj det anpassade attributet (till exempel ”ShoeSize”). Klicka på **OK**.
5. Klicka på **Programanspråk** och välj det anpassade attributet. Klicka på **OK**.
6. Klicka på **spara** överst på bladet.

Du kan använda funktionen ”Kör nu” i principen för att kontrollera hur lösenordsåterställningen går till. Du bör nu se ”ShoeSize” i listan över attribut som samlas in under konsument registrering och se den i den token som skickas tillbaka till programmet.

## <a name="notes"></a>Anteckningar
* Tillsammans med registreringsprinciper, kan anpassade attribut också användas i registrerings-eller logga in och profilredigeringsprinciper.
* Det finns en känd begränsning av anpassade attribut. Det är bara skapas första gången den används i princip, men inte när du lägger till den i listan över **användarattribut**.

