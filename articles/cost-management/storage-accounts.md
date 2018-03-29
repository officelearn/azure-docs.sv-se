---
title: Konfigurera storage-konton för hantering av Azure kostnaden | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar Azure storage-konton och AWS lagring buckets för hantering av Azure kostnaden.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/27/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: 4ddd70501821941120d0d22e273b69842b567532
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="configure-storage-accounts-for-cost-management"></a>Konfigurera storage-konton för hantering av kostnad

<!--- intent: As a Cost Management user, I want to configure Cost Management to use my cloud service provider storage account to store my reports. -->

Du kan spara kostnad rapporter i Cloudyn portal, Azure storage eller AWS lagring buckets. Spara dina rapporter till portalen Cloudyn är kostnadsfritt. Dock spara rapporterna till din molntjänstleverantör lagring är valfritt och ytterligare kostnad uppkommer. Den här artikeln hjälper dig att konfigurera Azure storage-konton och Amazon Web Services (AWS) lagring behållare för att lagra dina rapporter.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha ett Azure storage-konto eller ett Amazon lagring bucket.

Om du inte har ett Azure storage-konto, måste du skapa en. Mer information om hur du skapar ett Azure storage-konto finns [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account).

Om du inte har en AWS enkla service (S3) bucket måste du skapa en. Mer information om hur du skapar en S3-bucket finns [skapa en Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Konfigurera Azure storage-konto

Konfigurerar du är Azure storage för användning av hantering av kostnaden enkelt. Samla in information om lagringskontot och kopiera dem i Cloudyn-portalen.

1. Logga in på Azure Portal på http://portal.azure.com.
2. Klicka på **alla tjänster**väljer **lagringskonton**, bläddra till det lagringskonto som du vill använda och välj kontot.
3. På sidan storage-konto under **inställningar**, klickar du på **åtkomstnycklar**.
4. Kopiera ditt **lagringskontonamnet** och **anslutningssträngen** under key1.  
![Snabbtangenter för Azure-lagring](./media/storage-accounts/azure-storage-access-keys.png)  
5. Öppna Cloudyn portalen från Azure-portalen eller navigera till https://azure.cloudyn.com och logga in.
6. Klicka på symbolen kugge och välj sedan **rapporter lagringshantering**.
7. Klicka på **Lägg till ny +** och kontrollera att Microsoft Azure är markerad. Klistra in namnet på ditt Azure lagringskonto i den **namn** område. Klistra in din **anslutningssträngen** i området för motsvarande. Ange ett behållarnamn på och klicka sedan på **spara**.  
![Cloudyn lagring som är konfigurerad för Azure](./media/storage-accounts/azure-cloudyn-storage.png)

  Inmatningen för lagring av nya Azure rapport visas i listan storage-konto.  
    ![Ny Azure rapportlagring i listan](./media/storage-accounts/azure-storage-entry.png)


Du kan nu spara rapporterna till Azure-lagring. I en rapport klickar du på **åtgärder** och välj sedan **schemalägga rapport**. Namn på rapporten och lägga till din egen URL eller använder automatiskt skapade URL-Adressen. Välj **spara till lagring** och välj sedan lagringskontot. Ange ett prefix som kommer att läggas till filnamnet för rapporten. Markera CSV- eller JSON-filformat och spara rapporten.

## <a name="configure-an-aws-storage-bucket"></a>Konfigurera en AWS lagring bucket

Cloudyn använder befintliga AWS autentiseringsuppgifter: användaren eller rollen, för att spara rapporterna till din bucket. Om du vill testa åtkomst Cloudyn försöker spara en liten textfil bucket med filnamnet _Kontrollera-bucket-permission.txt_.

Du förse Cloudyn roll eller användare med behörighet att PutObject din bucket. Använd en befintlig bucket eller skapa en ny för att spara rapporter sedan. Slutligen bestämma hur du hanterar lagring-klassen, ange livscykel regler eller ta bort onödiga filer.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Tilldela behörigheter till din AWS användaren eller rollen

När du skapar en ny princip kan ange du exakt behörighet för att spara en rapport i en S3-bucket.

1. Logga in på AWS-konsolen och välj **Services**.
2. Välj **IAM** från listan över tjänster.
3. Välj **principer** på vänster sida av konsolen och klicka sedan på **skapa princip**.
4. Klicka på den **JSON** fliken.
5. Följande princip kan du spara en rapport i en S3-bucket. Kopiera och klistra in i följande exempel principen till den **JSON** fliken. Ersätt &lt;bucketname&gt; med Bucketnamn.

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
7. Ange ett namn för principen på sidan Granska principen. Till exempel _CloudynSaveReport2S3_.
8. Klicka på **skapa princip**.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Koppla principen till en Cloudyn roll eller användare i ditt konto

Lägg till den nya principen genom att öppna AWS-konsolen och redigera Cloudyn rollen eller användaren.

1. Logga in på AWS-konsolen och välj **Services**och välj **IAM** från listan över tjänster.
2. Välj antingen **roller** eller **användare** från vänster sida av konsolen.

**För roller:**

  1. Klicka på din Cloudyn rollnamn.
  2. På den **behörigheter** klickar du på **koppla principen**.
  3. Sök efter den princip som du skapade och markera den och sedan klicka på **koppla principen**.
    ![AWS - koppla princip för en roll](./media/storage-accounts/aws-attach-policy-role.png)

**För användare:**

1. Välj den Cloudyn användare.
2. På den **behörigheter** klickar du på **lägga till behörigheter**.
3. I den **bevilja behörighet** väljer **koppla befintliga principer direkt**.
4. Sök efter den princip som du skapade och markera den och sedan klicka på **nästa: granska**.
5. Klicka på Lägg till behörigheter till rollsidan namn, **lägga till behörigheter**.  
    ![AWS - koppla princip för en användare](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Valfritt: Ange behörigheter med bucket-princip

Du kan också ange behörighet för att skapa rapporter om din S3-bucket med en bucket-princip. I den klassiska S3-vyn:

1. Skapa eller välj en befintlig bucket.
2. Välj den **behörigheter** och klicka sedan på **Bucket princip**.
3. Kopiera och klistra in i följande exempel principen. Ersätt &lt;bucket\_namn&gt; och &lt;Cloudyn\_princip&gt; med ARN av din bucket. Ersätt ARN på rollen eller användaren som används av Cloudyn.

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

4. I Redigeraren för Bucket, klickar du på **spara**.

### <a name="add-aws-report-storage-to-cloudyn"></a>Lägga till lagringsenheter för AWS-rapport i Cloudyn

1. Öppna Cloudyn portalen från Azure-portalen eller navigera till https://azure.cloudyn.com och logga in.
2. Klicka på symbolen kugge och välj sedan **rapporter lagringshantering**.
3. Klicka på **Lägg till ny +** och kontrollera att AWS är markerad.
4. Välj ett konto och lagring bucket. Namnet på AWS lagring bucket är automatiskt ifyllda.  
    ![Lägga till rapporten lagringsutrymme för AWS bucket](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Klicka på **spara** och klicka sedan på **Ok**.

    Nya AWS rapporten lagring posten visas i listan storage-konto.  
    ![Ny AWS rapportlagring i listan](./media/storage-accounts/aws-storage-entry.png)


Du kan nu spara rapporterna till Azure-lagring. I en rapport klickar du på **åtgärder** och välj sedan **schemalägga rapport**. Namn på rapporten och lägga till din egen URL eller använder automatiskt skapade URL-Adressen. Välj **spara till lagring** och välj sedan lagringskontot. Ange ett prefix som kommer att läggas till filnamnet för rapporten. Markera CSV- eller JSON-filformat och spara rapporten.

## <a name="next-steps"></a>Nästa steg

- Granska [förstå kostnad rapporter](understanding-cost-reports.md) vill veta mer om grundläggande struktur och funktioner för kostnadsrapporter.
