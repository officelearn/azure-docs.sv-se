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
ms.openlocfilehash: 88956b324a543c5347e16b1278f6b2179a3b9c24
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="securing-azure-content-delivery-network-assets-with-token-authentication"></a>Att säkra Azure Content Delivery Network tillgångar med tokenautentisering

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt

Token-autentisering är en mekanism som gör det möjligt att förhindra att Azure Content Delivery Network (CDN) som betjänar tillgångar obehörig klienter. Tokenautentisering normalt görs för att förhindra ”hotlinking” av innehåll, som använder en annan webbplats, ofta anslagstavla dina tillgångar utan behörighet. Hotlinking kan påverka dina kostnader för leverans av innehåll. Genom att aktivera den här funktionen på CDN verifieras begäranden av CDN edge POP innan CDN levererar innehållet. 

## <a name="how-it-works"></a>Hur det fungerar

Tokenautentisering verifierar begäranden som genereras av en tillförlitlig plats genom att kräva att begäranden som innehåller en token-värde som innehåller kodad information om beställaren. Innehållet skickades till en beställare bara om kodad information uppfyller kraven. annars nekas begäranden. Du kan ställa in kraven med hjälp av en eller flera av följande parametrar:

- Land: Tillåt eller neka förfrågningar som kommer från angivna länder.  [Lista över giltiga landskoder.](https://msdn.microsoft.com/library/mt761717.aspx) 
- URL: Tillåt endast angivna tillgång eller sökvägen att begära.  
- Värden: Tillåt eller neka begäranden med angivna värdar i huvudet i begäran.
- Referent: Tillåt eller neka angivna referent begära.
- IP-adress: endast tillåta begäranden som kommer från särskilda IP-adress eller IP-undernät.
- Protokoll: Tillåt eller blockera förfrågningar baserat på det protokoll som används för att begära innehållet.
- Förfallotid: tilldela viss datum och tid för att säkerställa att en länk endast förblir giltig för en begränsad tidsperiod.

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

    1. Ange en unik krypteringsnyckel för **primärnyckel**.  Ange en annan för **säkerhetskopiering nyckel**

        ![Konfigurationsnyckel för CDN-token auth](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. Ställ in kryptering parametrar med krypteringsverktyget (Tillåt eller neka förfrågningar baserat på förfallotid, land, referent, protokoll, klientens IP-Adressen. Du kan använda valfri kombination.)

        ![CDN kryptera verktyget](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

        - EC-gälla: tilldelar en förfallotid för en token efter en angiven tidsperiod. Begäranden skickas efter förfallotiden nekas. Den här parametern används Unix tidsstämpel (baserat på sekunder sedan standard epok av 1/1/1970 00:00:00 GMT. Du kan använda online verktyg för att tillhandahålla konvertering mellan standard och Unix tid.) Om du vill ställa in token att gälla på exempelvis `12/31/2016 12:00:00 GMT`, använda tidsstämpelvärde Unix `1483185600`, enligt följande.
    
        ![CDN ec_expire exempel](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
        - EC-url-Tillåt: du kan anpassa token till en viss tillgång eller sökväg. Den begränsar åtkomsten till begäranden vars URL börjar med en viss relativ sökväg. Du kan ange flera sökvägar Avgränsa varje sökväg med ett kommatecken. URL: er är skiftlägeskänsliga. Beroende på krav, kan du ställa in olika värden att tillhandahålla olika åtkomstnivå. Nedan visas ett par scenarier:
        
            Om du har en URL: http://www.mydomain.com/pictures/city/strasbourg.png. Se indatavärdet ”” och dess åtkomst nivå därefter

            1. Ange värdet ”/”: alla begäranden som tillåts
            2. Ange värdet ”/ bilder”: alla följande ansökningar om kommer att tillåta
            
                - http://www.mydomain.com/Pictures.PNG
                - http://www.mydomain.com/Pictures/City/Strasbourg.PNG
                - http://www.mydomain.com/picturesnew/City/strasbourgh.PNG
            3. Ange värdet ”/ bilder /”: endast begäran om /pictures/ ska tillåtas
            4. Ange värdet ”/ pictures/city/strasbourg.png”: endast tillåter att begäran för den här tillgången
    
        ![CDN ec_url_allow exempel](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
    
        - EC land tillåter: endast tillåta begäranden som kommer från en eller flera angivna länder. Att kommer nekas förfrågningar som kommer från andra länder. Använd landskoden för att ställa in parametrar och avgränsa varje landskod med kommatecken. Till exempel om du vill tillåta åtkomst från USA och Frankrike indata oss FR i kolumnen som nedan.  
        
        ![CDN ec_country_allow exempel](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

        - EC land neka: nekar förfrågningar som kommer från en eller flera angivna länder. Förfrågningar som kommer från andra länder tillåts. Använd landskoden för att ställa in parametrar och avgränsa varje landskod med kommatecken. Till exempel om du vill neka åtkomst från USA och Frankrike indata oss FR i kolumnen.
    
        - ec_ref_allow: innebär att begäranden från den angivna referent. En referent identifierar URL-Adressen till den webbsida som är länkad till den begärda resursen. Inkludera inte protokollet i referent parametervärdet. Följande typer av indata är tillåtna för parametervärde:
           - Ett värdnamn eller ett värdnamn och en sökväg.
           - Flera referenter. Om du vill lägga till flera referenter, Avgränsa varje referent med kommatecken. Om du anger ett värde för referent, men referent information skickas inte i begäran på grund av konfiguration av webbläsaren, nekas de begäranden som standard. 
           - Begäranden med referent information saknas. Ange den text som ”saknas” eller ett tomt värde för att tillåta dessa typer av begäranden. 
           - Underdomäner. Ange en asterisk (*) om du vill tillåta underdomäner. Till exempel för att tillåta alla underdomäner i `consoto.com` ange `*.consoto.com`. 
           
          I följande exempel visas indata för att tillåta åtkomst för begäranden från `www.consoto.com`, alla underordnade domäner under `consoto2.com`, och begäranden med referenter saknas eller är tomt.
        
          ![CDN ec_ref_allow exempel](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
        - EC ref neka: nekar förfrågningar från angivna referent. Finns information och exempel i ”ec-ref-Tillåt”-parametern.
         
        - EC proto Tillåt: endast tillåta begäranden från angivna protokollet. Till exempel http eller https.
        
        ![CDN ec_proto_allow exempel](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
            
        - EC proto neka: nekar förfrågningar från angivna protokollet. Till exempel http eller https.
    
        - EC clientip: begränsar åtkomsten till angivna beställaren IP-adress. Både IPV4 och IPV6 stöds. Du kan ange enskild begäran IP-adress eller IP-undernät.
            
        ![CDN ec_clientip exempel](./media/cdn-token-auth/cdn-token-auth-clientip.png)
        
    3. Du kan testa din token med verktyget beskrivning.

    4. Du kan också anpassa typ av svar som returneras till användaren när begäran nekas. Som standard använder vi 403.

3. Klicka på **regelmotor** fliken **HTTP stora**. Du använder den här fliken för att definiera sökvägar för att tillämpa funktionen, aktivera funktionen tokenautentisering och aktivera ytterligare token autentisering-relaterade funktioner.

    - Använd kolumnen ”om” om du vill definiera tillgång eller sökväg som du vill använda tokenautentisering. 
    - Klicka på Lägg till ”Token Auth” i funktionen listrutan för att aktivera tokenautentisering.
        
    ![CDN regler motorn tokenautentisering aktivera exempel](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. I den **regelmotor** fliken finns det några ytterligare funktioner som du kan aktivera.
    
    - Token auth DOS-kod: Anger typ av svar som returneras till användaren när en begäran nekas. Regler som ställts in här åsidosätter det DOS-koden på fliken token auth.
    - Ignorera token auth: Anger om URL: en som används för att validera token ska vara skiftlägeskänslig.
    - Token auth-parameter: Byt namn på token auth-frågesträngparametern visar begärd URL. 
        
    ![CDN-regler motorn tokenautentisering inställningar exempel](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Du kan anpassa din token som är ett program som genererar token för tokenbaserad autentiseringsfunktioner. Källkoden kan användas här i [GitHub](https://github.com/VerizonDigital/ectoken).
Tillgängliga språk är:
    
    - C
    - C#
    - PHP
    - Perl
    - Java
    - Python    


## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN funktioner och providern priser

Finns det [CDN-översikt](cdn-overview.md) avsnittet.
