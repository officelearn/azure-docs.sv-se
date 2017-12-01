---
title: "Lägg till en anpassad domän i CDN-slutpunkten | Microsoft Docs"
description: "Lär dig hur Azure CDN innehållet till en anpassad domän."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 289f8d9e-8839-4e21-b248-bef320f9dbfc
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: mazha
ms.openlocfilehash: fd36b94c64ad31064dbb2e0badceaee5e5bc400f
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="add-a-custom-domain-to-your-cdn-endpoint"></a>Lägg till en anpassad domän i CDN-slutpunkten
När du skapar en profil kan du vanligtvis också skapa en eller flera CDN [slutpunkter](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint) (en underdomän till `azureedge.net`) kan leverera ditt innehåll med HTTP och HTTPS. Som standard den här slutpunkten ingår i alla URL: er (till exempel `https://contoso.azureedge.net/photo.png`). För din bekvämlighet Azure CDN kan du associera en anpassad domän (till exempel `www.contoso.com`) med slutpunkten. Med det här alternativet kan använda du en anpassad domän kan leverera ditt innehåll i stället för din slutpunkt. Det här alternativet är användbart om du till exempel som ett eget domännamn ska vara synlig för kunderna för företagsanpassning syften.

Om du inte redan har en anpassad domän måste du först köpa ett med en domän-provider. När du har fått en anpassad domän, gör du följande:
1. [Åtkomst till DNS-posterna för din domänleverantör](#step-1-access-dns-records-by-using-your-domain-provider)
2. [Skapa CNAME DNS-poster](#step-2-create-the-cname-dns-records)
    - Alternativ 1: Direkt mappning av den anpassade domänen till CDN-slutpunkten
    - Alternativ 2: Mappningen av den anpassade domänen till CDN-slutpunkten med hjälp av cdnverify 
3. [Aktivera mappning för CNAME-post i Azure](#step-3-enable-the-cname-record-mapping-in-azure)
4. [Kontrollera att den anpassa underdomänen refererar CDN-slutpunkten](#step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint)
5. [(Beroende steg) Mappa permanent anpassad domän till CDN-slutpunkten](#step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint)

## <a name="step-1-access-dns-records-by-using-your-domain-provider"></a>Steg 1: Åtkomst DNS registrerar med hjälp av din domänleverantör

Om du använder Azure till värd din [DNS-domäner](https://docs.microsoft.com/en-us/azure/dns/dns-overview), måste du delegera domän leverantörens DNS för en Azure DNS. Mer information finns i [delegera en domän till Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)

Annars, om du använder domän-providern ska hantera din DNS-domän, logga in på webbplatsen på din domän-provider. Hitta sidan för att hantera DNS-poster efter samråd med leverantörens dokumentation eller söka efter områden på webbplatsen med namnet **domännamn**, **DNS**, eller **namn serverhantering**. Du kan ofta hitta sidan DNS-poster genom att visa din kontoinformation och söker efter en länk som **min domäner**. Vissa providrar har olika länkar för att lägga till olika typer av poster.

> [!NOTE]
> För vissa leverantörer, till exempel GoDaddy, börjar ändringar i DNS-posterna inte att gälla förrän du väljer en separat **Spara ändringar**-länk. 


## <a name="step-2-create-the-cname-dns-records"></a>Steg 2: Skapa CNAME DNS-poster

Innan du kan använda en anpassad domän med en Azure CDN-slutpunkt, måste du först skapa en kanoniskt namn (CNAME) post med domänleverantören av. En CNAME-post är en typ av post i DNS Domain Name System () som mappar ett källdomänen till en måldomänen genom att ange ett alias domännamn för ”kanoniska” eller true domännamnet. För Azure CDN källdomänen anpassad domän (och underdomän) och måldomänen är CDN-slutpunkten. Azure CDN verifierar CNAME DNS-post när du lägger till anpassad domän till slutpunkten från portalen eller API. 

En CNAME-post mappa en specifik domän och en underdomän, t.ex `www.contoso.com` eller `cdn.contoso.com`; det går inte att mappa en CNAME-post till en rotdomän som `contoso.com`. En underdomän kan associeras med endast en CDN-slutpunkten och CNAME-posten som du skapar kommer att vidarebefordra all trafik till en underdomän till den angivna slutpunkten. Om du kopplar till exempel `www.contoso.com` med din CDN-slutpunkten kan du associera den med en annan Azure slutpunkt, till exempel en slutpunkt för storage-konto eller ett moln tjänstslutpunkten. Du kan dock använda olika underdomäner från samma domän för olika slutpunkter. Du kan också mappa olika underdomäner till samma CDN-slutpunkten.

Använd en av följande alternativ för att mappa en anpassad domän till en CDN-slutpunkten:

- Alternativ 1: Direkt mappning. Om ingen produktion trafik körs på den anpassade domänen kan du mappa en anpassad domän till en CDN-slutpunkt direkt. Processen för att mappa den anpassade domänen till CDN-slutpunkten kan resultera i en kort period driftstopp för domänen när du registrerar domänen i Azure-portalen. Posten CNAME-mappningen ska vara i formatet: 
 
  | NAMN             | TYP  | VÄRDE                  |
  |------------------|-------|------------------------|
  | www\.contoso.com | CNAME | Contoso\.azureedge.net |


- Alternativ 2: Mappning med den **cdnverify** underdomänen. Om produktion trafik som inte kan avbrytas körs på den anpassade domänen kan skapa du en tillfällig CNAME-mappning till CDN-slutpunkten. Med det här alternativet kan du använda Azure **cdnverify** underdomän att tillhandahålla en mellanliggande registreringssteget så att användare kan komma åt din domän utan avbrott när DNS-mappning äger rum.

   1. Skapa en ny CNAME-post och ange en underdomän alias som innehåller den **cdnverify** underdomänen. Till exempel **cdnverify.www** eller **cdnverify.cdn**. 
   2. Ange värdnamnet, vilket är din CDN-slutpunkten i följande format: `cdnverify.<EndpointName>.azureedge.net`. Posten CNAME-mappningen ska vara i formatet: 

   | NAMN                       | TYP  | VÄRDE                            |
   |----------------------------|-------|----------------------------------|
   | cdnverify.www\.contoso.com | CNAME | cdnverify.contoso\.azureedge.net | 


## <a name="step-3-enable-the-cname-record-mapping-in-azure"></a>Steg 3: Aktivera mappning för CNAME-post i Azure

När du har registrerat din anpassade domän med någon av de föregående procedurerna, kan du aktivera funktionen anpassade domäner i Azure CDN. 

1. Logga in på den [Azure-portalen](https://portal.azure.com/) och bläddra till CDN-profilen med den slutpunkt som du vill koppla till en anpassad domän.  
2. I den **CDN-profilen** bladet välj CDN-slutpunkt som du vill associera underdomänen.
3. I det övre vänstra hörnet på bladet för slutpunkten, klickar du på **anpassad domän**. 

   ![Knappen Anpassad domän](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

4. I den **anpassad hostname** text Skriv din domän, inklusive underdomänen. Till exempel `www.contoso.com` eller `cdn.contoso.com`.

   ![Lägg till en anpassad domän dialog](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain-dialog.png)

5. Klicka på **Lägg till**.

   Azure verifierar att domännamnet som du har angett har en CNAME-post. Om CNAME är korrekt verifieras din anpassade domän. Det kan ta lite tid för CNAME-posten ska spridas till namnservrar. Om din domän inte har verifierats omedelbart, kontrollera att CNAME-post är korrekt, vänta en stund och försök igen. För **Azure CDN från Verizon** (Standard och Premium) slutpunkter kan det ta upp till 90 minuter för anpassade domäninställningarna att spridas till alla noder i CDN-kant.  


## <a name="step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>Steg 4: Verifiera att den anpassa underdomänen refererar CDN-slutpunkten

När du har slutfört registreringen av den anpassade domänen, kan du kontrollera att den anpassa underdomänen refererar CDN-slutpunkten.
 
1. Se till att du har offentligt innehåll som cachelagras på slutpunkten. Om din CDN-slutpunkten är kopplat till ett lagringskonto cachelagrar i CDN innehållet i offentlig blob-behållare. Kontrollera att din behållaren tillåter offentlig åtkomst och innehåller minst en blob för att testa den anpassade domänen.

2. Navigera till blob-adressen med hjälp av den anpassade domänen i webbläsaren. Om din anpassade domän är till exempel `cdn.contoso.com`, URL-Adressen till den cachelagrade blobben bör likna följande URL: `http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg`.


## <a name="step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint"></a>Steg 5 (beroende steg): mappa permanent anpassad domän till CDN-slutpunkten

Det här steget är beroende av steg 2, alternativ 2 (mappning med den **cdnverify** underdomän). Om du använder tillfälligt **cdnverify** underdomän och har verifierat att den fungerar, du kan sedan mappa permanent domänen till CDN-slutpunkten.

1. Skapa en CNAME DNS-post permanent domänen att mappa till CDN-slutpunkten på domänen leverantörens webbplats. Posten CNAME-mappningen ska vara i formatet: 
 
   | NAMN             | TYP  | VÄRDE                  |
   |------------------|-------|------------------------|
   | www\.contoso.com | CNAME | Contoso\.azureedge.net |
2. Ta bort CNAME-post med den **cdnverify** underdomän som du skapade tidigare.

## <a name="see-also"></a>Se även
[Så här aktiverar du innehållsleveransnätverk (CDN) för Azure](cdn-create-new-endpoint.md)  
[Delegera din domän till Azure DNS](../dns/dns-domain-delegation.md)
