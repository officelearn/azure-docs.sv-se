---
title: Skydda Azure CDN-resurser med tokenautentisering | Microsoft Docs
description: Lär dig hur du använder autentisering med enhetstoken för att säkra åtkomst till dina Azure CDN-tillgångar.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mezha
ms.openlocfilehash: 75d6fb063a6cb5336a4d9945bf6a79a65ed25d40
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58918900"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Skydda Azure CDN-resurser med tokenautentisering

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt

Tokenautentisering är en mekanism som gör det möjligt att förhindra att Azure Content Delivery Network (CDN) som tillgångar till obehöriga klienter. Autentisering med enhetstoken normalt görs för att förhindra *hotlinking* av innehåll, där en annan webbplats, till exempel en anslagstavla använder dina tillgångar utan tillstånd. Hotlinking kan påverka dina kostnader för leverans av innehåll. Genom att aktivera autentisering med enhetstoken på CDN autentiseras begäranden med CDN edge-servern innan CDN levererar innehåll. 

## <a name="how-it-works"></a>Hur det fungerar

Tokenautentisering verifierar att begäranden genereras av en betrodd plats genom att kräva att begäranden som innehåller en token-värde som innehåller kodad information om den som begär. Innehållet visas till en beställare endast om kodad information uppfyller kraven. i annat fall nekas begäranden. Du kan ställa in kraven med hjälp av en eller flera av följande parametrar:

- Land/region: Tillåt eller neka förfrågningar som kommer från de länder/regioner som anges av deras [landskod](/previous-versions/azure/mt761717(v=azure.100)).
- URL: Tillåt endast begäranden som matchar den angivna resursen eller sökvägen.
- Värd: Tillåt eller neka förfrågningar som använder de angivna värdarna i rubriken.
- Referent: Tillåt eller neka begäran från den angivna referent.
- IP-adress: Tillåt endast begäranden som kommer från specifika IP-adress eller IP-undernät.
- Protokoll: Tillåt eller neka förfrågningar baserat på det protokoll som används för att begära innehållet.
- Förfallotid: Tilldela en datum- och tidsperioden för att säkerställa att en länk förblir giltig endast för en begränsad tidsperiod.

Mer information finns i exemplen detaljerad konfiguration för varje parameter i [hur du konfigurerar autentisering med enhetstoken](#setting-up-token-authentication).

>[!IMPORTANT] 
> Om token auktorisering har aktiverats för valfri sökväg på det här kontot, är standard-cache-läge det enda läge som kan användas för cachelagring av frågesträngar i fråga. Mer information finns i [Kontrollera cachelagringsbeteendet med frågesträngar](cdn-query-string-premium.md).

## <a name="reference-architecture"></a>Referensarkitektur

Följande arbetsflödesdiagram visar hur CDN använder autentisering med enhetstoken för att arbeta med din webbapp.

![CDN-tokenautentisering arbetsflöde](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Token validering av logik för CDN-slutpunkt
    
I följande flödesschema beskriver hur Azure CDN verifierar en klientbegäran om autentisering med enhetstoken är konfigurerat på CDN-slutpunkten.

![Token validering av logik för CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Konfigurera autentisering med enhetstoken

1. Från den [Azure-portalen](https://portal.azure.com), bläddra till din CDN-profil och markera **hantera** att starta den kompletterande portalen.

    ![Knappen för CDN-profil hantera](./media/cdn-token-auth/cdn-manage-btn.png)

2. Hovra över **HTTP stora**och välj sedan **Token Auth** i den utfällbar meny. Du kan ställa in krypteringsnyckeln och kryptering parametrar på följande sätt:

   1. Skapa en eller flera krypteringsnycklar. En krypteringsnyckel är skiftlägeskänsligt och kan innehålla vilken kombination av alfanumeriska tecken. Alla andra typer av tecken, inklusive blanksteg, tillåts inte. Den maximala längden är 250 tecken. Att säkerställa att krypteringsnycklarna är slumpmässig, vi rekommenderar att du skapar dem med hjälp av den [OpenSSL verktyget](https://www.openssl.org/). 

      OpenSSL-verktyget har följande syntax:

      ```rand -hex <key length>```

      Exempel:

      ```OpenSSL> rand -hex 32``` 

      Skapa både en primär och en reservnyckel för att undvika driftstopp. En reservnyckel ger oavbruten åtkomst till ditt innehåll när den primära nyckeln uppdateras.
    
   2. Ange en unik krypteringsnyckel i den **primärnyckel** rutan och du kan också ange en reservnyckel i den **Säkerhetskopieringsnyckel** box.

   3. Väljer du den minsta kryptering versionen för varje nyckel från dess **kryptering minimiversion** och sedan välja **uppdatering**:
      - **V2**: Anger att nyckeln kan användas för att generera version 2.0 och 3.0-token. Använd det här alternativet endast om du övergår från en äldre version 2.0 krypteringsnyckeln till en version 3.0-nyckel.
      - **V3**: (Rekommenderas) Anger att nyckeln endast kan användas för att generera token för version 3.0.

      ![Konfigurationsnyckel för CDN-token auth](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Verktyget encrypt används för att ställa in parametrar för kryptering och generera en token. Du kan använda verktyget encrypt, för att tillåta eller neka förfrågningar baserat på förfallotid, land, referent, protokoll och IP-adress för klient (i valfri kombination). Även om det finns ingen gräns för antalet och kombination av parametrar som kan kombineras för att skapa en token, är den totala längden på en token begränsat till 512 tecken. 

      ![CDN kryptera verktyget](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Ange värden för en eller flera av följande parametrar för kryptering i den **kryptera verktyget** avsnittet: 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Parameternamn</th> 
      >   <th>Beskrivning</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Tilldelar en förfallotid till en token, varefter token upphör att gälla. Begäranden skickas efter förfallotiden nekas. Den här parametern använder en Unix-tidsstämpel som är baserat på antal sekunder sedan den standard Unix epok av `1/1/1970 00:00:00 GMT`. (Du kan använda onlineverktyg för att konvertera mellan standard och Unix-tiden.)> 
      >    Exempel: Om du vill att denna token till går ut vid `12/31/2016 12:00:00 GMT`, ange värdet för Unix-tidsstämpel `1483185600`. 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Kan du skräddarsy token till en tillgångens eller sökvägen. Det begränsar åtkomsten till begäranden vars URL börjar med en viss relativ sökväg. URL: er är skiftlägeskänsliga. Ange flera sökvägar genom att avgränsa varje sökväg med semikolon; Lägg inte till blanksteg. Beroende på dina krav kan du ställa in olika värden att ge olika åtkomstnivåer.> 
      >    Till exempel för URL: en `http://www.mydomain.com/pictures/city/strasbourg.png`, dessa begäranden tillåts för indatavärdena som följande: 
      >    <ul>
      >       <li>Indatavärde `/`: Alla begäranden som tillåts.</li>
      >       <li>Indatavärde `/pictures`, följande begäranden tillåts: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Indatavärde `/pictures/`: Endast begäranden som innehåller den `/pictures/` sökväg är tillåtna. Till exempel `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Indatavärde `/pictures/city/strasbourg.png`: Endast begäranden för den här specifika sökvägen och tillgången är tillåtna.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Tillåter endast begäranden som kommer från en eller flera angivna länder. Begäranden som kommer från andra länder nekas. Använd två bokstäver [3166 ISO-landskod](/previous-versions/azure/mt761717(v=azure.100)) för varje land/region och avgränsar du dem med kommatecken, inte till ett blanksteg. Om du vill tillåta åtkomst från endast USA och Frankrike, till exempel anger `US,FR`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Nekar förfrågningar som kommer från en eller flera angivna länder. Begäranden som kommer från andra länder är tillåtna. Implementeringen är samma som den <b>ec_country_allow</b> parametern. Om en landskod finns i både den <b>ec_country_allow</b> och <b>ec_country_deny</b> parametrar, den <b>ec_country_allow</b> parametern företräde.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Tillåter endast begäranden från den angivna referent. En referent identifierar URL: en för den webbsida som är länkad till den resurs som begärts. Inkludera inte protokollet i parametervärdet.> 
      >    Följande typer av indata tillåts:
      >    <ul>
      >       <li>Ett värdnamn eller ett värdnamn och en sökväg.</li>
      >       <li>Flera referenter. Om du vill lägga till flera referenter, Avgränsa varje referent med semikolon; Lägg inte till ett blanksteg. Om du anger ett värde för referent, men referent informationen skickas inte i begäran på grund av konfiguration av webbläsaren, nekas begäran som standard.</li> 
      >       <li>Begäranden med saknas eller är tomt referent-information. Som standard den <b>ec_ref_allow</b> parametern blockerar dessa typer av begäranden. Ange antingen texten, ”saknas” eller ett tomt värde (med hjälp av kommatecken avslutande) för att tillåta dessa begäranden.</li> 
      >       <li>Subdomains. Om du vill tillåta underdomäner, anger du en asterisk (\*). Till exempel vill tillåta alla underdomäner i `contoso.com`, ange `*.contoso.com`.</li>
      >    </ul> 
      >    Till exempel för att tillåta åtkomst för begäranden från `www.contoso.com`, alla underordnade domäner under `contoso2.com`, och begäranden med tom eller saknas referenser, ange `www.contoso.com,*.contoso.com,missing`.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Nekar förfrågningar från den angivna referent. Implementeringen är samma som den <b>ec_ref_allow</b> parametern. Om en referent finns i både den <b>ec_ref_allow</b> och <b>ec_ref_deny</b> parametrar, den <b>ec_ref_allow</b> parametern företräde.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Tillåter endast begäranden från det angivna protokollet. Giltiga värden är `http`, `https`, eller `http,https`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Nekar förfrågningar från det angivna protokollet. Implementeringen är samma som den <b>ec_proto_allow</b> parametern. Om ett protokoll finns i både den <b>ec_proto_allow</b> och <b>ec_proto_deny</b> parametrar, den <b>ec_proto_allow</b> parametern företräde.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Begränsar åtkomsten till den angivna som begär IP-adress. Både IPV4 och IPV6 stöds. Du kan ange antingen en enskild begäran IP-adress eller IP-adresser som är associerade med ett specifikt undernät. Till exempel `11.22.33.0/22` tillåter att förfrågningar från IP-adresser 11.22.32.1 11.22.35.254.</td>
      > </tr>
      > </table>

   5. När du är klar med att ange parametervärden för kryptering, väljer du en nyckel för att kryptera (om du har skapat både en primär och en reservnyckel) från den **nyckel att kryptera** lista.
    
   6. Välj en krypteringsversion från den **Krypteringsversion** lista: **V2** för version 2 eller **V3** för version 3 (rekommenderas). 

   7. Välj **Encrypt** att generera token.

      När token har skapats visas den i den **genereras Token** box. För att använda token, lägger du till den som en frågesträng i slutet av filen i URL-sökväg. Till exempel `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Du kan testa din token med verktyget dekryptera så att du kan visa din token parametrar. Klistra in token-värde i den **Token för att dekryptera** box. Välj krypteringsnyckeln ska användas från den **nyckel att dekryptera** och sedan välja **dekryptera**.

      När token dekryptera, dess parametrar visas i den **ursprungliga parametrar** box.

   9. Du kan också anpassa typ av svarskod som returneras när en begäran nekas. Välj **aktiverad**, välj sedan svarskoden från den **svarskod** lista. **Rubriknamn** ställs automatiskt in **plats**. Välj **spara** att implementera nya svarskoden. För vissa svarskoder, måste du också ange Webbadressen till felsidan i den **huvudvärde** box. Den **403** Svarskod (förbjudet) väljs som standard. 

3. Under **HTTP stora**väljer **regelmotor**. Du kan använda regelmotorn för att definiera sökvägar för att tillämpa funktionen, aktivera funktionen tokenautentisering och aktivera ytterligare token authentication-relaterade funktioner. Mer information finns i [regelmotor – referens](cdn-rules-engine-reference.md).

   1. Välj en befintlig regel eller skapa en ny regel för att definiera tillgång eller sökväg som du vill använda autentisering med enhetstoken. 
   2. Aktivera autentisering med enhetstoken på en regel för att välja **[Token Auth](cdn-rules-engine-reference-features.md#token-auth)** från den **funktioner** och sedan välja **aktiverad**. Välj **uppdatering** om du uppdaterar en regel eller **Lägg till** om du skapar en regel.
        
      ![CDN regelmotor – tokenautentisering aktivera exempel](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. Du kan också aktivera ytterligare token authentication-relaterade funktioner i regler-motorn. Om du vill aktivera någon av följande funktioner, välja den från den **funktioner** och sedan välja **aktiverad**.
    
   - **[Token Auth Denial kod](cdn-rules-engine-reference-features.md#token-auth-denial-code)**: Anger typ av svar som returneras till en användare när en begäran nekas. Regler som anges här åsidosätter svarskoden som angetts i den **anpassad datorn för hantering av** på sidan för tokenbaserad autentisering.

   - **[Token Auth Ignorera skiftläge för URL: en](cdn-rules-engine-reference-features.md#token-auth-ignore-url-case)**: Anger om URL: en som används för att validera token är skiftlägeskänsliga.

   - **[Token Auth parametern](cdn-rules-engine-reference-features.md#token-auth-parameter)**: Byter namn på token auth frågesträngparametern som visas i den begärda URL: en. 
        
     ![CDN regelmotor – exempel på tokenautentisering inställningar](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Du kan anpassa din token genom att öppna källkoden i [GitHub](https://github.com/VerizonDigital/ectoken).
   Tillgängliga språk är:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN-funktioner och priser-providern

Information om funktionerna finns i [Azure CDN produktfunktioner](cdn-features.md). Information om priser finns i [prissättningen för CDN](https://azure.microsoft.com/pricing/details/cdn/).
