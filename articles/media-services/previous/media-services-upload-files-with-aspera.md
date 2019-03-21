---
title: Överföra filer till ett Azure Media Services-konto med Aspera | Microsoft Docs
description: Den här kursen visar hur du överför filer till ett lagringskonto som är associerat med ett Media Services-konto med tjänsten **Aspera Server On Demand** på Azure.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: 8812623a-b425-4a0f-9e05-0ee6c839b6f9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: 28b6732745947043847ce944243bc87398e159fc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57892155"
---
# <a name="upload-files-into-a-media-services-account-using-the-aspera-server-on-demand-service-on-azure"></a>Överföra filer till ett Media Services-konto med hjälp av tjänsten Aspera Server On Demand på Azure 

## <a name="overview"></a>Översikt

**Aspera** är en programvara för filöverföring i hög hastighet. **Aspera Server On Demand** för Azure möjliggör snabba överföringar och hämtning av stora filer direkt i Azure-blobobjektlagringen. Information om **Aspera On Demand** finns på webbplatsen [Aspera Cloud](http://cloud.asperasoft.com/). 
  
**Aspera Server On Demand** för Azure kan köpas på [Azure Marketplace](https://azure.microsoft.com/marketplace/). Du måste vara inloggad på Azure Marketplace med ditt Windows Live ID för att kunna köpa **Aspera Server On Demand** för Azure.

Den här kursen visar hur du överför filer till ett lagringskonto som är associerat med ett Media Services-konto med tjänsten **Aspera Server On Demand** på Azure. 

Ett exempel som visar hur du använder Azure-funktioner med Aspera och Media Services finns [här](https://github.com/Azure-Samples/media-services-dotnet-functions-integration/tree/master/103-aspera-ingest).

>[!NOTE]
>Det finns en gräns för maximal filstorlek för bearbetning med Azure Media Services-mediebearbetare (MP:er). Information om filstorleksbegränsningen finns i [den här](media-services-quotas-and-limitations.md) artikeln.
>

## <a name="prerequisites"></a>Förutsättningar 

För att slutföra den här kursen behöver du:

* Ett Windows Live ID
* Ett [Azure-konto](https://azure.microsoft.com). Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/). 
* Ett [Azure Media Services-konto](media-services-portal-create-account.md).

## <a name="purchase-aspera-on-demand-for-azure"></a>Köpa Aspera On Demand för Azure

När du är inloggad på Azure Marketplace kan du köpa Aspera On Demand för Azure genom att följande dessa enkla steg.

1. Sök efter Aspera och välj Server On Demand.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera001.png)

2. Granska prenumerationsavtalen och klicka på Sign Up (Registrera dig)

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera002.png)

3. Fyll i informationen för din för din prenumeration på Server On Demand.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera003.png)

4. Klicka på **Pricing Tier** (Prisnivå) och välj önskad månatlig volym på delpanelen. Välj **OK** på panelen **Plan details** (Avtalsinformation). Klicka sedan på **Select** (Välj) på panelen **Choose your Pricing Tier** (Välj din prisnivå).

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera004.png)

5. Klicka på **Legal terms** (Juridiska villkor) för att läsa och acceptera de juridiska villkoren på delpanelen. När du har granskat de juridiska villkoren klickar du på **Purchase** (Köp).

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera005.png)

6. Slutför köpet genom att klicka på **Create** (Skapa).

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera006.png)

7. På Azure-instrumentpanelen anges att tjänsten etableras.  När etableringen är klar kan du se den nya prenumerationen genom att söka efter namnet på tjänsten bland dina resurser. När du har hittat tjänsten dubbelklickar du på den för att starta hanteringsportalen för tjänsten.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera007.png)

8. Starta Aspera-hanteringsportalen. När du har hittat din nya Aspera-tjänst kan du öppna hanteringsportalen genom att klicka på tjänsten.  En ny panel öppnas. På den nya panelen klickar du på **resursnamnet** för den nya tjänsten.  På följande skärmbild är resursens namn AsperaTransferDemo. När du klickar på resursnamnet öppnas en ny panel. Länken Manage (Hantera) visas på den nyöppnade panelen. Klicka på länken för att öppna Aspera-hanteringsportalen.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera008.png)

9. När du klickar på hanteringslänken kommer du till registreringssidan (registrering krävs för att få åtkomst till tjänsten).

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera009.png)

10. Nu ska du ha åtkomst till Aspera-hanteringsportalen där du kan skapa åtkomstnycklar, hämta Aspera-klienter och licenser, visa användning och läsa om API:erna.

    Skärmbilden nedan visar hur åtkomst skapas. 

    ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera010.png)

    Skärmbilden nedan visar gränssnitt för användningsrapportering i portalen. 

    ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera011.png)

## <a name="upload-files-with-aspera"></a>Överföra filer med Aspera

1. Hämta och installera Aspera-klientprogrammet:
    
    * [Plugin-program för webbläsare](https://downloads.asperasoft.com/connect2/)
    * [Rich-klient](https://downloads.asperasoft.com/en/downloads/2)

2. Gör din första överföring. Innan du kan använda Aspera-klienten med Aspera-överföringstjänsten måste du göra följande: 

   1. Skapa en åtkomstnyckel med Aspera-portalen.  
   2. Hämta, installera och licensiera Aspera-klienten (programmet finns på Aspera-portalen).  

      >[!NOTE]
      >Läs konfigurationsinformationen i handboken för Aspera-klienten.
    
   3. Hämta information om lagringskontot som är associerat med ditt Azure Media-konto via [Azure Portal](https://portal.azure.com/). Du behöver namn och nyckel samt namnet på lagringsblobcontainern som du vill placera innehållet i. 

       * Så här hämtar du lagringsinformation från portalen: Leta reda på lagringskontot, klicka på Åtkomstnycklar och kopiera namnet och nyckeln för ditt konto.
       * Så här hämtar du containernamnet: Leta reda på lagringskontot, välj **Blobar** och välj namnet på den container som du vill överföra innehållet till. 

      Nedan visas en skärmbild på **Connection Manager** för Aspera-klienten. Där måste du ange Azure-lagringstyp, autentiseringsuppgifter och blobcontainern.

      ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera012.png)

## <a name="resources"></a>Resurser

Följande resurser har nämns i den här artikeln. 

* [Ansluta plugin-program för webbläsare](https://downloads.asperasoft.com/connect2/)
* [Anslutningsguide](https://downloads.asperasoft.com/en/documentation/8)
* [Aspera-klient](https://downloads.asperasoft.com/en/downloads/2)
* [Klientguide](https://downloads.asperasoft.com/en/documentation/2)

## <a name="next-steps"></a>Nästa steg

Du kan nu [kopiera blobar från ett lagringskonto till ett AMS-konto](media-services-copying-existing-blob.md#copy-blobs-from-a-storage-account-into-an-ams-account).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

