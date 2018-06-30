---
title: Statisk webbplats värd i Azure Storage (förhandsversion) | Microsoft Docs
description: Azure Storage erbjuder statisk webbplats värd (förhandsgranskning), erbjuder en kostnadseffektiv och skalbar lösning för värd för moderna webbprogram.
services: storage
author: MichaelHauss
manager: vamshik
ms.service: storage
ms.topic: article
ms.date: 06/26/18
ms.author: mihauss
ms.openlocfilehash: df1661b5fe7a2c0e37deef5259d6b5842ed6ee5e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131617"
---
# <a name="static-website-hosting-in-azure-storage-preview"></a>Statisk webbplats värd i Azure Storage (förhandsgranskning)
Azure Storage erbjuder statisk webbplats värd (förhandsgranskning), så att du kan distribuera kostnadseffektiv och skalbar moderna webbprogram på Azure. På en statisk webbplats innehåller webbsidor statiskt innehåll och JavaScript eller andra klientkod. Däremot dynamiska webbplatser beroende serverkoden och kan finnas med [Azure Web Apps](/app-service/app-service-web-overview.md).

Distributioner SKIFT mot elastisk, kostnadseffektiv modeller, är möjlighet att leverera webbinnehåll utan att behöva serverhantering kritiska. Införandet av statisk webbplats värd i Azure Storage gör detta möjligt att aktivera funktioner för omfattande backend serverlösa arkitekturer utnyttja [Azure Functions](/azure-functions/functions-overview.md) och andra PaaS-tjänster.

## <a name="how-does-it-work"></a>Hur fungerar det?
När du aktiverar serverstatiska webbplatser på ditt lagringskonto skapas en ny slutpunkt för webbtjänsten i formuläret `<account-name>.<zone-name>.web.core.windows.net`.

Webbtjänstslutpunkt alltid tillåter anonym läsbehörighet, returnerar formaterade HTML-sidor som svar på service-fel och kan bara objektet läsåtgärder. Webbtjänstslutpunkt returnerar dokumentet index i den begärda katalogen för både roten och alla undermappar. När lagringstjänsten returnerar ett 404-fel, returnerar webbslutpunkten ett anpassat fel dokument om du har konfigurerat.

Innehållet för en statisk webbplats finns i en särskild behållare med namnet ”$web”. Som en del av processen aktivering skapas ”$web” om den inte redan finns. Innehållet i ”$web” kan användas i roten konto med hjälp av webbslutpunkten. Till exempel `https://contoso.z4.web.core.windows.net/` returnerar index-dokumentet som du har konfigurerat för din webbplats om ett dokument med samma namn finns i rotkatalogen för $web.

När du överför innehåll till din webbplats, Använd slutpunkt för blob-lagring. För att överföra en blob med namnet ”image.jpg” som kan användas i roten för kontot för att använda följande URL `https://contoso.blob.core.windows.net/$web/image.jpg`. Överförda bilden kan visas i en webbläsare på motsvarande webbslutpunkten `https://contoso.z4.web.core.windows.net/image.jpg`.


## <a name="custom-domain-names"></a>Egna domännamn
Du kan använda en anpassad domän som värd för ditt webbinnehåll. Om du vill göra det, följer du anvisningarna i [konfigurera ett anpassat domännamn för ditt Azure Storage-konto](storage-custom-domain-name.md). Du hittar din webbplats finns på ett anpassat domännamn via HTTPS i [använder Azure CDN åtkomst till blobbar med anpassade domäner via HTTPS](storage-https-custom-domain-cdn.md).

## <a name="pricing-and-billing"></a>Priser och fakturering
Statisk webbplats värd tillhandahålls utan extra kostnad. Mer information om priser för Azure Blob Storage ta en titt på [Azure Blob Storage sidan prisnivåer](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Snabbstart
### <a name="azure-portal"></a>Azure Portal
Om du vill börja vara värd för ditt webbprogram på Azure Storage, kan du konfigurera funktionen i Azure Portal och klicka på ”statisk webbplats (förhandsgranskning)” under ”inställningar” i det vänstra navigeringsfältet. Klicka på ”aktiverad” och ange namnet på indexet dokumentet och sökväg för anpassade fel dokumentet (valfritt).

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

Ladda upp dina webb-tillgångar till behållaren ”$web” som har skapats som en del av statisk webbplats aktivering. Du kan göra detta direkt i Azure Portal och du kan dra nytta av [Azure Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/) att överföra hela katalogstrukturer. Se till att omfatta en index dokumentet med namn som du har konfigurerat. I det här exemplet är det dokumentnamn ”index.html”.

> [!NOTE]
> Namnet är skiftlägeskänsligt och måste därför exakt matchar namnet på filen i lagring.

Slutligen, navigera till din webbslutpunkt för att testa din webbplats.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
**Finns serverstatiska webbplatser för alla typer av lagring?**  
Nej, statisk webbplats värd finns bara i GPv2 standard storage-konton.

**Är lagring VNET och brandväggsregler som stöds på nya webbslutpunkten?**  
Ja, nya webbslutpunkten följs virtuella nätverk och brandvägg reglerna som konfigurerats för lagringskontot.

## <a name="next-steps"></a>Nästa steg
* [Använder Azure CDN åtkomst till blobbar med anpassade domäner via HTTPS](storage-https-custom-domain-cdn.md)
* [Konfigurera ett anpassat domännamn för din slutpunkt för blob- eller webbserver](storage-custom-domain-name.md)
* [Azure Functions](/azure-functions/functions-overview.md)
* [Azure Web Apps](/app-service/app-service-web-overview.md)
* [Skapa din första serverlösa webbapp](https://aka.ms/static-serverless-webapp)
