---
title: Integrera en statisk webbplats med Azure CDN - Azure Storage
description: Lär dig hur du cachelagrar statiskt webbplatsinnehåll från ett Azure Storage-konto med hjälp av CDN (Azure Content Delivery Network).
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 04/07/2020
ms.openlocfilehash: 4516e9f48174a0f1f5201c46cf114badf13d99d6
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878837"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Integrera en statisk webbplats med Azure CDN

Du kan aktivera [CDN (Azure Content Delivery Network)](../../cdn/cdn-overview.md) för att cachelagra innehåll från en [statisk webbplats](storage-blob-static-website.md) som finns i ett Azure-lagringskonto. Du kan använda Azure CDN för att konfigurera den anpassade domänslutpunkten för din statiska webbplats, etablera anpassade TLS/SSL-certifikat och konfigurera anpassade omskrivningsregler. Konfiguration av Azure CDN resulterar i ytterligare avgifter men ger konsekvent korta svarstider till din webbplats från hela världen. Azure CDN tillhandahåller också TLS-kryptering med ditt eget certifikat. 

Information om priser för Azure CDN finns i [Azure CDN-priser](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="enable-azure-cdn-for-your-static-website"></a>Aktivera Azure CDN för din statiska webbplats

Du kan aktivera Azure CDN för din statiska webbplats direkt från ditt lagringskonto. Om du vill ange avancerade konfigurationsinställningar för din CDN-slutpunkt, som [optimering av nedladdning av stora filer](../../cdn/cdn-optimization-overview.md#large-file-download), kan du istället använda [Azure CDN-tillägget](../../cdn/cdn-create-new-endpoint.md) till att skapa en CDN-profil och en slutpunkt.

1. Leta reda på ditt lagringskonto i Azure-portalen och visa kontoöversikten.

1. Under **Blob-tjänstmenyn** väljer du **Azure CDN** för att öppna Sidan **Azure CDN:**

    ![Skapa en CDN-slutpunkt](media/storage-blob-static-website-custom-domain/cdn-storage-new.png)

1. I avsnittet **CDN-profil** anger du om du vill skapa en ny CDN-profil eller använda en befintlig. En CDN-profil är en samling CDN-slutpunkter som delar en prisnivå och leverantör. Ange sedan ett namn på CDN som är unikt i din prenumeration.

1. Ange en prisnivå för CDN-slutpunkten. Mer information om priser finns i [Priser för Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/). Mer information om de funktioner som är tillgängliga för varje nivå finns i [Jämför Azure CDN-produktfunktioner](../../cdn/cdn-features.md).

1. Ange ett namn på CDN-slutpunkten i **namnfältet CDN.** CDN-slutpunkten måste vara unik i Azure och tillhandahåller den första delen av slutpunkts-URL:en. Formuläret verifierar att slutpunktsnamnet är unikt.

1. Ange din statiska webbplatsslutpunkt i fältet **Ursprungsvärdnamn.** 

   Om du vill hitta din statiska webbplatsslutpunkt navigerar du till de **statiska webbplatsinställningarna** för ditt lagringskonto.  Kopiera den primära slutpunkten och klistra in den i CDN-konfigurationen.

   > [!IMPORTANT]
   > Se till att ta bort protokollidentifieraren *(t.ex.* HTTPS) och det efterföljande snedstrecket i URL:en. Om till exempel den statiska `https://mystorageaccount.z5.web.core.windows.net/`webbplatsslutpunkten `mystorageaccount.z5.web.core.windows.net` är , skulle du ange i fältet **Ursprungsvärdnamn.**

   Följande bild visar en exempelslutpunktskonfiguration:

   ![Skärmbild som visar konfigurationen av CDN-slutpunkter](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Välj **Skapa**och vänta sedan på att CDN ska etableras. När slutpunkten har skapats visas den i listan med slutpunkter. (Om du har några fel i formuläret visas ett utropstecken bredvid fältet.)

1. Om du vill kontrollera att CDN-slutpunkten är korrekt konfigurerad klickar du på slutpunkten för att navigera till dess inställningar. Från CDN-översikten för ditt lagringskonto letar du reda på slutpunktsvärden och navigerar till slutpunkten, som visas i följande bild. Formatet på CDN-slutpunkten liknar `https://staticwebsitesamples.azureedge.net`.

    ![Skärmbild som visar översikt över CDN-slutpunkten](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

1. När CDN-slutpunkten har etablerats visas innehållet i filen index.html som du tidigare har laddat upp till den statiska webbplatsen genom att navigera till CDN-slutpunkten.

1. Om du vill granska ursprungsinställningarna för CDN-slutpunkten navigerar du till **Origin** under avsnittet **Inställningar** för CDN-slutpunkten. Du kommer att se att fältet **Ursprungstyp** är inställt på *Anpassat ursprung* och att fältet **Ursprungsvärdnamn** visar din statiska webbplatsslutpunkt.

    ![Skärmbild som visar Origin-inställningar för CDN-slutpunkt](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Ta bort innehåll från Azure CDN

Om du inte längre vill cachelagra ett objekt i Azure CDN kan du vidta något av följande steg:

* Gör containern privat i stället för offentlig. Mer information finns i [Hantera anonym läsåtkomst till behållare och blobbar](storage-manage-access-to-resources.md).
* Inaktivera eller ta bort CDN-slutpunkten med hjälp av Azure Portal.
* Ändra din värdbaserade tjänst så att den inte längre svarar på förfrågningar för objektet.

Ett objekt som redan har cachelagrats i Azure CDN förblir cachelagrat tills objektets Time to Live-period har löpt ut eller tills slutpunkten har [rensats](../../cdn/cdn-purge-endpoint.md). När Time to Live-perioden har löpt ut avgör Azure CDN om CDN-slutpunkten fortfarande är giltigt och om objektet fortfarande är tillgängligt anonymt. Om så inte är fallet kommer objektet inte längre att cachelagras.

## <a name="next-steps"></a>Nästa steg

(Valfritt) Lägg till en anpassad domän till din Azure CDN-slutpunkt. Se [självstudiekurs: Lägg till en anpassad domän i din Azure CDN-slutpunkt](../../cdn/cdn-map-content-to-custom-domain.md).
