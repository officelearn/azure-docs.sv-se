---
title: Ansluta en Amazon Web Services-konto till Azure kostnaden Management | Microsoft Docs
description: Ansluta en Amazon Web Services-konto om du vill visa kostnad och användningsdata i kostnad rapporter.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: c2c7ea043d2da41442829321ac663325f30ff066
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297336"
---
# <a name="connect-an-amazon-web-services-account"></a>Ansluta en Amazon Web Services-konto

Du har två alternativ för att ansluta ditt konto Amazon Web Services (AWS) till Azure kostnaden Management. Du kan ansluta med en IAM-roll eller med ett skrivskyddat användarkonto för IAM. Rollen IAM rekommenderas eftersom du kan delegera åtkomst med definierade behörighet till betrodda enheter. Rollen IAM behöver du inte dela långsiktiga åtkomstnycklar. När du ansluter ett AWS-konto till kostnaden Management är kostnad och användningsdata tillgängliga i kostnaden Management rapporter. Det här dokumentet hjälper dig att båda alternativen.

Mer information om AWS IAM identiteter finns [identiteter (användare, grupper och roller)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Aktivera också AWS detaljerad fakturering rapporter och lagra informationen i en AWS enkla service (S3) bucket. Detaljerad fakturerings-rapporterna innehåller fakturering tillägg med information om taggen och resurser på timbasis. Lagra rapporterna ger kostnaden hantering att hämta dem från din bucket och visa informationen i rapporterna.


## <a name="aws-role-based-access"></a>AWS rollbaserad åtkomst

Följande avsnitt beskriver hur du skapar en skrivskyddad IAM-roll för att ge åtkomst till hantering av kostnaden.

### <a name="get-your-cost-management-account-external-id"></a>Hämta kostnaden Management konto externa-ID

Det första steget är att hämta lösenfrasen unik anslutning från hanteringsportalen för Azure kostnaden. Den används i AWS som den **externa ID**.

1. Öppna Cloudyn portalen från Azure-portalen eller navigera till [ https://azure.cloudyn.com ](https://azure.cloudyn.com) och logga in.
2. Klicka på symbolen kugge och välj sedan **moln konton**.
3. I hantering av konton, väljer du den **AWS konton** och klicka sedan på **Lägg till ny +**.
4. I den **Lägg till konto AWS** dialogrutan, kopiera den **externa ID** och spara värdet för AWS rollen skapa stegen i nästa avsnitt. Det externa ID: T är unikt för ditt konto. I följande bild, exemplet externa ID: T är _Contoso_ följt av ett tal. Ditt ID skiljer sig åt.  
    ![Externt ID](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Lägg till AWS skrivskyddad rollbaserad åtkomst

1. Logga in på AWS-konsolen vid https://console.aws.amazon.com/iam/home och välj **roller**.
2. Klicka på **skapa roll** och välj sedan **en annan AWS konto**.
3. I den **konto-ID** klistra in `432263259397`. Detta konto-ID är kostnad Management data collector kontot som tilldelats av AWS Cloudyn-tjänsten. Använd det exakta konto-ID som visas.
4. Bredvid **alternativ**väljer **kräver externa ID**. Klistra in din unikt värde som tidigare kopieras från den **externa ID** i kostnaden Management. Klicka på **nästa: behörigheter**.  
    ![Skapa en roll](./media/connect-aws-account/create-role01.png)
5. Under **ansluta behörigheterna principer**i den **principtypen** filter rutan Sök, skriver `ReadOnlyAccess`väljer **ReadOnlyAccess**, klicka på **nästa: Granska**.  
    ![Skrivskyddad åtkomst](./media/connect-aws-account/readonlyaccess.png)
6. På sidan Granska Kontrollera inställningarna är korrekta och ange en **rollnamn**. Till exempel *Azure-kostnad-Mgt*. Ange en **Rollbeskrivning**. Till exempel _rolltilldelning för Azure kostnaden Management_, klicka på **skapa roll**.
7. I den **roller** listan, klicka på den roll som du har skapat och kopiera den **rollen ARN** värde från sammanfattningssidan. Använda rollen ARN (Amazon resursnamnet) värdet senare när du registrerar din konfiguration i Azure kostnaden Management.  
    ![Rollen ARN](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>Konfigurera AWS IAM rollåtkomst i kostnaden Management

1. Öppna Cloudyn portalen från Azure-portalen eller navigera till https://azure.cloudyn.com/ och logga in.
2. Klicka på symbolen kugge och välj sedan **moln konton**.
3. I hantering av konton, väljer du den **AWS konton** och klicka sedan på **Lägg till ny +**.
4. I **kontonamn**, Skriv ett namn för kontot.
5. Bredvid **behörighet**väljer **IAM rollen**.
6. I den **rollen ARN** fältet, klistra in det värde som du kopierade tidigare och klicka sedan på **spara**.  
    ![Lägga till AWS-konto](./media/connect-aws-account/add-aws-account-box.png)


AWS-konto visas i listan över konton. Den **ägar-ID** i listan matchar rollen ARN-värde. Din **kontostatus** ska ha en grön bock symbol som anger att kostnaden Management kan komma åt ditt AWS-konto. Tills du aktiverar detaljerade AWS fakturering konsolideringsstatus visas som **fristående**.

![Status för AWS](./media/connect-aws-account/aws-account-status01.png)

Kostnad Management börjar samla in data och skapa rapporter. Nästa [Aktivera detaljerad AWS fakturering](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>AWS användarbaserade åtkomst

Följande avsnitt beskriver hur du skapar en skrivskyddad användare för att ge åtkomst till hantering av kostnaden.

### <a name="add-aws-read-only-user-based-access"></a>Lägg till AWS användarbaserade läsbehörighet

1. Logga in på AWS-konsolen vid https://console.aws.amazon.com/iam/home och välj **användare**.
2. Klicka på **lägga till användare**.
3. I den **användarnamn** skriver du ett användarnamn.
4. För **komma åt typen**väljer **Programmeringsåtkomst** och på **nästa: behörigheter**.  
    ![Lägg till användare](./media/connect-aws-account/add-user01.png)
5. Behörigheter, Välj **koppla befintliga principer direkt**.
6. Under **ansluta behörigheterna principer**i den **principtypen** filter rutan Sök, skriver `ReadOnlyAccess`väljer **ReadOnlyAccess**, och klicka sedan på **nästa : Granska**.  
    ![Ange behörigheter för användare](./media/connect-aws-account/set-permission-for-user.png)
7. Se till att inställningarna är korrekta och klicka sedan på sidan Granska **skapa användare**.
8. På sidan har slutförts visas din nyckel-ID och Hemlig åtkomst snabbtangent. Du kan använda den här informationen för att konfigurera registrering i hantering av kostnaden.
9. Klicka på **hämta .csv** och spara filen credentials.csv till en säker plats.  
    ![Hämta autentiseringsuppgifter](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cost-management"></a>Konfigurera AWS IAM användarbaserade åtkomst i kostnaden Management

1. Öppna Cloudyn portalen från Azure-portalen eller navigera till https://azure.cloudyn.com/ och logga in.
2. Klicka på symbolen kugge och välj sedan **moln konton**.
3. I hantering av konton, väljer du den **AWS konton** och klicka sedan på **Lägg till ny +**.
4. För **kontonamn**, ange ett kontonamn.
5. Bredvid **behörighet**väljer **IAM användaren**.
6. I **åtkomstnyckeln**, klistra in den **åtkomst nyckeln ID** värdet från filen credentials.csv.
7. I **hemlig nyckel**, klistra in den **hemliga åtkomstnyckeln** värdet från credentials.csv-filen och klicka sedan på **spara**.  

AWS-konto visas i listan över konton. Din **kontostatus** ska ha en grön bock symbol.

Kostnad Management börjar samla in data och skapa rapporter. Nästa [Aktivera detaljerad AWS fakturering](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Aktivera detaljerad AWS fakturering

Använd följande steg för att få din AWS rollen ARN. Du kan använda rollen ARN för att bevilja läsbehörighet till en fakturering bucket.

1. Logga in på AWS-konsolen vid https://console.aws.amazon.com och välj **Services**.
2. Sök i tjänsten skriver *IAM*, och väljer det alternativet.
3. Välj **roller** i den vänstra menyn.
4. Markera den roll som du skapade för Cloudyn åtkomst i listan över roller.
5. På sidan Rollsammanfattning Klicka för att kopiera den **rollen ARN**. Behåll rollen ARN praktisk för senare steg.

### <a name="create-an-s3-bucket"></a>Skapa en S3-bucket

Du skapar ett S3-bucket för att lagra detaljerad faktureringsinformation.

1. Logga in på AWS-konsolen vid https://console.aws.amazon.com och välj **Services**.
2. Sök i tjänsten skriver *S3*, och välj **S3**.
3. På sidan Amazon S3 **skapa bucket**.
4. I guiden Skapa bucket väljer ett Bucket-namn och en Region och klicka sedan på **nästa**.  
    ![Skapa bucket](./media/connect-aws-account/create-bucket.png)
5. På den **ange egenskaper för** , Behåll standardinställningarna och klicka sedan på **nästa**.
6. På sidan Granska **skapa bucket**. Bucket-listan visas.
7. Klicka på en bucket som du skapade och välj den **behörigheter** och markera sedan **Bucket princip**. Redigeraren för Bucket öppnas.
8. Kopiera följande JSON-exempel och klistra in den i Redigeraren för Bucket.
  - Ersätt `<BillingBucketName>` med namnet på S3-bucket.
  - Ersätt `<ReadOnlyUserOrRole>` med rollen eller användaren ARN som du tidigare har kopierat.

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
    ![Bucket redigeraren](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Aktivera AWS fakturering rapporter

När du skapar och konfigurerar S3-bucket går du till [fakturering inställningar](https://console.aws.amazon.com/billing/home?#/preference) i AWS-konsolen.

1. På sidan Inställningar väljer **får fakturering rapporter**.
2. Under **får fakturering rapporter**, ange namnet på en bucket som du skapade och klicka sedan på **Kontrollera**.  
3. Välj alla fyra rapportera granularitet alternativ och klickar sedan på **Spara inställningar**.  
    ![Aktivera rapporter](./media/connect-aws-account/enable-reports.png)

Kostnad Management hämtar detaljerad faktureringsinformation från S3-bucket och fyller rapporter efter detaljerad fakturering har aktiverats. Det kan ta upp till 24 timmar tills detaljerad faktureringsinformation visas i konsolen Cloudyn. När det finns detaljerad faktureringsinformation status för ditt konto konsolidering visas som **konsoliderade**. Kontostatus som ska visas som **slutförd**.

![Kontot konsoliderade Status](./media/connect-aws-account/consolidated-status.png)

Vissa av optimering rapporter kan kräva data att hämta en lämplig data provtagning för korrekt rekommendationer för några dagar.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om Azure kostnaden Management kan fortsätta att den [granska användning och kostnader](tutorial-review-usage.md) självstudiekurs för hantering av kostnaden.
