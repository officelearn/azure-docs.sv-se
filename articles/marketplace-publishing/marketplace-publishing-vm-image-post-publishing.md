---
title: Hantera din avbildning av virtuell dator i Azure Marketplace | Microsoft Docs
description: "Detaljerad vägledning om hur du hanterar din avbildning av virtuell dator i Azure Marketplace efter första publicering"
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: hascipio;
ms.openlocfilehash: e1f90650e71345957c2d353774cb8bef62c1868b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Släppts guide för erbjudanden som virtuell dator i Azure Marketplace
Den här artikeln förklarar hur du kan uppdatera ett erbjudande för Direktmigrering av virtuell dator i Azure Marketplace. Det hjälper dig genom processen att lägga till en eller flera nya SKU: er i ett befintligt erbjudande. Även guidar dig genom processen för att ta bort ett erbjudande för Direktmigrering av virtuell dator eller SKU från på Marketplace.

När ett erbjudande/SKU mellanlagras i den [Azure-portalen](http://portal.azure.com), du kan inte ändra följande textrutor:

* **Erbjuder identifierare**: I det Publishing portal, gå till **virtuella datorer** och välj erbjudandet. Klicka på **VM-AVBILDNINGAR** > **erbjuder identifierare**.
* **SKU-ID**: I det Publishing portal, gå till **virtuella datorer** och välj erbjudandet. Klicka på **SKU: er** > **lägga till en SKU**.
* **Publisher Namespace**: I det Publishing portal, gå till **virtuella datorer** > **genomgången** > **berätta för oss om ditt företag** (hittas under ”steg 2 registrera företagsnamn”) > **Publisher Namespace** > **Namespace**.

När erbjudandet/SKU ingår i den [Marketplace](http://azure.microsoft.com/marketplace), du kan inte ändra följande textrutor:

* **Erbjuder identifierare**: I det Publishing portal, gå till **virtuella datorer** och välj erbjudandet. Klicka på **VM-AVBILDNINGAR** > **erbjuder identifierare**.
* **SKU-ID**: I det Publishing portal, gå till **virtuella datorer** och välj erbjudandet. Klicka på **SKU: er** > **lägga till en SKU**.
* **Publisher Namespace**: I det Publishing portal, gå till **virtuella datorer** > **genomgången** > **berätta för oss om ditt företag** (hittas under ”steg 2 registrera”) **Publisher Namespace** > **Namespace**.
* **Portar**: I det Publishing portal, gå till **virtuella datorer** och välj erbjudandet. Klicka på **VM-AVBILDNINGAR** > **öppna portar**.
* **Priser för ändring av listan SKU(s)**
* **Fakturering modellen ändring av listan SKU(s)**
* **Borttagning av fakturering områden i listan SKU(s)**
* **Ändring av listan SKU(s) datadiskar**

## <a name="update-the-technical-details-of-a-sku"></a>Uppdatera de tekniska detaljerna för en SKU
Följ dessa steg om du vill lägga till en ny version i listan SKU: N och publicera erbjudandet:

1. Logga in på den [publicering portal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och välj erbjudandet.
3. Klicka på menyn till vänster i **VM-AVBILDNINGAR** fliken.
4. I den **SKU: er** avsnittet, leta upp SKU: N som du vill uppdatera.
5. Lägga till ett nytt versionsnummer för SKU: N och klickar på den  **+**  knappen. Den nya versionen ska vara i ett X.Y.Z format, där X, Y och Z är heltal. Bara ska version ändringar inkrementell.
6. I den **OS VHD URL** anger signatur för delad åtkomst URI som skapats för operativsystemet VHD och spara ändringarna.

   > [!IMPORTANT]
   > Du kan inte öka/minska datadiskar för en listade SKU: N. Du måste skapa en ny SKU i det här fallet. Detaljerad information finns i avsnittet [lägga till en ny SKU under listan erbjudande](#add-a-new-sku-under-a-listed-offer).
   >
   >
7. Gå till den **publicera** och på **PUSH till FÖRPRODUKTION**. Detaljerad information om hur du testar erbjudandet i mellanlagringsmiljön finns [testa VM erbjudandet på Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. När du har testat erbjudandet i Förproduktion, gå till den **publicera** fliken i publicering portal. Klicka på **begär godkännande att PUSH till produktion** publicera om erbjudandet på Marketplace.

    ![VM-avbildningar](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="update-the-nontechnical-details-of-an-offer-or-a-sku"></a>Uppdatera sökassistent information om ett erbjudande eller en SKU
Du kan uppdatera den sökassistent (marknadsföring, juridiska, hantera, kategorier) information om din live erbjudande eller SKU på Marketplace.

### <a name="update-the-offer-description-and-logos"></a>Uppdatera erbjudande beskrivning och logotyper
Följ dessa steg för att uppdatera information om erbjudandet och publicera erbjudandet:

1. Logga in på den [publicering portal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och välj erbjudandet.
3. Klicka på menyn till vänster i **MARKNADSFÖRING** fliken.
4. Klicka på **engelska (USA)**.
5. Klicka på den **information** fliken. I den **beskrivning** avsnittet, uppdatera erbjudandet **rubrik**, **sammanfattning**, och **LÅNG sammanfattning** och spara ändringarna.

   > [!NOTE]
   > När du uppdaterar SKU-information kan du vara medveten om dessa begränsningar: 
   * Ange inte dubbla text för erbjudande beskrivning och SKU-beskrivning.
   * Ange inte dubbla text för SKU-rubrik och lång sammanfattning erbjudandet. 
   * Ange inte dubbla text för SKU-rubrik och sammanfattning för erbjudande.
   >
   >

    ![Information](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. I den **logotyper** avsnitt i den **information** uppdaterar logotyperna. Se till att logotyperna följer den [riktlinjer för Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).

   > [!NOTE]
   > Ikon för hjälte är valfritt. Du kan välja att inte ladda upp en hjälte ikon. Men när en hjälte ikon har överförts skall inte ta bort det från publicering portal. Följ den [hjälte ikonen riktlinjer](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
   >
   >
7. Gå till den **publicera** och på **PUSH till FÖRPRODUKTION**. Detaljerad information om hur du testar erbjudandet i mellanlagringsmiljön finns [testa VM erbjudandet på Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. När du har testat erbjudandet i Förproduktion, gå till den **publicera** fliken i publicering portal. Klicka på **begär godkännande att PUSH till produktion** publicera om erbjudandet på Marketplace.

    ![Logotyper](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="update-the-sku-description"></a>Uppdatera SKU-beskrivning
Följ dessa steg om du vill uppdatera SKU-information och publicera erbjudandet:

1. Logga in på den [publicering portal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och välj erbjudandet.
3. Klicka på menyn till vänster i **MARKNADSFÖRING** fliken.
4. Klicka på **engelska (USA)**.
5. Klicka på den **planer** fliken. I den **SKU: er** avsnittet, uppdatera SKU: N **rubrik**, **sammanfattning**, och **beskrivning** och spara ändringarna.

   > [!NOTE]
   > När du uppdaterar SKU-information kan du vara medveten om dessa begränsningar: 
   * Ange inte dubbla text för erbjudande beskrivning och SKU-beskrivning. 
   * Ange inte dubbla text för SKU-rubrik och lång sammanfattning erbjudandet. 
   * Ange inte dubbla text för SKU-rubrik och sammanfattning för erbjudande.
   >
   >
6. Gå till den **publicera** och på **PUSH till FÖRPRODUKTION**. Detaljerad information om hur du testar erbjudandet i mellanlagringsmiljön finns [testa VM erbjudandet på Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. När du har testat erbjudandet i Förproduktion, gå till den **publicera** fliken i publicering portal. Klicka på **begär godkännande att PUSH till produktion** publicera om erbjudandet på Marketplace.

    ![Planer](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="change-existing-links-or-add-new-links"></a>Ändra befintliga länkar eller lägga till nya länkar
Om du vill ändra befintliga länkar eller lägga till nya länkar och publicera erbjudandet, gör du följande:

1. Logga in på den [publicering portal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och välj erbjudandet.
3. Klicka på menyn till vänster i **MARKNADSFÖRING** fliken.
4. Klicka på **engelska (USA)**.
5. Klicka på den **länkar** fliken.
6. Lägga till en ny länk i den **länkar** klickar du på **+ Lägg till länk**. I den **lägga till länken** dialogrutan Ange länken **rubrik** och **URL** och spara ändringarna. Du kan ange en länk som innehåller information som kan hjälpa kunderna.
7. Klicka på länken om du vill uppdatera eller ta bort en befintlig länk, och klicka på den **redigera** knappen eller **ta bort** knappen.

   > [!NOTE]
   > Se till att länkar som du har angett i det här avsnittet fungerar korrekt, eftersom dessa länkar hämta verifieras under din begäran produktionsprocess.
   >
   >
8. Gå till den **publicera** och på **PUSH till FÖRPRODUKTION**. Detaljerad information om hur du testar erbjudandet i mellanlagringsmiljön finns [testa VM erbjudandet på Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
9. När du har testat erbjudandet i Förproduktion, gå till den **publicera** fliken i publicering portal. Klicka på **begär godkännande att PUSH till produktion** publicera om erbjudandet på Marketplace.

    ![Länkar](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![Lägga till en länk](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="change-an-existing-sample-image-or-add-a-new-sample-image"></a>Ändra en befintlig exempelbild eller lägga till en ny exempelbild
Om du vill ändra en befintlig exempelbild eller lägga till nya exempel bilder och sedan publicera erbjudandet, gör du följande:

> [!NOTE]
> Endast en exempelbild visas i den [Azure-portalen](https://portal.azure.com).
>
>

1. Logga in på den [publicering portal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och välj erbjudandet.
3. Klicka på menyn till vänster i **MARKNADSFÖRING** fliken.
4. Klicka på **engelska (USA)**.
5. Klicka på den **exempel bilder** fliken.
6. Att lägga till en ny avbildning i exemplet i det **exempel bilder** klickar du på **ladda upp en ny avbildning** och spara ändringarna.

   > [!NOTE]
   > Inklusive en exempelbild är valfritt.
   >
7. Gå till den **publicera** och på **PUSH till FÖRPRODUKTION**. Detaljerad information om hur du testar erbjudandet i mellanlagringsmiljön finns [testa VM erbjudandet på Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. När du har testat erbjudandet i Förproduktion, gå till den **publicera** fliken i publicering portal. Klicka på **begär godkännande att PUSH till produktion** publicera om erbjudandet på Marketplace.

    ![Exempel bilder](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="update-the-legal-content"></a>Uppdatera juridiska innehåll
Följ dessa steg om du vill uppdatera juridiska innehållet och publicera erbjudandet:

1. Logga in på den [publicering portal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och välj erbjudandet.
3. Klicka på menyn till vänster i **MARKNADSFÖRING** fliken.
4. Klicka på **engelska (USA)**.
5. Klicka på den **JURIDISKA** fliken. I den **juridiska** avsnittet, uppdatera principer/villkor för användning. Skriv eller klistra in principer/villkoren i den **ANVÄNDNINGSVILLKOREN** och spara ändringarna.
6. Maximalt antal tillåtna tecken för de juridiska villkoren för användning är 1 miljon tecken.
7. Gå till den **publicera** och på **PUSH till FÖRPRODUKTION**. Detaljerad information om hur du testar erbjudandet i mellanlagringsmiljön finns [testa VM erbjudandet på Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. När du har testat erbjudandet i Förproduktion, gå till den **publicera** fliken i publicering portal. Klicka på **begär godkännande att PUSH till produktion** publicera om erbjudandet på Marketplace.

    ![Juridisk information](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="update-the-support-information"></a>Uppdatera supportinformation
Följ dessa steg om du vill uppdatera supportinformation och publicera erbjudandet:

1. Logga in på den [publicering portal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och välj erbjudandet.
3. Klicka på menyn till vänster i **stöd** fliken.
4. I den **Engineering Kontakta** avsnittet, uppdatera engineering kontaktinformation. Denna information används för intern kommunikation mellan partner och Microsoft endast.
5. I den **kundsupport** avsnittet, uppdatera kontaktinformation för support och **stöder URL**. Denna information används för intern kommunikation mellan partner och Microsoft endast.

   > [!NOTE]
   > Om du vill stödja endast e-post, ange ett dummy telefonnummer i den **kundsupport** avsnitt. I det här fallet används den e-postadress som du angav i stället.
   >
   >
6. Gå till den **publicera** och på **PUSH till FÖRPRODUKTION**. Detaljerad information om hur du testar erbjudandet i mellanlagringsmiljön finns [testa VM erbjudandet på Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. När du har testat erbjudandet i Förproduktion, gå till den **publicera** fliken i publicering portal. Klicka på **begär godkännande att PUSH till produktion** publicera om erbjudandet på Marketplace.

    ![Support](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="update-the-categories"></a>Uppdatera kategorier
Följ dessa steg om du vill uppdatera avsnittet kategorier för erbjudandet och publicera erbjudandet:

1. Logga in på den [publicering portal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och välj erbjudandet.
3. Klicka på menyn till vänster i **kategorier** fliken.
4. I den **kategorier** avsnittet, uppdatera kategorier för erbjudandet och spara ändringarna. Du kan välja upp till fem kategorier för Azure Marketplace-galleriet.
5. Gå till den **publicera** och på **PUSH till FÖRPRODUKTION**. Detaljerad information om hur du testar erbjudandet i mellanlagringsmiljön finns [testa VM erbjudandet på Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
6. När du har testat erbjudandet i Förproduktion, gå till den **publicera** fliken i publicering portal. Klicka på **begär godkännande att PUSH till produktion** publicera om erbjudandet på Marketplace.

    ![Kategorier](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="add-a-new-sku-under-a-listed-offer"></a>Lägg till en ny SKU under listan erbjudande
Följ dessa steg för att lägga till en ny SKU i erbjudandet live:

1. Logga in på den [publicering portal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och välj erbjudandet.
3. Klicka på menyn till vänster i **SKU: er** fliken. Klicka på **lägga till en SKU**. 
4. I dialogrutan anger du en **SKU-ID** med små bokstäver. Välj den **ta egen licens (BYOL) faktureringsmodellen** kryssruta om du vill publicera den nya SKU: N med en BYOL faktureringsmodell som tillämpas. Annars avmarkerar du kryssrutan. Klicka om du vill skapa en ny SKU skalstreck. Om du inte väljer BYOL fakturering modellen anges vilken faktureringsmodell som tillämpas automatiskt till varje timme. Om du vill 30-dagars utvärderingsversion för varje timme fakturering modellen väljer **en månad** för **finns en kostnadsfri utvärderingsversion?** Annars väljer **Nej utvärderingsversion**. (**Finns en kostnadsfri utvärderingsversion?**  visas bara om du inte har valt BYOL när du skapar nya SKU: N.)

   > [!IMPORTANT]
   > **Dölj den här SKU från Marketplace eftersom alltid ska köpas via en lösningsmall** ska vara **Ja** *endast* om du har godkänt för att publicera en lösningsmall. Annars kan det här alternativet ska alltid vara **nr**.
   >
   >
4. Klicka på menyn till vänster i **VM-AVBILDNINGAR** fliken och ta reda på den nya SKU: N som du har skapat.
5. Om du vill konfigurera nya SKU: N finns [erhålla certifikat för VM-avbildning](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) anvisningar.
6. Om du vill lägga till marknadsföringsmaterial för nya SKU: N, se [ange Marketplace marknadsföring innehåll](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Om du vill lägga till prisinformation för nya SKU: N, se [ange dina priser](marketplace-publishing-push-to-staging.md#step-2-set-your-prices).
8. Gå till den **publicera** och på **PUSH till FÖRPRODUKTION**. Detaljerad information om hur du testar erbjudandet i mellanlagringsmiljön finns [testa VM erbjudandet på Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
9. När du har testat erbjudandet i Förproduktion, gå till den **publicera** fliken i publicering portal. Klicka på **begär godkännande att PUSH till produktion** publicera om erbjudandet på Marketplace.

    ![SKU: er](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![Lägg till en SKU](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="change-the-data-disk-count-for-a-listed-sku"></a>Ändra listan SKU datadiskar
Du kan inte öka/minska datadiskar för en listade SKU: N. Du måste skapa en ny SKU i det här fallet. Detaljerad information finns i avsnittet [lägga till en ny SKU under listan erbjudande](#add-a-new-sku-under-a-listed-offer).

## <a name="delete-a-listed-offer-from-the-marketplace"></a>Ta bort ett listade erbjudande från Marketplace
Olika aspekter måste typen för en begäran om att ta bort en levande erbjudandet. Följ dessa steg för att få information från supportteamet att ta bort ett listade erbjudande från Marketplace:

1. Generera ett supportärende på den [skapa en incident](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681) sidan.

2. Välj **problemtyp** som **hantera erbjudanden**, och välj **kategori** som **ändra ett erbjudande och/eller SKU redan i produktion**.
3. Förfrågan.

Support-teamet hjälper dig att borttagningsprocessen erbjudandet/SKU.

> [!NOTE]
> Du kan alltid ta bort erbjudandet när den är i statusen utkast (men inte mellanlagring eller produktion). På den **HISTORIK** klickar du på **KASTA utkast**.
>
>

## <a name="delete-a-listed-sku-from-the-marketplace"></a>Ta bort en listade SKU från Marketplace
Följ dessa steg för att ta bort en listade SKU från Marketplace:

1. Logga in på den [publicering portal](https://publish.windowsazure.com).

2. Gå till den **virtuella datorer** och välj erbjudandet.
3. I rutan till vänster, klicka på den **SKU: er** fliken.
4. Välj SKU: N som du vill ta bort och klicka på den **ta bort** knappen.
5. Gå till den **publicera** fliken i publicering portal. Klicka på **begär godkännande att PUSH till produktion** publicera erbjudande på Marketplace.
6. När erbjudandet publiceras i Marketplace tas SKU: N bort från Marketplace och Azure portal.

## <a name="delete-the-current-version-of-a-listed-sku-from-the-marketplace"></a>Ta bort den aktuella versionen av listan SKU från Marketplace
Följ dessa steg för att ta bort den aktuella versionen av listan SKU från Marketplace: 

1. Logga in på den [publicering portal](https://publish.windowsazure.com).

2. Gå till den **virtuella datorer** och välj erbjudandet.
3. Klicka på menyn till vänster i **VM-AVBILDNINGAR** fliken.
4. Välj SKU vars aktuella versionen som du vill ta bort och klicka på den **ta bort** knappen.
5. Gå till den **publicera** fliken i publicering portal. Klicka på **begär godkännande att PUSH till produktion** publicera erbjudande på Marketplace.
6. När erbjudandet hämtar publiceras i Marketplace bort den aktuella versionen av den angivna SKU: N från Marketplace och Azure portal. SKU: N återställs sedan till en tidigare version.

## <a name="revert-the-listing-price-to-production-values"></a>Återställa lista priset till produktion värden
Följ dessa steg om du vill återställa lista priset till produktion värden:

1. Logga in på den [publicering portal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och välj erbjudandet.
3. Klicka på menyn till vänster i **priser** fliken.
4. Välj en region vars priser som du vill återställa.

    ![Priser regioner](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. Återställa priser för alla kärnor för SKU: er med en timme fakturering modell, som i produktion för den valda regionen. För SKU: er med en BYOL fakturering modell tillgängliggöra SKU: N i region genom att markera kryssrutan för SKU: N i den **EXTERNALLY-LICENSED (BYOL) SKU tillgänglighet** avsnitt.

    ![Prissättningsmodeller](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. Klicka på **AUTOPRICE andra marknader utifrån priser i USA**.

   > [!NOTE]
   > Knappens etikett kan vara olika beroende på region som du väljer. Eftersom vi valde USA knappen är märkt **AUTOPRICE andra marknader baserat på priser i UNITED STATES**.
   >
   >

    ![Autoprice](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. På sidan 1 i guiden Autoprice, Välj den grundläggande marknaden och klicka på den **pilen** knappen.

    ![Grundläggande marknaden](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. På sidan 2, väljer serviceplaner och mätare (kärnor) och klicka på den **pilen** knappen.

    ![Tjänsten planer och mätare](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. Klicka på sidan 3 **växla alla** att välja alla regioner. Du kan manuellt ange kryssrutorna för vissa regioner. Klicka på den **pilen** knappen.

    ![Välj marknader](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. På 4 granskar du de exchange-satser och klicka på **Slutför**. Guiden återställer priser enligt dina val.

11. På den **priser** klickar du på **visa sammanfattning och ändringar**.
    För **Visa Version**väljer **utkast**, och för **Jämför med**väljer **produktion**. Om du ser inga prisnivå skillnaden återställts prissättning till produktion-värden.

    ![Visa sammanfattning och ändringar](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. Gå till den **publicera** och på **PUSH till FÖRPRODUKTION**. Detaljerad information om hur du testar erbjudandet i mellanlagringsmiljön finns [testa VM erbjudandet på Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
13. När du har testat erbjudandet i Förproduktion, gå till den **publicera** fliken i publicering portal. Klicka på **begär godkännande att PUSH till produktion** publicera om erbjudandet på Marketplace.

## <a name="revert-the-billing-model-to-production-values"></a>Återställa fakturering modellen till produktion värden
Följ dessa steg om du vill återställa fakturering modellen till produktion värden:

1. Logga in på den [publicering portal](https://publish.windowsazure.com).

2. Gå till den **virtuella datorer** och välj erbjudandet.
3. Klicka på menyn till vänster i **SKU: er** fliken.
4. Klicka på den **redigera** för att återställa vilken faktureringsmodell som tillämpas. I fönstret som öppnas, markera eller avmarkera den **debitering och licensiering görs externt från Azure (aka Bring Your Own License)** kryssrutan.

    ![Redigera fakturering](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. Följ stegen i ”Återställ lista priset till värden för produktion” i den här artikeln.
6. Gå till den **publicera** och på **PUSH till FÖRPRODUKTION**. Detaljerad information om hur du testar erbjudandet i mellanlagringsmiljön finns [testa VM erbjudandet på Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. När du har testat erbjudandet i Förproduktion, gå till den **publicera** fliken i publicering portal. Klicka på **begär godkännande att PUSH till produktion** publicera om erbjudandet på Marketplace.

## <a name="revert-the-visibility-setting-of-a-listed-sku-to-the-production-value"></a>Återställa inställningar för en listade SKU att produktionsvärdet för
Följ dessa steg om du vill återställa inställningar för en listade SKU till produktionsvärde:

1. Logga in på den [publicering portal](https://publish.windowsazure.com).

2. Gå till den **virtuella datorer** och välj erbjudandet.
3. Klicka på menyn till vänster i **SKU: er** fliken.
4. Välj din SKU och återställa inställningar för SKU: N till produktionsvärde.

    ![Synlighet](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. När du är klar med ändringarna klickar du på **begär godkännande att PUSH till produktion** publicera om erbjudandet på Marketplace.

## <a name="see-also"></a>Se även
* [Komma igång: Publicera ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md)
* [Förstå beloppet reporting](marketplace-publishing-report-payout.md)
* [Ändra din leverantör av Molnlösningar återförsäljare morot](marketplace-publishing-csp-incentive.md)
* [Felsöka vanliga problem i Marketplace](marketplace-publishing-support-common-issues.md)
* [Få support som en utgivare](marketplace-publishing-get-publisher-support.md)
* [Skapa en VM-avbildning lokalt](marketplace-publishing-vm-image-creation-on-premise.md)
* [Skapa en virtuell dator som kör Windows i Azure preview portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
