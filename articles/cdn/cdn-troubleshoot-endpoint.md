---
title: Felsöka Azure CDN-slutpunkter som returnerar statuskod 404 | Microsoft Docs
description: Felsöka 404 svarskoder med Azure CDN-slutpunkter.
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
ms.openlocfilehash: 1cffef5bbda475032ee7ff07188ab0d9d52846ea
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33766112"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Felsöka Azure CDN-slutpunkter som returnerar statuskod 404
Den här artikeln kan du felsöka problem med slutpunkter i Azure Content Delivery Network (CDN) som returnerar 404 statuskoder för HTTP-svar.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du även filen en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **Get Support**.

## <a name="symptom"></a>Symtom
Du har skapat en CDN-profil och en slutpunkt, men innehållet verkar inte vara tillgängliga på CDN. Användare som försöker få åtkomst till ditt innehåll via CDN-URL: en får en HTTP 404-statuskod. 

## <a name="cause"></a>Orsak
Det finns flera möjliga orsaker, bland annat:

* Filens ursprung visas inte för CDN.
* Slutpunkten är felkonfigurerad orsakar CDN ska se ut på fel plats.
* Värden tar inte emot värdadressen från CDN.
* Slutpunkten har inte varit sprids i hela CDN.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
> [!IMPORTANT]
> När du har skapat en CDN-slutpunkt blir den omedelbart inte tillgängliga för användning, som det tar tid för registreringen ska spridas via CDN:
> - För **Azure CDN Standard från Microsoft** profiler, spridningen vanligtvis är klar på tio minuter. 
> - För **Azure CDN Standard från Akamai** profiler, spridningen vanligtvis slutförs inom en minut. 
> - För **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** profiler, spridningen vanligtvis har slutförts inom 90 minuter. 
> 
> Om du har slutfört stegen i det här dokumentet och du håller fortfarande på att 404 svar, Överväg att vänta några timmar för att kontrollera igen innan du öppnar ett supportärende.
> 
> 

### <a name="check-the-origin-file"></a>Kontrollera till originalfilen
Kontrollera först att filen till cacheminnet är tillgänglig på den ursprungliga servern och är allmänt tillgänglig på internet. Det snabbaste sättet att göra det är att öppna en webbläsare i en privat eller incognito-session och gå direkt till filen. Skriv eller klistra in Webbadressen i adressfältet och kontrollera att den resulterar i filen som du förväntar dig. Anta att du har en fil i ett Azure Storage-konto, tillgängliga https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Om du kan läsa innehållet i den här filen, över testet.

![Lyckades!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Även om detta är det snabbaste och enklaste sättet att verifiera din fil är offentligt tillgänglig, vissa nätverkskonfigurationer i din organisation gör det visas som är en fil offentligt tillgängliga när det är faktum är bara synliga för användare i nätverket (även om den finns i Azure). För att säkerställa att detta inte är fallet, testa filen med en extern webbläsare, till exempel en mobil enhet som inte är ansluten till din organisations nätverk eller en virtuell dator i Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Kontrollera inställningarna för ursprung
När du har kontrollerat att filen är offentligt tillgänglig på internet, verifiera inställningarna ursprung. I den [Azure Portal](https://portal.azure.com), bläddra till CDN-profilen och välj den slutpunkt som du felsöker. Från den resulterande **Endpoint** väljer ursprung.  

![Slutpunkten sida med ursprung markerat](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Den **ursprung** visas. 

![Sidan ursprung](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Typ av ursprung och värdnamn
Kontrollera att värdena för den **typ av ursprung** och **ursprungsvärdnamn** är korrekta. I det här exemplet https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, hostname-delen av URL: en är *cdndocdemo.blob.core.windows.net*, vilket är korrekt. Eftersom Azure Storage, webbprogram och Molntjänsten ursprung använder listrutan värdet för den **ursprungsvärdnamn** fältet, felaktig stavning är inte ett problem. Om du använder en anpassad ursprung, kontrollera att värdnamnet är rätt stavat.

#### <a name="http-and-https-ports"></a>HTTP och HTTPS-portar
Kontrollera din **HTTP** och **HTTPS-portar**. I de flesta fall 80 och 443 är korrekta och du behöver inga ändringar.  Om den ursprungliga servern lyssnar på en annan port, som dock behöver representeras här. Om du inte vet Visa URL-Adressen för din ursprungsfilen. HTTP och HTTPS-specifikationer använder portarna 80 och 443 som standard. I exempel-URL https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, en port har angetts, så antas standardvärdet 443 och inställningarna är korrekta.  

Däremot URL: en för ursprungsfilen som du tidigare har testat är http:\//www.contoso.com:8080/file.txt. Observera den *: 8080* del i slutet av hostname-segment. Att antalet instruerar webbläsaren att använda port 8080 för att ansluta till webbservern i www.contoso.com, därför måste du ange *8080* i den **HTTP-porten** fältet. Det är viktigt att Observera att dessa portinställningar påverkar endast vilken port slutpunkten används för att hämta information från ursprunget.

> [!NOTE]
> **Azure CDN Standard från Akamai** slutpunkter inte tillåter fullständig TCP-portintervallet för ursprung.  En lista över ursprungsportar som inte tillåts finns i [Azure CDN från Akamai-tillåtna ursprungsportar](https://msdn.microsoft.com/library/mt757337.aspx).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Kontrollera inställningarna för slutpunkten
På den **Endpoint** väljer den **konfigurera** knappen.

![Slutpunkten sida med Konfigurera knappen markerad](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

CDN-slutpunkten **konfigurera** visas.

![Konfigurera](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokoll
För **protokoll**, kontrollera att det protokoll som används av klienterna har valts. Eftersom samma protokoll som används av klienten är den som används för att komma åt ursprung, är det viktigt att ha ursprung portar som konfigurerats på rätt sätt i föregående avsnitt. CDN-slutpunkten lyssnar bara på HTTP och HTTPS standardportarna (80 och 443), oavsett ursprung-portar.

Nu ska vi gå tillbaka till vår hypotetiska exempel med http:\//www.contoso.com:8080/file.txt.  Som du kommer ihåg Contoso angetts *8080* sina HTTP-port, men vi förutsätter också de angivna *44300* som deras HTTPS-port.  Om de har skapat en slutpunkt med namnet *contoso*, deras CDN-slutpunktens värdnamn skulle vara *contoso.azureedge.net*.  En begäran om http:\//contoso.azureedge.net/file.txt är en HTTP-begäran så slutpunkten använder HTTP på port 8080 vill hämta från ursprunget.  En säker begäran via HTTPS, https: \/ /contoso.azureedge.net/file.txt, skulle orsaka slutpunkten att använda HTTPS på port 44300 när hämta filen från ursprunget.

#### <a name="origin-host-header"></a>Ursprungsvärdadress
Den **ursprungsvärdadress** är värden huvudets värde skickas till ursprunget med varje begäran.  I de flesta fall bör detta vara samma som den **ursprungsvärdnamn** vi verifierat tidigare.  Ett felaktigt värde i det här fältet normalt orsakar 404 status, men kan medföra andra 4xx statusar, beroende på vad som förväntas ursprung.

#### <a name="origin-path"></a>Sökväg till ursprung
Till sist ska vi ska verifiera vår **ursprungssökväg**.  Detta är tomt som standard.  Du bör bara använda det här fältet om du vill begränsa omfånget för de ursprung finns resurser som du vill ska vara tillgängliga på CDN.  

I exempel-slutpunkt som vi vill att alla resurser på lagringskontot för att vara tillgänglig så **ursprungssökväg** var tomt.  Detta innebär att en begäran till https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt resulterar i en anslutning från slutpunkten till cdndocdemo.core.windows.net som begär */publicblob/lorem.txt*.  På samma sätt kan en begäran för https:\//cdndocdemo.azureedge.net/donotcache/status.png resulterar i slutpunkten begär */donotcache/status.png* från ursprunget.

Men vad händer om du inte vill använda CDN för varje sökväg på din ursprung?  Att du bara vill exponera den *publicblob* sökväg.  Om vi anger */publicblob* i den **ursprungssökväg** fält som gör att infoga slutpunkten */publicblob* innan alla begäranden som görs till ursprunget.  Detta innebär att begäran för https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt nu faktiskt tar den begärt som del av URL-Adressen */publicblob/lorem.txt*, och Lägg till */publicblob* till början. Detta resulterar i en begäran om */publicblob/publicblob/lorem.txt* från ursprunget.  Om sökvägen inte matcha en verklig fil, returnerar ursprung status 404.  Rätt URL för att hämta lorem.txt i det här exemplet skulle kunna vara https:\//cdndocdemo.azureedge.net/lorem.txt.  Observera att vi inte innehåller den */publicblob* sökväg, eftersom begäran-delen i Webbadressen är */lorem.txt* och lägger till slutpunkten */publicblob*, vilket resulterar i */publicblob/lorem.txt* begäran skickas till ursprunget.

