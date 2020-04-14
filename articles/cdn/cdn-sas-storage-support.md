---
title: Använda Azure CDN med SAS | Microsoft-dokument
description: Azure CDN stöder användning av SAS (Shared Access Signature) för att bevilja begränsad åtkomst till privata lagringsbehållare.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: allensu
ms.openlocfilehash: c2580aa4ee22996c1bf0fe5c86064a6543450071
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260181"
---
# <a name="using-azure-cdn-with-sas"></a>Använda Azure CDN med SAS

När du konfigurerar ett lagringskonto för CDN (Azure Content Delivery Network) som ska användas för att cachelagra innehåll kan som standard alla som känner till url:erna för dina lagringsbehållare komma åt de filer som du har laddat upp. Om du vill skydda filerna i ditt lagringskonto kan du ange åtkomsten för dina lagringsbehållare från offentliga till privata. Men om du gör det kommer ingen att kunna komma åt dina filer. 

Om du vill ge begränsad åtkomst till privata lagringscontainrar kan du använda funktionen SAS (signatur för delad åtkomst) för Azure-lagringskontot. En SAS är en URI som ger begränsad åtkomst till dina Azure-lagringsresurser utan att du exponerar din kontonyckel. Du kan tillhandahålla en SAS till klienter som du inte litar på med din lagringskontonyckel men som du vill delegera åtkomst till vissa lagringskontoresurser. Genom att distribuera en uri för delad åtkomstsignatur till dessa klienter ger du dem åtkomst till en resurs under en angiven tidsperiod.
 
Med en SAS kan du definiera olika parametrar för åtkomst till en blob, till exempel start- och utgångstider, behörigheter (läs/skrivning) och IP-intervall. I den här artikeln beskrivs hur du använder SAS tillsammans med Azure CDN. Mer information om SAS, inklusive hur du skapar den och dess parameteralternativ, finns [i Använda SIGNATURER för delad åtkomst (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Konfigurera Azure CDN så att den fungerar med lagrings-SAS
Följande tre alternativ rekommenderas för att använda SAS med Azure CDN. Alla alternativ förutsätter att du redan har skapat en fungerande SAS (se förutsättningar). 
 
### <a name="prerequisites"></a>Krav
Starta ett lagringskonto och sedan generera en SAS för din tillgång. Du kan generera två typer av signaturer för lagrad åtkomst: en service-SAS eller ett konto SAS. Mer information finns i [Typer av signaturer för delad åtkomst](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

När du har genererat en SAS-token kan du komma `?sv=<SAS token>` åt din blob-lagringsfil genom att lägga till webbadressen. Den här WEBBADRESSen har följande format: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Ett exempel:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Mer information om hur du ställer in parametrar finns i [överväganden för SAS-parameter](#sas-parameter-considerations) och [parametrar för signatur för delad åtkomst](https://docs.microsoft.com/azure/storage/common/storage-sas-overview#how-a-shared-access-signature-works).

![CDN SAS-inställningar](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Alternativ 1: Använda SAS med vidaregenomströmning till blob-lagring från Azure CDN

Det här alternativet är det enklaste och använder en enda SAS-token, som skickas från Azure CDN till ursprungsservern.
 
1. Välj en slutpunkt, välj **Cachelagringsregler**och välj sedan **Cachelagra varje unik URL** i listan **Frågesträngcachelagring.**

    ![CDN-cachelagringsregler](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. När du har konfigurerat SAS på ditt lagringskonto måste du använda SAS-token med CDN-slutpunkten och ursprungsserveradresserna för att komma åt filen. 
   
   Den resulterande CDN-slutpunkts-URL:en har följande format:`https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Ett exempel:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Finjustera cachevaraktigheten antingen genom att använda `Cache-Control` cacheregler eller genom att lägga till rubriker på ursprungsservern. Eftersom Azure CDN behandlar SAS-token som en vanlig frågesträng bör du som bästa praxis ställa in en cachelagringstid som löper ut vid eller före SAS förfallotid. Annars, om en fil cachelagras för en längre tid än SAS är aktiv, kan filen vara tillgänglig från Azure CDN ursprungsservern efter SAS utgångsdatum har förflutit. Om den här situationen inträffar och du vill göra den cachelagrade filen otillgänglig måste du utföra en rensningsåtgärd på filen för att rensa den från cacheminnet. Information om hur du anger cachevaraktighet på Azure CDN finns i [Kontrollera Azure CDN-cachelagring med cachelagringsregler](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Alternativ 2: Dold CDN SAS-token med en omskrivningsregel
 
Det här alternativet är endast tillgängligt för **Azure CDN Premium från** Verizon-profiler. Med det här alternativet kan du skydda blob-lagringen på ursprungsservern. Du kanske vill använda det här alternativet om du inte behöver specifika åtkomstbegränsningar för filen, men vill hindra användare från att komma åt lagringsursprunget direkt för att förbättra Azure CDN-avlastningstider. SAS-token, som är okänd för användaren, krävs för alla som kommer åt filer i den angivna behållaren för ursprungsservern. På grund av URL-omskrivningsregeln krävs inte SAS-token på CDN-slutpunkten.
 
1. Använd [regelmotorn](cdn-rules-engine.md) för att skapa en URL-skriv om-regel. Det tar upp till 4 timmar innan nya regler sprids.

   ![Knappen CDN-hantering](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![KNAPPEN CDN-regler motor](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Följande exempel-URL-omskrivningsregel använder ett mönster för reguljära uttryck med en hämtningsgrupp och en slutpunkt med namnet *sasstoragedemo:*
   
   Källa:   
   `(container1\/.*)`
   
   Mål:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL Skriv om](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![regel - vänster CDN URL Skriv om regel - höger](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. När den nya regeln har aktiverats kan vem som helst komma åt filer i den angivna behållaren på CDN-slutpunkten oavsett om de använder en SAS-token i URL:en. Här är formatet:`https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Ett exempel:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Finjustera cachevaraktigheten antingen genom att använda `Cache-Control` cacheregler eller genom att lägga till rubriker på ursprungsservern. Eftersom Azure CDN behandlar SAS-token som en vanlig frågesträng bör du som bästa praxis ställa in en cachelagringstid som löper ut vid eller före SAS förfallotid. Annars, om en fil cachelagras för en längre tid än SAS är aktiv, kan filen vara tillgänglig från Azure CDN ursprungsservern efter SAS utgångsdatum har förflutit. Om den här situationen inträffar och du vill göra den cachelagrade filen otillgänglig måste du utföra en rensningsåtgärd på filen för att rensa den från cacheminnet. Information om hur du anger cachevaraktighet på Azure CDN finns i [Kontrollera Azure CDN-cachelagring med cachelagringsregler](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Alternativ 3: Använda CDN-säkerhetstokenautentisering med en omskrivningsregel

Om du vill använda Azure CDN-autentisering av säkerhetstoken måste du ha en **Azure CDN Premium från** Verizon-profilen. Det här alternativet är det säkraste och anpassningsbara. Klientåtkomsten baseras på de säkerhetsparametrar som du anger på säkerhetstoken. När du har skapat och konfigurerat säkerhetstoken krävs det på alla CDN-slutpunktsadresser. På grund av URL-omskrivningsregeln krävs inte SAS-token på CDN-slutpunkten. Om SAS-token senare blir ogiltig kan Azure CDN inte längre förnya innehållet från ursprungsservern.

1. [Skapa en Azure CDN-säkerhetstoken](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) och aktivera den med hjälp av regelmotorn för CDN-slutpunkten och sökvägen där användarna kan komma åt filen.

   En url för slutpunkt för säkerhetstoken har följande format:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Ett exempel:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Parameteralternativen för en säkerhetstokenautentisering skiljer sig från parameteralternativen för en SAS-token. Om du väljer att använda en förfallotid när du skapar en säkerhetstoken bör du ange den till samma värde som förfallotiden för SAS-token. Detta säkerställer att utgångstiden är förutsägbar. 
 
2. Använd [regelmotorn](cdn-rules-engine.md) för att skapa en URL-skriva om regel för att aktivera SAS-tokenåtkomst till alla blobbar i behållaren. Det tar upp till 4 timmar innan nya regler sprids.

   Följande exempel-URL-omskrivningsregel använder ett mönster för reguljära uttryck med en hämtningsgrupp och en slutpunkt med namnet *sasstoragedemo:*
   
   Källa:   
   `(container1\/.*)`
   
   Mål:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL Skriv om](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![regel - vänster CDN URL Skriv om regel - höger](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Om du förnyar SAS, se till att du uppdaterar url skriva regeln med den nya SAS-token. 

## <a name="sas-parameter-considerations"></a>Överväganden för SAS-parameter

Eftersom SAS-parametrar inte är synliga för Azure CDN kan Azure CDN inte ändra sitt leveransbeteende baserat på dem. De definierade parameterbegränsningarna gäller endast för begäranden som Azure CDN gör på ursprungsservern, inte för begäranden från klienten till Azure CDN. Den här skillnaden är viktig att tänka på när du ställer in SAS-parametrar. Om dessa avancerade funktioner krävs och du använder [alternativ 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule)anger du lämpliga begränsningar för Azure CDN-säkerhetstoken.

| SAS-parameternamn | Beskrivning |
| --- | --- |
| Start | Den tid som Azure CDN kan börja komma åt blob-filen. På grund av klocksnedställning (när en klocksignal anländer vid olika tidpunkter för olika komponenter) väljer du en tid 15 minuter tidigare om du vill att tillgången ska vara tillgänglig omedelbart. |
| Slut | Den tid efter vilken Azure CDN inte längre kan komma åt blob-filen. Tidigare cachelagrade filer på Azure CDN är fortfarande tillgängliga. Om du vill styra filutgångstiden anger du lämplig utgångstid på Azure CDN-säkerhetstoken eller rensar tillgången. |
| Tillåtna IP-adresser | Valfri. Om du använder **Azure CDN från Verizon**kan du ange den här parametern till de intervall som definierats i Azure [CDN från Verizon Edge Server IP Ranges](/azure/cdn/cdn-pop-list-api). Om du använder **Azure CDN från Akamai**kan du inte ange parametern IP-intervall eftersom IP-adresserna inte är statiska.|
| Tillåtna protokoll | Protokollen/protokollen tillåts för en begäran som görs med kontot SAS. HTTPS-inställningen rekommenderas.|

## <a name="next-steps"></a>Nästa steg

Mer information om SAS finns i följande artiklar:
- [Använda signaturer för delad åtkomst (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Signaturer för delad åtkomst, del 2: Skapa och använd en SAS med Blob-lagring](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)

Mer information om hur du konfigurerar tokenautentisering finns i [Skydda Azure Content Delivery Network-resurser med tokenautentisering](https://docs.microsoft.com/azure/cdn/cdn-token-auth).
