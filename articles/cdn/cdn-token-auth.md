---
title: "Skydda Azure CDN tillgångar med tokenautentisering | Microsoft Docs"
description: "Lär dig hur du använder token-autentisering för att skydda åtkomst till Azure CDN-tillgångar."
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
ms.date: 11/03/2017
ms.author: mezha
ms.openlocfilehash: 700f4c49bbcda1eccbcc7eafc703e625697fa2b4
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="securing-azure-content-delivery-network-assets-with-token-authentication"></a>Att säkra Azure Content Delivery Network tillgångar med tokenautentisering

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt

Token-autentisering är en mekanism som gör det möjligt att förhindra att Azure Content Delivery Network (CDN) som betjänar tillgångar obehörig klienter. Tokenautentisering normalt görs för att förhindra ”hotlinking” av innehåll, som använder en annan webbplats, ofta anslagstavla dina tillgångar utan behörighet. Hotlinking kan påverka dina kostnader för leverans av innehåll. Genom att aktivera tokenautentisering på CDN verifieras begäranden av CDN edge POP innan CDN levererar innehållet. 

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

2. Hovra över **HTTP stora**, och klicka sedan på **Token Auth** i utfällda. Du kan ställa in krypteringsnyckeln och kryptering parametrar på följande sätt:

    1. Ange en unik krypteringsnyckel i den **primärnyckel** rutan och även ange en reservnyckel i den **Reservnyckel** rutan.

        ![Konfigurationsnyckel för CDN-token auth](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. Ställ in kryptering parametrar med verktyget kryptera. Du kan tillåta eller neka förfrågningar baserat på förfallotid, land, referent, protokoll och klientens IP-Adressen (i valfri kombination) med verktyget kryptera. 

        ![CDN kryptera verktyget](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

       Ange värden för en eller flera av följande parametrar för kryptering i den **kryptera verktyget** område:  

       - **ec_expire**: tilldelar en förfallotid till en token efter vilken token upphör att gälla. Begäranden skickas efter förfallotiden nekas. Den här parametern används en Unix-tidsstämpel som baseras på antalet sekunder sedan standard epok av `1/1/1970 00:00:00 GMT`. (Du kan använda online verktyg för att konvertera mellan standard och Unix tid.) Om du vill att token ut vid exempelvis `12/31/2016 12:00:00 GMT`, använda tidsstämpelvärde Unix `1483185600`, enligt följande. 
    
         ![CDN ec_expire exempel](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
       - **ec_url_allow**: du kan anpassa token till en viss tillgång eller sökväg. Den begränsar åtkomsten till begäranden vars URL börjar med en viss relativ sökväg. URL: er är skiftlägeskänsliga. Ange flera sökvägar genom att avgränsa varje sökväg med ett kommatecken. Beroende på dina krav kan ställa du in olika värden för att tillhandahålla olika åtkomstnivå. 
        
         Till exempel för URL: en `http://www.mydomain.com/pictures/city/strasbourg.png`, dessa begäranden som tillåts för följande indatavärdena:

         - Ange värde `/`: alla begäranden som tillåts.
         - Ange värde `/pictures`, följande ansökningar om tillåts:
            - `http://www.mydomain.com/pictures.png`
            - `http://www.mydomain.com/pictures/city/strasbourg.png`
            - `http://www.mydomain.com/picturesnew/city/strasbourgh.png`
         - Ange värde `/pictures/`: endast begäranden som innehåller den `/pictures/` sökväg är tillåtna. Till exempel `http://www.mydomain.com/pictures/city/strasbourg.png`.
         - Ange värde `/pictures/city/strasbourg.png`: endast begäranden för den här specifika sökvägen och tillgången är tillåtna.
    
       - **ec_country_allow**: endast tillåta begäranden som kommer från en eller flera angivna länder. Nekas begäranden som kommer från andra länder. Använd landskoder och avgränsa dem med kommatecken. Till exempel om du vill tillåta åtkomst från USA och Frankrike indata oss FR i rutan på följande sätt.  
        
           ![CDN ec_country_allow exempel](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

       - **ec_country_deny**: nekar förfrågningar som kommer från en eller flera angivna länder. Förfrågningar som kommer från andra länder tillåts. Använd landskoder och avgränsa dem med kommatecken. Till exempel om du vill neka åtkomst från USA och Frankrike indata oss FR i rutan.
    
       - **ec_ref_allow**: endast tillåta begäranden från den angivna referent. En referent identifierar URL-Adressen till den webbsida som är länkad till den begärda resursen. Inkludera inte protokollet i referent parametervärdet. Följande typer av indata är tillåtna för parametervärde:
           - Ett värdnamn eller ett värdnamn och en sökväg.
           - Flera referenter. Om du vill lägga till flera referenter, Avgränsa varje referent med kommatecken. Om du anger ett värde för referent, men referent information skickas inte i begäran på grund av konfiguration av webbläsaren, nekas de begäranden som standard. 
           - Begäranden med referent information saknas. Ange den text som ”saknas” eller ett tomt värde för att tillåta dessa typer av begäranden. 
           - Underdomäner. Om du vill tillåta underdomäner, anger du en asterisk (\*). Till exempel för att tillåta alla underdomäner i `consoto.com`, ange `*.consoto.com`. 
           
          I följande exempel visas indata för att tillåta åtkomst för begäranden från `www.consoto.com`, alla underordnade domäner under `consoto2.com`, och begäranden med referenter saknas eller är tomt.
        
          ![CDN ec_ref_allow exempel](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
       - **ec_ref_deny**: nekar förfrågningar från den angivna referent. Genomförandet är samma som parametern ec_ref_allow.
         
       - **ec_proto_allow**: endast tillåta begäranden från det angivna protokollet. Till exempel HTTP eller HTTPS.
            
       - **ec_proto_deny**: nekar förfrågningar från det angivna protokollet. Till exempel HTTP eller HTTPS.
    
       - **ec_clientip**: begränsar åtkomsten till angivna beställaren IP-adress. Både IPV4 och IPV6 stöds. Du kan ange antingen en enskild begäran IP-adress eller ett IP-undernät.
            
         ![CDN ec_clientip exempel](./media/cdn-token-auth/cdn-token-auth-clientip.png)

    3. När du är klar med att ange parametervärden för kryptering, Välj typ av nyckel för kryptering (om du har skapat både en primär och en reservnyckel) från den **nyckel för att kryptera** lista, en version av kryptering från den  **Kryptering** listan och klicka sedan på **kryptera**.
        
    4. Du kan testa din token med verktyget dekryptering. Klistra in token-värde i den **Token för att dekryptera** rutan. Välj den typ av krypteringsnyckeln för avkryptering från den **nyckel att dekryptera** nedrullningsbara listan och klicka sedan på **dekryptera**.

    5. Du kan också anpassa typ av svarskod som returneras när en begäran nekas. Välj koden från den **svarskoden** listrutan och klicka på **spara**. Den **403** svarskoden (förbjuden) väljs som standard. För vissa svarskoder, du kan också ange Webbadressen till felsidan i den **huvudvärde** rutan. 

    6. När du har genererat en krypterad token kan du lägga till den som en frågesträng till slutet av filen i URL-sökväg. Till exempel `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.

3. Under **HTTP stora**, klickar du på **regelmotor**. Du kan använda regelmotor för att definiera sökvägar för att tillämpa funktionen, aktivera funktionen tokenautentisering och aktivera ytterligare token autentisering-relaterade funktioner. Mer information finns i [regler motorn referens](cdn-rules-engine-reference.md).

    1. Välj en befintlig regel eller skapa en ny regel för att definiera tillgång eller sökväg som du vill använda tokenautentisering. 
    2. Välj för att aktivera token autentisering på en regel  **[Token Auth](cdn-rules-engine-reference-features.md#token-auth)**  från den **funktioner** nedrullningsbara listan och välj sedan **aktiverad**. Klicka på **uppdatering** om du uppdaterar en regel eller **Lägg till** om du skapar en regel.
        
    ![CDN regler motorn tokenautentisering aktivera exempel](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. Du kan också aktivera ytterligare token autentisering-relaterade funktioner i regler-motorn. Om du vill aktivera någon av följande funktioner, välja den från den **funktioner** nedrullningsbara listan och välj sedan **aktiverad**.
    
    - **[Token Auth DOS-kod](cdn-rules-engine-reference-features.md#token-auth-denial-code)**: Anger typ av svar som returneras till en användare när en begäran nekas. Anger här åsidosätter svarskoden i den **anpassad DOS-hantering** avsnitt på sidan tokenbaserad autentisering.
    - **[Token Auth Ignorera URL fallet](cdn-rules-engine-reference-features.md#token-auth-ignore-url-case)**: Anger om URL: en som används för att validera token är skiftlägeskänsliga.
    - **[Token Auth parametern](cdn-rules-engine-reference-features.md#token-auth-parameter)**: byter namn på token auth frågesträngparametern som visas i den begärda URL: en. 
        
    ![CDN-regler motorn tokenautentisering inställningar exempel](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Du kan anpassa din token genom att öppna källkoden i [GitHub](https://github.com/VerizonDigital/ectoken).
Tillgängliga språk är:
    
- C
- C#
- PHP
- Perl
- Java
- Python    

## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN funktioner och providern priser

Mer information finns i [CDN-översikt](cdn-overview.md).
