---
title: Värd för statisk webbplats i Azure Storage (förhandsversion) | Microsoft Docs
description: Azure Storage erbjuder nu statisk webbplats som är värd för (förhandsversion), ger en kostnadseffektiv och skalbar lösning som värd för moderna webbprogram.
services: storage
author: MichaelHauss
ms.service: storage
ms.topic: article
ms.date: 08/17/18
ms.author: mihauss
ms.component: blobs
ms.openlocfilehash: 65a1cd85baf18ac1f0d193e7e6d6c3139919fb59
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617405"
---
# <a name="static-website-hosting-in-azure-storage-preview"></a>Värd för statisk webbplats i Azure Storage (förhandsversion)
Azure Storage erbjuder nu statisk webbplats som är värd för (förhandsversion), så att du kan distribuera kostnadseffektivt och skalbart moderna webbprogram på Azure. På en statisk webbplats innehåller webbsidor statiskt innehåll och JavaScript eller andra klientkod. Däremot dynamiska webbplatser beroende serverkod och kan hanteras med hjälp av [Azure Web Apps](/azure/app-service/app-service-web-overview).

Eftersom distributioner SKIFT mot elastisk, kostnadseffektiv modeller, är möjligheten att leverera webbinnehåll utan behovet av serverhantering kritiska. Introduktionen av värd för statisk webbplats i Azure Storage gör det möjligt att aktivera omfattande serverdelsfunktioner med arkitekturer utan server att använda [Azure Functions](/azure/azure-functions/functions-overview) och andra PaaS-tjänster.

## <a name="how-does-it-work"></a>Hur fungerar det?
När du aktiverar serverstatiska webbplatser på ditt lagringskonto, skapas en ny slutpunkt för webbtjänsten formulärets `<account-name>.<zone-name>.web.core.windows.net`.

Web service-slutpunkt alltid tillåter anonym läsbehörighet, returnerar formaterade HTML-sidor som svar på fel i tjänsten, och tillåter endast de objekt som Läs-och skrivåtgärder. Web service-slutpunkt returnerar index dokumentet i den begärda katalogen för både roten och alla underkataloger. När tjänsten storage returnerar ett 404-fel, returnerar webbslutpunkt ett anpassat fel dokument om du har konfigurerat den.

Innehåll för din statiska webbplats finns i en särskild behållare med namnet ”$web”. Som en del av processen aktivering har ”$web” skapats för dig om den inte redan finns. Innehållet i ”$web” kan nås i roten konto med hjälp av webbslutpunkt. Till exempel `https://contoso.z4.web.core.windows.net/` returnerar index-dokumentet som du har konfigurerat för din webbplats, om ett dokument med det namnet finns i rotkatalogen för $web.

När du överför innehåll till din webbplats, Använd blob storage-slutpunkten. Ladda upp en blob med namnet ”image.jpg” som kan användas i roten konto använder följande URL `https://contoso.blob.core.windows.net/$web/image.jpg`. Den uppladdade bilden kan visas i en webbläsare på motsvarande webbslutpunkt `https://contoso.z4.web.core.windows.net/image.jpg`.


## <a name="custom-domain-names"></a>Egna domännamn
Du kan använda en anpassad domän som värd för ditt webbinnehåll. Om du vill göra det, följer du riktlinjerna i [konfigurera ett anpassat domännamn för Azure Storage-kontot](storage-custom-domain-name.md). Du hittar din webbplats på ett anpassat domännamn via HTTPS i [använda Azure CDN för att få åtkomst till blobar med anpassade domäner över HTTPS](storage-https-custom-domain-cdn.md). Peka CDN-nätverket till webbslutpunkt till skillnad från blob-slutpunkten och Kom ihåg att CDN konfiguration inte ske omedelbart, så du kan behöva vänta några minuter innan innehållet är synligt.

## <a name="pricing-and-billing"></a>Priser och fakturering
Som är värd för statisk webbplats tillhandahålls utan extra kostnad. Mer information om priser för Azure Blob Storage finns i [Prissättningssidan för Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Snabbstart
### <a name="azure-portal"></a>Azure Portal
Om du inte redan gjort [skapa ett GPv2-lagringskonto](../common/storage-quickstart-create-account.md) om du vill börja vara värd för ditt webbprogram, du kan konfigurera funktionen i Azure Portal och klicka på ”statisk webbplats (förhandsversion)” under ”inställningar” i det vänstra navigeringsfältet. Klicka på ”aktiverad” och ange namnet på indexet dokumentet och (frivilligt) anpassade feldokumentets sökväg.

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

Ladda upp dina web-tillgångar till behållaren ”$web” som har skapats som en del av statisk webbplats aktivering. Du kan göra detta direkt i Azure Portal eller du kan dra nytta av [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) att ladda upp hela katalogstrukturer. Se till att inkludera ett index-dokument med namnet som du har konfigurerat. I det här exemplet är det dokumentets namn ”index.html”.

> [!NOTE]
> Dokumentnamnet är skiftlägeskänsligt och måste därför exakt matchar namnet på filen i lagring.

Slutligen navigera till din webbslutpunkt för att testa din webbplats.

### <a name="azure-cli"></a>Azure CLI
Installera tillägget storage förhandsversion:

```azurecli-interactive
az extension add --name storage-preview
```
Aktivera funktionen:

```azurecli-interactive
az storage blob service-properties update --account-name <account-name> --static-website --404-document <error-doc-name> --index-document <index-doc-name>
```
Fråga för web slutpunkts-URL:

```azurecli-interactive
az storage account show -n <account-name> -g <resource-group> --query "primaryEndpoints.web" --output tsv
```

Överföra objekt till $web behållaren:

```azurecli-interactive
az storage blob upload-batch -s deploy -d $web --account-name <account-name>
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
**Är serverstatiska webbplatser tillgänglig för alla typer av konton?**  
Nej, som är värd för statisk webbplats är endast tillgänglig i GPv2-konton för standardlagring.

**Är lagring VNET och brandväggsregler som stöds på den nya web-slutpunkten?**  
Ja, den nya webbslutpunkt följs virtuellt nätverk och brandvägg reglerna som konfigurerats för lagringskontot.

**Är webbslutpunkt skiftlägeskänsligt?**  
Ja, web-slutpunkt är skiftlägeskänsligt precis som blob-slutpunkten. 

## <a name="next-steps"></a>Nästa steg
* [Använda Azure CDN för att få åtkomst till blobar med anpassade domäner över HTTPS](storage-https-custom-domain-cdn.md)
* [Konfigurera ett anpassat domännamn för din blob eller web-slutpunkt](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure Web Apps](/azure/app-service/app-service-web-overview)
* [Skapa din första serverlösa webbapp](https://aka.ms/static-serverless-webapp)
