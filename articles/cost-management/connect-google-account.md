---
title: Ansluta ett Google Cloud Platform-konto till Cloudyn i Azure | Microsoft Docs
description: Ansluta ett Google Cloud Platform-konto för att visa data för kostnader och användning i Cloudyn-rapporter.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 937d1b6e0bc9ece0507821538fafb0f5d8c0ef99
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230136"
---
# <a name="connect-a-google-cloud-platform-account"></a>Ansluta ett Google Cloud Platform-konto

Du kan ansluta ditt befintliga Google Cloud Platform-konto till Cloudyn. När du ansluter ditt konto till Cloudyn, är kostnader och användning data tillgängliga i Cloudyn-rapporter. Den här artikeln hjälper dig att konfigurera och ansluta ditt Google-konto med Cloudyn.


## <a name="collect-project-information"></a>Samla in projektinformation

Du startar genom att samla in information om ditt projekt.

1. Logga in på Google Cloud Platform-konsolen på [https://console.cloud.google.com](https://console.cloud.google.com).
2. Granska projekt informationen som du vill publicera i Cloudyn och anteckna **projektets namn** och **projekt-ID: t**. Spara informationen till hands för senare steg.  
    ![projekt namn och projekt-ID som visas i Google Cloud Platform-konsolen](./media/connect-google-account/gcp-console01.png)
3. Om fakturering inte är aktiverat och länka till ditt projekt, skapa ett faktureringskonto. Mer information finns i [skapa ett nytt fakturerings konto](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account).

## <a name="enable-storage-bucket-billing-export"></a>Aktivera storage bucket fakturering export

Cloudyn hämtar Google faktureringsinformation från en bucket för lagring. Behåll **Bucket** -och **rapportens prefix** -information praktiskt för senare användning vid Cloudyn-registrering.

Använda Google Cloud Storage för att lagra användningsrapporter medför minimal avgifter. Mer information finns i [priser för moln lagring](https://cloud.google.com/storage/pricing).

1. Om du inte har aktiverat fakturerings export till en fil följer du anvisningarna i [så här aktiverar du fakturerings export till en fil](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Du kan använda JSON- eller CSV fakturering exportformat.
2. Annars går du till **fakturerings** > **fakturerings export**i Google Cloud Platform-konsolen. Notera namnet på din fakturerings- **Bucket** och ditt **rapport prefix**.  
    ![fakturerings export information som visas på sidan för fakturerings export](./media/connect-google-account/billing-export.png)

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
2. Gå till **API: er & Services** > **Library**.
3. Använda sökning var tidigare har listats API.
4. Kontrol lera att **API aktiverat** visas för varje API. Annars klickar du på **Aktivera**.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Lägg till ett Google Cloud-konto i Cloudyn

1. Öppna Cloudyn-portalen från Azure Portal eller navigera till [https://azure.cloudyn.com](https://azure.cloudyn.com/) och logga in.
2. Klicka på **Inställningar** (kugg hjuls symbol) och välj sedan **moln konton**.
3. I **konto hantering**väljer du fliken **Google-konton** och klickar sedan på **Lägg till ny +** .
4. I **Google-konto namn**anger du e-postadressen för fakturerings kontot och klickar sedan på **Nästa**.
5. I dialog rutan Google-autentisering väljer eller anger du ett Google-konto och **ger** sedan Cloudyn.com åtkomst till ditt konto.
6. Lägga till projektinformation begäran som användes tidigare anges. De omfattar **projekt-ID**, **projekt** namn, namn på **fakturerings** Bucket och prefix för **fakturerings fil** och klicka sedan på **Spara**.  
    ![lägga till Google Project i Cloudyn-konto](./media/connect-google-account/add-project.png)

Ditt Google-konto visas i listan över konton och det bör stå **autentiserat**. Ditt projektnamn i Google och ID ska visas och har en grön bock symbol under det. Konto status bör stå på **slutförd**.

Inom några timmar visar Cloudyn-rapporter information för kostnader och användning av Google.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om Cloudyn fortsätter du till självstudien [Granska användning och kostnader](./tutorial-review-usage.md) för Cloudyn.
