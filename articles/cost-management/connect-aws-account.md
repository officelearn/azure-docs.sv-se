---
title: Ansluta en Amazon Web Services-konto till Cloudyn i Azure | Microsoft Docs
description: Anslut en Amazon Web Services-konto för att visa data för kostnader och användning i Cloudyn-rapporter.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: ''
ms.openlocfilehash: 45baefbea3d2bd03098c045c42dc67dccceba8c2
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275375"
---
# <a name="connect-an-amazon-web-services-account"></a>Ansluta till en Amazon Web Services-konto

Du har två alternativ för att ansluta ditt konto för Amazon Web Services (AWS) till Cloudyn. Du kan ansluta med en IAM-roll eller med en skrivskyddad IAM-användarkonto. Rollen IAM rekommenderas eftersom du kan delegera åtkomst med definierade behörigheter till betrodda enheter. IAM-rollen måste inte du dela långsiktig åtkomstnycklar. När du ansluter ett AWS-konto till Cloudyn, är kostnader och användning data tillgängliga i Cloudyn-rapporter. Det här dokumentet hjälper dig att båda alternativen.

Läs mer om AWS IAM identiteter, [identiteter (användare, grupper och roller)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Dessutom du aktiverar AWS detaljerad fakturering rapporter och lagra informationen i en bucket med AWS simple storage service (S3). Detaljerad fakturering rapporterna innehåller debiterade avgifterna med information om taggen och resurs per timme. Lagra rapporterna kan Cloudyn att hämta dem från din bucket och visa informationen i rapporterna.


## <a name="aws-role-based-access"></a>AWS rollbaserad åtkomst

I följande avsnitt vägleder dig genom att skapa en skrivskyddad IAM-roll för att ge åtkomst till Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>Hämta ditt externa ID-konto för Cloudyn

Det första steget är att få unika anslutning lösenfrasen från Cloudyn-portalen. Den används i AWS som den **externt ID**.

1. Öppna Cloudyn-portalen från Azure-portalen eller navigera till [ https://azure.cloudyn.com ](https://azure.cloudyn.com) och logga in.
2. Klicka på kugghjulet för symbolen och välj sedan **Molnkonton**.
3. Kontohantering, Välj den **AWS-konton** fliken och klicka sedan på **Lägg till ny +**.
4. I den **Lägg till konto för AWS** dialogrutan, kopiera den **externt ID** och spara värdet för AWS skapa stegen i nästa avsnitt. Externt ID är unikt för ditt konto. I följande bild, exemplet externt ID är _Contoso_ följt av ett tal. Ditt ID skiljer sig åt.  
    ![Externt ID](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Lägg till AWS skrivskyddad rollbaserad åtkomst

1. Logga in på AWS-konsolen i https://console.aws.amazon.com/iam/home och välj **roller**.
2. Klicka på **skapa roll** och välj sedan **en annan AWS-konto**.
3. I den **konto-ID** rutan, klistra in `432263259397`. Detta konto-ID är Cloudyn data collector kontot som tilldelats av AWS Cloudyn-tjänsten. Använd det exakta konto-ID som visas.
4. Bredvid **alternativ**väljer **kräver externt ID**. Klistra in din unikt värde som tidigare har kopierat från den **externt ID** i Cloudyn. Klicka sedan på **nästa: behörigheter**.  
    ![Skapa roll](./media/connect-aws-account/create-role01.png)
5. Under **bifoga säkerhetsbehörighetsprinciper**i den **principtypen** filter rutan Sök, skriver du in `ReadOnlyAccess`väljer **ReadOnlyAccess**, klicka sedan på **nästa: Granska**.  
    ![Skrivskyddad åtkomst](./media/connect-aws-account/readonlyaccess.png)
6. På sidan Granska kontrollerar du att inställningarna är korrekta och Skriv en **rollnamn**. Till exempel *Azure-kostnad-Mgt*. Ange en **Rollbeskrivning**. Till exempel _rolltilldelningen för Cloudyn_, klicka sedan på **skapa roll**.
7. I den **roller** listan, klicka på den roll som du har skapat och kopiera den **rollen ARN** värdet på sammanfattningssidan. Använd rollen ARN (Amazon resursnamnet) värdet senare när du registrerar din konfiguration i Cloudyn.  
    ![Rollen ARN](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Konfigurera AWS IAM rollåtkomst i Cloudyn

1. Öppna Cloudyn-portalen från Azure-portalen eller navigera till https://azure.cloudyn.com/ och logga in.
2. Klicka på kugghjulet för symbolen och välj sedan **Molnkonton**.
3. Kontohantering, Välj den **AWS-konton** fliken och klicka sedan på **Lägg till ny +**.
4. I **kontonamn**, Skriv ett namn för kontot.
5. Bredvid **åtkomsttyp**väljer **IAM rollen**.
6. I den **rollen ARN** fältet, klistra in värdet som du tidigare kopierade och klicka sedan på **spara**.  
    ![Lägg till box för AWS-konto](./media/connect-aws-account/add-aws-account-box.png)


AWS-konto visas i listan över konton. Den **ägar-ID** i listan matchar din roll ARN-värde. Din **kontostatus** bör ha en grön bock symbol som indikerar att Cloudyn kan komma åt din AWS-konto. Tills du möjliggjort fakturering för detaljerad AWS, din konsolideringsstatus visas som **fristående**.

![AWS-kontostatus](./media/connect-aws-account/aws-account-status01.png)

Cloudyn börjar samla in data och fylla i rapporter. Nästa [Aktivera detaljerad AWS fakturering](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>AWS användarbaserad åtkomsthantering

I följande avsnitt vägleder dig genom att skapa en skrivskyddad användare för att ge åtkomst till Cloudyn.

### <a name="add-aws-read-only-user-based-access"></a>Lägg till AWS användarbaserade läsbehörighet

1. Logga in på AWS-konsolen i https://console.aws.amazon.com/iam/home och välj **användare**.
2. Klicka på **lägga till användare**.
3. I den **användarnamn** skriver ett användarnamn.
4. För **åtkomsttyp**väljer **Programmeringsåtkomst** och klicka på **nästa: behörigheter**.  
    ![Lägg till användare](./media/connect-aws-account/add-user01.png)
5. Behörigheter, Välj **koppla befintliga principer direkt**.
6. Under **bifoga säkerhetsbehörighetsprinciper**i den **principtypen** filter rutan Sök, skriver du in `ReadOnlyAccess`väljer **ReadOnlyAccess**, och klicka sedan på **nästa : Granska**.  
    ![Ange behörigheter för användare](./media/connect-aws-account/set-permission-for-user.png)
7. Se till att inställningarna är korrekta och klicka sedan på sidan Granska **skapa användare**.
8. På sidan har slutförts visas din nyckel-ID och hemlighet åtkomst åtkomstnyckel. Du kan använda den här informationen för att konfigurera registrering i Cloudyn.
9. Klicka på **ladda ned CSV** och spara filen credentials.csv till en säker plats.  
    ![Hämta autentiseringsuppgifter](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Konfigurera AWS IAM användarbaserade åtkomst i Cloudyn

1. Öppna Cloudyn-portalen från Azure Portal eller gå till https://azure.cloudyn.com/ och logga in.
2. Klicka på kugghjulet för symbolen och välj sedan **Molnkonton**.
3. Kontohantering, Välj den **AWS-konton** fliken och klicka sedan på **Lägg till ny +**.
4. För **kontonamn**, ange ett kontonamn.
5. Bredvid **åtkomsttyp**väljer **IAM användaren**.
6. I **åtkomstnyckeln**, klistra in den **åtkomst ID-nyckeln** värdet från filen credentials.csv.
7. I **hemlig nyckel**, klistra in den **hemliga åtkomstnyckeln** från credentials.csv-filen och klicka sedan på **spara**.  

AWS-konto visas i listan över konton. Din **kontostatus** bör ha en grön bock symbol.

Cloudyn börjar samla in data och fylla i rapporter. Nästa [Aktivera detaljerad AWS fakturering](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Aktivera detaljerad AWS-fakturering

Använd följande steg för att hämta dina AWS rollen ARN. Du kan använda rollen ARN för att bevilja läsbehörighet till en fakturering bucket.

1. Logga in på AWS-konsolen i https://console.aws.amazon.com och välj **Services**.
2. I tjänsten Search skriver *IAM*, och väljer det alternativet.
3. Välj **roller** på den vänstra menyn.
4. I listan över roller, väljer du den roll som du skapade för Cloudyn-åtkomst.
5. På sidan Sammanfattning klickar du på för att kopiera den **rollen ARN**. Behåll rollen ARN praktiskt för senare steg.

### <a name="create-an-s3-bucket"></a>Skapa en S3-bucket

Du skapar en S3-bucket för att lagra detaljerad faktureringsinformation.

1. Logga in på AWS-konsolen i https://console.aws.amazon.com och välj **Services**.
2. I tjänsten Search skriver *S3*, och välj **S3**.
3. På sidan Amazon S3 **skapa bucket**.
4. Välj en Bucketnamn och en Region i guiden Skapa bucket och klicka sedan på **nästa**.  
    ![Skapa bucket](./media/connect-aws-account/create-bucket.png)
5. På den **ange egenskaper** , Behåll standardvärdena, och klicka sedan på **nästa**.
6. På sidan Granska **skapa bucket**. Bucket-listan visas.
7. Klicka på en bucket som du skapade och välj den **behörigheter** fliken och välj sedan **Bucket princip**. Bucket-principredigeraren öppnas.
8. Kopiera följande JSON-exempel och klistra in den i Redigeraren för grupprinciper Bucket.
  - Ersätt `<BillingBucketName>` med namnet på din S3-bucket.
  - Ersätt `<ReadOnlyUserOrRole>` med rollen eller användaren ARN som du hade tidigare kopierade.

  ```
  {
    "Version": "2012-10-17",
    "Id": "Policy1426774604000",
    "Statement": [
        {
            "Sid": "Stmt1426774604000",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": [
                "s3:GetBucketAcl",
                "s3:GetBucketPolicy"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>"
        },
        {
            "Sid": "Stmt1426774604001",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        },
        {
            "Sid": "Stmt1426774604002",
            "Effect": "Allow",
            "Principal": {
                "AWS": "<ReadOnlyUserOrRole>"
            },
            "Action": [
                "s3:List*",
                "s3:Get*"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        }
    ]
  }
  ```

9. Klicka på **Spara**.  
    ![Bucket-principredigeraren](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Aktivera AWS fakturering rapporter

När du skapar och konfigurerar en S3-bucket, går du till [fakturering inställningar](https://console.aws.amazon.com/billing/home?#/preference) i AWS-konsolen.

1. På sidan Inställningar väljer **får fakturering rapporter**.
2. Under **får fakturering rapporter**, anger du namnet på en bucket som du skapade och klicka sedan på **Kontrollera**.  
3. Välj alla fyra rapportalternativ granularitet och klicka sedan på **Spara inställningar**.  
    ![Aktivera rapporter](./media/connect-aws-account/enable-reports.png)

Cloudyn hämtar detaljerad faktureringsinformation från S3-bucket och fylls rapporter efter detaljerad fakturering. Det kan ta upp till 24 timmar tills detaljerad faktureringsinformation visas i Cloudyn-konsolen. Om det detaljerade faktureringsdata status för ditt konto konsolidering visas som **konsoliderade**. Kontostatus visas som **slutförd**.

![Konto konsolideras Status](./media/connect-aws-account/consolidated-status.png)

Vissa av optimeringsrapporter kan kräva några få dagar för att hämta en tillräcklig data exempelstorlek för korrekta rekommendationer.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om Cloudyn kan fortsätta att den [granska användning och kostnader](tutorial-review-usage.md) självstudiekursen för Cloudyn.
