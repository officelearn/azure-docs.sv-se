---
title: Konfigurera AWS-integrering med Azure Cost Management
description: Den här artikeln beskriver hur du konfigurerar och konfigurerar AWS-integrering av kostnader och användnings rapporter med Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 5dbc6ca836c8d1c8b717fd4bf23eab5aa360a288
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988700"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Konfigurera och konfigurera AWS-integrering för kostnads-och användnings rapporter

Med Amazon Web Services (AWS) integrering av kostnader och användnings rapporter (akt) kan du övervaka och kontrol lera dina AWS-utgifter i Azure Cost Management. Integrationen gör att du kan använda en enda plats i Azure Portal där du övervakar och kontrollerar utgifter för både Azure och AWS. Den här artikeln förklarar hur du ställer in integreringen och konfigurerar den så att du kan använda Azure Cost Management funktioner för att analysera kostnader och granska budgetar.

Cost Management bearbetar AWS kostnad och användnings rapport som lagras i en S3-Bucket med hjälp av dina AWS-autentiseringsuppgifter för att hämta rapport definitioner och hämta rapport GZIP CSV-filer.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Skapa en kostnads-och användnings rapport i AWS

Att använda en kostnads-och användnings rapport är det AWS-rekommenderade sättet att samla in och bearbeta AWS-kostnader. Mer information finns i dokumentation om [AWS-kostnad och användnings rapporter](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) .

Använd sidan **kostnads & användnings rapporter** i fakturerings-och Cost Managements konsolen i AWS för att skapa en kostnads-och användnings rapport med följande steg:

1. Logga in på hanterings konsolen för AWS och öppna [fakturerings-och Cost Managements konsolen](https://console.aws.amazon.com/billing).
2. I navigerings fönstret väljer du **kostnads & användnings rapporter**.
3. Välj **Skapa rapport**.
4. I **rapport namn**anger du ett namn för rapporten.
5. Under **ytterligare rapport information**väljer du **Inkludera resurs-ID**.
6. För **Inställningar för data uppdatering**väljer du om du vill att rapporten AWS kostnad och användning ska uppdateras om AWS använder åter betalningar, krediter eller support avgifter till ditt konto när du har slutfört din faktura. När en rapport uppdateras överförs en ny rapport till Amazon S3. Vi rekommenderar att du låter inställningen vara vald.
7. Välj **Nästa**.
8. För **S3 Bucket**väljer du **Konfigurera**.
9. Gör något av följande i dialog rutan konfigurera S3 Bucket:
    1. Välj en befintlig Bucket i list rutan och välj **Nästa**.
    2. Ange ett Bucket-namn och den region där du vill skapa en ny Bucket och välj **Nästa**.
10. Välj **Jag har bekräftat att den här principen är korrekt**och klicka sedan på **Spara**.
11. Valfritt För prefixet för rapport Sök väg anger du det prefix för rapport Sök väg som du vill anpassningsprefix till namnet på rapporten.
Om du inte anger något prefix är standardprefixet det namn som du har angett för rapporten. Datum intervallet har formatet `/report-name/date-range/`.
12. Välj **varje timme**för **tidsenhet**.
13. För **rapport versions hantering**väljer du om du vill att varje version av rapporten ska skriva över den tidigare versionen, eller om du vill ha fler nya rapporter.
14. Ingen val krävs för **att aktivera data integrering för**.
15. För **komprimering**väljer du **gzip**.
16. Välj **Nästa**.
17. När du har granskat inställningarna för rapporten väljer du **Granska och slutför**.

    Notera rapport namnet. Du använder den i senare steg.

Det kan ta upp till 24 timmar innan AWS börjar leverera rapporter till Amazon S3-Bucket. När leveransen har påbörjats uppdaterar AWS AWS Cost-och Usage-rapporter minst en gång om dagen. Du kan fortsätta att konfigurera AWS-miljön utan att vänta på att leveransen ska starta.

## <a name="create-a-role-and-policy-in-aws"></a>Skapa en roll och princip i AWS

Azure Cost Management får åtkomst till S3-Bucket där rapporten kostnad och användning finns flera gånger per dag. Tjänsten behöver åtkomst till autentiseringsuppgifter för att söka efter nya data. Du skapar en roll och princip i AWS för att tillåta Cost Management åtkomst till den.

Om du vill aktivera rollbaserad åtkomst till ett AWS-konto i Cost Management skapas rollen i AWS-konsolen. Du måste ha _rollen ARN_ och _externt ID_ från AWS-konsolen. Senare använder du dem på sidan **skapa en AWS-koppling** i Cost Management.

Använd guiden skapa en ny roll:

1. Logga in på AWS-konsolen och välj **tjänster**.
2. I listan över tjänster väljer du **IAM**.
3. Välj **roller** och välj sedan **skapa roll**.
4. På nästa sida väljer du **ett annat AWS-konto**.
5. I **konto-ID**anger du **432263259397**.
6. I **alternativ**väljer du **Kräv externt ID (bästa praxis när en tredje part kommer att anta den här rollen)** .
7. I **externt ID**anger du det externa ID som är ett delat lösen ord mellan AWS-rollen och Azure Cost Management. Samma externa ID används också på sidan **ny koppling** i Cost Management. Microsoft rekommenderar att du använder en stark lösen ords princip när du anger det externa ID: t.

    > [!NOTE]
    > Ändra inte valet för **KRÄV MFA**. Den bör förbli avmarkerad.
8. Välj **Nästa: behörigheter**.
9. Välj **Skapa princip**. En ny webbläsarflik öppnas. Det är där du skapar en princip.
10. Välj **Välj en tjänst**.

Konfigurera behörighet för rapporten kostnad och användning:

1. Ange **rapporten kostnad och användning**.
2. Välj **åtkomst nivå** > **läsa** > **DescribeReportDefinitions**. I det här steget kan Cost Management läsa vilka aktuella rapporter som definieras och avgöra om de matchar rapport definitionens krav.
3. Välj **Lägg till ytterligare behörigheter**.

Konfigurera behörighet för S3-Bucket och-objekt:

1. Välj **Välj en tjänst**.
2. Ange **S3**.
3. Välj **åtkomst nivå** > **lista** > **ListBucket**. Den här åtgärden hämtar listan över objekt i S3-Bucket.
4. Välj **åtkomst nivå** > **läsa** > **GetObject**. Med den här åtgärden kan du ladda ned fakturerings filer.
5. Välj **resurser**.
6. Välj **Bucket – Lägg till ARN**.
7. I **Bucket-namn**anger du den Bucket som används för att lagra de akt filerna.
8. Välj **objekt – Lägg till ARN**.
9. I **Bucket-namn**anger du den Bucket som används för att lagra de akt filerna.
10. I **objekt namn**väljer du **valfri**.
11. Välj **Lägg till ytterligare behörigheter**.

Konfigurera behörighet för Cost Explorer:

1. Välj **Välj en tjänst**.
2. Ange **tjänsten Cost Explorer**.
3. Välj **alla tjänst åtgärder i Cost Explorer (CE:\*)** . Den här åtgärden kontrollerar att samlingen är korrekt.
4. Välj **Lägg till ytterligare behörigheter**.

Lägg till behörighet för AWS-organisationer:

1. Ange **organisationer**.
2. Välj **åtkomst nivå** > **lista** > **ListAccounts**. Den här åtgärden hämtar namnen på kontona.
3. I **Granska princip**anger du ett namn för den nya principen. Kontrol lera att du har angett rätt information och välj sedan **Skapa princip**.
4. Gå tillbaka till föregående flik och uppdatera webbläsarens webb sida. Sök efter din nya princip i Sök fältet.
5. Välj **Nästa: granska**.
6. Ange ett namn för den nya rollen. Kontrol lera att du har angett rätt information och välj sedan **skapa roll**.

    Observera rollens ARN och det externa ID som användes i föregående steg när du skapade rollen. Du kommer att använda dem senare när du konfigurerar Azure Cost Management-anslutningen.

Princip-JSON bör likna följande exempel. Ersätt _bucketname_ med namnet på din S3-Bucket.

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
            "ce:*",
            "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-aws-connector-in-azure"></a>Konfigurera en ny AWS-koppling i Azure

Använd följande information för att skapa en AWS-koppling och börja övervaka dina AWS-kostnader:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Gå till **Cost Management + fakturering** > **Cost Management**.
3. Under **Inställningar**väljer du **moln anslutningar (förhands granskning)** .  
    ![exempel som visar inställningen för moln anslutningar (förhands granskning)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Välj **+ Lägg till** överst på sidan för att skapa en koppling.
5. På sidan **skapa en AWS-koppling** , i **visnings namn**, anger du ett namn för din anslutning.  
    ![exempel på sidan för att skapa en AWS-koppling](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Du kan också välja standard hanterings gruppen. Alla identifierade länkade konton sparas. Du kan ställa in det senare.
7. I **fakturerings** avsnittet väljer du **automatiskt debitera 1% vid allmän tillgänglighet** om du vill säkerställa kontinuerlig drift när förhands granskningen går ut. Om du väljer alternativet automatisk måste du välja en fakturerings prenumeration.
8. För **roll ARN**anger du det värde som du använde när du konfigurerade rollen i AWS.
9. För **externt ID**anger du det värde som du använde när du konfigurerade rollen i AWS.
10. I **rapport namn**anger du det namn som du skapade i AWS.
11. Välj **Nästa** och välj sedan **skapa**.

Det kan ta några timmar för de nya AWS-omfattningarna, AWS konsoliderat konto, AWS länkade konton och deras kostnads data att visas.

När du har skapat anslutningen rekommenderar vi att du tilldelar åtkomst kontroll till den. Användare tilldelas behörigheter till nyligen identifierade omfattningar: AWS konsoliderat konto och AWS länkade konton. Den användare som skapar anslutningen är kontaktens ägare, det konsoliderade kontot och alla länkade konton.

Tilldela anslutnings behörigheter till användare efter identifieringen ger inte behörighet till de befintliga AWS-omfattningarna. I stället tilldelas endast nya länkade konton behörigheter.

## <a name="take-additional-steps"></a>Vidta ytterligare åtgärder

- [Konfigurera hanterings grupper](../../governance/management-groups/overview.md#initial-setup-of-management-groups), om du inte redan gjort det.
- Kontrol lera att nya omfattningar läggs till i din omfattnings väljare. Välj **Uppdatera** för att visa den senaste informationen.
- På sidan **moln anslutningar** väljer du din anslutning och väljer **gå till fakturerings konto** för att tilldela det länkade kontot till hanterings grupper.

## <a name="manage-cloud-connectors"></a>Hantera moln anslutningar

När du väljer en koppling på sidan **moln anslutningar** kan du:

- Välj **gå till fakturerings konto** om du vill visa information om det KONSOLIDERAde AWS-kontot.
- Välj **Access Control** för att hantera roll tilldelningen för anslutningen.
- Välj **Redigera** för att uppdatera anslutningen. Du kan inte ändra konto numret för AWS, eftersom det visas i rollen ARN. Men du kan skapa en ny anslutning.
- Välj **Verifiera** för att köra verifierings testet igen för att se till att Cost Management kan samla in data med hjälp av anslutnings inställningarna.

![Exempel lista över skapade AWS-kopplingar](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Konfigurera hanterings grupper för Azure

Placera dina Azure-prenumerationer och AWS-länkade konton i samma hanterings grupp för att skapa en enda plats där du kan se information om flera moln leverantörer. Om du inte redan har konfigurerat Azure-miljön med hanterings grupper kan du läsa den [första installationen av hanterings grupper](../../governance/management-groups/overview.md#initial-setup-of-management-groups).

Om du vill särskilja kostnader kan du skapa en hanterings grupp som bara innehåller AWS länkade konton.

## <a name="set-up-an-aws-consolidated-account"></a>Konfigurera ett konsoliderat AWS-konto

Det konsoliderade AWS-kontot kombinerar fakturering och betalning för flera AWS-konton. Det fungerar också som ett AWS-länkat konto.

![Exempel information för ett AWS konsoliderat konto](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

På sidan kan du:

- Välj **Uppdatera** för att massredigera kopplingen av AWS länkade konton till en hanterings grupp.
- Välj **Access Control** för att ange roll tilldelningen för omfånget.

### <a name="permissions-for-an-aws-consolidated-account"></a>Behörigheter för ett konsoliderat AWS-konto

Som standard anges behörigheter för ett AWS konsol IDE rad konto när kontot skapas, baserat på AWS anslutnings behörigheter. Connector-Skaparen är ägare.

Du hanterar åtkomst nivån genom att använda sidan **åtkomst nivå** i det KONSOLIDERAde AWS-kontot. AWS länkade konton ärver dock inte behörigheter till det konsoliderade AWS-kontot.

## <a name="set-up-an-aws-linked-account"></a>Konfigurera ett AWS-länkat konto

Det länkade AWS-kontot är där AWS-resurser skapas och hanteras. Ett länkat konto fungerar också som en säkerhets gränser.

Från den här sidan kan du:

- Välj **Uppdatera** för att uppdatera associationen för ett AWS-länkat konto med en hanterings grupp.
- Välj **Access Control** för att ange en roll tilldelning för omfånget.

![Exempel på sidan AWS-länkat konto](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>Behörigheter för ett AWS-länkat konto

Som standard anges behörigheter för ett AWS-länkat konto när de skapas, baserat på AWS anslutnings behörigheter. Connector-Skaparen är ägare. Du hanterar åtkomst nivån genom att använda sidan **åtkomst nivå** i det länkade AWS-kontot. AWS länkade konton ärver inte behörigheter från ett AWS-konsoliderat konto.

AWS-länkade konton ärver alltid behörigheter från den hanterings grupp som de tillhör.

## <a name="next-steps"></a>Nästa steg

- Nu när du har konfigurerat och konfigurerat AWS för kostnads-och användnings rapporter fortsätter du med att [Hantera AWS-kostnader och användning](aws-integration-manage.md).
- Om du inte är bekant med kostnads analys läser du [utforska och analysera kostnader med snabb start för kostnads analys](quick-acm-cost-analysis.md) .
- Om du inte är bekant med budgetar i Azure kan du läsa [skapa och hantera Azure-budgetar](tutorial-acm-create-budgets.md).
