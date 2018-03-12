---
title: Ansluta ett konto med Google Cloud Platform till Azure kostnaden Management | Microsoft Docs
description: "Ansluta till en Google Cloud Platform-konto om du vill visa kostnad och användningsdata i kostnaden Management repots."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/05/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 8f8c157be0a369817099afa211015ba7587017e3
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="connect-a-google-cloud-platform-account"></a>Ansluta till en Google Cloud Platform-konto

Du kan ansluta till ditt befintliga Google Cloud Platform-konto till Azure kostnaden Management. När du ansluter kontot till kostnaden Management är kostnad och användningsdata tillgängliga i kostnaden Management rapporter. Den här artikeln hjälper dig att konfigurera och ansluta ditt Google-konto med hantering av kostnaden.

## <a name="collect-project-information"></a>Samla in information om projektet

Börja med att samla in information om projektet.

1. Logga in på Google Cloud Platform konsolen vid [https://console.cloud.google.com](https://console.cloud.google.com).
2. Granska informationen som du vill publicera till kostnaden hanterings- och Observera den **projektnamn** och **projekt-ID**. Behåll informationen praktiska för senare steg.  
    ![Google Cloud Platform konsolen](./media/connect-google-account/gcp-console01.png)
3. Om faktureringen inte är aktiverad och länka till ditt projekt, skapa ett faktureringskonto. Mer information finns i [skapar ett nytt faktureringskonto](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create\_a\_new\_billing\_account).

## <a name="enable-storage-bucket-billing-export"></a>Aktivera storage bucket fakturering export

Kostnad Management hämtar dina Google fakturering data från en lagring bucket. Behåll den **Bucketnamn** och **rapporten prefix** informationen till hands för senare användning vid hantering av kostnaden registreringen.

Använda Google Cloud-lagring för att lagra användningsrapporter har minimal avgifter. Mer information finns i [Cloud Storage-priser](https://cloud.google.com/storage/pricing).

1. Om du inte har aktiverat fakturering export till en fil, följer du anvisningarna [hur du aktiverar fakturering export till en fil](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Du kan använda JSON eller CSV fakturering exportformat.
2. Annars i Google Cloud Platform-konsolen navigerar du till **fakturering** > **fakturering export**. Observera faktureringen **Bucket namnet** och **rapporten prefixet**.  
    ![Fakturering export](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Aktivera Google Cloud Platform API: er

Om du vill samla in information om användning och tillgångsinformation hanteringsbehov kostnaden följande Google Cloud Platform API: erna aktiverad:

- BigQuery API
- Google Cloud SQL
- Google Cloud-datalagret API
- Google Cloud Storage
- Google Cloud Storage JSON API
- Google Compute Engine API

### <a name="enable-or-verify-apis"></a>Aktivera eller verifiera API: er

1. I konsolen för Google Cloud Platform väljer du projektet som du vill registrera med hantering av kostnaden.
2. Gå till **API: er och tjänster** > **biblioteket**.
3. Använda sökning varje listats tidigare API.
4. För varje API, kontrollerar du att **API aktiverat** visas. Annars klickar du på **aktivera**.

## <a name="add-a-google-cloud-account-to-cost-management"></a>Lägg till ett Google Cloud-konto till kostnaden Management

1. Öppna Cloudyn portalen från Azure-portalen eller navigera till [https://azure.cloudyn.com](https://azure.cloudyn.com/) och logga in.
2. Klicka på **inställningar** (kugghjulet symbol) och välj sedan **moln konton**.
3. I **kontohantering**, Välj den **Google-konton** och klicka sedan på **Lägg till ny +**.
4. I **Google kontonamn**, ange den e-postadressen för kontot fakturering och klicka sedan på **nästa**.
5. Välj eller ange ett Google-konto i autentiseringsdialogrutan Google och sedan **TILLÅT** cloudyn.com tillgång till ditt konto.
6. Lägga till projektinformation begäran om att du haft tidigare anges. De omfattar **projekt-ID**, **projekt** namn, **fakturering** Bucketnamn och **fakturering filen** rapportera prefix och klicka sedan på  **Spara**.  
    ![Lägga till Google-projekt](./media/connect-google-account/add-project.png)

Google-konto visas i listan över konton och det bör stå **autentiserad**. Ditt Google projektnamn och -ID ska visas och har en grön bock symbol under det. Kontostatus som ska stå **slutförd**.

Inom några timmar visar kostnad rapporter information för kostnad och användning av Google.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om Azure kostnaden Management kan fortsätta att den [granska användning och kostnader](./tutorial-review-usage.md) självstudiekurs för hantering av kostnaden.
