---
title: Ansluta ett Amazon Web Services-konto till Cloudyn i Azure | Microsoft Docs
description: Anslut ett Amazon Web Services-konto om du vill visa kostnader och användningsdata i Cloudyn-rapporter.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ms.openlocfilehash: dcb4c30fe485559834791fa567856bc78cff067e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76770336"
---
# <a name="connect-an-amazon-web-services-account"></a>Ansluta ett Amazon Web Services-konto

Du har två alternativ för att ansluta ditt Amazon Web Services-konto (AWS) till Cloudyn. Du kan ansluta med en IAM-roll eller med ett skrivskyddat IAM-användarkonto. IAM-rollen rekommenderas eftersom du kan delegera åtkomst med definierade behörigheter till betrodda entiteter. IAM-rollen kräver inte att du delar långsiktiga åtkomstnycklar. När du har anslutit ett AWS-konto till Cloudyn finns kostnader och användningsdata i Cloudyn-rapporter. Det här dokumentet vägleder dig genom båda alternativen.

Mer information om AWS IAM-identiteter finns i [Identiteter (användare, grupper och roller)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Dessutom aktiverar du detaljerade faktureringsrapporter för AWS och lagrar informationen i en AWS S3-bucket (Simple Storage Service). Detaljerade faktureringsrapporter inkluderar faktureringsavgifter med tagg- och resursinformation per timme. När rapporterna lagras kan Cloudyn hämta dem från din bucket och visa informationen i sina rapporter.


## <a name="aws-role-based-access"></a>Rollbaserad åtkomst för AWS

Följande avsnitt beskriver hur du skapar en skrivskyddad IAM-roll för att ge åtkomst till Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>Hämta externt ID för ditt Cloudyn-konto

Det första steget är att hämta den unika anslutningslösenfrasen från Cloudyn-portalen. Den används i AWS som **externt ID**.

1. Öppna Cloudyn-portalen från Azure-portalen eller gå till [https://azure.cloudyn.com](https://azure.cloudyn.com) och logga in.
2. Klicka på kugghjulssymbolen och välj **Molnkonton**.
3. I Kontohantering väljer du fliken **AWS-konton** och klickar sedan på **Lägg till nytt +** .
4. I dialogrutan **Lägg till AWS-konto** kopierar du **externt ID** och sparar värdet för stegen för att skapa AWS-roll i nästa avsnitt. Det externa ID:t är unikt för ditt konto. I följande bild är exemplet på externt ID _Contoso_ följt av ett tal. Ditt ID är annorlunda.  
    ![Externt ID som visas i rutan Lägg till AWS-konto](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Lägga till skrivskyddad rollbaserad åtkomst för AWS

1. Logga in på AWS-konsolen på https://console.aws.amazon.com/iam/home och välj **Roller**.
2. Klicka på **Skapa roll** och välj sedan **Ett annat AWS-konto**.
3. I rutan **Konto-ID** klistrar du in `432263259397`. Detta konto-ID är Cloudyn-datainsamlingskonto som tilldelats av AWS till Cloudyn-tjänsten. Använd det exakta konto-ID som visas.
4. Bredvid **Alternativ** väljer du **Kräv externt ID**. Klistra in ditt unika värde som tidigare kopierades från fältet **Externt ID** i Cloudyn. Klicka sedan på **Nästa: Behörigheter**.  
    ![klistra in externt ID från Cloudyn på sidan Skapa roll](./media/connect-aws-account/create-role01.png)
5. Under **Koppla behörighetsprinciper** går du till filtersökrutan för **Principtyp** och skriver `ReadOnlyAccess`. Välj **ReadOnlyAccess** och klicka sedan på **Nästa: Granskning**.  
    ![välja skrivskyddad åtkomst i listan över principnamn](./media/connect-aws-account/readonlyaccess.png)
6. På sidan Granska kontrollerar du att dina val är korrekta och skriver ett **rollnamn**. Det kan till exempel vara *Azure-Cost-Mgt*. Ange en **Rollbeskrivning**. Det kan till exempel vara _Rolltilldelning för Cloudyn_. Klicka sedan på **Skapa roll**.
7. I listan **Roller** klickar du på den roll som du skapade och kopierar värdet för **Roll-ARN** från sidan Sammanfattning. Använd värdet för roll-ARN (Amazon Resource Name) senare när du registrerar konfigurationen i Cloudyn.  
    ![kopiera roll-ARN från sidan Sammanfattning](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Konfigurera IAM-rollåtkomst för AWS i Cloudyn

1. Öppna Cloudyn-portalen från Azure-portalen eller gå till https://azure.cloudyn.com/ och logga in.
2. Klicka på kugghjulssymbolen och välj **Molnkonton**.
3. I Kontohantering väljer du fliken **AWS-konton** och klickar sedan på **Lägg till nytt +** .
4. I **Kontonamn** skriver du ett namn för kontot.
5. Intill **Åtkomsttyp** väljer du **IAM-roll**.
6. I fältet **Roll-ARN** klistrar du in det värde som du tidigare kopierade och klickar sedan på **Spara**.  
    ![klistra in roll-ARN i rutan Lägg till AWS-konto](./media/connect-aws-account/add-aws-account-box.png)


Ditt AWS-konto visas i listan över konton. Det **Ägar-ID** som visas i listan matchar ditt värde för Roll-ARN. Din **Kontostatus** ska ha en grön kryssmarkering som visar att Cloudyn kan komma åt ditt AWS-konto. Innan du aktiverar detaljerad AWS fakturering visas din konsolideringsstatus som **Fristående**.

![AWS-kontostatus som visas på sidan Kontohantering](./media/connect-aws-account/aws-account-status01.png)

Cloudyn börjar samla in data och fylla i rapporter. Sedan [aktiverar du detaljerad AW-fakturering](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Användarbaserad åtkomst för AWS

Följande avsnitt beskriver hur du skapar en skrivskyddad användare för att ge åtkomst till Cloudyn.

### <a name="add-aws-read-only-user-based-access"></a>Lägga till skrivskyddad användarbaserad åtkomst för AWS

1. Logga in på AWS-konsolen på https://console.aws.amazon.com/iam/home och välj **Användare**.
2. Klicka på **Lägg till användare**.
3. I fältet **Användarnamn** skriver du ett användarnamn.
4. För **Åtkomsttyp** väljer du **Programmatisk åtkomst** och klickar på **Nästa: Behörigheter**.  
    ![ange ett användarnamn på sidan Lägg till användare](./media/connect-aws-account/add-user01.png)
5. För behörigheter väljer du **Koppla befintliga principer direkt**.
6. Under **Koppla behörighetsprinciper** går du till filtersökrutan för **Principtyp** och skriver `ReadOnlyAccess`. Välj **ReadOnlyAccess** och klicka sedan på **Nästa: Granskning**.  
    ![välja ReadOnlyAccess för att ange behörigheter för användaren](./media/connect-aws-account/set-permission-for-user.png)
7. På sidan Granska kontrollerar du att dina val är korrekta och klickar sedan på **Skapa användare**.
8. På sidan Slutfört visas ditt åtkomstnyckel-ID och din hemliga åtkomstnyckel. Du använder den här informationen för att konfigurera registrering i Cloudyn.
9. Klicka på **Ladda ned .csv** och spara filen credentials.csv på en säker plats.  
    ![klicka på Ladda ned .csv för att spara autentiseringsuppgifterna](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Konfigurera användarbaserad IAM-åtkomst för AWS i Cloudyn

1. Öppna Cloudyn-portalen från Azure Portal eller gå till https://azure.cloudyn.com/ och logga in.
2. Klicka på kugghjulssymbolen och välj **Molnkonton**.
3. I Kontohantering väljer du fliken **AWS-konton** och klickar sedan på **Lägg till nytt +** .
4. I **Kontonamn** skriver du ett kontonamn.
5. Intill **Åtkomsttyp** väljer du **IAM-användare**.
6. I **Åtkomstnyckel** klistrar du värdet för **åtkomstnyckel-ID** från filen credentials.csv.
7. I **Hemlig nyckel** klistrar du in värdet för **Hemlig åtkomstnyckel** från filen credentials.csv och klickar sedan på **Spara**.  

Ditt AWS-konto visas i listan över konton. Din **Kontostatus** bör ha en grön kryssmarkering.

Cloudyn börjar samla in data och fylla i rapporter. Sedan [aktiverar du detaljerad AW-fakturering](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Aktivera detaljerad AW-fakturering

Använd följande steg för att hämta din roll-ARN för AWS. Du använder roll-ARN för att bevilja läsbehörighet till en faktureringsbucket.

1. Logga in på AWS-konsolen på https://console.aws.amazon.com och välj **Tjänster**.
2. I sökrutan för tjänster skriver du *IAM* och väljer det alternativet.
3. Välj **Roller** på menyn till vänster.
4. I listan över roller väljer du den roll som du skapade för Cloudyn-åtkomst.
5. På sidan Rollsammanfattning klickar du för att kopiera **Roll-ARN**. Ha roll-ARN till hands för senare steg.

### <a name="create-an-s3-bucket"></a>Skapa en S3-bucket

Du skapar en S3-bucket för att lagra detaljerad faktureringsinformation.

1. Logga in på AWS-konsolen på https://console.aws.amazon.com och välj **Tjänster**.
2. I sökrutan för tjänster skriver du *S3* och väljer **S3**.
3. På Amazon S3-sidan klickar du på **Skapa bucket**.
4. I guiden Skapa bucket väljer du ett bucketnamn och en region och klickar sedan på **Nästa**.  
    ![exempelinforation på sidan Skapa bucket](./media/connect-aws-account/create-bucket.png)
5. På sidan **Ange egenskaper** behåller du standardvärdena och klickar sedan på **Nästa**.
6. På sidan Granska klickar du på **Skapa bucket**. Din bucketlista visas.
7. Klicka på den bucket som du skapade, välj fliken **Behörigheter** och välj sedan **Bucketprincip**. Bucketprincipredigeraren öppnas.
8. Kopiera följande JSON-exempel och klistra in det i bucketprincipredigeraren.
   - Ersätt `<BillingBucketName>` med namnet på din S3-bucket.
   - Ersätt `<ReadOnlyUserOrRole>` med den roll- eller användar-ARN som du tidigare kopierade.

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

9. Klicka på **Spara**.  
    ![klicka på Spara i bucketprincipredigeraren](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Aktivera AWS-faktureringsrapporter

När du har skapat och konfigurerat S3-bucketen går du till [Faktureringsinställningar](https://console.aws.amazon.com/billing/home?#/preference) i AWS-konsolen.

1. På sidan Inställningar väljer du **Ta emot faktureringsrapporter**.
2. Under **Ta emot faktureringsrapporter** anger du namnet på den bucket som du skapade och klickar sedan på **Verifiera**.  
3. Välj alla fyra alternativ för rapportkornighet och klicka sedan på **Spara inställningar**.  
    ![välja kornighet för att aktivera rapporter](./media/connect-aws-account/enable-reports.png)

Cloudyn hämtar detaljerad faktureringsinformation från S3-bucketen och fyller i rapporter efter att detaljerad fakturering har aktiverats. Det kan ta upp till 24 timmar innan detaljerade faktureringsdata visas i Cloudyn-konsolen. När detaljerade faktureringsdata är tillgängliga visas din status för kontokonsolidering som **Konsoliderat**. Kontostatusen visas som **Slutförd**.

![konsolideringsstatus som visas på fliken AWS-konton](./media/connect-aws-account/consolidated-status.png)

Vissa optimeringsrapporter kan kräva några dagars data för att få en lämplig dataurvalsstorlek för korrekta rekommendationer.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om Cloudyn kan du fortsätta till självstudien [Granska användning och kostnader](tutorial-review-usage.md) för Cloudyn.
