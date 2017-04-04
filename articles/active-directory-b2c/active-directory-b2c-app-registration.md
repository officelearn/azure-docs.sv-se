---
title: 'Azure Active Directory B2C: Programregistrering | Microsoft Docs'
description: Registrera ditt program med Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/13/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: e04fbd97dd4d5ecaf12edf47d80572b32d29ed00
ms.lasthandoff: 03/23/2017



---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: Registrera ditt program

> [!IMPORTANT]
> Program som har skapats från Azure AD B2C-bladet i Azure Portal måste hanteras från samma plats. Om du redigerar B2C-program med hjälp av PowerShell eller en annan portal stöds de inte och kommer troligen inte att fungera med Azure AD B2C.
> 
> 

## <a name="prerequisite"></a>Krav
Om du vill skapa ett program som accepterar registrering och inloggning av konsumenter måste du först registrera programmet med en Azure Active Directory B2C-klient. Skaffa en egen klient genom att följa stegen i [Skapa en Azure AD B2C-klient](active-directory-b2c-get-started.md). När du har följt alla steg i artikeln är B2C-funktionsbladet fäst på startsidan.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Gå till B2C-funktionsbladet
Om B2C-funktionsbladet är fäst på startsidan visas bladet så fort du loggar in på [Azure-portalen](https://portal.azure.com/) som global administratör för B2C-klienten.

Du kan också öppna bladet genom att klicka på **Fler tjänster** och sedan söka på **Azure AD B2C** i det vänstra navigeringsfönstret på [Azure Portal](https://portal.azure.com/).

> [!IMPORTANT]
> Du måste vara global administratör för B2C-klienten för att kunna komma åt B2C-funktionsbladet. En global administratör från en annan klient eller en användare från en klient kan inte komma åt det.  Du kan växla till B2C-klienten med klientväxlaren i det övre högra hörnet i Azure Portal.
> 
> 

## <a name="register-a-web-application"></a>Registrera ett webbprogram
1. Klicka på **Program** på B2C-funktionsbladet på Azure-portalen.
2. Klicka på **+Lägg till** överst på bladet.
3. Ange ett **namn** för programmet som beskriver det för konsumenterna. Du kan till exempel skriva ”Contoso B2C-app”.
4. Ändra **Include web app/web API** (Ta med webbapp/webb-API) till **Ja**. **Svars-URL:erna** är slutpunkter där Azure AD B2C returnerar de token som ditt program begär. Ange till exempel `https://localhost:44316/`.
5. Klicka på **Skapa** för att registrera ditt program.
6. Klicka på det program som du just har skapat och kopiera det globalt unika **klient-ID:t** som du ska använda senare i koden. 
7. Om din webbapp även ska anropa ett webb-API som skyddas av Azure AD B2C bör du skapa en **programhemlighet** genom att gå till bladet **Nycklar** och klicka på knappen **Generera nyckel**.

> [!NOTE]
> En **programhemlighet** är en viktig autentiseringsuppgift och bör skyddas på lämpligt sätt.
> 
   

## <a name="register-a-web-api"></a>Registrera en webb-API
1. Klicka på **Program** på B2C-funktionsbladet på Azure-portalen.
2. Klicka på **+Lägg till** överst på bladet.
3. Ange ett **namn** för programmet som beskriver det för konsumenterna. Du kan till exempel skriva ”Contoso B2C-api”.
4. Ändra **Include web app/web API** (Ta med webbapp/webb-API) till **Ja**. **Svars-URL:erna** är slutpunkter där Azure AD B2C returnerar de token som ditt program begär. Ange till exempel `https://localhost:44316/`.
5. Ange en **App-ID-URI**. Det här är identifieraren som används för ditt webb-API. Ange till exempel ”information”. Den genererar den fullständiga identifierar-URI:n nedanför. 
6. Klicka på **Skapa** för att registrera ditt program.
7. Klicka på det program som du just har skapat och kopiera det globalt unika **klient-ID:t** som du ska använda senare i koden.
8. Klicka på alternativet för **publicerade omfång**. Här definierar du behörigheterna (omfång) som kan beviljas till andra program.
9. Lägg till fler omfång efter behov. Som standard definieras omfånget ”user_impersonation”. Detta ger andra program möjlighet att komma åt det här API:et för den inloggade användarens räkning. Du kan ta bort det om du vill. 
10. Klicka på **Spara**.

## <a name="register-a-mobilenative-application"></a>Registrera ett mobilt/internt program
1. Klicka på **Program** på B2C-funktionsbladet på Azure-portalen.
2. Klicka på **+Lägg till** överst på bladet.
3. Ange ett **namn** för programmet som beskriver det för konsumenterna. Du kan till exempel skriva ”Contoso B2C-app”.
4. Ändra **Include native client** (Ta med intern klient) till **Ja**.
5. Ange en **Omdirigerings-URI** med ett eget schema. Till exempel com.onmicrosoft.contoso.appname://redirect/path. Tänk på att välja en [bra omdirigerings-URI](#choosing-a-redirect-uri).
6. Klicka på **Spara** för att registrera programmet.
7. Klicka på det program som du just har skapat och kopiera det globalt unika **klient-ID:t** som du ska använda senare i koden.
8. Om ditt interna program även ska anropa ett webb-API som skyddas av Azure AD B2C bör du skapa en **programhemlighet** genom att gå till bladet **Nycklar** och klicka på knappen **Generera nyckel**.

> [!NOTE]
> En **programhemlighet** är en viktig autentiseringsuppgift och bör skyddas på lämpligt sätt.
> 

### <a name="choosing-a-redirect-uri"></a>Välja en omdirigerings-URI
Det finns två viktiga överväganden när du väljer en omdirigerings-URI för mobila/interna program: 
* **Unik**: Schemat för omdirigerings-URI måste vara unikt för varje program. I vårt exempel (com.onmicrosoft.contoso.appname://redirect/path) använder vi com.onmicrosoft.contoso.appname som schema. Vi rekommenderar att detta mönster följs. Om två program delar samma schema visas en dialogruta för att "välja app". Inloggningen misslyckas om användaren gör ett felaktigt val. 
* **Fullständig**: Omdirigerings-URI måste ha ett schema och en sökväg. Sökvägen måste innehålla minst ett snedstreck efter domänen (till exempel fungerar //contoso/ medan //contoso misslyckas). 

## <a name="build-a-quick-start-application"></a>Skapa ett snabbstartsprogram
Nu när du har registrerat ett program med Azure AD B2C kan du gå en av våra snabbstartsguider för att komma igång. Här är några rekommendationer:

[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]


