---
title: Installera och konfigurera AWS kostnader och användning rapport-integrering med Azure Cost Management
description: Den här artikeln vägleder dig om att installera och konfigurera AWS kostnader och användning rapport-integrering med Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 688bcc02b14d101008afc76662fd6548446cb329
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870289"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Installera och konfigurera AWS kostnader och användning rapport-integrering

Med Amazon Web Services kostnader och användning rapport-integrering kan du övervaka och styr dina AWS utgifter i Azure Cost Management. Integrationen kan en enda plats i Azure-portalen där du kan övervaka och styra utgifter för både Azure och AWS. Den här artikeln förklarar hur du ställer in integration och konfigurera den så att du använda Cost Management-funktioner för att analysera kostnaderna och granska din budget.

Kostnadshantering läser rapporten AWS kostnader och användning lagras i en S3-bucket med hjälp av din AWS-autentiseringsuppgifter att hämta rapportdefinitioner och ladda ned rapporten GZIP CSV-filer.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Skapa en rapport för kostnader och användning i AWS

AWS rekommenderade sätt att samla in och bearbeta AWS-kostnaderna är att använda en rapport för kostnader och användning. Mer information finns i den [AWS-kostnaderna och användningsrapporten](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) dokumentationsartikel.

Använd den **rapporter** sidan i konsolen för fakturering och kostnadshantering i AWS skapa en rapport för kostnader och användning med följande steg.

1. Logga in på AWS-hanteringskonsolen och öppna konsolen för fakturering och kostnadshantering i https://console.aws.amazon.com/billing.
2. I navigeringsfönstret klickar du på **rapporter**.
3. Klicka på **skapa rapport**.
4. För **rapportnamn**, Skriv ett namn för rapporten.
5. För **tidsenhet**, Välj **per timme**.
6. För **inkludera**lägger du till ID: N för varje resurs i rapporten och välj **resurs-ID: N**.
7. För **aktivera stöd för**, ingen markering krävs.
8. För **inställningar för datauppdatering**väljer **automatiskt uppdatera dina kostnader &amp; användningsrapporten när avgifter identifieras i föregående månader med stängd fakturor**.
9. Klicka på **Nästa**.
10. För **Amazon S3-bucket**, skriver du namnet på Amazon S3-bucket där du vill att rapporter som levereras till och klickar på **Kontrollera**. Bucket måste ha behörighet att vara giltigt. Läs mer om att lägga till behörigheter i bucketen [inställningen Bucket och åtkomstbehörigheter för objektet](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/set-permissions.html).
11. För **rapporten sökvägsprefix**, ange det prefix för rapporten sökväg som ska läggas till i namnet på rapporten.
12. För **komprimering**väljer **GZIP**.
13. Klicka på **Nästa**.
14. När du har granskat inställningarna för rapporten, klicka på **granska och slutför**.
    Obs den **rapportnamn**. Du kan använda den i senare steg.

Det kan ta upp till 24 timmar för AWS att börja leverera rapporter till Amazon S3-bucket. När leverans har startat uppdaterar AWS rapportfiler AWS kostnader och användning på minst en gång om dagen.

## <a name="create-a-role-and-policy-in-aws"></a>Skapa en roll och en princip i AWS

Azure Cost Management får åtkomst till en S3-bucket där rapporten kostnader och användning finns flera gånger per dag. Kostnadshantering behöver åtkomst till autentiseringsuppgifter för att söka efter nya data. Du skapar en roll och principen i AWS att tillåta åtkomst av Cost Management.

Om du vill aktivera rollbaserad åtkomst till ett AWS-konto i Azure Cost Management skapas rollen i AWS-konsolen. Du måste ha den _rollen ARN_ och _externt ID_ från AWS-konsolen. Senare kan använda du dem i avsnittet Skapa en anslutningsapp-sidan för AWS i Azure Cost Management.

1. Logga in på din AWS-konsolen och välj **Services**.
2. Välj i listan över tjänster, **IAM**.
3. Välj **roller** och klicka sedan på **skapa roll**.
4. I nästa sida väljer **en annan AWS-konto**.
5. I **konto-ID** , ange _432263259397_.
6. I **alternativ**väljer **kräver externt ID (metodtips när en tredje part tar den här rollen)**.
7. I **externt ID**, ange den externa-ID. Den externa ID är ett delat lösenord mellan AWS-rollen och Azure Cost Management. Samma externa ID används också på sidan ny koppling i Cost Management. Exempelvis kan ett externt ID liknar _Companyname1234567890123_.
    Ändra inte valet för **kräver MFA**. Det bör vara omarkerad.
8. Klicka på **Nästa: Behörigheter**.
9. Klicka på **skapa princip**. En ny webbläsarflik öppnas där du skapar en ny princip.
10. Klicka på **väljer du en tjänst**.
11. Typ **kostnaden och användningsrapporten**.
12. Välj **åtkomstnivå**, **Läs** > **DescribeReportDefinitions**. På så sätt kan du kostnadshantering läsa vad utför den rapporterar definieras och avgöra om de matchar nödvändiga för definition av rapporten.
13. Klicka på **lägga till ytterligare behörigheter**.
14. Klicka på **väljer du en tjänst**.
15. Typ _S3_.
16. Välj **åtkomstnivå**, **lista** > **ListBucket**. Den här åtgärden hämtar listan över objekt i S3-Bucket.
17. Välj **åtkomstnivå**, **Läs** > **GetObject**. Denna åtgärd kan fakturering hämtning av filer.
18. Välj **resurser**.
19. Välj **bucket – Lägg till ARN**.
20. I **bucketnamnet**, ange en bucket som används för att lagra filerna som utför den.
21. Välj **objekt – Lägg till ARN**.
22. I **bucketnamnet**, ange en bucket som används för att lagra filerna som utför den.
23. I **objektnamn**väljer **alla**.
24. Klicka på **lägga till ytterligare behörigheter**.
25. Klicka på **väljer du en tjänst**.
26. Typ _kostnad Explorer Service_.
27. Välj **alla Explorer tjänster åtgärder (ce:\*)**. Den här åtgärden verifierar att samlingen är korrekt.
28. Klicka på **lägga till ytterligare behörigheter**.
29. Typ **organisationer**.
30. Välj **åtkomstnivån, listan** > **ListAccounts**. Den här åtgärden hämtar namnen på kontona.
31. I **granska princip**, ange ett namn för den nya principen. Kontroll för att kontrollera att du har angett rätt information och klicka sedan på **skapa princip**.
32. Gå tillbaka till den föregående fliken och uppdatera webbsidan i webbläsaren. I sökfältet söker du efter den nya principen.
33. Välj **nästa: granska**.
34. Ange ett namn för den nya rollen. Kontroll för att kontrollera att du har angett rätt information och klicka sedan på **skapa roll**.
    Obs den **rollen ARN** och **externt ID** använde i föregående steg när du skapade rollen. Du kan använda dem senare när du ställer in Azure Cost Management-anslutningstjänsten.

Princip-JSON bör likna följande exempel. Ersätt _bucketname_ med namnet på din S3-bucket.

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

## <a name="set-up-a-new-aws-connector-in-azure"></a>Konfigurera en ny AWS-anslutning i Azure

Använd följande information för att skapa en ny koppling för AWS och börja övervaka din AWS-kostnaderna.

1. Logga in på Azure Portal på https://portal.azure.com.
2. Gå till **Cost Management + fakturering** > **kostnadshantering**.
3. Under **inställningar**, klickar du på **kopplingar (förhandsversion) i molnet**.  
    ![Exemplet visar Cloud-anslutningsappar (förhandsversion inställning)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Klicka på **+ Lägg till** överst på sidan för att skapa en ny anslutning.
5. På sidan Skapa en AWS connector, skriver en **visningsnamn** att namnge din anslutningsapp.  
    ![Exempel för att skapa en koppling för AWS-sida](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Du kan också väljer du standard-hanteringsgrupp. Det lagrar alla identifierade länkade konton. Du kan konfigurera det senare.
7. Under den **fakturering** väljer **automatiskt debiterar 1-% vid allmän tillgänglighet** om du vill säkerställa kontinuerlig drift när förhandsversionen upphör att gälla. Om du väljer det automatiska alternativet måste du välja en fakturering **prenumeration**.
8. Ange den **rollen ARN**. Det är det värde som du använde när du installerar rollen i AWS.
9. Ange den **externt ID**. Det är det värde som du använde när du installerar rollen i AWS.
10. Ange den **rapportnamn** som du skapade i AWS.
11. Klicka på **nästa** och klicka sedan på **skapa**.

Det kan ta några timmar innan nya AWS-scope, AWS konsolideras-konto och länkade AWS-konton och deras kostnadsdata ska visas.

När du har skapat anslutningen, rekommenderar vi att du tilldela behörighet till anslutningen. Användare tilldelas behörigheter till de nyligen identifierade Omfattningarna: AWS konsoliderade konto och AWS länkade konton. Den användare som skapar anslutningen är ägare av koppling, konsoliderade konto och alla länkade konton.

Tilldela behörigheter för anslutningsapp till användare när identifieringen sker tilldela inte behörigheter till de befintliga Omfattningarna för AWS. I stället tilldelas endast nya länkade konton behörigheter.

## <a name="additional-steps"></a>Ytterligare steg

- [Konfigurera hanteringsgrupper](../governance/management-groups/index.md#initial-setup-of-management-groups), om du inte redan har gjort.
- Kontrollera att nya scope läggs till i omfånget Väljaren. Glöm inte att klicka på **uppdatera** att visa den senaste informationen.
- På sidan Cloud connector, Välj din anslutning och klicka på **går du till faktureringskonto** att tilldela kontot länkade till hanteringsgrupper.

## <a name="manage-cloud-connectors"></a>Hantera moln-kopplingar

När du väljer en koppling på sidan anslutningsprogram för molnet, kan du:

- Klicka på **går du till faktureringskonto** att visa konsoliderade AWS-kontoinformation.
- Klicka på **Access Control** att hantera rolltilldelningen för anslutningen.
- Klicka på **redigera** att uppdatera anslutningen. Du kan inte ändra antal AWS-konto, som det visas i rollen ARN. Du kan dock skapa en ny anslutning.
- Klicka på **Kontrollera** köra verifieringstest att se till att Cost Management kan du samla in data med hjälp av inställningarna för koppling på nytt.

![Exempel som visar listan över skapade AWS-kopplingar](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="role-of-azure-management-groups"></a>Rollen för Azure-hanteringsgrupper

Om du vill skapa en och samma plats om du vill visa information om molnet, måste du placera din Azure-prenumerationer och länkad AWS-konton i samma hanteringsgrupp. Om du inte redan har konfigurerat Azure-miljön med hanteringsgrupper, se [inledande installationen av hanteringsgrupper](../governance/management-groups/index.md#initial-setup-of-management-groups).

Om du vill separera kostnader kan du skapa en hanteringsgrupp som innehåller bara länkad AWS-konton.

## <a name="aws-consolidated-account"></a>Konsoliderade AWS-konto

Konsoliderade AWS-konto används för att konsolidera fakturering och betalning för flera AWS-konton. AWS sammanslagna kontot fungerar också som ett länkat AWS-konto.

![Exempel på AWS-konsoliderad kontoinformation](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Från sidan kan du:

- Klicka på **uppdatera** till Massuppdatering AWS länkade konton kopplingen till en hanteringsgrupp.
- Klicka på **Access Control** att ställa in rolltilldelningen för detta scope.

### <a name="aws-consolidated-account-permissions"></a>AWS sammanställs behörigheterna

Som standard konsoliderat i AWS konto behörigheterna har ställts in när de skapas, baserat på behörigheter för AWS-anslutningsapp. Connector skaparen är ägaren.

Du kan hantera åtkomstnivån som med hjälp av sidan åtkomstnivå i AWS konsolideras-kontot. Dock behörigheter till AWS konsoliderat konto ärvs inte av den länkade AWS-konton.

## <a name="aws-linked-account"></a>Länkat AWS-konto

Det länkade AWS-kontot är där AWS-resurser skapas och hanteras. Ett länkat konto fungerar också som en säkerhetsgräns.

Från den här sidan kan du:

- Klicka på **uppdatera** att uppdatera länkade AWS-kontoassociation med en hanteringsgrupp.
- Klicka på **Access Control** att ställa in en rolltilldelning för detta scope.

![Exempel på sidan länkat AWS-konto](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="aws-linked-account-permissions"></a>Behörighet för den länkade AWS

AWS länkade behörigheterna är som standard när de skapas, baserat på behörigheter för AWS-anslutningsapp. Connector skaparen är ägaren. Du kan hantera åtkomstnivån som med hjälp av sidan åtkomstnivå för AWS länkat konto. Behörigheterna för kontot AWS konsolideras ärvs inte av den länkade AWS-konton.

Länkade AWS-konton kan du alltid ärver behörigheter från den hanteringsgrupp som de tillhör.

## <a name="next-steps"></a>Nästa steg

- Nu när du har ställt in och konfigurerat AWS kostnader och användning rapport-integrering, fortsätter du till [hantera AWS kostnader och användning](aws-integration-manage.md).
- Om du är bekant med kostnadsanalys kan se [utforska och analysera kostnaderna med kostnadsanalys](quick-acm-cost-analysis.md) Snabbstart.
- Om du är bekant med budgetar i Azure kan du läsa [skapa och hantera Azure budgetar](tutorial-acm-create-budgets.md) självstudien.
