---
title: Skydda Azure CDN-resurser med tokenautentisering| Microsoft-dokument
description: Lär dig hur du använder tokenautentisering för att skydda åtkomsten till dina Azure CDN-resurser.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mezha
ms.openlocfilehash: fa71f472294b91baebc2a6075ddb2b50123e545d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593394"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Skydda Azure CDN-resurser med tokenautentisering

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt

Tokenautentisering är en mekanism som gör att du kan förhindra att CDN (Azure Content Delivery Network) betjänar tillgångar till obehöriga klienter. Tokenautentisering görs vanligtvis för att förhindra *hotlinking* av innehåll, där en annan webbplats, till exempel en anslagstavla, använder dina tillgångar utan tillstånd. Hotlinking kan påverka dina leveranskostnader för innehåll. Genom att aktivera tokenautentisering på CDN autentiseras begäranden av CDN-edgeservern innan CDN levererar innehållet. 

## <a name="how-it-works"></a>Hur det fungerar

Tokenautentisering verifierar att begäranden genereras av en betrodd plats genom att begäranden måste innehålla ett tokenvärde som innehåller kodad information om beställaren. Innehållet överförs endast till en beställare om den kodade informationen uppfyller kraven. I annat fall nekas begäranden. Du kan ställa in kraven med hjälp av en eller flera av följande parametrar:

- Land: Tillåt eller neka begäranden som kommer från de länder/regioner som anges i [landskoden](/previous-versions/azure/mt761717(v=azure.100)).
- URL: Tillåt endast begäranden som matchar den angivna tillgången eller sökvägen.
- Värd: Tillåt eller neka begäranden som använder de angivna värdarna i begäranden.
- Referen: Tillåt eller neka begäran från den angivna referenten.
- IP-adress: Tillåt endast begäranden som kommer från specifik IP-adress eller IP-undernät.
- Protokoll: Tillåt eller neka begäranden baserat på det protokoll som används för att begära innehållet.
- Förfallotid: Tilldela ett datum och en tidsperiod för att säkerställa att en länk endast förblir giltig under en begränsad tidsperiod.

Mer information finns i de detaljerade konfigurationsexexemplen för varje parameter i [Konfigurera tokenautentisering](#setting-up-token-authentication).

>[!IMPORTANT] 
> Om tokenauktorisering är aktiverat för en sökväg för det här kontot är standardcacheläget det enda läge som kan användas för frågesträngcachelagring. Mer information finns i [Kontrollera cachelagringsbeteendet med frågesträngar](cdn-query-string-premium.md).

## <a name="reference-architecture"></a>Referensarkitektur

I följande arbetsflödesdiagram beskrivs hur CDN använder tokenautentisering för att arbeta med webbappen.

![Arbetsflöde för autentisering av CDN-token](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Tokenverifieringslogik på CDN-slutpunkten
    
Följande flödesschema beskriver hur Azure CDN validerar en klientbegäran när tokenautentisering konfigureras på CDN-slutpunkten.

![VALIDERINGslogik för CDN-token](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Ställa in tokenautentisering

1. Från [Azure-portalen](https://portal.azure.com)bläddrar du till din CDN-profil och väljer sedan **Hantera** för att starta den kompletterande portalen.

    ![Knappen Hantera CDN-profil](./media/cdn-token-auth/cdn-manage-btn.png)

2. Hovra över **HTTP Large**och välj sedan **Token Auth** i det utfällbara. Du kan sedan ställa in krypteringsnyckeln och krypteringsparametrarna enligt följande:

   1. Skapa en eller flera krypteringsnycklar. En krypteringsnyckel är skiftlägeskänslig och kan innehålla valfri kombination av alfanumeriska tecken. Andra typer av tecken, inklusive blanksteg, är inte tillåtna. Den maximala längden är 250 tecken. För att säkerställa att krypteringsnycklarna är slumpmässiga rekommenderar vi att du skapar dem med hjälp av [OpenSSL-verktyget](https://www.openssl.org/). 

      OpenSSL-verktyget har följande syntax:

      ```rand -hex <key length>```

      Ett exempel:

      ```OpenSSL> rand -hex 32``` 

      För att undvika driftstopp skapar du både en primär och en säkerhetskopieringsnyckel. En säkerhetskopia ger oavbruten åtkomst till ditt innehåll när primärnyckeln uppdateras.
    
   2. Ange en unik krypteringsnyckel i rutan **Primärnyckel** och ange eventuellt en säkerhetskopieringsnyckel i rutan **Säkerhetskopieringsnyckel.**

   3. Välj den lägsta krypteringsversionen för varje nyckel från listan **Lägsta krypteringsversion** och välj sedan **Uppdatera:**
      - **V2**: Anger att nyckeln kan användas för att generera version 2.0- och 3.0-token. Använd det här alternativet endast om du övergår från en äldre version 2.0-krypteringsnyckel till en version 3.0-nyckel.
      - **V3**: (Rekommenderas) Anger att nyckeln endast kan användas för att generera version 3.0-token.

      ![CDN token auth setup key CDN token auth setup key CDN token auth setup key CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Använd krypteringsverktyget för att ställa in krypteringsparametrar och generera en token. Med krypteringsverktyget kan du tillåta eller neka begäranden baserat på förfallotid, land/region, hänvisning, protokoll och klient-IP (i valfri kombination). Även om det inte finns någon gräns för antalet och kombinationen av parametrar som kan kombineras för att bilda en token, är den totala längden på en token begränsad till 512 tecken. 

      ![Cdn-krypteringsverktyg](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Ange värden för en eller flera av följande krypteringsparametrar i avsnittet **Kryptera verktyg:** 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Parameternamn</th> 
      >   <th>Beskrivning</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Tilldelar en förfallotid till en token, varefter token upphör att gälla. Begäranden som skickas efter utgångsdatumet nekas. Den här parametern använder en Unix-tidsstämpel, som baseras på antalet `1/1/1970 00:00:00 GMT`sekunder sedan standardepixepoken för Unix i . (Du kan använda onlineverktyg för att konvertera mellan standardtid och Unix-tid.)> 
      >    Om du till exempel vill att `12/31/2016 12:00:00 GMT`token ska upphöra att gälla `1483185600`vid anger du tidsstämpelvärdet Unix. 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Gör att du kan skräddarsy token till en viss tillgång eller sökväg. Det begränsar åtkomsten till begäranden vars URL börjar med en viss relativ sökväg. Webbadresser är skiftlägeskänsliga. Mata in flera sökvägar genom att avgränsa varje sökväg med ett kommatecken. Lägg inte till blanksteg. Beroende på dina behov kan du ställa in olika värden för att ge olika åtkomstnivåer.> 
      >    För URL:en `http://www.mydomain.com/pictures/city/strasbourg.png`tillåts till exempel dessa begäranden för följande indatavärden: 
      >    <ul>
      >       <li>Indatavärde `/`: Alla begäranden är tillåtna.</li>
      >       <li>Indatavärde `/pictures`tillåts följande begäranden: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Indatavärde `/pictures/`: Endast `/pictures/` begäranden som innehåller sökvägen tillåts. Till exempel `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Indatavärde `/pictures/city/strasbourg.png`: Endast begäranden för den här specifika sökvägen och tillgången tillåts.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Tillåter endast begäranden som kommer från ett eller flera angivna länder/regioner. Begäranden som kommer från alla andra länder/regioner nekas. Använd en landskod med två bokstäver i [ISO 3166](/previous-versions/azure/mt761717(v=azure.100)) för varje land och separera var och en med kommatecken. Lägg inte till ett blanksteg. Om du till exempel vill tillåta åtkomst från endast `US,FR`USA och Frankrike anger du .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Nekar begäranden som kommer från ett eller flera angivna länder/regioner. Begäranden som kommer från alla andra länder/regioner är tillåtna. Implementeringen är densamma som <b>parametern ec_country_allow.</b> Om det finns en landskod i parametrarna <b>ec_country_allow</b> och <b>ec_country_deny</b> har <b>parametern ec_country_allow</b> företräde.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Tillåter endast begäranden från den angivna referenten. En referent identifierar webbadressen till webbsidan som är länkad till den begärda resursen. Ta inte med protokollet i parametervärdet.> 
      >    Följande typer av indata är tillåtna:
      >    <ul>
      >       <li>Ett värdnamn eller ett värdnamn och en sökväg.</li>
      >       <li>Flera värvare. Om du vill lägga till flera referenter separerar du varje referent med ett kommatecken. Lägg inte till ett blanksteg. Om du anger ett hänvisningsvärde, men hänvisningsinformationen inte skickas i begäran på grund av webbläsarkonfigurationen, nekas begäran som standard.</li> 
      >       <li>Begäranden med information om saknad eller tom referer. Som standard blockerar <b>parametern ec_ref_allow</b> dessa typer av begäranden. Om du vill tillåta dessa begäranden anger du antingen texten "saknas" eller anger ett tomt värde (med hjälp av ett avslutande kommatecken).</li> 
      >       <li>Underdomäner. Om du vill tillåta underdomäner\*anger du en asterisk ( ). Om du till exempel vill tillåta `contoso.com`alla `*.contoso.com`underdomäner i anger du .</li>
      >    </ul> 
      >    Om du till exempel vill `www.contoso.com`tillåta åtkomst för `contoso2.com`begäranden från , alla underdomäner under och begäranden med tomma eller saknade referenter anger du `www.contoso.com,*.contoso.com,missing`.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Nekar begäranden från den angivna referenten. Implementeringen är densamma som <b>parametern ec_ref_allow.</b> Om det finns en referent i parametrarna <b>ec_ref_allow</b> och <b>ec_ref_deny</b> har <b>parametern ec_ref_allow</b> företräde.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Tillåter endast begäranden från det angivna protokollet. Giltiga värden `http` `https`är `http,https`, eller .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Nekar begäranden från det angivna protokollet. Implementeringen är densamma som <b>parametern ec_proto_allow.</b> Om det finns ett protokoll i parametrarna <b>ec_proto_allow</b> och <b>ec_proto_deny</b> har <b>parametern ec_proto_allow</b> företräde.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Begränsar åtkomsten till den angivna beställarens IP-adress. Både IPV4 och IPV6 stöds. Du kan ange antingen en enda IP-adress för begäran eller IP-adresser som är associerade med ett visst undernät. Tillåt till `11.22.33.0/22` exempel begäranden från IP-adresser 11.22.32.1 till 11.22.35.254.</td>
      > </tr>
      > </table>

   5. När du har angett värden för krypteringsparameter väljer du en nyckel som ska krypteras (om du har skapat både en primär och en reservnyckel) i listan **Nyckel att kryptera.**
    
   6. Välj en krypteringsversion i listan **Krypteringsversion:** **V2** för version 2 eller **V3** för version 3 (rekommenderas). 

   7. Välj **Kryptera för** att generera token.

      När token har genererats visas den i rutan **Genererad token.** Om du vill använda token lägger du till den som en frågesträng i slutet av filen i URL-sökvägen. Till exempel `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Du kan också testa din token med dekrypteringsverktyget så att du kan visa tokens parametrar. Klistra in tokenvärdet i rutan **Token till Dekryptering.** Välj den krypteringsnyckel som ska användas i listan **Nyckel till dekryptering** och välj sedan **Dekryptera**.

      När token har dekrypterats visas dess parametrar i rutan **Ursprungliga parametrar.**

   9. Du kan också anpassa den typ av svarskod som returneras när en begäran nekas. Välj **Aktiverad**och välj sedan svarskoden i listan **Svarskod.** **Sidhuvudnamn** ställs automatiskt in på **Plats**. Välj **Spara** om du vill implementera den nya svarskoden. För vissa svarskoder måste du också ange url:en för felsidan i rutan **Sidhuvudvärde.** **403-svarskoden** (Forbidden) är markerad som standard. 

3. Under **HTTP Large**väljer du **Regelmotor**. Du använder regelmotorn för att definiera sökvägar för att tillämpa funktionen, aktivera funktionen tokenautentisering och aktivera ytterligare tokenautentiseringsrelaterade funktioner. Mer information finns i [Regelmotorreferens](cdn-rules-engine-reference.md).

   1. Välj en befintlig regel eller skapa en ny regel för att definiera den tillgång eller sökväg som du vill tillämpa tokenautentisering för. 
   2. Om du vill aktivera tokenautentisering på en regel väljer du **[Token Auth](cdn-verizon-premium-rules-engine-reference-features.md#token-auth)** i **listan Funktioner** och väljer sedan **Aktiverad**. Välj **Uppdatera** om du uppdaterar en regel eller **Lägg till** om du skapar en regel.
        
      ![CDN regler motor token autentisering aktivera exempel](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. I regelmotorn kan du också aktivera ytterligare tokenautentiseringsrelaterade funktioner. Om du vill aktivera någon av följande funktioner markerar du den i listan **Funktioner** och väljer sedan **Aktiverad**.
    
   - **[Token Auth Denial Code](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-denial-code)**: Bestämmer vilken typ av svar som returneras till en användare när en begäran nekas. Regler som anges här åsidosätter svarskoden som anges i avsnittet **Anpassad förnekelsehantering** på den tokenbaserade autentiseringssidan.

   - **[Token Auth Ignore URL Case](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-ignore-url-case)**: Avgör om den URL som används för att validera token är skiftlägeskänslig.

   - **[TokenAuth Parameter](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-parameter)**: Byter namn på parametern token auth query string som visas i den begärda URL:en. 
        
     ![Exempel på autentiseringsinställningar för CDN-regler motortoken](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Du kan anpassa din token genom att komma åt källkoden i [GitHub](https://github.com/VerizonDigital/ectoken).
   Tillgängliga språk är:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN-funktioner och leverantörspriser

Information om funktioner finns i [Azure CDN-produktfunktioner](cdn-features.md). Information om priser finns i [priser för Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).
