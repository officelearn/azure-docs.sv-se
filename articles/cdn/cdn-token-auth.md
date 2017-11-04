---
title: "Skydda Azure CDN tillgångar med tokenautentisering | Microsoft Docs"
description: "Token autentisering med få säker åtkomst till dina Azure CDN tillgångar."
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: 
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/11/2016
ms.author: mezha
ms.openlocfilehash: b1bcaf14e9805afa82c765092b62201f58c7cbc4
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="securing-azure-content-delivery-network-assets-with-token-authentication"></a>Att säkra Azure Content Delivery Network tillgångar med tokenautentisering

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt

Token-autentisering är en mekanism som gör det möjligt att förhindra att Azure Content Delivery Network (CDN) som betjänar tillgångar obehörig klienter. Tokenautentisering normalt görs för att förhindra ”hotlinking” av innehåll, som använder en annan webbplats, ofta anslagstavla dina tillgångar utan behörighet. Hotlinking kan påverka dina kostnader för leverans av innehåll. Genom att aktivera den här funktionen på CDN verifieras begäranden av CDN edge POP innan CDN levererar innehållet. 

## <a name="how-it-works"></a>Hur det fungerar

Tokenautentisering verifierar begäranden som genereras av en tillförlitlig plats genom att kräva att begäranden som innehåller en token-värde som innehåller kodad information om beställaren. Innehållet skickades till en beställare bara om kodad information uppfyller kraven. annars nekas begäranden. Du kan ställa in kraven med hjälp av en eller flera av följande parametrar:

- Land: Tillåt eller neka förfrågningar som kommer från länder som anges av deras [landskod](https://msdn.microsoft.com/library/mt761717.aspx).
- URL: Tillåt endast förfrågningar som matchar den angivna resursen eller sökväg.
- Värden: Tillåt eller neka förfrågningar som använder de angivna värdarna i huvudet i begäran.
- Referent: Tillåt eller neka begäran från den angivna referent.
- IP-adress: tillåta begäranden som kommer från särskilda IP-adress eller IP-undernät.
- Protokoll: Tillåt eller neka förfrågningar baserat på det protokoll som används för att begära innehållet.
- Förfallotid: tilldela viss datum och tid för att säkerställa att en länk förblir giltig endast för en begränsad tidsperiod.

Mer information finns i de detaljerade konfigurationen för varje parameter i [konfigurerar tokenautentisering](#setting-up-token-authentication).

När du skapar en krypterad token kan du lägga till den som en frågesträng till slutet av URL-sökvägen till filen. Till exempel `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.

## <a name="reference-architecture"></a>Referensarkitektur

Följande arbetsflödesdiagram beskriver hur CDN använder token-autentisering ska fungera med ditt webbprogram.

![CDN-tokenautentisering arbetsflöde](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Token valideringslogik på CDN-slutpunkten
    
I följande flödesschema beskrivs hur Azure CDN verifierar klientens begäran om tokenautentisering är konfigurerat på CDN-slutpunkten.

![CDN-token valideringslogik](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Konfigurera tokenautentisering

1. Från den [Azure-portalen](https://portal.azure.com), bläddra till CDN-profilen och klickar sedan på **hantera** att starta den kompletterande portalen.

    ![CDN-profilen hantera knappen](./media/cdn-rules-engine/cdn-manage-btn.png)

2. Hovra över **HTTP stora**, och klicka sedan på **Token Auth** i utfällda. Du har skapat krypteringsnyckeln och kryptering parametrar i den här fliken.

    1. Ange en unik krypteringsnyckel för **primärnyckel** och ange en annan för **säkerhetskopiering nyckeln**.

        ![Konfigurationsnyckel för CDN-token auth](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. Ställ in kryptering parametrar med verktyget kryptera. Du kan tillåta eller neka förfrågningar baserat på förfallotid, land, referent, protokoll och klientens IP-Adressen (i valfri kombination) med verktyget kryptera.

        ![CDN kryptera verktyget](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

       - ec_expire: en förfallotid tilldelar en token efter vilken token upphör att gälla. Begäranden skickas efter förfallotiden nekas. Den här parametern används en Unix-tidsstämpel som baseras på antalet sekunder sedan standard epok av `1/1/1970 00:00:00 GMT`. (Du kan använda online verktyg för att konvertera mellan standard och Unix tid.) Om du vill ställa in token att gälla på exempelvis `12/31/2016 12:00:00 GMT`, använda tidsstämpelvärde Unix `1483185600`, enligt följande. 
    
         ![CDN ec_expire exempel](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
       - ec_url_allow: du kan anpassa token till en viss tillgång eller sökväg. Den begränsar åtkomsten till begäranden vars URL börjar med en viss relativ sökväg. URL: er är skiftlägeskänsliga. Ange flera sökvägar genom att avgränsa varje sökväg med ett kommatecken. Beroende på dina krav kan ställa du in olika värden för att tillhandahålla olika åtkomstnivå. 
        
          Till exempel för URL: en `http://www.mydomain.com/pictures/city/strasbourg.png`, ställer indatavärdet och dess åtkomstnivå på följande sätt:

           - Ange värde `"/"`: alla begäranden som tillåts
           - Ange värde `"/pictures`. Följande ansökningar om tillåts:
              - `http://www.mydomain.com/pictures.png`
              - `http://www.mydomain.com/pictures/city/strasbourg.png`
              - `http://www.mydomain.com/picturesnew/city/strasbourgh.png`
            - Ange värde `/pictures/`: endast begäranden om `/pictures/` tillåts. Till exempel `http://www.mydomain.com/pictures/city/strasbourg.png`.
            - Ange värde `/pictures/city/strasbourg.png`: endast begäranden för specifika tillgången är tillåtna.
    
          ![CDN ec_url_allow exempel](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
    
       - ec_country_allow: endast tillåta begäranden som kommer från en eller flera angivna länder. Nekas begäranden som kommer från andra länder. Använd landskoder och avgränsa dem med kommatecken. Till exempel om du vill tillåta åtkomst från USA och Frankrike indata oss FR i fältet på följande sätt.  
        
           ![CDN ec_country_allow exempel](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

       - ec_country_deny: nekar förfrågningar som kommer från en eller flera angivna länder. Förfrågningar som kommer från andra länder tillåts. Använd landskoder och avgränsa dem med kommatecken. Till exempel om du vill neka åtkomst från USA och Frankrike indata oss FR i fältet.
    
       - ec_ref_allow: endast tillåta begäranden från den angivna referent. En referent identifierar URL-Adressen till den webbsida som är länkad till den begärda resursen. Inkludera inte protokollet i referent parametervärdet. Följande typer av indata är tillåtna för parametervärde:
           - Ett värdnamn eller ett värdnamn och en sökväg.
           - Flera referenter. Om du vill lägga till flera referenter, Avgränsa varje referent med kommatecken. Om du anger ett värde för referent, men referent information skickas inte i begäran på grund av konfiguration av webbläsaren, nekas de begäranden som standard. 
           - Begäranden med referent information saknas. Ange den text som ”saknas” eller ett tomt värde för att tillåta dessa typer av begäranden. 
           - Underdomäner. Ange en asterisk (*) om du vill tillåta underdomäner. Till exempel för att tillåta alla underdomäner i `consoto.com` ange `*.consoto.com`. 
           
          I följande exempel visas indata för att tillåta åtkomst för begäranden från `www.consoto.com`, alla underordnade domäner under `consoto2.com`, och begäranden med referenter saknas eller är tomt.
        
          ![CDN ec_ref_allow exempel](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
       - ec_ref_deny: nekar förfrågningar från den angivna referent. Genomförandet är samma som parametern ec_ref_allow.
         
       - ec_proto_allow: endast tillåta begäranden från det angivna protokollet. Till exempel HTTP eller HTTPS.
        
         ![CDN ec_proto_allow exempel](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
            
       - ec_proto_deny: nekar förfrågningar från det angivna protokollet. Till exempel HTTP eller HTTPS.
    
       - ec_clientip: begränsar åtkomsten till angivna beställaren IP-adress. Både IPV4 och IPV6 stöds. Du kan ange antingen en enskild begäran IP-adress eller ett IP-undernät.
            
         ![CDN ec_clientip exempel](./media/cdn-token-auth/cdn-token-auth-clientip.png)
        
    3. Du kan testa din token med verktyget beskrivning.

    4. Du kan också anpassa typ av svar som returneras när en begäran nekas. Svarskoden 403 används som standard.

3. Under **HTTP stora**, klickar du på **regelmotor**. Du kan använda regelmotor för att definiera sökvägar för att tillämpa funktionen, aktivera funktionen tokenautentisering och aktivera ytterligare token autentisering-relaterade funktioner.

    1. Använd den **om** kolumnen att definiera tillgång eller sökväg som du vill använda tokenautentisering. 
    2. Välj för att aktivera tokenautentisering **Token Auth** från den **funktioner** listrutan.
        
    ![CDN regler motorn tokenautentisering aktivera exempel](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. Det finns några ytterligare funktioner som du kan aktivera i regler-motorn:
    
    - **Token Auth DOS-kod**: Anger typ av svar som returneras till en användare när en begäran nekas. Regler som anger här åsidosätta DOS-kod i den **Token Auth** fliken.
    - **Token Auth Ignorera URL fallet**: Anger om URL: en som används för att validera token är skiftlägeskänsliga.
    - **Token Auth parametern**: byter namn på token auth frågesträngparametern som visas i den begärda URL: en. 
        
    ![CDN-regler motorn tokenautentisering inställningar exempel](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Du kan anpassa din token, som är ett program som genererar token för tokenbaserad autentiseringsfunktioner. Källkoden kan nås i [GitHub](https://github.com/VerizonDigital/ectoken).
Tillgängliga språk är:
    
    - C
    - C#
    - PHP
    - Perl
    - Java
    - Python    


## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN funktioner och providern priser

Mer information finns i [CDN-översikt](cdn-overview.md).
