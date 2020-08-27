---
title: Konfigurera lagringskonton för Cloudyn i Azure
description: I den här artikeln beskrivs hur du konfigurerar Azure Storage-konton och AWS-lagringsbucketar för Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: secdec18
ROBOTS: NOINDEX
ms.openlocfilehash: 130542833f2f5e0d4ae63cfa7f681b390e46fa1e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689668"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Konfigurera lagringskonton för Cloudyn

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

Du kan spara Cloudyn-rapporter i Cloudyn-portalen, Azure Storage eller AWS-lagringsbucketar. Det kostar inget att spara rapporterna i Cloudyn-portalen. Men att spara rapporter till molntjänstleverantörens lagring är valfritt och medför ytterligare kostnader. Den här artikeln hjälper dig att konfigurera Azure Storage-konton och AWS-lagringsbucketar (Amazon Web Services) för att lagra dina rapporter.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="prerequisites"></a>Krav

Du måste ha antingen ett Azure Storage-konto eller en AWS-lagringsbucket.

Om du inte har ett Azure Storage-konto måste du skapa ett. Mer information om hur du skapar ett Azure Storage-konto finns i [Skapa ett lagringskonto](../../storage/common/storage-account-create.md).

Om du inte har en S3-bucket (AWS Simple Storage Service) måste du skapa en. Mer information om hur du skapar en S3-bucket finns i [Skapa en bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Konfigurera ditt Azure Storage-konto

Det är enkelt att konfigurera Azure Storage för användning i Cloudyn. Samla information om lagringskontot och kopiera den i Cloudyn-portalen.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Klicka på **Alla tjänster**, välj **Lagringskonton**, rulla till lagringskontot du vill använda och välj kontot.
3. På sidan för lagringskontot går du till **Inställningar** och klickar på **Åtkomstnycklar**.
4. Kopiera ditt **lagringskontonamn** och din **anslutningssträng** under key1.  
   ![Kopiera lagringskontonamnet och anslutningssträngen](./media/storage-accounts/azure-storage-access-keys.png)  
5. Öppna Cloudyn-portalen från Azure-portalen eller gå till [https://azure.cloudyn.com](https://azure.cloudyn.com) och logga in.
6. Klicka på kugghjulssymbolen och välj sedan **Hantering av lagringsrapporter**.
7. Klicka på **Lägg till ny +** och se till att Microsoft Azure är markerat. Klistra in namnet på ditt Azure Storage-konto i området **Namn**. Klistra in **anslutningssträngen** i motsvarande område. Ange ett containernamn och klicka sedan på **Spara**.  
   ![Klistra in Azure Storage-kontonamnet och anslutningssträngen i rutan Lägg till en ny rapportlagring](./media/storage-accounts/azure-cloudyn-storage.png)

   Din nya Azure-rapportlagringspost visas i listan med lagringskonton.  
    ![Ny Azure-rapportlagringspost i listan](./media/storage-accounts/azure-storage-entry.png)


Nu kan du spara rapporter i Azure Storage. Klicka på **Actions** (Åtgärder) och välj sedan **Schedule report** (Schemalägg rapport). Ge rapporten ett namn och lägg sedan till din egen URL eller använd den URL som skapats automatiskt. Välj **Save to storage** (Spara till lagring) och välj sedan lagringskontot. Ange ett prefix som läggs till i rapportfilnamnet. Välj antingen CSV- eller JSON-filformat och spara sedan rapporten.

## <a name="configure-an-aws-storage-bucket"></a>Konfigurera en AWS-lagringsbucket

Cloudyn använder befintliga AWS-autentiseringsuppgifter: Användare eller roll, för att spara rapporterna i din bucket. För att testa åtkomsten försöker Cloudyn spara en liten textfil med filnamnet _check-bucket-permission.txt_ i bucketen.

Du anger Cloudyn-rollen eller Cloudyn-användaren med PutObject-behörighet till din bucket. Sedan använder du en befintlig bucket eller skapar en ny för att spara rapporter. Bestäm slutligen hur du vill hantera lagringsklassen, ange livscykelregler eller ta bort onödiga filer.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Tilldela behörigheter till din AWS-användare eller AWS-roll

När du skapar en ny princip anger du de exakta behörigheter som krävs för att spara en rapport i en S3-bucket.

1. Logga in på AWS-konsolen och välj **Services** (Tjänster).
2. Välj **IAM** i listan över tjänster.
3. Välj **Policies** (Principer) till vänster i konsolen och klicka sedan på **Create Policy** (Skapa princip).
4. Klicka på fliken **JSON**.
5. Med följande princip kan du spara en rapport i en S3-bucket. Kopiera och klistra in följande principexempel på fliken **JSON**. Ersätt &lt;bucketname&gt; med ditt bucketnamn.

   ```json
   {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid":  "CloudynSaveReport2S3",
        "Effect":      "Allow",
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "arn:aws:s3:::<bucketname>/*"
        ]
      }
    ]
   }
   ```

6. Klicka på **Review policy** (Granska princip).  
    ![AWS JSON-princip som visar exempelinformation](./media/storage-accounts/aws-policy.png)  
7. På sidan Review policy (Granska princip) anger du ett namn för principen. Till exempel _CloudynSaveReport2S3_.
8. Klicka på **Create policy** (Skapa princip).

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Koppla principen till en Cloudyn-roll eller Cloudyn-användare i ditt konto

Om du vill koppla den nya principen öppnar du AWS-konsolen och redigerar Cloudyn-rollen eller Cloudyn-användaren.

1. Logga in på AWS-konsolen och välj **Services** (Tjänster). Välj sedan **IAM** i listan med tjänster.
2. Välj antingen **Roles** (Roller) eller **Users** (Användare) till vänster i konsolen.

**För roller:**

  1. Klicka på namnet på Cloudyn-rollen.
  2. På fliken **Permissions** (Behörigheter) klickar du på **Attach Policy** (Koppla princip).
  3. Sök efter principen som du skapade, markera den och klicka sedan på **Attach Policy** (Koppla princip).
    ![Exempelprincip som är kopplad till din Cloudyn-roll](./media/storage-accounts/aws-attach-policy-role.png)

**För användare:**

1. Välj Cloudyn-användaren.
2. Klicka på **Permissions** (Behörigheter) på fliken **Add permissions** (Lägg till behörigheter).
3. I avsnittet **Grant Permission** (Bevilja behörighet) väljer du **Attach existing policies directly** (Koppla befintliga principer direkt).
4. Sök efter principen som du skapade, markera den och klicka sedan på **Nästa: Granskning**.
5. På sidan Add permissions to role name (Lägg till behörigheter för rollnamn) klickar du på **Add permissions** (Lägg till behörigheter).  
    ![Exempelprincip som är kopplad till din Cloudyn-användare](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Valfritt: Ange behörighet med bucketprincip

Du kan också ange behörighet för att skapa rapporter i din S3-bucket med hjälp av en bucketprincip. I den klassiska S3-vyn:

1. Skapa eller välj en befintlig bucket.
2. Välj fliken **Permissions** (Behörigheter) och klicka sedan på **Bucket policy** (Bucketprincip).
3. Kopiera och klistra in följande principexempel. Ersätt &lt;bucket\_name&gt; och &lt;Cloudyn\_principle&gt; med ARN för din bucket. Ersätt ARN för antingen rollen eller användaren som används av Cloudyn.

   ```
   {
   "Id": "Policy1485775646248",
   "Version": "2012-10-17",
   "Statement": [
    {
      "Sid": "SaveReport2S3",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "<bucket_name>/*",
      "Principal": {
        "AWS": [
          "<Cloudyn_principle>"
        ]
      }
    }
   ]
   }
   ```

4. I bucketprincipredigeraren klickar du på **Save** (Spara).

### <a name="add-aws-report-storage-to-cloudyn"></a>Lägga till AWS-rapportlagring i Cloudyn

1. Öppna Cloudyn-portalen från Azure-portalen eller gå till [https://azure.cloudyn.com](https://azure.cloudyn.com) och logga in.
2. Klicka på kugghjulssymbolen och välj sedan **Hantering av lagringsrapporter**.
3. Klicka på **Lägg till ny +** och se till att AWS är markerat.
4. Välj ett konto och en lagringsbucket. Namnet på AWS-lagringsbucketen fylls i automatiskt.  
    ![Exempelinformation i rutan Lägg till en ny rapportlagring](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Klicka på **Spara** och sedan på **Ok**.

    Din nya AWS-rapportlagringspost visas i listan med lagringskonton.  
    ![En ny AWS-rapportlagringspost visas i listan med lagringskonton](./media/storage-accounts/aws-storage-entry.png)


Nu kan du spara rapporter i Azure Storage. Klicka på **Actions** (Åtgärder) uppe till och välj **Schedule report** (Shemalägg rapport). Ge rapporten ett namn och lägg sedan till din egen URL eller använd den URL som skapats automatiskt. Välj **Save to storage** (Spara till lagring) och välj sedan lagringskontot. Ange ett prefix som läggs till i rapportfilnamnet. Välj antingen CSV- eller JSON-filformat och spara sedan rapporten.

## <a name="next-steps"></a>Nästa steg

- Läs [förstå Cloudyn-rapporter](understanding-cost-reports.md) om du vill veta mer om den grundläggande strukturen och funktionerna i Cloudyn-rapporter.
