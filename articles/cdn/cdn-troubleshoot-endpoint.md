---
title: Felsöka Azure CDN-slutpunkter som returnerar 404-statuskod | Microsoft Docs
description: Felsök 404-svarskoder med Azure CDN-slutpunkter.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 66ee211856bb451caad7af02103aa306d76e8f97
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916282"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Felsöka Azure CDN-slutpunkter som returnerar 404-statuskod
Den här artikeln kan du felsöka problem med Azure Content Delivery Network (CDN)-slutpunkter som returnerar 404 statuskoder för HTTP-svar.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [Azure MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Du kan alternativt kan du även skicka en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **få Support**.

## <a name="symptom"></a>Symtom
Du har skapat en CDN-profil och en slutpunkt, men innehållet verkar inte finnas på CDN. Användare som försöker komma åt ditt innehåll via CDN-URL får en HTTP 404-statuskod. 

## <a name="cause"></a>Orsak
Det finns flera tänkbara orsaker, bland annat:

* Filens ursprung är inte synliga för CDN.
* Slutpunkten är felkonfigurerad orsakar CDN för att titta på fel plats.
* Värden avvisar värdhuvudet från CDN.
* Slutpunkten har inte haft tid att spridas i CDN.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
> [!IMPORTANT]
> När du har skapat en CDN-slutpunkt, blir den inte omedelbart tillgänglig för användning, eftersom det tar tid för registreringen att spridas i CDN:
> - För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom tio minuter. 
> - För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
> - För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 90 minuter. 
> 
> Överväg att vänta några timmar för att kontrollera igen innan du öppnar ett supportärende om du har slutfört stegen i det här dokumentet och du fortfarande får 404-svar.
> 
> 

### <a name="check-the-origin-file"></a>Kontrollera den ursprungliga filen
Kontrollera först att filen till cache är tillgängligt på den ursprungliga servern och är offentligt tillgänglig på internet. Det snabbaste sättet att göra det är att öppna en webbläsare i en privat eller inkognito sessions- och gå direkt till filen. Skriv eller klistra in Webbadressen i adressfältet och kontrollera att den leder till den fil som du förväntar dig. Anta exempelvis att du har en fil i ett Azure Storage-konto, tillgänglig på https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Om du kan läsa innehållet i den här filen, över testet.

![Lyckades!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Även om det är det snabbaste och enklaste sättet att verifiera din fil är allmänt tillgängliga, vissa nätverkskonfigurationer i din organisation kan göra det visas som är en fil allmänt tillgängliga när det är faktiskt, endast synliga för användare i nätverket (även om den finns i Azure). För att säkerställa att detta inte är fallet, testa filen med en extern webbläsare, till exempel en mobil enhet som inte är ansluten till din organisations nätverk eller en virtuell dator i Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Kontrollera inställningarna för ursprung
När du har kontrollerat att filen är offentligt tillgänglig på internet, verifiera inställningarna ursprung. I den [Azure-portalen](https://portal.azure.com)bläddrar du till din CDN-profil och väljer du slutpunkten som du felsöker. Från den resulterande **Endpoint** väljer ursprunget.  

![Slutpunkt-sida med ursprung markerat](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Den **ursprung** visas. 

![Ursprung sidan](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Typ av ursprung och värdnamn
Kontrollera att värdena för den **ursprungstyp** och **ursprungsvärdnamn** är korrekta. I det här exemplet https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, hostname-delen av URL: en är *cdndocdemo.blob.core.windows.net*, vilket är korrekt. Eftersom Azure Storage, Web App och Molntjänsten ursprung använder listrutan värdet för den **ursprungsvärdnamn** fältet, felaktig stavning är inte ett problem. Om du använder ett anpassat ursprung, kontrollera att din värdnamn är korrekt.

#### <a name="http-and-https-ports"></a>HTTP och HTTPS-portar
Kontrollera din **HTTP** och **HTTPS-portar**. I de flesta fall 80 och 443 är korrekta och behöver du inga ändringar.  Om den ursprungliga servern lyssnar på en annan port, som dock behöver visas här. Om du inte är säker kan du visa URL-Adressen för den ursprungliga filen. HTTP och HTTPS-specifikationer använder portarna 80 och 443 som standard. I exempel-URL, https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, en port inte anges så antas standardvärdet 443 och inställningarna är korrekta.  

Däremot URL: en för den ursprungliga filen som du tidigare har testat är http:\//www.contoso.com:8080/file.txt. Obs den *: 8080* del i slutet av hostname-segment. Att antalet instruerar webbläsaren att använda port 8080 för att ansluta till webbservern i www\.contoso.com, därför måste du ange *8080* i den **HTTP-porten** fält. Det är viktigt att notera att de här portinställningarna påverkar bara vilken port slutpunkten använder för att hämta information från ursprunget.

> [!NOTE]
> **Azure CDN Standard från Akamai** slutpunkter inte tillåter fullständig TCP-portintervallet för ursprung.  En lista över ursprungsportar som inte tillåts finns i [Azure CDN från Akamai-tillåtna ursprungsportar](/previous-versions/azure/mt757337(v=azure.100)).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Kontrollera slutpunktsinställningarna för
På den **Endpoint** väljer den **konfigurera** knappen.

![Slutpunkt-sida med Konfigurera knappen markerad](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

CDN-slutpunkten **konfigurera** visas.

![Konfigurera sidan](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokoll
För **protokoll**, kontrollera att det protokoll som används av klienterna har valts. Eftersom samma protokoll som används av klienten är den som används för att få åtkomst till ursprunget, är det viktigt att ha ursprungsportar som har konfigurerats korrekt i föregående avsnitt. CDN-slutpunkten lyssnar endast på HTTP och HTTPS standardportarna (80 och 443), oavsett ursprungsportar.

Nu ska vi gå tillbaka till vår hypotetiska exempel med http:\//www.contoso.com:8080/file.txt.  Eftersom du kommer ihåg Contoso anges *8080* som deras HTTP-port, men vi antar också de anges *44300* som deras HTTPS-port.  Om de har skapat en slutpunkt med namnet *contoso*, deras CDN-slutpunktens värdnamn är *contoso.azureedge.net*.  En begäran om http:\//contoso.azureedge.net/file.txt är en HTTP-förfrågan slutpunkten då använda HTTP på port 8080 för att hämta det från ursprunget.  En säker begäran via HTTPS, https: \/ /contoso.azureedge.net/file.txt, skulle orsaka slutpunkten så att den använder HTTPS på port 44300 för att hämta filen från ursprunget.

#### <a name="origin-host-header"></a>Ursprungsvärdadress
Den **ursprungsvärdadress** är det värdhuvudsvärde som skickas till ursprunget med varje begäran.  I de flesta fall bör detta vara samma som den **ursprungsvärdnamn** vi verifierat tidigare.  Ett felaktigt värde i det här fältet Allmänt orsaka inte 404-status, men kan orsaka andra 4xx statusar, beroende på vad som förväntas ursprunget.

#### <a name="origin-path"></a>Sökväg till ursprung
Till sist ska vi verifiera vår **sökväg till ursprung**.  Detta är tomt som standard.  Du bör bara använda det här fältet om du vill begränsa omfånget för de ursprung som värd resurser som du vill göra tillgängliga i CDN.  

I exempel-slutpunkt som vi ville ha alla resurser för lagringskontot ska vara tillgängligt, så **sökväg till ursprung** har tomt.  Detta innebär att en begäran till https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt resulterar i en anslutning från slutpunkt till cdndocdemo.core.windows.net som begär */publicblob/lorem.txt*.  På samma sätt kan en begäran för https:\//cdndocdemo.azureedge.net/donotcache/status.png som leder till att den slutpunkt som begärande */donotcache/status.png* från ursprunget.

Men vad händer om du inte vill använda CDN för varje sökväg på din ursprungsserver?  Anta att du endast vill exponera den *publicblob* sökväg.  Om vi anger */publicblob* i den **originalsökväg** fält som leder till att infoga slutpunkten */publicblob* före varje begäran som görs till ursprunget.  Detta innebär att begäran för https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt nu faktiskt tar begäran-delen av URL: en, */publicblob/lorem.txt*, och lägger till */publicblob* i början. Detta resulterar i en begäran om */publicblob/publicblob/lorem.txt* från ursprunget.  Om sökvägen inte matchas till en själva filen, returnerar ursprunget ett 404-status.  Rätt URL att hämta lorem.txt i det här exemplet blir faktiskt https:\//cdndocdemo.azureedge.net/lorem.txt.  Observera att vi inte inkluderar den */publicblob* sökväg, eftersom begäran-delen av URL: en är */lorem.txt* och lägger till slutpunkten */publicblob*, vilket resulterar i */publicblob/lorem.txt* begäran skickas till ursprunget.

