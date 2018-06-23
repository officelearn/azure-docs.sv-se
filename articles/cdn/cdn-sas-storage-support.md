---
title: Med hjälp av Azure CDN med SAS | Microsoft Docs
description: Azure CDN har stöd för delad åtkomst signatur (SAS) att bevilja begränsad åtkomst till privata behållare.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: v-deasim
ms.openlocfilehash: 15a4e0a8d62b38fa7aa542d95e53d29621965666
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316576"
---
# <a name="using-azure-cdn-with-sas"></a>Med SAS Azure CDN

När du ställer in ett lagringskonto för Azure innehåll innehållsleveransnätverk (CDN) ska användas till att cachelagra innehåll som standard som skickas till URL: er för behållare för lagring kan komma åt filer som du har överfört. Du kan ange åtkomsten till behållare från offentligt till privat för att skydda filerna i ditt lagringskonto. Om du gör det kommer ingen att kunna komma åt dina filer. 

Du kan använda funktionen delade signatur åtkomst (SAS) i Azure storage-konto om du vill bevilja begränsad åtkomst till privata behållare. En SAS är en URI som ger begränsad behörighet till Azure Storage-resurser utan att utsätta din kontonyckel. Du kan ange en SAS för klienter som du inte litar med din åtkomstnyckel för lagring men som du vill delegera åtkomst till vissa konto lagringsresurser. Genom att distribuera en signatur för delad åtkomst URI för dessa klienter ger du dem åtkomst till en resurs för en angiven tidsperiod.
 
Du kan använda en SAS för att definiera olika parametrar för åtkomst till en blobb som start-och upphör att gälla, behörigheter (läsa/skriva) och IP-adressintervall. Den här artikeln beskriver hur du använder SAS tillsammans med Azure CDN. Läs mer om SAS, inklusive hur du skapar den och dess parameteralternativ [använder signaturer för delad åtkomst (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Ställa in Azure CDN för att arbeta med SAS-lagring
Följande tre alternativ rekommenderas för att använda SAS med Azure CDN. Alla alternativ förutsätter att du redan har skapat en fungerande SAS (se krav). 
 
### <a name="prerequisites"></a>Förutsättningar
Starta, skapa ett lagringskonto och generera en SAS för din tillgång. Du kan skapa två typer av signaturer lagrade åtkomst: tjänst-SAS eller en konto-SAS. Mer information finns i [typer av signaturer för delad åtkomst](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

När du har skapat en SAS-token, du har åtkomst till ditt blob storage-fil genom att lägga till `?sv=<SAS token>` till URL: en. Denna URL har följande format: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Exempel:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Mer information om inställningen parametrar finns [SAS parametern överväganden](#sas-parameter-considerations) och [parametrar för delad åtkomst signatur](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters).

![CDN SAS-inställningar](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Alternativ 1: Med hjälp av SAS med direkt till blob storage från Azure CDN

Det här alternativet är den enklaste och använder en enda SAS-token som skickas till den ursprungliga servern från Azure CDN. Det stöds av **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** profiler. 
 
1. Välj en slutpunkt **cachelagring regler**och välj **cachelagra varje unik URL** från den **cachelagring av frågesträngar i frågan** lista.

    ![CDN cachelagring regler](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. När du har skapat SAS på ditt lagringskonto måste du använda SAS-token med CDN-slutpunkten och ursprung server URL: er till filen. 
   
   Resulterande CDN slutpunkts-URL har följande format: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Exempel:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Finjustera cachelagringens varaktighet med cachelagring regler eller genom att lägga till `Cache-Control` rubriker på den ursprungliga servern. Eftersom Azure CDN behandlar SAS-token som en vanlig frågesträng, som bästa praxis bör du ställa in en cachelagringens varaktighet som upphör att gälla på eller före utgångstiden SAS. I annat fall om en fil cachelagras under en längre tid än SAS är aktiv, kan filen vara tillgänglig från ursprungsservern Azure CDN SAS förfallotid har förflutit. Om detta inträffar och du vill isolera cachelagrade filen, måste du utföra en Rensa åtgärden på filen för att ta bort den från cacheminnet. Information om hur du anger cachelagringens varaktighet på Azure CDN finns [kontroll Azure CDN cachelagring av frågesträngar med cachelagring regler](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Alternativ 2: Dolda CDN SAS-token med en regel för omarbetning
 
Det här alternativet är bara tillgängligt för **Azure CDN Premium från Verizon** profiler. Med det här alternativet kan du skydda blobblagring på den ursprungliga servern. Du kanske vill använda det här alternativet om du inte behöver specifika åtkomstbegränsningar för filen, men vill hindra användare från att komma åt lagringsutrymme ursprung direkt för att förbättra Azure CDN avlastning gånger. SAS-token som är okända för användaren, krävs för alla filer i den angivna behållaren på den ursprungliga servern. På grund av URL-omskrivning om regeln krävs SAS-token men inte för CDN-slutpunkten.
 
1. Använd den [regelmotor](cdn-rules-engine.md) att skapa en regel för URL-omskrivning om. Nya regler ta ungefär 10 minuter för att sprida.

   ![CDN hantera knappen](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN regler motorn knappen](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Följande exempel URL-omskrivning om regel använder ett mönster för reguljärt uttryck med en sparandet grupp och en slutpunkt med namnet *storagedemo*:
   
   Källa:   
   `(\/container1\/.*)`
   
   Mål:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN-URL-omskrivning regel - vänster](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![CDN URL-omskrivning regel - höger](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-2.png)

2. När den nya regeln aktiveras alla kan komma åt filer i den angivna behållaren på slutpunkten för Innehållsleveransnätverk oavsett om de använder en SAS-token i URL-Adressen. Här är formatet: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Exempel:   
   `https://demoendpoint.azureedge.net/container1/demo.jpg`
       

3. Finjustera cachelagringens varaktighet med cachelagring regler eller genom att lägga till `Cache-Control` rubriker på den ursprungliga servern. Eftersom Azure CDN behandlar SAS-token som en vanlig frågesträng, som bästa praxis bör du ställa in en cachelagringens varaktighet som upphör att gälla på eller före utgångstiden SAS. I annat fall om en fil cachelagras under en längre tid än SAS är aktiv, kan filen vara tillgänglig från ursprungsservern Azure CDN SAS förfallotid har förflutit. Om detta inträffar och du vill isolera cachelagrade filen, måste du utföra en Rensa åtgärden på filen för att ta bort den från cacheminnet. Information om hur du anger cachelagringens varaktighet på Azure CDN finns [kontroll Azure CDN cachelagring av frågesträngar med cachelagring regler](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Alternativ 3: Genom att använda CDN säkerhet token med en regel för omarbetning

Om du vill använda Azure CDN säkerhet token-autentisering måste du ha en **Azure CDN Premium från Verizon** profil. Det här alternativet är den mest säkra och anpassningsbara. Klientåtkomst baseras på de parametrar som du angett för säkerhetstoken. När du har skapat och konfigurerat säkerhetstoken, behöver den på alla webbadresser för CDN-slutpunkten. På grund av URL-omskrivning om regeln krävs SAS-token men inte för CDN-slutpunkten. Om SAS-token senare blir ogiltigt, kommer Azure CDN inte längre att kunna verifiera innehållet från den ursprungliga servern.

1. [Skapa en Azure CDN säkerhetstoken](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) och aktivera den med hjälp av motorn för regler för CDN-slutpunkten och sökvägen där användarna kan komma åt filen.

   En säkerhets-token slutpunkts-URL har följande format:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Exempel:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Parameteralternativ för säkerhet tokenautentisering skiljer sig från parameteralternativ för en SAS-token. Om du väljer att använda en förfallotid när du skapar en säkerhetstoken, anger du den till samma värde som förfallotiden för SAS-token. På så sätt att förfallotiden är förutsägbar. 
 
2. Använd den [regelmotor](cdn-rules-engine.md) att skapa en URL-omskrivning om regel om du vill aktivera SAS-token åtkomst till alla blobbar i behållaren. Nya regler ta ungefär 10 minuter för att sprida.

   Följande exempel URL-omskrivning om regel använder ett mönster för reguljärt uttryck med en sparandet grupp och en slutpunkt med namnet *storagedemo*:
   
   Källa:   
   `(\/container1\/.*)`
   
   Mål:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN-URL-omskrivning regel - vänster](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![CDN URL-omskrivning regel - höger](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-3.png)

3. Om du förnyar SAS kontrollerar du att du uppdaterar Url-omskrivning om regeln med den nya SAS-token. 

## <a name="sas-parameter-considerations"></a>Överväganden för SAS-parameter

Eftersom SAS-parametrarna inte är synligt för Azure CDN, kan inte Azure CDN ändra dess leveransbeteende utifrån dem. Definierade parametern begränsningar gäller bara för förfrågningar som gör att Azure CDN till den ursprungliga servern, inte för förfrågningar från klienten till Azure CDN. Denna skillnad är viktigt att tänka på när du ställer in SAS-parametrar. Om dessa avancerade funktioner som krävs och du använder [alternativ 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), ange lämpliga begränsningar för säkerhetstoken Azure CDN.

| Parameternamnet SAS | Beskrivning |
| --- | --- |
| Start | Den tid som Azure CDN kan börja få åtkomst till blob-fil. På grund av klockan skeva (när en klocka signal anländer vid olika tidpunkter för olika komponenter), Välj en gång tidigare 15 minuter om du vill att tillgången ska vara tillgängliga omedelbart. |
| Slut | Tid sedan Azure CDN kan inte längre komma åt blob-fil. Tidigare är cachelagrade filer på Azure CDN fortfarande tillgängliga. Om du vill styra förfallotiden fil, ange lämpliga förfallotiden för säkerhetstoken Azure CDN eller rensa tillgången. |
| Tillåtna IP-adresser | Valfri. Om du använder **Azure CDN från Verizon**, du kan konfigurera den här parametern med de intervall som definieras i [Azure CDN från Verizon Edge Server IP-adressintervall](https://msdn.microsoft.com/library/mt757330.aspx). Om du använder **Azure CDN från Akamai**, du kan inte ange parametern IP-adressintervall eftersom IP-adresser inte är statisk.|
| Tillåtna protokoll | Det protokoll som tillåts för en begäran med SAS-kontot. HTTPS-inställningen rekommenderas.|

## <a name="next-steps"></a>Nästa steg

Mer information om SAS finns i följande artiklar:
- [Använda signaturer för delad åtkomst (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Signaturer för delad åtkomst, del 2: Skapa och använda en SAS med Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)

Läs mer om hur du konfigurerar tokenautentisering [skydda Azure Content Delivery Network tillgångar med tokenautentisering](https://docs.microsoft.com/azure/cdn/cdn-token-auth).
