---
title: Konfigurera lagringskonton för Cloudyn i Azure | Microsoft Docs
description: Den här artikeln beskrivs hur du konfigurerar Azure storage-konton och AWS storage buckets för Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/05/2018
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: ''
ms.openlocfilehash: f7092a08e501ae61ef93be383290db575b5ad1f1
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52995558"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Konfigurera lagringskonton för Cloudyn

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

Du kan spara Cloudyn-rapporter i Cloudyn-portalen, Azure storage eller AWS storage buckets. Det är kostnadsfritt att spara dina rapporter på Cloudyn-portalen. Men spara dina rapporter till din molntjänstleverantör lagring är valfritt och tillkommer ytterligare kostnader. Den här artikeln hjälper dig att konfigurera Azure storage-konton och Amazon Web Services (AWS) lagring buckets för att lagra dina rapporter.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha ett Azure storage-konto eller en Amazon storage bucket.

Om du inte har ett Azure storage-konto kan behöva du skapa en. Mer information om hur du skapar ett Azure storage-konto finns i [skapa ett lagringskonto](../storage/common/storage-quickstart-create-account.md).

Om du inte har en AWS bucket för enkel service (S3), måste du skapa en. Läs mer om hur du skapar en S3-bucket [skapa en Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Konfigurera ditt Azure storage-konto

Konfigurerar du är Azure storage för användning av Cloudyn enkelt. Samla in information om storage-konto och kopiera dem i Cloudyn-portalen.

1. Logga in på Azure Portal på http://portal.azure.com.
2. Klicka på **alla tjänster**väljer **lagringskonton**, bläddra till det lagringskonto som du vill använda och välj sedan kontot.
3. På sidan med storage-konto under **inställningar**, klickar du på **åtkomstnycklar**.
4. Kopiera din **lagringskontonamn** och **anslutningssträngen** under key1.  
![Azure lagringsåtkomstnycklar](./media/storage-accounts/azure-storage-access-keys.png)  
5. Öppna Cloudyn-portalen från Azure Portal eller gå till https://azure.cloudyn.com och logga in.
6. Klicka på kugghjulet för symbolen och välj sedan **rapporter lagringshantering**.
7. Klicka på **Lägg till ny +** och se till att Microsoft Azure har valts. Klistra in namnet på ditt Azure storage-konto i den **namn** området. Klistra in din **anslutningssträngen** i området för motsvarande. Ange ett namn på behållare och klicka sedan på **spara**.  
![Cloudyn-lagring som konfigurerats för Azure](./media/storage-accounts/azure-cloudyn-storage.png)

  Inmatningen för lagring av nya Azure-rapport visas i listan över storage-konto.  
    ![Ny rapport Azure-lagring i listan](./media/storage-accounts/azure-storage-entry.png)


Du kan nu spara rapporter till Azure storage. I en rapport klickar du på **åtgärder** och välj sedan **Schemalägg rapport**. Namnge rapporten och lägga till din egen URL eller använder automatiskt skapade URL: en. Välj **spara i storage** och välj sedan lagringskontot. Ange ett prefix som kommer att läggas till filnamnet för rapporten. Välj CSV eller JSON-filformat och sedan spara rapporten.

## <a name="configure-an-aws-storage-bucket"></a>Konfigurera en bucket för AWS-lagring

Innehåller Cloudyn använder befintliga AWS-autentiseringsuppgifter: användaren eller rollen, för att spara rapporterna till din bucket. Om du vill testa åtkomsten Cloudyn försöker spara en liten textfil i bucketen med filnamnet _Kontrollera-bucket-permission.txt_.

Du anger den Cloudyn roll eller en användare med behörigheten PutObject till din bucket. Sedan kan använda en befintlig bucket eller skapa en ny om du vill spara rapporter. Slutligen kan bestämma hur du hanterar lagring-klassen, ange livscykel regler eller ta bort onödiga filer.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Tilldela behörigheter till din AWS-användare eller roll

När du skapar en ny princip kan ange du de exakta behörigheter som behövs för att spara en rapport till en S3-bucket.

1. Logga in på AWS-konsolen och välj **Services**.
2. Välj **IAM** från listan över tjänster.
3. Välj **principer** på vänster sida av konsolen och klicka sedan på **skapa princip**.
4. Klicka på den **JSON** fliken.
5. Följande princip kan du spara en rapport till en S3-bucket. Kopiera och klistra in följande princip exemplet till den **JSON** fliken. Ersätt &lt;bucketname&gt; med bucketnamnet på din.

  ```
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

6. Klicka på **granska princip**.  
    ![Granska princip](./media/storage-accounts/aws-policy.png)  
7. Ange ett namn för principen på sidan Granska princip. Till exempel _CloudynSaveReport2S3_.
8. Klicka på **skapa princip**.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Koppla principen till en Cloudyn roll eller användare i ditt konto

Lägg till den nya principen genom att öppna AWS-konsolen och redigera på Cloudyn-rollen eller användare.

1. Logga in på AWS-konsolen och välj **Services**och välj sedan **IAM** från listan över tjänster.
2. Välj antingen **roller** eller **användare** från vänster sida av konsolen.

**För roller:**

  1. Klicka på ditt Cloudyn-rollnamn.
  2. På den **behörigheter** fliken **bifoga princip**.
  3. Sök efter den princip som du skapade och markera den och sedan klicka på **bifoga princip**.
    ![AWS - bifoga princip för en roll](./media/storage-accounts/aws-attach-policy-role.png)

**För användare:**

1. Välj den Cloudyn-användare.
2. På den **behörigheter** fliken **Lägg till behörigheter**.
3. I den **ge behörighet** väljer **koppla befintliga principer direkt**.
4. Sök efter den princip som du skapade och markera den och sedan klicka på **nästa: granska**.
5. Klicka på Lägg till behörigheter till rollsidan namn, **Lägg till behörigheter**.  
    ![AWS - bifoga princip för en användare](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Valfritt: Ange behörighet med bucket-princip

Du kan också ange behörighet att skapa rapporter på din S3-bucket med hjälp av en bucket-princip. I den klassiska S3-vyn:

1. Skapa eller välj en befintlig bucket.
2. Välj den **behörigheter** fliken och klicka sedan på **Bucket princip**.
3. Kopiera och klistra in följande princip-exemplet. Ersätt &lt;bucket\_namn&gt; och &lt;Cloudyn\_princip&gt; med ARN av din bucket. Ersätt ARN på rollen eller användaren som används av Cloudyn.

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

4. I principredigeraren Bucket klickar du på **spara**.

### <a name="add-aws-report-storage-to-cloudyn"></a>Lägga till lagringsenheter för AWS-rapport i Cloudyn

1. Öppna Cloudyn-portalen från Azure Portal eller gå till https://azure.cloudyn.com och logga in.
2. Klicka på kugghjulet för symbolen och välj sedan **rapporter lagringshantering**.
3. Klicka på **Lägg till ny +** och se till att AWS har valts.
4. Välj ett konto och storage-bucket. Namnet på AWS storage bucket har fyllts i automatiskt.  
    ![Lägg till rapportlagring för AWS-bucket](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Klicka på **spara** och klicka sedan på **Ok**.

    Din nya AWS rapporten storage posten visas i lagringskontolistan.  
    ![Ny rapport AWS-lagring i listan](./media/storage-accounts/aws-storage-entry.png)


Du kan nu spara rapporter till Azure storage. I en rapport klickar du på **åtgärder** och välj sedan **Schemalägg rapport**. Namnge rapporten och lägga till din egen URL eller använder automatiskt skapade URL: en. Välj **spara i storage** och välj sedan lagringskontot. Ange ett prefix som kommer att läggas till filnamnet för rapporten. Välj CSV eller JSON-filformat och sedan spara rapporten.

## <a name="next-steps"></a>Nästa steg

- Granska [förstå Cloudyn rapporterar](understanding-cost-reports.md) vill veta mer om grundläggande struktur och funktioner i Cloudyn-rapporter.
