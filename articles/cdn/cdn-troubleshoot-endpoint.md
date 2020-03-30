---
title: Felsöka Azure CDN-slutpunkter – 404 statuskod
description: Felsöka 404 svarskoder med Azure CDN-slutpunkter.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c332c6712cdf057491e3039854aa1a29bd54196f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083115"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Felsöka Azure CDN-slutpunkter som returnerar en 404-statuskod
Med den här artikeln kan du felsöka problem med CDN-slutpunkter (Azure Content Delivery Network) som returnerar 404 HTTP-svarsstatuskoder.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Alternativt kan du också arkivera en Azure-supportincident. Gå till [Azure Support-webbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.

## <a name="symptom"></a>Symptom
Du har skapat en CDN-profil och en slutpunkt, men innehållet verkar inte vara tillgängligt på CDN. Användare som försöker komma åt ditt innehåll via CDN-URL:en får en HTTP 404-statuskod. 

## <a name="cause"></a>Orsak
Det finns flera möjliga orsaker, inklusive:

* Filens ursprung är inte synligt för CDN.
* Slutpunkten är felkonfigurerad, vilket gör att CDN att titta på fel ställe.
* Värden avvisar värdhuvudet från CDN.
* Slutpunkten har inte hunnit spridas i hela CDN.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
> [!IMPORTANT]
> När du har skapat en CDN-slutpunkt är den inte omedelbart tillgänglig för användning, eftersom det tar tid för registreringen att spridas via CDN:
> - För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom tio minuter. 
> - För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
> - För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 90 minuter. 
> 
> Om du slutför stegen i det här dokumentet och fortfarande får 404 svar kan du vänta några timmar för att kontrollera igen innan du öppnar en supportbiljett.
> 
> 

### <a name="check-the-origin-file"></a>Kontrollera ursprungsfilen
Kontrollera först att filen som ska cachelagras på ursprungsservern och är allmänt tillgänglig på internet. Det snabbaste sättet att göra det är att öppna en webbläsare i en privat eller inkognitosession och bläddra direkt till filen. Skriv eller klistra in URL:en i adressrutan och kontrollera att den resulterar i den fil du förväntar dig. Anta till exempel att du har en fil i\/ett Azure Storage-konto, tillgänglig på https: /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Om du kan läsa in filens innehåll klarar den testet.

![Lyckades!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Även om detta är det snabbaste och enklaste sättet att verifiera att filen är allmänt tillgänglig, kan vissa nätverkskonfigurationer i organisationen få det att se ut som om en fil är allmänt tillgänglig när den i själva verket bara är synlig för användare av nätverket (även om den finns i Azure). För att säkerställa att så inte är fallet testar du filen med en extern webbläsare, till exempel en mobil enhet som inte är ansluten till organisationens nätverk eller en virtuell dator i Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Kontrollera ursprungsinställningarna
När du har verifierat att filen är allmänt tillgänglig på internet kontrollerar du dina ursprungsinställningar. I [Azure Portal](https://portal.azure.com)bläddrar du till din CDN-profil och väljer den slutpunkt som du felsöker. Välj ursprunget på den resulterande **slutpunktssidan.**  

![Slutpunktssida med ursprung markerat](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Sidan **Ursprung** visas. 

![Sidan Ursprung](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Ursprungstyp och värdnamn
Kontrollera att värdena för **origintypen** och **Origin-värdnamnet** är korrekta. I det här\/exemplet https: /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt är värdnamnsdelen av URL:en *cdndocdemo.blob.core.windows.net*, vilket är korrekt. Eftersom azure storage-, webbapp- och molntjänstursprung använder ett listvärde för **origin-värdnamnsfältet** är felaktiga stavningar inte ett problem. Om du använder ett anpassat ursprung kontrollerar du dock att värdnamnet är rättstavat.

#### <a name="http-and-https-ports"></a>HTTP- och HTTPS-portar
Kontrollera **HTTP-** och **HTTPS-portarna**. I de flesta fall är 80 och 443 korrekta, och du kommer inte att kräva några ändringar.  Men om ursprungsservern lyssnar på en annan port måste den representeras här. Om du är osäker kan du visa webbadressen för ursprungsfilen. HTTP- och HTTPS-specifikationerna använder portarna 80 och 443 som standard. I exempel-URL:en, https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, anges inte en port, så standardvärdet 443 antas och inställningarna är korrekta.  

Anta dock att URL:en för ursprungsfilen som\/du testade tidigare är http: /www.contoso.com:8080/file.txt. Observera *:8080-delen* i slutet av värdnamnssegmentet. Det numret instruerar webbläsaren att använda port 8080 för\.att ansluta till webbservern på www contoso.com, därför måste du ange *8080* i **HTTP-portfältet.** Det är viktigt att notera att dessa portinställningar bara påverkar vilken port slutpunkten använder för att hämta information från ursprunget.

> [!NOTE]
> **Azure CDN Standard från Akamai-slutpunkter** tillåter inte hela TCP-portintervallet för ursprung.  En lista över ursprungsportar som inte tillåts finns i [Azure CDN från Akamai-tillåtna ursprungsportar](/previous-versions/azure/mt757337(v=azure.100)).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Kontrollera slutpunktsinställningarna
Välj knappen **Konfigurera** på sidan **Slutpunkt.**

![Slutpunktssida med konfigurera knappen markerad](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Sidan Konfigurera **CDN-slutpunkt** visas.

![Konfigurera sida](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokoll
För **protokoll**kontrollerar du att protokollet som används av klienterna har valts. Eftersom samma protokoll som används av klienten är det som används för att komma åt ursprunget, är det viktigt att ha ursprungsportarna korrekt konfigurerade i föregående avsnitt. CDN-slutpunkten lyssnar bara på standard-HTTP- och HTTPS-portarna (80 och 443), oavsett ursprungsportar.

Låt oss återgå till vårt hypotetiska exempel med http:\//www.contoso.com:8080/file.txt.  Som du kommer ihåg angav Contoso *8080* som sin HTTP-port, men låt oss också anta att de angav *44300* som sin HTTPS-port.  Om de skapade en slutpunkt med namnet *contoso*skulle deras CDN-slutpunktsvärdnamn *vara contoso.azureedge.net*.  En begäran om\/http: /contoso.azureedge.net/file.txt är en HTTP-begäran, så slutpunkten skulle använda HTTP på port 8080 för att hämta den från ursprunget.  En säker begäran via\/HTTPS, https: /contoso.azureedge.net/file.txt, skulle orsaka att slutpunkten använder HTTPS på port 44300 när filen hämtas från ursprunget.

#### <a name="origin-host-header"></a>Ursprungsvärdadress
**Ursprungsvärdhuvudet** är värdhuvudvärdet som skickas till ursprunget för varje begäran.  I de flesta fall bör detta vara samma som **Origin-värdnamnet** som vi verifierade tidigare.  Ett felaktigt värde i det här fältet orsakar i allmänhet inte 404 status, men kommer sannolikt att orsaka andra 4xx-statusar, beroende på vad ursprunget förväntar sig.

#### <a name="origin-path"></a>Sökväg till ursprung
Slutligen bör vi kontrollera vår **Origin väg**.  Som standard är detta tomt.  Du bör bara använda det här fältet om du vill begränsa omfattningen av de ursprungsbaserade resurser som du vill göra tillgängliga på CDN.  

I exempelslutpunkten ville vi att alla resurser på lagringskontot skulle vara tillgängliga, så **Origin-sökvägen** lämnades tom.  Det innebär att en\/begäran till https: /cdndocdemo.azureedge.net/publicblob/lorem.txt resulterar i en anslutning från slutpunkten till cdndocdemo.core.windows.net som begär */publicblob/lorem.txt*.  På samma sätt resulterar en begäran om https:\//cdndocdemo.azureedge.net/donotcache/status.png i slutpunkten som begär */donotcache/status.png* från ursprunget.

Men vad händer om du inte vill använda CDN för varje väg på ditt ursprung?  Säg att du bara ville avslöja *publicblob-vägen.*  Om vi anger */publicblob* i fältet **Ursprungssökväg,** kommer det att leda till att slutpunkten infogas */publicblob* innan varje begäran görs till ursprunget.  Det innebär att begäran\/om https: /cdndocdemo.azureedge.net/publicblob/lorem.txt nu faktiskt tar begäran delen av WEBBADRESSEN, */publicblob/lorem.txt*och lägger till */publicblob* till början. Detta resulterar i en begäran om */publicblob/publicblob/lorem.txt* från ursprunget.  Om sökvägen inte går att lösa till en faktisk fil returneras en 404-status.  Rätt URL för att hämta lorem.txt i\/det här exemplet skulle faktiskt vara https: /cdndocdemo.azureedge.net/lorem.txt.  Observera att vi inte inkluderar *sökvägen /publicblob* alls, eftersom begäran delen av webbadressen är */lorem.txt* och slutpunkten lägger till */publicblob*, vilket resulterar i */publicblob/lorem.txt* är begäran som skickas till ursprunget.

