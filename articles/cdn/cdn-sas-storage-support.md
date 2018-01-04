---
title: "Med hjälp av Azure CDN med SAS | Microsoft Docs"
description: 
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: rli v-deasim
ms.openlocfilehash: de30f4319be75362131f8c8ad71aad57b0528f05
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="using-azure-cdn-with-sas"></a>Med SAS Azure CDN

När du hantera innehåll från lagringsbehållaren för ditt lagringskonto, kanske du vill skydda hur användare kan komma åt dina filer genom att tilldela privat åtkomst till lagringsbehållaren för. Annars kan en lagringsbehållare som offentlig åtkomst har beviljats nås av alla som känner till Webbadressen. Du kan använda funktionen delade signatur åtkomst (SAS) från Azure storage för att bevilja begränsad åtkomst till privata behållare för att skydda ett lagringskonto som du har tillåtit innehållsleveransnätverk (CDN) att komma åt.

En SAS är en URI som ger begränsad behörighet till Azure Storage-resurser utan att utsätta din kontonyckel. Du kan ange en SAS för klienter som du inte litar med din åtkomstnyckel för lagring men som du vill delegera åtkomst till vissa konto lagringsresurser. Genom att distribuera en signatur för delad åtkomst URI för dessa klienter ger du dem åtkomst till en resurs för en angiven tidsperiod.
 
SAS kan du definiera olika parametrar för åtkomst till en blobb som start-och upphör att gälla, behörigheter (läsa/skriva) och IP-adressintervall. Den här artikeln beskriver hur du använder SAS tillsammans med Azure CDN. Läs mer om SAS, inklusive hur du skapar den och dess parameteralternativ [använder signaturer för delad åtkomst (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Ställa in Azure CDN för att arbeta med SAS-lagring
Följande tre alternativ rekommenderas för att använda SAS med Azure CDN. Alla alternativ förutsätter att du redan har skapat en fungerande SAS (se krav). 
 
### <a name="prerequisites"></a>Förutsättningar
Starta, skapa ett lagringskonto och generera en SAS för din tillgång. Du kan skapa två typer av signaturer lagrade åtkomst: tjänst-SAS eller en konto-SAS. Mer information finns i [typer av signaturer för delad åtkomst](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

När du har genererat en SAS kan du komma åt blob storage-fil med en URL som har följande format:`https://<account>.blob.core.windows.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
Exempel:
 ```
https://democdnstorage1.blob.core.windows.net/container1/sasblob.txt?sv=2017-04-17&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Mer information om inställningen parametrar finns [SAS parametern överväganden](#sas-parameter-considerations) och [parametrar för delad åtkomst signatur](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters).

![CDN SAS-inställningar](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-the-cdn"></a>Alternativ 1: Med hjälp av SAS med direkt till blob storage från CDN

Det här alternativet är den enklaste och använder endast en enda SAS-token som skickas från Innehållsleverantörsnätverket till den ursprungliga servern. Det stöds av **Azure CDN från Verizon**, för både Standard- och Premium-profiler och **Azure CDN från Akamai**. 
 
1. Välj en slutpunkt, klicka på **cachelagring regler**och välj **cachelagra varje unik URL** från den **cachelagring av frågesträngar i frågan** lista.

    ![CDN cachelagring regler](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. När du har skapat SAS på ditt lagringskonto använda SAS-token med CDN-URL för att komma åt filen. 
   
   Den resulterande URL har följande format:`https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`

   Exempel:   
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg/?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Finjustera cachelagringens varaktighet med cachelagring regler eller genom att lägga till `Cache-Control` huvuden vid ursprung. Eftersom CDN behandlar SAS-token som en vanlig frågesträng, som bästa praxis bör du ställa in en cachelagringens varaktighet som upphör att gälla på eller före utgångstiden SAS. I annat fall om en fil cachelagras under en längre tid än SAS är aktiv, kan filen vara tillgänglig från ursprungsservern CDN SAS förfallotid har förflutit. Om detta inträffar och du vill isolera cachelagrade filen, måste du utföra en Rensa åtgärden på filen för att ta bort den från cacheminnet. Information om hur du anger cachelagringens varaktighet på CDN finns [kontroll Azure Content Delivery Network cachelagring av frågesträngar med cachelagring regler](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-security-token-using-rewrite-rule"></a>Alternativ 2: Dolda CDN säkerhetstoken med omarbetning regel
 
Du kan skydda ursprung blob-lagring utan en SAS-token för CDN-användare med det här alternativet. Du kanske vill använda det här alternativet om du inte behöver specifika åtkomstbegränsningar för filen, men vill hindra användare från att komma åt lagringsutrymme ursprung direkt för att förbättra CDN avlastning gånger. Det här alternativet är bara tillgängligt för **Azure CDN Premium från Verizon** profiler. 
 
1. Använd den [regelmotor](cdn-rules-engine.md) att skapa en regel för URL-omskrivning om. Nya regler tar ungefär 90 minuter för att sprida.

   ![CDN hantera knappen](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN regler motorn knappen](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Det här exemplet URL-omskrivning om regeln har följande mönster:
   
   Källa:   
   `/test/demo.jpg`
   
   Mål:   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![URL-CDN omskrivning om regeln](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
 
2. Åtkomst till filen på din CDN utan SAS-token i följande format:`https://<endpoint>.azureedge.net/<folder>/<file>`
 
   Exempel:   
   `https://demoendpoint.azureedge.net/test/demo.jpg`
       
   Observera att vem som helst, oavsett om de använder en SAS-token har åtkomst till en CDN-slutpunkt. 

3. Finjustera cachelagringens varaktighet med cachelagring regler eller genom att lägga till `Cache-Control` huvuden vid ursprung. Eftersom CDN behandlar SAS-token som en vanlig frågesträng, som bästa praxis bör du ställa in en cachelagringens varaktighet som upphör att gälla på eller före utgångstiden SAS. I annat fall om en fil cachelagras under en längre tid än SAS är aktiv, kan filen vara tillgänglig från ursprungsservern CDN SAS förfallotid har förflutit. Om detta inträffar och du vill isolera cachelagrade filen, måste du utföra en Rensa åtgärden på filen för att ta bort den från cacheminnet. Information om hur du anger cachelagringens varaktighet på CDN finns [kontroll Azure Content Delivery Network cachelagring av frågesträngar med cachelagring regler](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Alternativ 3: Genom att använda CDN säkerhet token med en regel för omarbetning

Det här alternativet är den mest säkra och anpassningsbara. Om du vill använda tokenautentisering för CDN säkerhet, måste du ha en **Azure CDN Premium från Verizon** profil. Klientåtkomst baseras på säkerhetsparametrar på CDN-säkerhetstoken. Dock om SAS blir ogiltigt kommer inte CDN att kunna verifiera innehållet från den ursprungliga servern.

1. [Skapa en CDN-säkerhetstoken](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) och aktivera den med hjälp av motorn för regler för CDN-slutpunkten och sökvägen där användarna kan komma åt filen.

   En SAS-URL har följande format:   
   `https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
   Exempel:   
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Parameteralternativ för CDN säkerhet tokenautentisering skiljer sig från parameteralternativ för en SAS-token. Om du väljer att använda en förfallotid när du skapar en CDN-säkerhetstoken ange samma värde som förfallotiden för SAS-token. På så sätt att förfallotiden är förutsägbar. 
 
2. Använd den [regelmotor](cdn-rules-engine.md) att skapa en URL-omskrivning om regel om du vill aktivera token åtkomst till alla blobbar i behållaren. Nya regler tar ungefär 90 minuter för att sprida.

   Det här exemplet URL-omskrivning om regeln har följande mönster:
   
   Källa:   
   `/test/demo.jpg`
   
   Mål:   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![URL-CDN omskrivning om regeln](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)

3. När du förnyar SAS uppdatera Url-omskrivning om regeln för att använda den nya SAS-token. 

## <a name="sas-parameter-considerations"></a>Överväganden för SAS-parameter

Eftersom SAS parametrarna inte är synlig för CDN kan inte CDN ändra dess leveransbeteende utifrån dem. Definierade parametern begränsningar gäller bara för förfrågningar som CDN gör till den ursprungliga servern, inte för förfrågningar från klienten till CDN. Denna skillnad är viktigt att tänka på när du ställer in SAS-parametrar. Om dessa avancerade funktioner som krävs och du använder [alternativ 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), ange lämpliga begränsningar för CDN-säkerhetstoken.

| Parameternamnet SAS | Beskrivning |
| --- | --- |
| Start | Den tid som CDN kan börja få åtkomst till blob-fil. På grund av klockan skeva (när klockan en signal anländer vid olika tidpunkter för olika komponenter), Välj en gång tidigare 15 minuter om du vill att tillgången ska vara tillgängliga omedelbart. |
| Slut | Tid sedan CDN kan inte längre komma åt blob-fil. Tidigare är cachelagrade filer på CDN fortfarande tillgängliga. För att styra förfallotiden fil, ange lämpliga förfallotiden för CDN-säkerhetstoken eller rensa tillgången. |
| Tillåtna IP-adresser | Valfri. Om du använder **Azure CDN från Verizon**, du kan konfigurera den här parametern med de intervall som definieras i [Azure CDN från Verizon Edge Server IP-adressintervall](https://msdn.microsoft.com/library/mt757330.aspx). Om du använder **Azure CDN från Akamai**, du kan inte ange parametern IP-adressintervall eftersom IP-adresser inte är statisk.|
| Tillåtna protokoll | Det protokoll som tillåts för en begäran med SAS-kontot. HTTPS-inställningen rekommenderas.|

## <a name="see-also"></a>Se också
- [Använda signaturer för delad åtkomst (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Signaturer för delad åtkomst, del 2: Skapa och använda en SAS med Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)
- [Att säkra Azure Content Delivery Network tillgångar med tokenautentisering](https://docs.microsoft.com/azure/cdn/cdn-token-auth)
