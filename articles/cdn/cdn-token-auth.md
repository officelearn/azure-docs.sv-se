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
ms.date: 11/17/2017
ms.author: mezha
ms.openlocfilehash: f6d008a92677d28d0184e64637dcb2e093299519
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2017
---
# <a name="securing-azure-content-delivery-network-assets-with-token-authentication"></a>Att säkra Azure Content Delivery Network tillgångar med tokenautentisering

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt

Token-autentisering är en mekanism som gör det möjligt att förhindra att Azure Content Delivery Network (CDN) som betjänar tillgångar obehörig klienter. Tokenautentisering normalt görs för att förhindra ”hotlinking” av innehåll, som använder en annan webbplats, till exempel en anslagstavla dina tillgångar utan behörighet. Hotlinking kan påverka dina kostnader för leverans av innehåll. Genom att aktivera tokenautentisering på CDN verifieras begäranden av CDN gränsservern innan CDN levererar innehållet. 

## <a name="how-it-works"></a>Hur det fungerar

Tokenautentisering verifierar att begäranden som genereras av en betrodd plats genom att kräva att begäranden som innehåller en token-värde som innehåller kodad information om beställaren. Innehållet skickades till en beställare bara om kodad information uppfyller kraven. annars nekas begäranden. Du kan ställa in kraven med hjälp av en eller flera av följande parametrar:

- Land: Tillåt eller neka förfrågningar som kommer från länder som anges av deras [landskod](https://msdn.microsoft.com/library/mt761717.aspx).
- URL: Tillåt endast förfrågningar som matchar den angivna resursen eller sökväg.
- Värden: Tillåt eller neka förfrågningar som använder de angivna värdarna i huvudet i begäran.
- Referent: Tillåt eller neka begäran från den angivna referent.
- IP-adress: tillåta begäranden som kommer från särskilda IP-adress eller IP-undernät.
- Protokoll: Tillåt eller neka förfrågningar baserat på det protokoll som används för att begära innehållet.
- Förfallotid: tilldela viss datum och tid för att säkerställa att en länk förblir giltig endast för en begränsad tidsperiod.

Mer information finns i de detaljerade konfigurationen för varje parameter i [konfigurerar tokenautentisering](#setting-up-token-authentication).

## <a name="reference-architecture"></a>Referensarkitektur

Följande arbetsflödesdiagram beskriver hur CDN använder token-autentisering ska fungera med ditt webbprogram.

![CDN-tokenautentisering arbetsflöde](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Token valideringslogik på CDN-slutpunkten
    
I följande flödesschema beskrivs hur Azure CDN verifierar en klientbegäran om tokenautentisering är konfigurerat på CDN-slutpunkten.

![CDN-token valideringslogik](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Konfigurera tokenautentisering

1. Från den [Azure-portalen](https://portal.azure.com), bläddra till CDN-profilen och klickar sedan på **hantera** att starta den kompletterande portalen.

    ![CDN-profilen hantera knappen](./media/cdn-token-auth/cdn-manage-btn.png)

2. Hovra över **HTTP stora**, klicka på **Token Auth** i utfällda. Du kan ställa in krypteringsnyckeln och kryptering parametrar på följande sätt:

    1. Skapa en eller flera krypteringsnycklar. En krypteringsnyckel är skiftlägeskänsligt och kan innehålla vilken kombination av alfanumeriska tecken. Andra typer av tecken, inklusive blanksteg tillåts inte. Den maximala längden är 250 tecken. Att säkerställa att krypteringsnycklarna är slumpmässig, bör du skapa dem med hjälp av den [OpenSSL verktyget](https://www.openssl.org/). 

       Verktyget OpenSSL har följande syntax:

       ```rand -hex <key length>```

       Exempel:

       ```OpenSSL> rand -hex 32``` 

       Skapa både en primär och en reservnyckel för att undvika driftsavbrott. En reservnyckel ger oavbruten tillgång till ditt innehåll när primärnyckel uppdateras.
    
    2. Ange en unik krypteringsnyckel i den **primärnyckel** rutan och även ange en reservnyckel i den **Reservnyckel** rutan.

    3. Välj den minsta kryptering versionen för varje nyckel från dess **kryptering minimiversionen** listan och klicka sedan på **uppdatering**:
       - **V2**: Anger att nyckeln kan användas för att generera version 2.0 och 3.0-token. Använd det här alternativet om du övergång från en äldre version 2.0 krypteringsnyckeln till en nyckel för version 3.0.
       - **V3**: (rekommenderas) anger att nyckeln endast kan användas för att generera token för version 3.0.

    ![Konfigurationsnyckel för CDN-token auth](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    4. Verktyget kryptera används för att ställa in parametrar för kryptering och generera en token. Du kan tillåta eller neka förfrågningar baserat på förfallotid, land, referent, protokoll och klientens IP-Adressen (i valfri kombination) med verktyget kryptera. Även om det finns ingen gräns för antalet och kombination av parametrar som kan kombineras för att skapa en token, är den totala längden för en token högst 512 tecken. 

       ![CDN kryptera verktyget](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

       Ange värden för en eller flera av följande parametrar för kryptering i den **kryptera verktyget** avsnitt: 

       > [!div class="mx-tdCol2BreakAll"] 
       > <table>
       > <tr>
       >   <th>Parameternamn</th> 
       >   <th>Beskrivning</th>
       > </tr>
       > <tr>
       >    <td><b>ec_expire</b></td>
       >    <td>Tilldelar en förfallotid till en token efter vilken token upphör att gälla. Begäranden skickas efter förfallotiden nekas. Den här parametern används en Unix-tidsstämpel som baseras på antalet sekunder sedan standard Unix-epok av `1/1/1970 00:00:00 GMT`. (Du kan använda online verktyg för att konvertera mellan standard och Unix tid.)> 
       >    Om du vill att token ut vid exempelvis `12/31/2016 12:00:00 GMT`, ange tidsstämpelvärde Unix `1483185600`. 
       > </tr>
       > <tr>
       >    <td><b>ec_url_allow</b></td> 
       >    <td>Kan du skräddarsy token till en viss tillgång eller sökväg. Den begränsar åtkomsten till begäranden vars URL börjar med en viss relativ sökväg. URL: er är skiftlägeskänsliga. Ange flera sökvägar genom att avgränsa varje sökväg med ett kommatecken; Lägg inte till blanksteg. Beroende på dina krav kan ställa du in olika värden för att tillhandahålla olika åtkomstnivå.> 
       >    Till exempel för URL: en `http://www.mydomain.com/pictures/city/strasbourg.png`, dessa begäranden som tillåts för följande indatavärdena: 
       >    <ul>
       >       <li>Ange värde `/`: alla begäranden som tillåts.</li>
       >       <li>Ange värde `/pictures`, följande ansökningar om tillåts: <ul>
       >          <li>`http://www.mydomain.com/pictures.png`</li>
       >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
       >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
       >       </ul></li>
       >       <li>Ange värde `/pictures/`: endast begäranden som innehåller den `/pictures/` sökväg är tillåtna. Till exempel `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
       >       <li>Ange värde `/pictures/city/strasbourg.png`: endast begäranden för den här specifika sökvägen och tillgången är tillåtna.</li>
       >    </ul>
       > </tr>
       > <tr>
       >    <td><b>ec_country_allow</b></td> 
       >    <td>Endast tillåta begäranden som kommer från en eller flera angivna länder. Nekas begäranden som kommer från andra länder. Använda två bokstäver [ISO 3166 landskod](https://msdn.microsoft.com/library/mt761717.aspx) för varje land och avgränsa dem med kommatecken, inte till ett blanksteg. Om du vill tillåta åtkomst från USA och Frankrike t.ex `US,FR`.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_country_deny</b></td> 
       >    <td>Nekar förfrågningar som kommer från en eller flera angivna länder. Förfrågningar som kommer från andra länder tillåts. Genomförandet är samma som den <b>ec_country_allow</b> parameter. Om en landskod finns i både den <b>ec_country_allow</b> och <b>ec_country_deny</b> parametrar, den <b>ec_country_allow</b> parametern företräde.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_ref_allow</b></td>
       >    <td>Tillåta begäranden endast från den angivna referent. En referent identifierar URL-Adressen till den webbsida som är länkad till den begärda resursen. Inkludera inte protokollet i parametervärdet.>    
       >    Följande typer av indata tillåts:
       >    <ul>
       >       <li>Ett värdnamn eller ett värdnamn och en sökväg.</li>
       >       <li>Flera referenter. Om du vill lägga till flera referenter, Avgränsa varje referent med kommatecken; Lägg inte till ett blanksteg. Om du anger ett värde för referent, men referent information skickas inte i begäran på grund av konfiguration av webbläsaren, nekas begäran som standard.</li> 
       >       <li>Begäranden med information som saknas eller är tomt referent. Som standard den <b>ec_ref_allow</b> parametern blockerar dessa typer av begäranden. Ange antingen den text som ”saknas” eller ett tomt värde (med hjälp av en efterföljande komma) för att tillåta dessa förfrågningar.</li> 
       >       <li>Underdomäner. Om du vill tillåta underdomäner, anger du en asterisk (\*). Till exempel för att tillåta alla underdomäner i `contoso.com`, ange `*.contoso.com`.</li>
       >    </ul>     
       >    Till exempel för att tillåta åtkomst för begäranden från `www.contoso.com`, alla underordnade domäner under `contoso2.com`, och ange begäranden med saknas eller är tomt referenter `www.contoso.com,*.contoso.com,missing`.</td>
       > </tr>
       > <tr> 
       >    <td><b>ec_ref_deny</b></td>
       >    <td>Nekar förfrågningar från den angivna referent. Genomförandet är samma som den <b>ec_ref_allow</b> parameter. Om en referent finns i både den <b>ec_ref_allow</b> och <b>ec_ref_deny</b> parametrar, den <b>ec_ref_allow</b> parametern företräde.</td>
       > </tr>
       > <tr> 
       >    <td><b>ec_proto_allow</b></td> 
       >    <td>Endast tillåta begäranden från det angivna protokollet. Giltiga värden är `http`, `https`, eller `http,https`.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_proto_deny</b></td>
       >    <td>Nekar förfrågningar från det angivna protokollet. Genomförandet är samma som den <b>ec_proto_allow</b> parameter. Om ett protokoll finns i både den <b>ec_proto_allow</b> och <b>ec_proto_deny</b> parametrar, den <b>ec_proto_allow</b> parametern företräde.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_clientip</b></td>
       >    <td>Begränsar åtkomsten till angivna beställaren IP-adress. Både IPV4 och IPV6 stöds. Du kan ange antingen en enskild begäran IP-adress eller IP-adresser som är associerade med ett specifikt undernät. Till exempel `11.22.33.0/22` tillåta begäranden från IP-adresser 11.22.32.1 11.22.35.254.</td>
       > </tr>
       > </table>

    5. När du är klar med att ange parametervärden för kryptering markerar du en nyckel för att kryptera (om du har skapat både en primär och en reservnyckel) från den **nyckel för att kryptera** lista.
    
    6. Välj en kryptering version från den **kryptering** lista: **V2** för version 2 eller **V3** för version 3 (rekommenderas). 

    7. Klicka på **kryptera** att generera token.

    När token som har genererats visas den i den **genereras Token** rutan. Om du vill använda token, lägger du till dem som en frågesträng till slutet av filen i URL-sökväg. Till exempel `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
    8. Du kan testa din token med verktyget dekryptera så att du kan visa din token-parametrar. Klistra in token-värde i den **Token för att dekryptera** rutan. Välj krypteringsnyckeln ska användas från den **nyckel att dekryptera** listan och klicka sedan på **dekryptera**.

    När token dekrypteras dess parametrar visas i den **ursprungliga parametrarna** rutan.

    9. Du kan också anpassa typ av svarskod som returneras när en begäran nekas. Välj **aktiverad**, Välj svarskod från det **svarskoden** lista. **Huvudets namn** automatiskt **plats**. Klicka på **spara** att implementera nya svarskoden. För vissa svarskoder, måste du också ange Webbadressen till felsidan i den **huvudvärde** rutan. Den **403** svarskoden (förbjuden) väljs som standard. 

3. Under **HTTP stora**, klickar du på **regelmotor**. Du kan använda regelmotor för att definiera sökvägar för att tillämpa funktionen, aktivera funktionen tokenautentisering och aktivera ytterligare token autentisering-relaterade funktioner. Mer information finns i [regler motorn referens](cdn-rules-engine-reference.md).

    1. Välj en befintlig regel eller skapa en ny regel för att definiera tillgång eller sökväg som du vill använda tokenautentisering. 
    2. Välj för att aktivera token autentisering på en regel  **[Token Auth](cdn-rules-engine-reference-features.md#token-auth)**  från den **funktioner** lista och sedan välja **aktiverad**. Klicka på **uppdatering** om du uppdaterar en regel eller **Lägg till** om du skapar en regel.
        
    ![CDN regler motorn tokenautentisering aktivera exempel](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. Du kan också aktivera ytterligare token autentisering-relaterade funktioner i regler-motorn. Om du vill aktivera någon av följande funktioner, välja den från den **funktioner** lista och sedan välja **aktiverad**.
    
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

Mer information om funktionerna finns [CDN-översikt](cdn-overview.md). Information om priser finns i [innehållsleveransnätverk priser](https://azure.microsoft.com/pricing/details/cdn/).
