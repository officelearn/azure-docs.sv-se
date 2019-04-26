---
title: Använda Azure CDN med SAS | Microsoft Docs
description: Azure CDN stöder användning av signatur för delad åtkomst (SAS) att bevilja begränsad åtkomst till privata storage-behållare.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.openlocfilehash: 7edf0a9f8d4eb4c01b6d80fd82a1061b6cbb1e35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60324172"
---
# <a name="using-azure-cdn-with-sas"></a>Använda Azure CDN med SAS

När du ställer in ett storage-konto för Azure CDN Content Delivery Network () du använder för att cachelagra innehåll som standard som skickas till URL: er för storage-behållare kan komma åt de filer som du har överfört. För att skydda filer i ditt storage-konto kan ange du åtkomst till ditt storage-behållare från offentligt till privat. Om du gör det kommer ingen att kunna komma åt dina filer. 

Om du vill ge begränsad åtkomst till privata lagringscontainrar kan du använda funktionen SAS (signatur för delad åtkomst) för Azure-lagringskontot. En SAS är en URI som ger begränsad åtkomst till dina Azure-lagringsresurser utan att du exponerar din kontonyckel. Du kan ange en SAS för klienter som du inte litar med din lagringskontonyckel men som du vill delegera åtkomst till vissa lagringskontoresurserna. Genom att distribuera en signatur för delad åtkomst URI: N till dessa klienter ger du dem åtkomst till en resurs för en angiven tidsperiod.
 
Du kan använda en SAS för att definiera olika parametrar för åtkomst till en blob som start-och förfallodatum, behörigheter (Läs/Skriv) och IP-intervall. Den här artikeln beskriver hur du använder SAS tillsammans med Azure CDN. Läs mer om SAS, inklusive hur du skapar den och dess parameteralternativ [använda signaturer för delad åtkomst (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Ställa in Azure CDN för att arbeta med SAS-lagring
Följande tre alternativ rekommenderas för att använda SAS med Azure CDN. Alla alternativ förutsätter att du redan har skapat ett fungerande SAS (se krav). 
 
### <a name="prerequisites"></a>Nödvändiga komponenter
Skapa ett lagringskonto för att starta, och sedan skapa en SAS för tillgången. Du kan skapa två typer av signaturer lagrade åtkomst: tjänst-SAS eller ett SAS-konto. Mer information finns i [typer av signaturer för delad åtkomst](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

När du har genererat en SAS-token kan du komma åt din blob storage-fil genom att lägga till `?sv=<SAS token>` till din URL. Denna URL har följande format: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Exempel:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Mer information om inställningen parametrar finns i [SAS parametern överväganden](#sas-parameter-considerations) och [parametrar för delad åtkomst signaturer](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters).

![CDN SAS-inställningar](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Alternativ 1: Med hjälp av SAS med direkt till blob storage från Azure CDN

Det här alternativet är den enklaste och använder en enda SAS-token som skickas från Azure CDN till ursprungsservern.
 
1. Välj en slutpunkt, Välj **Cachelagringsregler**och välj sedan **cachelagra varje unik URL** från den **cachelagring av frågesträngar i frågan** lista.

    ![CDN-cachelagringsregler](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. När du har skapat SAS på ditt lagringskonto måste du använda SAS-token med CDN-slutpunkten och det ursprungliga server URL: er för åtkomst till filen. 
   
   Resulterande CDN-slutpunktens URL har följande format: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Exempel:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Finjustera cachelagringens varaktighet med hjälp av cachelagringsregler eller genom att lägga till `Cache-Control` rubriker på den ursprungliga servern. Eftersom Azure CDN behandlar SAS-token som en vanlig frågesträng, som bästa praxis bör du ställa in en lagringstiden som upphör att gälla från och med den SAS upphör att gälla. I annat fall om en fil cachelagras under en längre tid än SAS är aktiv och kanske filen är tillgängligt från Azure CDN ursprungsservern förfallotiden för SAS har förflutit. Om den här situationen uppstår och du vill isolera cachelagrade filen, måste du utföra en rensning-åtgärden på filen för att radera den från cachen. Information om hur du anger cachelagringens varaktighet på Azure CDN finns i [Kontrollera Cachelagringsbeteendet med cachelagringsregler](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Alternativ 2: Dolda CDN SAS-token med hjälp av en omskrivningsregel
 
Det här alternativet är endast tillgänglig för **Azure CDN Premium från Verizon** profiler. Med det här alternativet kan du skydda bloblagringen vid den ursprungliga servern. Du kanske vill använda det här alternativet om du inte behöver specifika åtkomstbegränsningar för filen, men vill hindra användare från att komma åt storage ursprunget direkt för att förbättra Azure CDN-avlastning gånger. SAS-token som är okända för användaren, krävs för vem som helst komma åt filer i den angivna behållaren för den ursprungliga servern. Men på grund av URL-Omskrivningsregler-regeln krävs SAS-token inte för CDN-slutpunkt.
 
1. Använd den [regelmotor](cdn-rules-engine.md) att skapa en regel för URL-Omskrivningsregler. Nya regler ta upp till fyra timmar att sprida.

   ![CDN hantera knappen](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN regelmotor – knappen](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Följande exempel URL-Omskrivningsregler regel använder ett mönster för reguljärt uttryck med en hämtad grupp och en slutpunkt med namnet *sasstoragedemo*:
   
   Källa:   
   `(container1\/.*)`
   
   Mål:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL-Omskrivningsregler regel - vänster](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![CDN URL-Omskrivningsregler regel - höger](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. När den nya regeln aktiveras, alla kan komma åt filer i den angivna behållaren för CDN-slutpunkt, oavsett om de använder en SAS-token i Webbadressen. Här är formatet: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Exempel:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Finjustera cachelagringens varaktighet med hjälp av cachelagringsregler eller genom att lägga till `Cache-Control` rubriker på den ursprungliga servern. Eftersom Azure CDN behandlar SAS-token som en vanlig frågesträng, som bästa praxis bör du ställa in en lagringstiden som upphör att gälla från och med den SAS upphör att gälla. I annat fall om en fil cachelagras under en längre tid än SAS är aktiv och kanske filen är tillgängligt från Azure CDN ursprungsservern förfallotiden för SAS har förflutit. Om den här situationen uppstår och du vill isolera cachelagrade filen, måste du utföra en rensning-åtgärden på filen för att radera den från cachen. Information om hur du anger cachelagringens varaktighet på Azure CDN finns i [Kontrollera Cachelagringsbeteendet med cachelagringsregler](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Alternativ 3: Med en omskrivningsregel tokenautentisering för CDN säkerhet

Om du vill använda Azure CDN token autentisering, måste du ha en **Azure CDN Premium från Verizon** profil. Det här alternativet är säker och anpassningsbara. Klientåtkomst baseras på de parametrar som du angett för säkerhetstoken. När du har skapat och ställa in säkerhetstoken, behöver den på alla webbadresser för CDN-slutpunkten. Men på grund av URL-Omskrivningsregler-regeln krävs SAS-token inte för CDN-slutpunkt. Om SAS-token senare blir ogiltig, kommer Azure CDN inte längre att kunna verifiera innehållet från den ursprungliga servern.

1. [Skapa en Azure CDN-säkerhetstoken](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) och aktivera den med hjälp av regelmotorn för CDN-slutpunkten och sökväg där användarna kan komma åt filen.

   En security token slutpunkts-URL har följande format:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Exempel:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Parameteralternativ för en token säkerhetsautentisering skiljer sig från parameteralternativ för en SAS-token. Om du väljer att använda en förfallotid när du skapar en säkerhetstoken, bör du ange den till samma värde som förfallotiden för SAS-token. På så sätt att förfallotiden är förutsägbara. 
 
2. Använd den [regelmotor](cdn-rules-engine.md) att skapa en URL-Omskrivningsregler regel om du vill aktivera SAS-token åtkomst till alla blobar i behållaren. Nya regler ta upp till fyra timmar att sprida.

   Följande exempel URL-Omskrivningsregler regel använder ett mönster för reguljärt uttryck med en hämtad grupp och en slutpunkt med namnet *sasstoragedemo*:
   
   Källa:   
   `(container1\/.*)`
   
   Mål:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL-Omskrivningsregler regel - vänster](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![CDN URL-Omskrivningsregler regel - höger](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Om du förnyar SAS, se till att du uppdatera Webbadressomskrivning regeln med den nya SAS-token. 

## <a name="sas-parameter-considerations"></a>Överväganden för SAS-parameter

Eftersom SAS-parametrar inte är synliga för Azure CDN kan kan inte Azure CDN ändra dess leveransbeteende utifrån dem. Definierad parameter begränsningar gäller endast för förfrågningar som Azure CDN gör till den ursprungliga servern, inte för förfrågningar från klienten till Azure CDN. Skillnaden är viktigt att tänka på när du ställer in SAS-parametrar. Om dessa avancerade funktioner som krävs och du använder [alternativ 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), ange lämpliga begränsningar för Azure CDN-säkerhetstoken.

| SAS-parameternamn | Beskrivning |
| --- | --- |
| Start | Den tid som Azure CDN kan börja komma åt blob-fil. På grund av klockan förskjuta (när en signal klockan anländer vid olika tidpunkter för olika komponenter), Välj en tid 15 minuter tidigare om du vill att tillgången ska vara tillgängliga omedelbart. |
| Slut | Den tid då Azure CDN inte längre har åtkomst till blobbfilen. Tidigare är cachelagrade filer på Azure CDN fortfarande tillgängliga. För att styra förfallotiden fil, ange rätt förfallotiden för Azure CDN-säkerhetstoken eller rensa tillgången. |
| Tillåtna IP-adresser | Valfri. Om du använder **Azure CDN från Verizon**, du kan ställa in den här parametern till de intervall som har definierats i [Azure CDN från Verizon Edge Server IP-intervall](/azure/cdn/cdn-pop-list-api). Om du använder **Azure CDN från Akamai**, du kan inte ange parametern IP-adressintervall eftersom de inte statisk IP-adresser.|
| Tillåtna protokoll | De protokoll som tillåts för en förfrågan gjord med kontot med delad Åtkomstsignatur. HTTPS-inställningen rekommenderas.|

## <a name="next-steps"></a>Nästa steg

Mer information om SAS finns i följande artiklar:
- [Använda signaturer för delad åtkomst (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Signaturer för delad åtkomst, del 2: Skapa och använda en SAS med Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)

Läs mer om hur du konfigurerar autentisering med enhetstoken [skydda Azure Content Delivery Network-resurser med tokenautentisering](https://docs.microsoft.com/azure/cdn/cdn-token-auth).
