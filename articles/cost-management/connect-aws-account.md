---
title: Ansluta en Amazon Web Services-konto till Cloudyn i Azure | Microsoft Docs
description: Anslut en Amazon Web Services-konto för att visa data för kostnader och användning i Cloudyn-rapporter.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 676c01a26d67b395340e5b1ed2dacc6b3b824742
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219743"
---
# <a name="connect-an-amazon-web-services-account"></a>Ansluta till en Amazon Web Services-konto

Du har två alternativ för att ansluta ditt konto för Amazon Web Services (AWS) till Cloudyn. Du kan ansluta med en IAM-roll eller med en skrivskyddad IAM-användarkonto. Rollen IAM rekommenderas eftersom du kan delegera åtkomst med definierade behörigheter till betrodda enheter. IAM-rollen måste inte du dela långsiktig åtkomstnycklar. När du ansluter ett AWS-konto till Cloudyn, är kostnader och användning data tillgängliga i Cloudyn-rapporter. Det här dokumentet hjälper dig att båda alternativen.

Mer information om AWS IAM-identiteter finns i [identiteter (användare, grupper och roller)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Dessutom du aktiverar AWS detaljerad fakturering rapporter och lagra informationen i en bucket med AWS simple storage service (S3). Detaljerad fakturering rapporterna innehåller debiterade avgifterna med information om taggen och resurs per timme. Lagra rapporterna kan Cloudyn att hämta dem från din bucket och visa informationen i rapporterna.


## <a name="aws-role-based-access"></a>AWS rollbaserad åtkomst

I följande avsnitt vägleder dig genom att skapa en skrivskyddad IAM-roll för att ge åtkomst till Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>Hämta ditt externa ID-konto för Cloudyn

Det första steget är att få unika anslutning lösenfrasen från Cloudyn-portalen. Den används i AWS som **externt ID**.

1. Öppna Cloudyn-portalen från Azure Portal eller navigera till [https://azure.cloudyn.com](https://azure.cloudyn.com) och logga in.
2. Klicka på kugg hjuls-symbolen och välj sedan **moln konton**.
3. I Accounts Management väljer du fliken **AWS Accounts** och klickar sedan på **Lägg till ny +** .
4. I dialog rutan **Lägg till AWS-konto** kopierar du det **externa ID: t** och sparar värdet för steg för att skapa AWS-roll i nästa avsnitt. Externt ID är unikt för ditt konto. I följande bild är exemplets externa ID _contoso_ följt av ett tal. Ditt ID skiljer sig åt.  
    ![externt ID som visas i rutan Lägg till AWS-konto](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Lägg till AWS skrivskyddad rollbaserad åtkomst

1. Logga in på AWS-konsolen på https://console.aws.amazon.com/iam/home och välj **roller**.
2. Klicka på **skapa roll** och välj sedan **ett annat AWS-konto**.
3. Klistra in `432263259397`i rutan **konto-ID** . Detta konto-ID är Cloudyn data collector kontot som tilldelats av AWS Cloudyn-tjänsten. Använd det exakta konto-ID som visas.
4. Bredvid **alternativ**väljer du **Kräv externt ID**. Klistra in ditt unika värde som kopierades tidigare från fältet **externt ID** i Cloudyn. Klicka sedan på **Nästa: behörigheter**.  
    ![klistra in externt ID från Cloudyn på sidan Skapa roll](./media/connect-aws-account/create-role01.png)
5. Under **bifoga behörighets principer**i rutan **princip typ** filter, skriver du `ReadOnlyAccess`, väljer **ReadOnlyAccess**och klickar sedan på **Nästa: granska**.  
    ![välja skrivskyddad åtkomst i listan över princip namn](./media/connect-aws-account/readonlyaccess.png)
6. På sidan Granska ser du till att dina val är korrekta och anger ett **roll namn**. Till exempel *Azure-Cost-service* Ange en **roll Beskrivning**. Till exempel _roll tilldelning för Cloudyn_och klicka sedan på **skapa roll**.
7. I listan **roller** klickar du på rollen som du skapade och kopierade **rollens ARN** -värde från sidan Sammanfattning. Använd rollen ARN (Amazon resursnamnet) värdet senare när du registrerar din konfiguration i Cloudyn.  
    ![kopiera rollen ARN från sammanfattnings sidan](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Konfigurera AWS IAM rollåtkomst i Cloudyn

1. Öppna Cloudyn-portalen från Azure Portal eller navigera till https://azure.cloudyn.com/ och logga in.
2. Klicka på kugg hjuls-symbolen och välj sedan **moln konton**.
3. I Accounts Management väljer du fliken **AWS Accounts** och klickar sedan på **Lägg till ny +** .
4. I **konto namn**anger du ett namn för kontot.
5. Bredvid **åtkomst typ**väljer du **IAM-rollen**.
6. I fältet **roll ARN** klistrar du in värdet som du tidigare kopierade och klickar sedan på **Spara**.  
    ![klistra in rollen ARN i rutan Lägg till AWS konto](./media/connect-aws-account/add-aws-account-box.png)


AWS-konto visas i listan över konton. **Ägar-ID: t** i listan matchar ditt roll ARN-värde. Din **konto status** bör ha en grön kryss markerings symbol som visar att Cloudyn kan komma åt ditt AWS-konto. Innan du aktiverar detaljerad AWS fakturering visas din konsoliderings status som **fristående**.

![AWS-kontostatus som visas på sidan konton](./media/connect-aws-account/aws-account-status01.png)

Cloudyn börjar samla in data och fylla i rapporter. Därefter [aktiverar du detaljerad AWS-fakturering](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>AWS användarbaserad åtkomsthantering

I följande avsnitt vägleder dig genom att skapa en skrivskyddad användare för att ge åtkomst till Cloudyn.

### <a name="add-aws-read-only-user-based-access"></a>Lägg till AWS användarbaserade läsbehörighet

1. Logga in på AWS-konsolen på https://console.aws.amazon.com/iam/home och välj **användare**.
2. Klicka på **Lägg till användare**.
3. I fältet **användar namn** anger du ett användar namn.
4. För **åtkomst typ**väljer du **programmatisk åtkomst** och klickar på **Nästa: behörigheter**.  
    ![ange ett användar namn på sidan Lägg till användare](./media/connect-aws-account/add-user01.png)
5. För behörigheter väljer du **koppla befintliga principer direkt**.
6. Under **bifoga behörighets principer**i rutan **princip typ** filter, skriver du `ReadOnlyAccess`, väljer **ReadOnlyAccess**och klickar sedan på **Nästa: granska**.  
    ![Välj ReadOnlyAccess för att ange behörigheter för användaren](./media/connect-aws-account/set-permission-for-user.png)
7. På sidan Granska ser du till att dina val är korrekta och klickar sedan på **skapa användare**.
8. På sidan har slutförts visas din nyckel-ID och hemlighet åtkomst åtkomstnyckel. Du kan använda den här informationen för att konfigurera registrering i Cloudyn.
9. Klicka på **Download. csv** och spara filen credentials. csv på en säker plats.  
    ![på Hämta. csv för att spara autentiseringsuppgifterna](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Konfigurera AWS IAM användarbaserade åtkomst i Cloudyn

1. Öppna Cloudyn-portalen från Azure Portal eller gå till https://azure.cloudyn.com/ och logga in.
2. Klicka på kugg hjuls-symbolen och välj sedan **moln konton**.
3. I Accounts Management väljer du fliken **AWS Accounts** och klickar sedan på **Lägg till ny +** .
4. För **konto namn**anger du ett konto namn.
5. Bredvid **åtkomst typ**väljer du **IAM-användare**.
6. I **åtkomst nyckeln**klistrar du in värdet för **Access Key ID** från filen credentials. csv.
7. I **hemlig nyckel**klistrar du in nyckel svärdet för **hemlig åtkomst** från filen credentials. csv och klickar sedan på **Spara**.  

AWS-konto visas i listan över konton. Din **konto status** bör ha en grön kryss markerings symbol.

Cloudyn börjar samla in data och fylla i rapporter. Därefter [aktiverar du detaljerad AWS-fakturering](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Aktivera detaljerad AWS-fakturering

Använd följande steg för att hämta dina AWS rollen ARN. Du kan använda rollen ARN för att bevilja läsbehörighet till en fakturering bucket.

1. Logga in på AWS-konsolen på https://console.aws.amazon.com och välj **tjänster**.
2. I Sök rutan tjänst skriver du *IAM*och väljer det alternativet.
3. Välj **roller** på menyn till vänster.
4. I listan över roller, väljer du den roll som du skapade för Cloudyn-åtkomst.
5. På sidan roll Sammanfattning klickar du på för att kopiera **rollen ARN**. Behåll rollen ARN praktiskt för senare steg.

### <a name="create-an-s3-bucket"></a>Skapa en S3-bucket

Du skapar en S3-bucket för att lagra detaljerad faktureringsinformation.

1. Logga in på AWS-konsolen på https://console.aws.amazon.com och välj **tjänster**.
2. I Sök rutan tjänst skriver du *S3*och väljer **S3**.
3. Klicka på **skapa Bucket**på sidan Amazon S3.
4. I guiden skapa Bucket väljer du ett Bucket-namn och en region och klickar sedan på **Nästa**.  
    ![exempel information som sidan Skapa Bucket](./media/connect-aws-account/create-bucket.png)
5. Behåll standardvärdena på sidan **Ange egenskaper** och klicka sedan på **Nästa**.
6. På sidan Granska klickar du på **skapa Bucket**. Bucket-listan visas.
7. Klicka på den Bucket som du skapade och välj fliken **behörigheter** och välj sedan **Bucket-princip**. Bucket-principredigeraren öppnas.
8. Kopiera följande JSON-exempel och klistra in den i Redigeraren för grupprinciper Bucket.
   - Ersätt `<BillingBucketName>` med namnet på din S3-Bucket.
   - Ersätt `<ReadOnlyUserOrRole>` med den roll-eller användar ARN som du tidigare kopierade.

   ```json
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

9. Klicka på **Save** (Spara).  
    ![på Spara i Bucket-princip redigeraren](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Aktivera AWS fakturering rapporter

När du har skapat och konfigurerat S3-Bucket går du till [fakturerings inställningar](https://console.aws.amazon.com/billing/home?#/preference) i AWS-konsolen.

1. På sidan Inställningar väljer du **ta emot fakturerings rapporter**.
2. Under **ta emot fakturerings rapporter**anger du namnet på den Bucket som du skapade och klickar sedan på **Verifiera**.  
3. Välj alla fyra alternativ för rapport granularitet och klicka sedan på **Spara inställningar**.  
    ![välja granularitet för att aktivera rapporter](./media/connect-aws-account/enable-reports.png)

Cloudyn hämtar detaljerad faktureringsinformation från S3-bucket och fylls rapporter efter detaljerad fakturering. Det kan ta upp till 24 timmar tills detaljerad faktureringsinformation visas i Cloudyn-konsolen. När detaljerade fakturerings data är tillgängliga visas konto konsoliderings status som **konsol IDE rad**. Konto status visas som **slutförd**.

![konsolideringsstatus visas på fliken AWS-konton](./media/connect-aws-account/consolidated-status.png)

Vissa av optimeringsrapporter kan kräva några få dagar för att hämta en tillräcklig data exempelstorlek för korrekta rekommendationer.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om Cloudyn fortsätter du till självstudien [Granska användning och kostnader](tutorial-review-usage.md) för Cloudyn.
