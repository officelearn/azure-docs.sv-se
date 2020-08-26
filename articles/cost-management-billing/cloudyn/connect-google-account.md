---
title: Koppla ett Google Cloud Platform-konto till Cloudyn i Azure
description: Anslut ett Google Cloud Platform-konto om du vill visa kostnader och användningsdata i Cloudyn-rapporter.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 8d549a8fd7c8b03f98fe2b11c94531ed323e0e3a
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690076"
---
# <a name="connect-a-google-cloud-platform-account"></a>Ansluta ett Google Cloud Platform-konto

Du kan ansluta ditt befintliga Google Cloud Platform-konto till Cloudyn. När du har anslutit kontot till Cloudyn finns kostnader och användningsdata i Cloudyn-rapporter. Den här artikeln hjälper dig att konfigurera och ansluta ditt Google-konto till Cloudyn.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="collect-project-information"></a>Samla in projektinformation

Du börjar med att samla in information om ditt projekt.

1. Logga in på Google Cloud Platform-konsolen på [https://console.cloud.google.com](https://console.cloud.google.com).
2. Granska den projektinformation som du vill publicera till Cloudyn och anteckna **projektnamnet** och **projekt-ID**. Ha informationen till hands för senare steg.  
    ![Projektnamn och projekt-ID som visas i Google Cloud Platform-konsolen](./media/connect-google-account/gcp-console01.png)
3. Om fakturering inte är aktiverat och kopplat till ditt projekt skapar du ett faktureringskonto. Mer information finns i [Skapa ett nytt faktureringskonto](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account).

## <a name="enable-storage-bucket-billing-export"></a>Aktivera export av fakturering från lagringsbucket

Cloudyn hämtar dina Google-faktureringsdata från en lagringsbucket. Ha **bucketnamnet** och **rapportens prefix** till hands för senare användning under Cloudyn-registreringen.

Användning av Google Cloud Storage för att lagra användningsrapporter medför minimala avgifter. Mer information finns i [prissättning för Cloud Storage](https://cloud.google.com/storage/pricing).

1. Om du inte har aktiverat faktureringsexport till en fil följer du anvisningarna i [Så här aktiverar du faktureringsexport till en fil](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Du kan använda antingen JSON- eller CSV-exportformatet för fakturering.
2. Annars går du i Google Cloud Platform-konsolen till **Fakturering** > **Faktureringsexport**. Observera **bucketnamnet** och **rapportens prefix** för fakturering.  
    ![Information om faktureringsexport som visas på sidan för faktureringsexport](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Aktivera Google Cloud Platform-API:er

För att samla in användnings- och tillgångsinformation behöver Cloudyn följande Google Cloud Platform-API:er aktiverade:

- BigQuery API
- Google Cloud SQL
- Google Cloud Datastore API
- Google Cloud Storage
- Google Cloud Storage JSON API
- Google Compute Engine API

### <a name="enable-or-verify-apis"></a>Aktivera eller verifiera API:er

1. I Google Cloud Platform-konsolen väljer du det projekt som du vill registrera med Cloudyn.
2. Gå till **API:er och tjänster** > **Bibliotek**.
3. Använd sökfunktionen för att hitta vart och ett av de tidigare angivna API:erna.
4. För varje API kontrollerar du att **API aktiverat** visas. Annars klickar du på **AKTIVERA**.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Lägga till ett Google Cloud-konto till Cloudyn

1. Öppna Cloudyn-portalen från Azure-portalen eller gå till [https://azure.cloudyn.com](https://azure.cloudyn.com/) och logga in.
2. Klicka på **Inställningar** (kugghjulssymbol) och välj sedan **Molnkonton**.
3. I **Kontohantering** väljer du fliken **Google-konton** och klickar sedan på **Lägg till nytt +** .
4. I **Google-kontonamn** anger du e-postadressen för faktureringskontot och klickar sedan på **Nästa**.
5. I dialogrutan för Google-autentisering väljer du eller anger ett Google-konto och väljer sedan **TILLÅT** för att cloudyn.com ska få åtkomst till ditt konto.
6. Lägg till den begärda projektinformationen som du tidigare antecknade. Detta omfattar **projekt-ID**, **projektnamn**, **bucketnamn för fakturering** och **rapportprefix för faktureringsfil**. Klicka sedan på **Spara**.  
    ![Lägga till Google-projekt till Cloudyn-konto](./media/connect-google-account/add-project.png)

Ditt Google-konto visas i listan över konton, och där bör det stå **Autentiserat**. Under det bör ditt Google-projektnamn och ID visas samt en grön bocksymbol. Kontostatus bör vara **Slutfört**.

Inom några timmar visar Cloudyn-rapporter Google-kostnad och användningsinformation.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om Cloudyn kan du fortsätta till självstudien [Granska användning och kostnader](tutorial-review-usage.md) för Cloudyn.
