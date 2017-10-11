---
title: "Mappa Azure CDN innehåll till en anpassad domän | Microsoft Docs"
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
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: cd6db44f7776859d1e6a893543cf0666182ca41a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="map-azure-cdn-content-to-a-custom-domain"></a>Mappa Azure CDN innehåll till en anpassad domän
Du kan mappa en anpassad domän till en CDN-slutpunkt för att kunna använda ditt eget domännamn i URL: er till cachelagrat innehåll i stället för en underdomän till azureedge.net.

Det finns två sätt att mappa en anpassad domän till en CDN-slutpunkten:

1. [Skapa en CNAME-post hos din domänregistrator och mappa dina anpassade domäner och underdomäner till CDN-slutpunkten](#register-a-custom-domain-for-an-azure-cdn-endpoint).
   
    En CNAME-post är en DNS-funktion som mappar en källdomän, som `www.contosocdn.com` eller `cdn.contoso.com`, till en måldomän. I det här fallet källdomänen är dina anpassade domäner och underdomäner (en underdomän, till exempel **www** eller **cdn** krävs alltid). Måldomänen är CDN-slutpunkten.  
   
    Processen för att mappa den anpassade domänen till CDN-slutpunkten kan dock leda till en kort period driftstopp för domänen medan du registrerar domänen i Azure Portal.
2. [Lägg till ett mellanliggande registreringssteget med **cdnverify**](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)
   
    Om den anpassade domänen för närvarande stöder ett program med ett servicenivåavtal (SLA) som måste det finnas utan avbrott, så du kan använda Azure **cdnverify** underdomän att tillhandahålla en mellanliggande registrering så att användare kommer att kunna komma åt din domän när DNS-mappning äger rum.  

När du har registrerat med någon av ovanstående metoder domänen kommer du vilja [Kontrollera att den anpassa underdomänen refererar CDN-slutpunkten](#verify-that-the-custom-subdomain-references-your-cdn-endpoint).

> [!NOTE]
> Du måste skapa en CNAME-post hos din domänregistrator för att mappa din domän till CDN-slutpunkten. CNAME-poster mappa specifika underdomäner som `www.contoso.com` eller `cdn.contoso.com`. Det går inte att mappa en CNAME-post till en rotdomän som `contoso.com`.
> 
> En underdomän kan bara vara kopplad till en CDN-slutpunkten. CNAME-posten som du skapar kommer att vidarebefordra all trafik till en underdomän till den angivna slutpunkten.  Om du kopplar till exempel `www.contoso.com` med din CDN-slutpunkten sedan du kan associera den med andra Azure-slutpunkter, till exempel en slutpunkt för storage-konto eller ett moln tjänstslutpunkten. Du kan dock använda olika underdomäner från samma domän för olika slutpunkter. Du kan också mappa olika underdomäner till samma CDN-slutpunkten.
> 
> För **Azure CDN från Verizon** (Standard och Premium) slutpunkter, Observera att det tar upp till **90 minuter** kant noder för anpassade domänändringar sprids till CDN.
> 
> 

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint"></a>Registrera en anpassad domän för en Azure CDN-slutpunkt
1. Logga in på den [Azure-portalen](https://portal.azure.com/).
2. Klicka på **Bläddra**, sedan **CDN profiler**, sedan CDN-profilen med slutpunkten som du vill mappa till en anpassad domän.  
3. I den **CDN-profilen** bladet, klickar du på CDN-slutpunkt som du vill associera underdomänen.
4. Överst på bladet för slutpunkten klickar du på den **Lägg till anpassad domän** knappen.  I den **lägga till en anpassad domän** bladet visas värdnamnet slutpunkt har härletts från din CDN-slutpunkten ska användas för att skapa en ny CNAME-post. Format för namn och adress på värden visas som  **&lt;EndpointName >. azureedge.net**.  Du kan kopiera värdnamn att använda skapa CNAME-post.  
5. Gå till din domänregistrators webbplats och leta upp avsnittet för att skapa DNS-poster. Du kan hitta det här i ett avsnitt som till exempel **Domännamn**, **DNS**, eller **hantering av namnhantering**.
6. Leta reda på avsnittet för att hantera CNAME. Du kan behöva gå till en sida med avancerade inställningar och leta efter orden CNAME, Alias eller Underdomäner.
7. Skapa en ny CNAME-post som mappar dina valda underdomänen (till exempel **www** eller **cdn**) till värdnamnet i den **lägga till en anpassad domän** bladet. 
8. Gå tillbaka till den **lägga till en anpassad domän** bladet och ange din domän, inklusive underdomänen, i dialogrutan. Till exempel ange domännamnet i formatet `www.contoso.com` eller `cdn.contoso.com`.   
   
   Azure verifierar att det finns en CNAME-posten för domännamnet som du har angett. Om CNAME är korrekt verifieras din anpassade domän.  För **Azure CDN från Verizon** slutpunkter (Standard och Premium), kan det ta upp till 90 minuter för anpassad domäninställningarna att spridas till alla noder för CDN-edge, men.  
   
   Observera att det i vissa fall kan ta tid för CNAME-posten ska spridas till namnservrar på Internet. Om din domän inte har verifierats omedelbart och du tror CNAME-post är korrekt, vänta en stund och försök igen.

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain"></a>Registrera en anpassad domän för ett Azure CDN-slutpunkten med hjälp av underdomänen mellanliggande cdnverify
1. Logga in på den [Azure-portalen](https://portal.azure.com/).
2. Klicka på **Bläddra**, sedan **CDN profiler**, sedan CDN-profilen med slutpunkten som du vill mappa till en anpassad domän.  
3. I den **CDN-profilen** bladet, klickar du på CDN-slutpunkt som du vill associera underdomänen.
4. Överst på bladet för slutpunkten klickar du på den **Lägg till anpassad domän** knappen.  I den **lägga till en anpassad domän** bladet visas värdnamnet slutpunkt har härletts från din CDN-slutpunkten ska användas för att skapa en ny CNAME-post. Format för namn och adress på värden visas som  **&lt;EndpointName >. azureedge.net**.  Du kan kopiera värdnamn att använda skapa CNAME-post.
5. Gå till din domänregistrators webbplats och leta upp avsnittet för att skapa DNS-poster. Du kan hitta det här i ett avsnitt som till exempel **Domännamn**, **DNS**, eller **hantering av namnhantering**.
6. Leta reda på avsnittet för att hantera CNAME. Du kan behöva gå till en sida med avancerade inställningar och leta efter orden **CNAME**, **Alias**, eller **underdomäner**.
7. Skapa en ny CNAME-post och ange en underdomän alias som innehåller den **cdnverify** underdomänen. Till exempel underdomänen som du anger ska vara i formatet **cdnverify.www** eller **cdnverify.cdn**. Ange värdnamnet, vilket är din CDN-slutpunkten i formatet **cdnverify.&lt; EndpointName >. azureedge.net**. DNS-mappning bör se ut som:`cdnverify.www.consoto.com   CNAME   cdnverify.consoto.azureedge.net`  
8. Gå tillbaka till den **lägga till en anpassad domän** bladet och ange din domän, inklusive underdomänen, i dialogrutan. Till exempel ange domännamnet i formatet `www.contoso.com` eller `cdn.contoso.com`. Observera att i det här steget kan du inte behöver inleder underdomänen med **cdnverify**.  
   
    Azure verifierar att det finns en CNAME-post för cdnverify domännamn som du har angett.
9. Nu är din anpassade domän har verifierats av Azure, men trafik till din domän är ännu inte dirigeras till CDN-slutpunkten. Efter att ha väntat tillräckligt länge för att tillåta anpassad domän-inställningarna att spridas till CDN kant noder (90 minuter för **Azure CDN från Verizon**, 1 till 2 minuter för **Azure CDN från Akamai**), gå tillbaka till din DNS registrators webbplats och skapa en annan CNAME-post som mappar en underdomän till CDN-slutpunkten. Till exempel ange underdomänen som **www** eller **cdn**, och värdnamn som  **&lt;EndpointName >. azureedge.net**. Registreringen av den anpassade domänen är klar med det här steget.
10. Slutligen kan du ta bort CNAME-post som du skapade med **cdnverify**eftersom det var nödvändigt endast som en mellanliggande steg.  

## <a name="verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>Kontrollera att den anpassa underdomänen refererar CDN-slutpunkten
* När du har slutfört registreringen av den anpassade domänen, kan du komma åt innehåll som cachelagras på CDN-slutpunkten med den anpassa domänen.
  Kontrollera först att du har offentligt innehåll som cachelagras på slutpunkten. Om din CDN-slutpunkten är kopplat till ett lagringskonto cachelagrar i CDN innehållet i offentlig blob-behållare. Se till att din behållare har angetts att tillåta offentlig åtkomst och att den innehåller minst en blob för att testa den anpassade domänen.
* Navigera till adressen till blob med den anpassa domänen i din webbläsare. Om din anpassade domän är till exempel `cdn.contoso.com`, URL-Adressen till en cachelagrade blob kommer att likna följande URL: http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg

## <a name="see-also"></a>Se även
[Så här aktiverar du innehållsleveransnätverk (CDN) för Azure](cdn-create-new-endpoint.md)  

