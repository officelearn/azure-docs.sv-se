---
title: Hantera din avbildning av virtuell dator på Azure Marketplace | Microsoft Docs
description: Detaljerad vägledning om hur du hanterar din avbildning av virtuell dator på Azure Marketplace efter första publicering
services: Azure Marketplace
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: hascipio;
ms.openlocfilehash: d4c7dce1876e9838fe986aebb7e38a09e8a82baf
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51252980"
---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Efter produktion guide för virtuell dator-erbjudanden på Azure Marketplace
Den här artikeln förklarar hur du kan uppdatera en Direktmigrering av virtuell dator-erbjudande på Azure Marketplace. Den vägleder dig genom processen att lägga till en eller flera nya SKU: er till ett befintligt erbjudande. Även vägleder dig genom processen med att ta bort ett erbjudande för Direktmigrering av virtuell dator eller SKU: N från Marketplace.

När ett erbjudande/SKU mellanlagras i den [Azure-portalen](http://portal.azure.com), du kan inte ändra följande textrutor:

* **Erbjuder identifierare**: I den Publisher-portalen, gå till **virtuella datorer** och välj ditt erbjudande. Klicka sedan på **VM-AVBILDNINGAR** > **erbjuder identifierare**.
* **SKU-identifierare**: I den Publisher-portalen, gå till **virtuella datorer** och välj ditt erbjudande. Klicka sedan på **SKU: er** > **Lägg till en SKU**.
* **Publisher-Namespace**: I den Publisher-portalen, gå till **virtuella datorer** > **genomgången** > **berätta för oss om ditt företag**(finns under ”steg 2 registrera ditt företag”) > **Publisher Namespace** > **Namespace**.

När erbjudande/SKU har listats i den [Marketplace](https://azure.microsoft.com/marketplace), du kan inte ändra följande textrutor:

* **Erbjuder identifierare**: I den Publisher-portalen, gå till **virtuella datorer** och välj ditt erbjudande. Klicka sedan på **VM-AVBILDNINGAR** > **erbjuder identifierare**.
* **SKU-identifierare**: I den Publisher-portalen, gå till **virtuella datorer** och välj ditt erbjudande. Klicka sedan på **SKU: er** > **Lägg till en SKU**.
* **Publisher-Namespace**: I den Publisher-portalen, gå till **virtuella datorer** > **genomgången** > **berätta för oss om ditt företag**(finns under ”steg 2 registrera”) **Publisher Namespace** > **Namespace**.
* **Portar**: I den Publisher-portalen, gå till **virtuella datorer** och välj ditt erbjudande. Klicka sedan på **VM-AVBILDNINGAR** > **öppnar du portar**.
* **Priser för ändring av listan SKU: er**
* **Fakturering modelländringen för angivna SKU: er**
* **Borttagning av fakturering regioner för angivna SKU: er**
* **Ändra datadiskar för angivna SKU: er**

## <a name="update-the-technical-details-of-a-sku"></a>Uppdatera de tekniska detaljerna för en SKU
Om du vill lägga till en ny version i den angivna SKU: N och publicera ditt erbjudande, Följ dessa steg:

1. Logga in på den [Publiceringsportal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och sedan ditt erbjudande.
3. I menyn till vänster, klickar du på den **VM-AVBILDNINGAR** fliken.
4. I den **SKU: er** avsnittet, leta upp den SKU som du vill uppdatera.
5. Lägg till ett nytt versionsnummer för SKU: N och klickar på den **+** knappen. Den nya versionen ska vara i ett X.Y.Z-format, där X, Y och Z är heltal. Ändringar av version ska endast inkrementell.
6. I den **OS VHD URL** anger signaturen för delad åtkomst URI: N som skapats för det virtuella Hårddisken för operativsystemet och spara ändringarna.

   > [!IMPORTANT]
   > Du kan inte öka/minska datadiskar med en listade SKU: N. Du behöver skapa en ny SKU i det här fallet. Detaljerad information finns i avsnittet [lägga till en ny SKU under en listade erbjudandet](#add-a-new-sku-under-a-listed-offer).
   >
   >
7. Gå till den **publicera** och på **mellanlagring PUSH TO STAGING**. Detaljerad vägledning om hur du testar erbjudandet i mellanlagringsmiljön finns i [testa ditt erbjudande för virtuell dator för Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. När du har testat ditt erbjudande i mellanlagringen, går du till den **publicera** flik i publiceringen portal. Klicka på **begära godkännande att skicka till produktion** att publicera ditt erbjudande i Marketplace.

    ![VM-avbildningar](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="update-the-nontechnical-details-of-an-offer-or-a-sku"></a>Uppdatera annan information om ett erbjudande eller en SKU
Du kan uppdatera den sökassistent (marknadsföring, juridiska, stöd, kategorier) information om din live erbjudandet eller SKU i Marketplace.

### <a name="update-the-offer-description-and-logos"></a>Uppdatera beskrivning av erbjudande och logotyper
Om du vill uppdatera informationen i erbjudandet och publicera ditt erbjudande, Följ dessa steg:

1. Logga in på den [Publiceringsportal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och sedan ditt erbjudande.
3. I menyn till vänster, klickar du på den **MARKNADSFÖRING** fliken.
4. Klicka på **engelska (USA)**.
5. Klicka på den **information** fliken. I den **beskrivning** avsnittet, uppdatera erbjudandet **rubrik**, **sammanfattning**, och **LÅNG sammanfattning** och spara ändringarna.

   > [!NOTE]
   > När du uppdaterar SKU-information, Tänk på dessa begränsningar: 
   * Ange inte dubbla text för beskrivning av erbjudande och SKU-beskrivning.
   * Ange inte dubbla text för SKU-rubrik och lång sammanfattning av erbjudandet. 
   * Ange inte dubbla text för SKU-rubrik och sammanfattning av erbjudandet.
   >
   >

    ![Information](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. I den **logotyper** delen av den **information** fliken, uppdatera logotyperna. Se till att logotyperna följer den [för Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).

   > [!NOTE]
   > En hero-ikon är valfritt. Du kan välja att inte ladda upp en hero-ikon. Men när en hero-ikon har överförts, skall inte att ta bort den från publiceringen portal. Följ den [hero ikonen riktlinjer](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
   >
   >
7. Gå till den **publicera** och på **mellanlagring PUSH TO STAGING**. Detaljerad vägledning om hur du testar erbjudandet i mellanlagringsmiljön finns i [testa ditt erbjudande för virtuell dator för Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. När du har testat ditt erbjudande i mellanlagringen, går du till den **publicera** flik i publiceringen portal. Klicka på **begära godkännande att skicka till produktion** att publicera ditt erbjudande i Marketplace.

    ![Logotyper](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="update-the-sku-description"></a>Uppdatera SKU-beskrivning
Följ dessa steg för att uppdatera SKU-information och publicera ditt erbjudande:

1. Logga in på den [Publiceringsportal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och sedan ditt erbjudande.
3. I menyn till vänster, klickar du på den **MARKNADSFÖRING** fliken.
4. Klicka på **engelska (USA)**.
5. Klicka på den **planer** fliken. I den **SKU: er** avsnittet, uppdatera SKU: N **rubrik**, **sammanfattning**, och **beskrivning** och spara ändringarna.

   > [!NOTE]
   > När du uppdaterar SKU-information, Tänk på dessa begränsningar: 
   * Ange inte dubbla text för beskrivning av erbjudande och SKU-beskrivning. 
   * Ange inte dubbla text för SKU-rubrik och lång sammanfattning av erbjudandet. 
   * Ange inte dubbla text för SKU-rubrik och sammanfattning av erbjudandet.
   >
   >
6. Gå till den **publicera** och på **mellanlagring PUSH TO STAGING**. Detaljerad vägledning om hur du testar erbjudandet i mellanlagringsmiljön finns i [testa ditt erbjudande för virtuell dator för Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. När du har testat ditt erbjudande i mellanlagringen, går du till den **publicera** flik i publiceringen portal. Klicka på **begära godkännande att skicka till produktion** att publicera ditt erbjudande i Marketplace.

    ![Planer](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="change-existing-links-or-add-new-links"></a>Ändra befintliga länkar eller lägga till nya länkar
Om du vill ändra befintliga länkar eller Lägg till nya länkar och sedan publicera ditt erbjudande, Följ dessa steg:

1. Logga in på den [Publiceringsportal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och sedan ditt erbjudande.
3. I menyn till vänster, klickar du på den **MARKNADSFÖRING** fliken.
4. Klicka på **engelska (USA)**.
5. Klicka på den **länkar** fliken.
6. Lägga till en ny länk i den **länkar** klickar du på **+ Lägg till länk**. I den **Lägg till länk** dialogrutan Ange länken **rubrik** och **URL** och spara ändringarna. Du kan ange en länk som innehåller information som kan hjälpa kunderna.
7. Klicka på länken om du vill uppdatera eller ta bort en befintlig länk, och klicka på den **redigera** knappen eller **ta bort** knappen.

   > [!NOTE]
   > Se till att de länkar som du har angett i det här avsnittet fungerar korrekt, eftersom dessa länkar hämta valideras under din begäran produktionsprocess.
   >
   >
8. Gå till den **publicera** och på **mellanlagring PUSH TO STAGING**. Detaljerad vägledning om hur du testar erbjudandet i mellanlagringsmiljön finns i [testa ditt erbjudande för virtuell dator för Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
9. När du har testat ditt erbjudande i mellanlagringen, går du till den **publicera** flik i publiceringen portal. Klicka på **begära godkännande att skicka till produktion** att publicera ditt erbjudande i Marketplace.

    ![Länkar](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![Lägg till länk](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="change-an-existing-sample-image-or-add-a-new-sample-image"></a>Ändra en befintlig exempelbild eller lägga till en ny exempelbild
Om du vill ändra en befintlig exempelbild eller Lägg till ny Exempelbilder och sedan publicera ditt erbjudande, Följ dessa steg:

> [!NOTE]
> Endast en exempelbild visas i den [Azure-portalen](https://portal.azure.com).
>
>

1. Logga in på den [Publiceringsportal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och sedan ditt erbjudande.
3. I menyn till vänster, klickar du på den **MARKNADSFÖRING** fliken.
4. Klicka på **engelska (USA)**.
5. Klicka på den **EXEMPELBILDER** fliken.
6. Så här lägger du till en ny avbildning i exemplet i den **Exempelbilder** klickar du på **ladda upp en ny avbildning** och spara ändringarna.

   > [!NOTE]
   > Det är valfritt att inkludera en exempelbild.
   >
7. Gå till den **publicera** och på **mellanlagring PUSH TO STAGING**. Detaljerad vägledning om hur du testar erbjudandet i mellanlagringsmiljön finns i [testa ditt erbjudande för virtuell dator för Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. När du har testat ditt erbjudande i mellanlagringen, går du till den **publicera** flik i publiceringen portal. Klicka på **begära godkännande att skicka till produktion** att publicera ditt erbjudande i Marketplace.

    ![Exempelbilder](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="update-the-legal-content"></a>Uppdatera juridiska innehåll
Om du vill uppdatera juridiska innehållet och publicera ditt erbjudande, Följ dessa steg:

1. Logga in på den [Publiceringsportal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och sedan ditt erbjudande.
3. I menyn till vänster, klickar du på den **MARKNADSFÖRING** fliken.
4. Klicka på **engelska (USA)**.
5. Klicka på den **JURIDISKA** fliken. I den **juridiska** och uppdatera dina policyer/villkor. Skriv eller klistra in policyer/villkor i den **ANVÄNDNINGSVILLKOR** och spara ändringarna.
6. Maximalt antal tillåtna tecken för de juridiska villkoren för användning är 1 miljon tecken.
7. Gå till den **publicera** och på **mellanlagring PUSH TO STAGING**. Detaljerad vägledning om hur du testar erbjudandet i mellanlagringsmiljön finns i [testa ditt erbjudande för virtuell dator för Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. När du har testat ditt erbjudande i mellanlagringen, går du till den **publicera** flik i publiceringen portal. Klicka på **begära godkännande att skicka till produktion** att publicera ditt erbjudande i Marketplace.

    ![Juridisk information](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="update-the-support-information"></a>Uppdatera supportinformationen om
Om du vill uppdatera supportinformationen om och publicera ditt erbjudande, Följ dessa steg:

1. Logga in på den [Publiceringsportal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och sedan ditt erbjudande.
3. I menyn till vänster, klickar du på den **SUPPORT** fliken.
4. I den **Engineering Kontakta** avsnittet, uppdatera engineering kontaktinformation. Informationen används för intern kommunikation mellan partner och Microsoft endast.
5. I den **kundsupporten** avsnittet, uppdatera kontaktinformation för support och **stöd för URL: en**. Informationen används för intern kommunikation mellan partner och Microsoft endast.

   > [!NOTE]
   > Om du vill ange e-postsupport, anger du ett påhittat telefonnummer i den **kundsupporten** avsnittet. I det här fallet används den e-postadress som du angav i stället.
   >
   >
6. Gå till den **publicera** och på **mellanlagring PUSH TO STAGING**. Detaljerad vägledning om hur du testar erbjudandet i mellanlagringsmiljön finns i [testa ditt erbjudande för virtuell dator för Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. När du har testat ditt erbjudande i mellanlagringen, går du till den **publicera** flik i publiceringen portal. Klicka på **begära godkännande att skicka till produktion** att publicera ditt erbjudande i Marketplace.

    ![Support](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="update-the-categories"></a>Uppdatera kategorier
Följ dessa steg för att uppdatera avsnittet kategorier för ditt erbjudande och publicera ditt erbjudande:

1. Logga in på den [Publiceringsportal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och sedan ditt erbjudande.
3. I menyn till vänster, klickar du på den **kategorier** fliken.
4. I den **kategorier** avsnittet, uppdatera kategorier för ditt erbjudande och spara ändringarna. Du kan välja upp till fem kategorier för Azure Marketplace-galleriet.
5. Gå till den **publicera** och på **mellanlagring PUSH TO STAGING**. Detaljerad vägledning om hur du testar erbjudandet i mellanlagringsmiljön finns i [testa ditt erbjudande för virtuell dator för Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
6. När du har testat ditt erbjudande i mellanlagringen, går du till den **publicera** flik i publiceringen portal. Klicka på **begära godkännande att skicka till produktion** att publicera ditt erbjudande i Marketplace.

    ![Kategorier](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="add-a-new-sku-under-a-listed-offer"></a>Lägg till en ny SKU under en listade erbjudande
Följ dessa steg för att lägga till en ny SKU i erbjudandet live:

1. Logga in på den [Publiceringsportal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och sedan ditt erbjudande.
3. I menyn till vänster, klickar du på den **SKU: er** fliken. Klicka sedan på **Lägg till en SKU**. 
4. I dialogrutan anger du en **SKU-identifierare** med små bokstäver. Välj den **ta med din egen licens (BYOL) faktureringsmodellen** markerar du kryssrutan om du vill publicera den nya SKU: N med en fakturering BYOL-modell. Annars avmarkerar du kryssrutan. Klicka på skalstreck för att skapa en ny SKU. Om du inte väljer BYOL faktureringsmodellen måste anges automatiskt faktureringsmodellen till varje timme. Om du vill att en 30-dagars kostnadsfri utvärderingsversion för varje timme faktureringsmodellen Välj **en månad** för **finns en kostnadsfri utvärderingsversion?** Annars väljer **nr utvärderingsversion**. (**Finns en kostnadsfri utvärderingsversion?**  visas bara om du inte har valt BYOL när du skapar den nya SKU: N.)

   > [!IMPORTANT]
   > **Dölj den här SKU: N från Marketplace eftersom det alltid ska köpas via en lösningsmall** ska vara **Ja** *endast* om du har godkänt för att publicera en lösningsmall. Annars kan det här alternativet bör alltid vara **nr**.
   >
   >
4. I menyn till vänster, klickar du på den **VM-AVBILDNINGAR** fliken och ta reda på den nya SKU: N som du har skapat.
5. Om du vill konfigurera den nya SKU: N, se [skaffa en certifiering för din avbildning](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) anvisningar.
6. Om du vill lägga till marknadsföringsmaterial för den nya SKU: N, se [ger Marketplace marknadsföring innehåll](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Om du vill lägga till prisinformationen för den nya SKU: N, se [ange priser](marketplace-publishing-push-to-staging.md#step-2-set-your-prices).
8. Gå till den **publicera** och på **mellanlagring PUSH TO STAGING**. Detaljerad vägledning om hur du testar erbjudandet i mellanlagringsmiljön finns i [testa ditt erbjudande för virtuell dator för Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
9. När du har testat ditt erbjudande i mellanlagringen, går du till den **publicera** flik i publiceringen portal. Klicka på **begära godkännande att skicka till produktion** att publicera ditt erbjudande i Marketplace.

    ![SKU:er](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![Lägg till en SKU](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="change-the-data-disk-count-for-a-listed-sku"></a>Ändra datadiskar för en listade SKU
Du kan inte öka/minska datadiskar med en listade SKU: N. Du behöver skapa en ny SKU i det här fallet. Detaljerad information finns i avsnittet [lägga till en ny SKU under en listade erbjudandet](#add-a-new-sku-under-a-listed-offer).

## <a name="delete-a-listed-offer-from-the-marketplace"></a>Ta bort en listade erbjudandet från Marketplace
Olika aspekter måste vara tar hand om när det gäller en begäran att ta bort ett erbjudande med live. Följ dessa steg för att få vägledning från support-teamet att ta bort ett listade erbjudande från Marketplace kan:

1. Generera ett supportärende på den [skapar en incident](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681) sidan.

2. Välj **problemtyp** som **hantera erbjudanden**, och välj **kategori** som **ändra ett erbjudande/SKU eller redan i produktion**.
3. Skicka din begäran.

Supportteamet vägleder dig genom borttagningsprocessen erbjudande/SKU.

> [!NOTE]
> Du kan alltid ta bort erbjudandet när den är i statusen utkast (men inte mellanlagring eller produktion). På den **HISTORIK** fliken **KASTA utkast**.
>
>

## <a name="delete-a-listed-sku-from-the-marketplace"></a>Ta bort en listade SKU från Marketplace
Följ dessa steg för att ta bort en listade SKU från Marketplace:

1. Logga in på den [Publiceringsportal](https://publish.windowsazure.com).

2. Gå till den **virtuella datorer** och sedan ditt erbjudande.
3. I fönstret till vänster, klickar du på den **SKU: er** fliken.
4. Välj den SKU som du vill ta bort och klicka på den **ta bort** knappen.
5. Gå till den **publicera** flik i publiceringen portal. Klicka på **begära godkännande att skicka till produktion** att publicera erbjudandet i Marketplace.
6. När erbjudandet publiceras i Marketplace, tas SKU: N bort från Marketplace och Azure-portalen.

## <a name="delete-the-current-version-of-a-listed-sku-from-the-marketplace"></a>Ta bort den aktuella versionen av en listade SKU från Marketplace
Följ dessa steg för att ta bort den aktuella versionen av en listade SKU från Marketplace: 

1. Logga in på den [Publiceringsportal](https://publish.windowsazure.com).

2. Gå till den **virtuella datorer** och sedan ditt erbjudande.
3. I menyn till vänster, klickar du på den **VM-AVBILDNINGAR** fliken.
4. Välj den SKU vars aktuella version som du vill ta bort och klicka på den **ta bort** knappen.
5. Gå till den **publicera** flik i publiceringen portal. Klicka på **begära godkännande att skicka till produktion** att publicera erbjudandet i Marketplace.
6. När erbjudandet hämtar publiceras i Marketplace, tas den aktuella versionen av den angivna SKU: N bort från Marketplace och Azure-portalen. SKU: N återställs sedan till en tidigare version.

## <a name="revert-the-listing-price-to-production-values"></a>Återställ lista priset till produktionsvärden
Följ dessa steg om du vill återställa lista priset till produktionsvärden:

1. Logga in på den [Publiceringsportal](https://publish.windowsazure.com).
2. Gå till den **virtuella datorer** och sedan ditt erbjudande.
3. I menyn till vänster, klickar du på den **priser** fliken.
4. Välj en region vars priser som du vill återställa.

    ![Priser för regioner](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. Återställa priserna för alla kärnor för SKU: er med en per timme faktureringsmodell som de är i produktion för den valda regionen. För SKU: er med BYOL-faktureringsmodell, tillgängliggör SKU i regionen genom att markera kryssrutan för SKU: N i den **EXTERNALLY-LICENSED (BYOL) SKU tillgänglighet** avsnittet.

    ![Prismodellerna](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. Klicka på **AUTOPRICE andra marknader utifrån priser i USA**.

   > [!NOTE]
   > Knappens etikett kan vara olika beroende på den region som du väljer. Eftersom vi valt USA knappen har etiketten **AUTOPRICE andra marknader baserat på PRISET i USA**.
   >
   >

    ![Autoprice](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. På sidan 1 i guiden Autoprice, Välj den grundläggande marknaden och klicka på den **pilen** knappen.

    ![Grundläggande marknaden](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. På sidan 2, väljer service-planer och mätare (kärnor) och klicka på den **pilen** knappen.

    ![Service-planer och mätare](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. Klicka på sidan 3 **växla alla** att välja alla regioner. Eller så kan manuellt välja kryssrutorna för specifika regioner. Klicka på den **pilen** knappen.

    ![Välj marknader](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. På sidan 4, granska växelkurserna och klicka på **Slutför**. Guiden återställer priser enligt dina val.

11. På den **priser** fliken **visa sammanfattning och ändringar**.
    För **Visa Version**väljer **Draft**, och för **Jämför med**väljer **produktion**. Om du ser ingen prisnivå skillnad återställts priserna till produktionsvärden.

    ![Visa sammanfattning och ändringar](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. Gå till den **publicera** och på **mellanlagring PUSH TO STAGING**. Detaljerad vägledning om hur du testar erbjudandet i mellanlagringsmiljön finns i [testa ditt erbjudande för virtuell dator för Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
13. När du har testat ditt erbjudande i mellanlagringen, går du till den **publicera** flik i publiceringen portal. Klicka på **begära godkännande att skicka till produktion** att publicera ditt erbjudande i Marketplace.

## <a name="revert-the-billing-model-to-production-values"></a>Återställ faktureringsmodell till produktionsvärden
Följ dessa steg om du vill återställa faktureringsmodell till produktionsvärden:

1. Logga in på den [Publiceringsportal](https://publish.windowsazure.com).

2. Gå till den **virtuella datorer** och sedan ditt erbjudande.
3. I menyn till vänster, klickar du på den **SKU: er** fliken.
4. Klicka på den **redigera** knappen för att återställa faktureringsmodellen. I fönstret som öppnas markerar eller avmarkerar du den **fakturering och licensiering görs externt från Azure (även kallat Bring Your Own License)** markerar du kryssrutan.

    ![Redigera fakturering](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. Följ stegen i ”Återställ lista priset till produktionsvärden” i den här artikeln.
6. Gå till den **publicera** och på **mellanlagring PUSH TO STAGING**. Detaljerad vägledning om hur du testar erbjudandet i mellanlagringsmiljön finns i [testa ditt erbjudande för virtuell dator för Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. När du har testat ditt erbjudande i mellanlagringen, går du till den **publicera** flik i publiceringen portal. Klicka på **begära godkännande att skicka till produktion** att publicera ditt erbjudande i Marketplace.

## <a name="revert-the-visibility-setting-of-a-listed-sku-to-the-production-value"></a>Återställa inställningar för en listade SKU för produktion-värden
Följ dessa steg om du vill återställa inställningar för en listade SKU till produktionsvärde:

1. Logga in på den [Publiceringsportal](https://publish.windowsazure.com).

2. Gå till den **virtuella datorer** och sedan ditt erbjudande.
3. I menyn till vänster, klickar du på den **SKU: er** fliken.
4. Välj din SKU och återställa inställningar för SKU: N till produktionsvärde.

    ![Synlighet](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. När du är klar med ändringarna klickar du på **begära godkännande att skicka till produktion** att publicera ditt erbjudande i Marketplace.

## <a name="see-also"></a>Se också
* [Komma igång: Publicera ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md)
* [Förstå betalnings reporting](marketplace-publishing-report-payout.md)
* [Ändra din återförsäljare Cloud Solution Provider-incitament](marketplace-publishing-csp-incentive.md)
* [Felsöka vanliga problem med publicering på Marketplace](marketplace-publishing-support-common-issues.md)
* [Få support som en utgivare](marketplace-publishing-get-publisher-support.md)
* [Skapa en VM-avbildning lokalt](marketplace-publishing-vm-image-creation-on-premise.md)
* [Skapa en virtuell dator som kör Windows i Azure preview portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
