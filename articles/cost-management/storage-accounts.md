---
title: Konfigurera lagringskonton för Cloudyn i Azure | Microsoft Docs
description: Den här artikeln beskrivs hur du konfigurerar Azure storage-konton och AWS storage buckets för Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: secdec18
ms.openlocfilehash: 1ac4442aa5a7e5e4367a03d33169412d37b3f1ea
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229929"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Konfigurera lagringskonton för Cloudyn

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

Du kan spara Cloudyn-rapporter i Cloudyn-portalen, Azure storage eller AWS storage buckets. Det är kostnadsfritt att spara dina rapporter på Cloudyn-portalen. Men spara dina rapporter till din molntjänstleverantör lagring är valfritt och tillkommer ytterligare kostnader. Den här artikeln hjälper dig att konfigurera Azure storage-konton och Amazon Web Services (AWS) lagring buckets för att lagra dina rapporter.

## <a name="prerequisites"></a>Krav

Du måste ha ett Azure storage-konto eller en Amazon storage bucket.

Om du inte har ett Azure storage-konto kan behöva du skapa en. Mer information om hur du skapar ett Azure Storage-konto finns i [skapa ett lagrings konto](../storage/common/storage-quickstart-create-account.md).

Om du inte har en AWS bucket för enkel service (S3), måste du skapa en. Mer information om hur du skapar en S3-Bucket finns i [skapa en Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Konfigurera ditt Azure storage-konto

Konfigurerar du är Azure storage för användning av Cloudyn enkelt. Samla in information om storage-konto och kopiera dem i Cloudyn-portalen.

1. Logga in på Azure Portal på https://portal.azure.com.
2. Klicka på **alla tjänster**, Välj **lagrings konton**, bläddra till det lagrings konto som du vill använda och välj sedan kontot.
3. På sidan lagrings konto under **Inställningar**klickar du på **åtkomst nycklar**.
4. Kopiera ditt **lagrings konto namn** och **anslutnings sträng** under KEY1.  
   ![kopiera lagrings konto namn och anslutnings sträng](./media/storage-accounts/azure-storage-access-keys.png)  
5. Öppna Cloudyn-portalen från Azure Portal eller gå till https://azure.cloudyn.com och logga in.
6. Klicka på kugg hjuls-symbolen och välj sedan **rapporter lagrings hantering**.
7. Klicka på **Lägg till ny +** och kontrol lera att Microsoft Azure är markerat. Klistra in namnet på ditt Azure Storage-konto i **namn** -fältet. Klistra in **anslutnings strängen** i motsvarande fält. Ange ett namn på behållaren och klicka sedan på **Spara**.  
   ![klistra in namnet på Azure Storage-kontot och anslutnings strängen i rutan Lägg till en ny rapport lagring](./media/storage-accounts/azure-cloudyn-storage.png)

   Inmatningen för lagring av nya Azure-rapport visas i listan över storage-konto.  
    ![Ny rapport Azure storage-post i listan](./media/storage-accounts/azure-storage-entry.png)


Du kan nu spara rapporter till Azure storage. I en rapport klickar du på **åtgärder** och väljer sedan **Schemalägg rapport**. Namnge rapporten och lägga till din egen URL eller använder automatiskt skapade URL: en. Välj **Spara till lagring** och välj sedan lagrings kontot. Ange ett prefix som kommer att läggas till filnamnet för rapporten. Välj CSV eller JSON-filformat och sedan spara rapporten.

## <a name="configure-an-aws-storage-bucket"></a>Konfigurera en bucket för AWS-lagring

Innehåller Cloudyn använder befintliga AWS-autentiseringsuppgifter: användaren eller rollen, för att spara rapporterna till din bucket. För att testa åtkomsten försöker Cloudyn Spara en liten textfil till Bucket med fil namnet _Check-Bucket-permission. txt_.

Du anger den Cloudyn roll eller en användare med behörigheten PutObject till din bucket. Sedan kan använda en befintlig bucket eller skapa en ny om du vill spara rapporter. Slutligen kan bestämma hur du hanterar lagring-klassen, ange livscykel regler eller ta bort onödiga filer.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Tilldela behörigheter till din AWS-användare eller roll

När du skapar en ny princip kan ange du de exakta behörigheter som behövs för att spara en rapport till en S3-bucket.

1. Logga in på AWS-konsolen och välj **tjänster**.
2. Välj **IAM** i listan över tjänster.
3. Välj **principer** på vänster sida av konsolen och klicka sedan på **Skapa princip**.
4. Klicka på **JSON** -fliken.
5. Följande princip kan du spara en rapport till en S3-bucket. Kopiera och klistra in följande princip exempel till **JSON** -fliken. Ersätt &lt;bucketname&gt; med ditt Bucket-namn.

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

6. Klicka på **Granska princip**.  
    ![AWS JSON-princip som visar exempel information](./media/storage-accounts/aws-policy.png)  
7. Ange ett namn för principen på sidan Granska princip. Till exempel _CloudynSaveReport2S3_.
8. Klicka på **Skapa princip**.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Koppla principen till en Cloudyn roll eller användare i ditt konto

Lägg till den nya principen genom att öppna AWS-konsolen och redigera på Cloudyn-rollen eller användare.

1. Logga in på AWS-konsolen och välj **tjänster**och välj sedan **IAM** i listan över tjänster.
2. Välj antingen **roller** eller **användare** från vänster sida av konsolen.

**För roller:**

  1. Klicka på ditt Cloudyn-rollnamn.
  2. På fliken **behörigheter** klickar du på **bifoga princip**.
  3. Sök efter den princip som du skapade och markera den och klicka sedan på **koppla princip**.
    ![exempel princip som är kopplad till Cloudyn-rollen](./media/storage-accounts/aws-attach-policy-role.png)

**För användare:**

1. Välj den Cloudyn-användare.
2. På fliken **behörigheter** klickar du på **Lägg till behörigheter**.
3. I avsnittet **bevilja behörighet** väljer du **koppla befintliga principer direkt**.
4. Sök efter principen som du skapade och markera den och klicka sedan på **Nästa: granska**.
5. På sidan Lägg till behörigheter till roll namn klickar du på **Lägg till behörigheter**.  
    ![exempel princip som är kopplad till din Cloudyn-användare](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Valfritt: Ange behörighet med bucket-princip

Du kan också ange behörighet att skapa rapporter på din S3-bucket med hjälp av en bucket-princip. I den klassiska S3-vyn:

1. Skapa eller välj en befintlig bucket.
2. Välj fliken **behörigheter** och klicka sedan på **Bucket-princip**.
3. Kopiera och klistra in följande princip-exemplet. Ersätt &lt;Bucket\_Name&gt; och &lt;Cloudyn\_-princip&gt; med ARN i Bucket. Ersätt ARN på rollen eller användaren som används av Cloudyn.

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

4. I redigeraren för Bucket-princip klickar du på **Spara**.

### <a name="add-aws-report-storage-to-cloudyn"></a>Lägga till lagringsenheter för AWS-rapport i Cloudyn

1. Öppna Cloudyn-portalen från Azure Portal eller gå till https://azure.cloudyn.com och logga in.
2. Klicka på kugg hjuls-symbolen och välj sedan **rapporter lagrings hantering**.
3. Klicka på **Lägg till ny +** och kontrol lera att AWS är markerat.
4. Välj ett konto och storage-bucket. Namnet på AWS storage bucket har fyllts i automatiskt.  
    ![Exempelinformation i Lägg till en ny rapport storage ruta](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Klicka på **Spara** och sedan på **OK**.

    Din nya AWS rapporten storage posten visas i lagringskontolistan.  
    ![Ny AWS rapporten storage posten visas i lagringskontolistan](./media/storage-accounts/aws-storage-entry.png)


Du kan nu spara rapporter till Azure storage. I en rapport klickar du på **åtgärder** och väljer sedan **Schemalägg rapport**. Namnge rapporten och lägga till din egen URL eller använder automatiskt skapade URL: en. Välj **Spara till lagring** och välj sedan lagrings kontot. Ange ett prefix som kommer att läggas till filnamnet för rapporten. Välj CSV eller JSON-filformat och sedan spara rapporten.

## <a name="next-steps"></a>Nästa steg

- Läs [förstå Cloudyn-rapporter](understanding-cost-reports.md) för att lära dig mer om grundläggande struktur och funktioner i Cloudyn-rapporter.
