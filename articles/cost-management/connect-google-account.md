---
title: Ansluta ett Google Cloud Platform-konto till Cloudyn i Azure | Microsoft Docs
description: Ansluta ett Google Cloud Platform-konto för att visa data för kostnader och användning i Cloudyn-rapporter.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/05/2018
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: ''
ms.openlocfilehash: 967404fbbff3616d3faf9e315123bccbf3ac9276
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969081"
---
# <a name="connect-a-google-cloud-platform-account"></a>Ansluta ett Google Cloud Platform-konto

Du kan ansluta ditt befintliga Google Cloud Platform-konto till Cloudyn. När du ansluter ditt konto till Cloudyn, är kostnader och användning data tillgängliga i Cloudyn-rapporter. Den här artikeln hjälper dig att konfigurera och ansluta ditt Google-konto med Cloudyn.

> [!NOTE]
> Google har ändrat deras kontosäkerhet, vilket förhindrar att nya anslutningar upprättas mellan Cloudyn och Google. Cloudyn fortsätter att samla in data i Google för användare som redan har Cloudyn som är anslutna till Google. Men du kan inte lägga till nya Google-konton till Cloudyn för närvarande. Cloudyn-teamet vet inte när supporten för att lägga till nya Google-konton till Cloudyn återupptas. Vi ska ta bort det här meddelandet när support återställs.

## <a name="collect-project-information"></a>Samla in projektinformation

Du startar genom att samla in information om ditt projekt.

1. Logga in på Google Cloud Platform-konsolen i [ https://console.cloud.google.com ](https://console.cloud.google.com).
2. Granska informationen som du vill att publicera till Cloudyn och anteckna den **projektnamn** och **projekt-ID**. Spara informationen till hands för senare steg.  
    ![Google Cloud Platform-konsolen](./media/connect-google-account/gcp-console01.png)
3. Om fakturering inte är aktiverat och länka till ditt projekt, skapa ett faktureringskonto. Mer information finns i [skapa en ny faktureringskonto](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account).

## <a name="enable-storage-bucket-billing-export"></a>Aktivera storage bucket fakturering export

Cloudyn hämtar Google faktureringsinformation från en bucket för lagring. Behåll den **bucketnamnet** och **rapporten prefix** informationen till hands för senare användning under Cloudyn-registreringen.

Använda Google Cloud Storage för att lagra användningsrapporter medför minimal avgifter. Mer information finns i [Cloud Storage-priser](https://cloud.google.com/storage/pricing).

1. Om du inte har aktiverat fakturering export till en fil, följer du anvisningarna i [hur du aktiverar fakturering export till en fil](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Du kan använda JSON- eller CSV fakturering exportformat.
2. Annars i Google Cloud Platform-konsolen navigerar du till **fakturering** > **fakturering export**. Observera faktureringen **bucketnamnet** och **rapporten prefix**.  
    ![Fakturering export](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Aktivera Google Cloud Platform API: er

För att samla in information om användning och tillgången Cloudyn måste du ha följande Google Cloud Platform API: erna aktiverat:

- BigQuery API
- Google Cloud SQL
- Google Cloud Datastore API
- Google Cloud Storage
- JSON-API: et för Google Cloud Storage
- Google Compute Engine API

### <a name="enable-or-verify-apis"></a>Aktivera eller verifiera API: er

1. Välj det projekt som du vill registrera med Cloudyn i Google Cloud Platform-konsolen.
2. Gå till **API: er och tjänster** > **biblioteket**.
3. Använda sökning var tidigare har listats API.
4. För varje API: et, kontrollerar du att **API aktiverat** visas. Annars klickar du på **aktivera**.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Lägg till ett Google Cloud-konto i Cloudyn

1. Öppna Cloudyn-portalen från Azure-portalen eller navigera till [ https://azure.cloudyn.com ](https://azure.cloudyn.com/) och logga in.
2. Klicka på **inställningar** (kugghjulet symbol) och välj sedan **Molnkonton**.
3. I **kontohantering**väljer den **Google-konton** fliken och klicka sedan på **Lägg till ny +**.
4. I **Google kontonamn**, ange den e-postadressen för fakturering kontot och klicka sedan på **nästa**.
5. Välj eller ange ett Google-konto i dialogrutan för Google-autentisering och sedan **TILLÅT** cloudyn.com åtkomst till ditt konto.
6. Lägga till projektinformation begäran som användes tidigare anges. De innehåller **projekt-ID**, **projekt** namn, **fakturering** bucketnamnet, och **fakturering filen** rapportera prefix och klicka sedan på  **Spara**.  
    ![Lägg till Google-projekt](./media/connect-google-account/add-project.png)

Ditt Google-konto visas i listan över konton och det ska stå **autentiserad**. Ditt projektnamn i Google och ID ska visas och har en grön bock symbol under det. Kontostatus som ska stå **slutförd**.

Inom några timmar visar Cloudyn-rapporter information för kostnader och användning av Google.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om Cloudyn kan fortsätta att den [granska användning och kostnader](./tutorial-review-usage.md) självstudiekursen för Cloudyn.
