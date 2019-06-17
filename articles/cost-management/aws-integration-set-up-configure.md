---
title: Installera och konfigurera AWS kostnader och användning rapport-integrering med Azure Cost Management
description: Den här artikeln vägleder dig genom att installera och konfigurera AWS kostnader och användning rapport-integrering med Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 951178a82e0975f5f2af71bd48cf0f931246ae37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002128"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Installera och konfigurera AWS kostnader och användning rapport-integrering

Med Amazon Web Services (AWS) kostnader och användning rapporten (akt)-integrering, som du kan övervaka och styr dina AWS utgifter i Azure Cost Management. Integrationen kan en enda plats i Azure-portalen där du kan övervaka och styra utgifter för både Azure och AWS. Den här artikeln förklarar hur du ställer in integration och konfigurera den så att du använder Azure Cost Management-funktioner för att analysera kostnaderna och granska din budget.

Kostnad processer för AWS-kostnader och användning rapporten som lagras i en S3-bucket med hjälp av din AWS-autentiseringsuppgifter att hämta rapportdefinitioner och ladda ned rapporten GZIP CSV-filer.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Skapa en rapport för kostnader och användning i AWS

Med hjälp av en rapport för kostnader och användning är det AWS-rekommenderade sättet att samla in och bearbeta AWS-kostnaderna. Mer information finns i den [AWS-kostnaderna och användningsrapporten](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) dokumentation.

Använd den **kostnaden & användningsrapporter** sidan i konsolen för fakturering och kostnadshantering i AWS skapa en rapport för kostnader och användning med följande steg:

1. Logga in på AWS-hanteringskonsolen och öppna den [fakturering och kostnadshantering konsolen](https://console.aws.amazon.com/billing).
2. I navigeringsfönstret väljer **kostnaden & användningsrapporter**.
3. Välj **skapa rapport**.
4. För **rapportnamn**, ange ett namn för rapporten.
5. För **ytterligare rapportinformation** , för att ta med ID: N för varje resurs i rapporten och välj **inkludera resurs-ID: N**.
6. För **inställningar för datauppdatering**, Välj om du vill att rapporten AWS kostnader och användning för att uppdatera om AWS gäller återbetalningar, krediter, eller supportavgifter till ditt konto när du slutför din faktura. När en rapport har uppdaterats laddas en ny rapport till Amazon S3. Det är rekommenderat att lämna den här inställningen på.
7. Välj **Nästa**.
8. För **S3-bucket**, Välj **konfigurera**.
9. Gör något av följande i dialogrutan Konfigurera S3-Bucket:
    1. Välj en befintlig bucket från den nedrullningsbara listan och välj **nästa**.
    2. Ange ett Bucketnamn och den Region där du vill skapa en ny bucket och välj **nästa**.
10. Välj som jag har bekräftat att den här principen är korrigera och klickar du på Spara.
11. (Valfritt) För rapporten sökvägsprefix, anger du sökvägsprefix för rapporten som du vill prepended till namnet på rapporten.
Om du inte anger ett prefix, är standardprefixet det namn som du har angett för rapporten i steg 4 och datumintervall för rapporten i följande format: `/report-name/date-range/`
12. För **tidsenhet**, Välj **per timme**.
13. För **rapporten versionshantering**, Välj om du vill att varje version av rapporten att skriva över den tidigare versionen av rapporten eller som ska levereras utöver de tidigare versionerna.
14. För **aktivera dataintegrering för**, ingen markering krävs.
15. För **komprimering**väljer **GZIP**.
16. Välj **Nästa**.
17. När du har granskat inställningarna för rapporten, Välj **granska och slutför**.

    Observera rapportens namn. Du ska använda den i senare steg.

Det kan ta upp till 24 timmar för AWS att börja leverera rapporter till Amazon S3-bucket. När leverans har startat uppdaterar AWS rapportfiler AWS kostnader och användning på minst en gång om dagen. Du kan fortsätta konfigurera AWS-miljön utan att behöva vänta leverans att starta.

## <a name="create-a-role-and-policy-in-aws"></a>Skapa en roll och en princip i AWS

Azure Cost Management får åtkomst till en S3-bucket där rapporten kostnader och användning finns flera gånger per dag. Tjänsten behöver åtkomst till autentiseringsuppgifter för att söka efter nya data. Du skapar en roll och principen i AWS så att kostnadshantering att komma åt den.

Om du vill aktivera rollbaserad åtkomst till ett AWS-konto i Cost Management skapas rollen i AWS-konsolen. Du måste ha den _rollen ARN_ och _externt ID_ från AWS-konsolen. Senare kan du använda dem på den **skapar du en koppling för AWS** sidan i Cost Management.

Använd guiden Skapa en ny roll:

1. Logga in till din AWS-konsolen och välj **Services**.
2. Välj i listan över tjänster, **IAM**.
3. Välj **roller** och välj sedan **skapa roll**.
4. På nästa sida väljer **en annan AWS-konto**.
5. I **konto-ID**, ange **432263259397**.
6. I **alternativ**väljer **kräver externt ID (metodtips när en tredje part tar den här rollen)** .
7. I **externt ID**, ange externt ID. Den externa ID är ett delat lösenord mellan AWS-rollen och Azure Cost Management. Samma externa ID används också på den **ny koppling** sidan i Cost Management. Exempelvis kan ett externt ID liknar _Companyname1234567890123_.

    > [!NOTE]
    > Ändra inte valet för **kräver MFA**. Det bör vara omarkerad.
8. Välj **Nästa: Behörigheter**.
9. Välj **skapa princip**. En ny webbläsarflik öppnas. Det är där du skapar en princip.
10. Välj **väljer du en tjänst**.

Konfigurera behörighet för rapporten kostnader och användning:

1. Ange **kostnaden och användningsrapporten**.
2. Välj **åtkomstnivå** > **Läs** > **DescribeReportDefinitions**. Det här steget kan kostnadshantering att läsa vilka akt rapporter definieras och avgöra om de matchar nödvändiga för definition av rapporten.
3. Välj **lägga till ytterligare behörigheter**.

Konfigurera behörighet för S3-bucket och objekt:

1. Välj **väljer du en tjänst**.
2. Ange **S3**.
3. Välj **åtkomstnivå** > **lista** > **ListBucket**. Den här åtgärden hämtar listan över objekt i S3-Bucket.
4. Välj **åtkomstnivå** > **Läs** > **GetObject**. Denna åtgärd kan hämtning av fakturering filer.
5. Välj **resurser**.
6. Välj **bucket – Lägg till ARN**.
7. I **bucketnamnet**, ange en bucket som används för att lagra filerna som utför den.
8. Välj **objekt – Lägg till ARN**.
9. I **bucketnamnet**, ange en bucket som används för att lagra filerna som utför den.
10. I **objektnamn**väljer **alla**.
11. Välj **lägga till ytterligare behörigheter**.

Konfigurera behörighet för kostnaden Explorer:

1. Välj **väljer du en tjänst**.
2. Ange **kostnad Explorer Service**.
3. Välj **alla Explorer tjänster åtgärder (ce:\*)** . Den här åtgärden verifierar att samlingen är korrekt.
4. Välj **lägga till ytterligare behörigheter**.

Lägg till behörighet för AWS-organisationer:

1. Ange **organisationer**.
2. Välj **åtkomstnivå** > **lista** > **ListAccounts**. Den här åtgärden hämtar namnen på kontona.
3. I **granska princip**, ange ett namn för den nya principen. Kontrollera att du angett rätt information och välj sedan **skapa princip**.
4. Gå tillbaka till den föregående fliken och uppdatera webbsidan i webbläsaren. I sökfältet, söka efter den nya principen.
5. Välj **nästa: granska**.
6. Ange ett namn för den nya rollen. Kontrollera att du angett rätt information och välj sedan **skapa roll**.

    Obs rollen ARN och externa ID som användes i föregående steg när du skapade rollen. Du kommer att använda dem senare när du ställer in Azure Cost Management-anslutningstjänsten.

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

Använd följande information för att skapa en anslutningsapp för AWS och börja övervaka dina kostnader för AWS:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Gå till **Cost Management + fakturering** > **kostnadshantering**.
3. Under **inställningar**väljer **kopplingar (förhandsversion) i molnet**.  
    ![Exemplet visar Cloud-anslutningsappar (förhandsversion) inställningen)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Välj **+ Lägg till** överst på sidan för att skapa en anslutning.
5. På den **skapar du en koppling för AWS** sidan **visningsnamn**, ange ett namn för din anslutningsapp.  
    ![Exempel på sidan för att skapa en AWS-koppling](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Du kan också väljer du standard-hanteringsgrupp. Det lagrar alla identifierade länkade konton. Du kan konfigurera det senare.
7. I den **fakturering** väljer **automatiskt debiterar 1-% vid allmän tillgänglighet** om du vill säkerställa kontinuerlig drift när förhandsversionen upphör att gälla. Om du väljer det automatiska alternativet måste du välja en prenumeration för fakturering.
8. För **rollen ARN**, ange det värde som du använde när du installerar rollen i AWS.
9. För **externt ID**, ange det värde som du använde när du installerar rollen i AWS.
10. För **rapportnamn**, anger du namnet som du skapade i AWS.
11. Välj **nästa** och välj sedan **skapa**.

Det kan ta några timmar innan de nya AWS-scope, AWS konsoliderade konto och länkad AWS-konton och deras kostnadsdata ska visas.

När du har skapat kopplingen rekommenderar vi att du tilldela behörighet till den. Användare tilldelas behörigheter till de nyligen identifierade Omfattningarna: AWS sammanslagna kontot och länkad AWS-konton. Den användare som skapar anslutningen är ägare av anslutningen och det sammanslagna kontot alla länkade konton.

Tilldela behörigheter för anslutningsapp till användare när identifieringen sker tilldela inte behörigheter till de befintliga Omfattningarna för AWS. I stället tilldelas endast nya länkade konton behörigheter.

## <a name="take-additional-steps"></a>Vidta ytterligare åtgärder

- [Konfigurera hanteringsgrupper](../governance/management-groups/index.md#initial-setup-of-management-groups), om du inte redan har gjort.
- Kontrollera att nya scope läggs till i omfånget Väljaren. Välj **uppdatera** att visa den senaste informationen.
- På den **Cloud kopplingar** väljer du din anslutning och välj **går du till faktureringskonto** att tilldela kontot länkade till hanteringsgrupper.

## <a name="manage-cloud-connectors"></a>Hantera moln-kopplingar

När du väljer en anslutning på den **Cloud kopplingar** sidan kan du:

- Välj **går du till faktureringskonto** visa information för AWS sammanslagna kontot.
- Välj **Access Control** att hantera rolltilldelningen för anslutningen.
- Välj **redigera** att uppdatera anslutningen. Du kan inte ändra kontonummer AWS eftersom den visas i rollen ARN. Men du kan skapa en ny anslutning.
- Välj **Kontrollera** köra verifieringstest att se till att Cost Management kan samla in data med hjälp av inställningarna för koppling på nytt.

![Exempel kopplingslista skapade AWS](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Konfigurera Azure-hanteringsgrupper

Om du vill skapa en och samma plats om du vill visa information om molnet, måste du placera din Azure-prenumerationer och länkad AWS-konton i samma hanteringsgrupp. Om du inte redan har konfigurerat Azure-miljön med hanteringsgrupper, se [inledande installationen av hanteringsgrupper](../governance/management-groups/index.md#initial-setup-of-management-groups).

Om du vill separera kostnader kan du skapa en hanteringsgrupp som innehåller bara länkad AWS-konton.

## <a name="set-up-an-aws-consolidated-account"></a>Konfigurera AWS konsoliderat konto

AWS sammanslagna kontot kombinerar fakturering och betalning för flera AWS-konton. Det fungerar också som en länkad AWS-konto.

![Exempel-information för AWS konsoliderat konto](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Sidan kan du:

- Välj **uppdatera** Massuppdatering associationen mellan AWS länkad konton med en hanteringsgrupp.
- Välj **Access Control** att ställa in rolltilldelningen för detta scope.

### <a name="permissions-for-an-aws-consolidated-account"></a>Behörigheter för AWS konsoliderat konto

Som standard anges behörigheterna för en konsoliderad AWS-konto vid kontoskapandet, baserat på behörigheter för AWS-anslutningsapp. Connector skaparen är ägaren.

Du hanterar åtkomstnivån som med hjälp av den **åtkomstnivå** för AWS konsoliderat konto. Dock AWS länkade konton inte ärver behörigheter till konsoliderade AWS-konto.

## <a name="set-up-an-aws-linked-account"></a>Konfigurera ett länkad AWS-konto

Länkad AWS-kontot är där AWS-resurser skapas och hanteras. Ett länkat konto fungerar också som en säkerhetsgräns.

Från den här sidan kan du:

- Välj **uppdatera** att uppdatera associationen mellan en AWS länkat konto med en hanteringsgrupp.
- Välj **Access Control** att ställa in en rolltilldelning för detta scope.

![Exempel på sidan länkat AWS-konto](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>Behörigheter för AWS länkat konto

Behörigheter för ett länkat AWS-konto är som standard när de skapas, baserat på behörigheter för AWS-anslutningsapp. Connector skaparen är ägaren. Du hanterar åtkomstnivån som med hjälp av den **åtkomstnivå** för AWS länkat konto. AWS länkade konton inte ärver behörigheter från en konsoliderad AWS-konto.

AWS länkade konton alltid ärver behörigheter från den hanteringsgrupp som de tillhör.

## <a name="next-steps"></a>Nästa steg

- Nu när du har ställt in och konfigurerat AWS kostnader och användning rapport-integrering, fortsätter du till [hantera AWS kostnader och användning](aws-integration-manage.md).
- Om du är bekant med kostnadsanalys kan se [utforska och analysera kostnaderna med kostnadsanalys](quick-acm-cost-analysis.md) Snabbstart.
- Om du är bekant med budgetar i Azure kan du läsa [skapa och hantera Azure budgetar](tutorial-acm-create-budgets.md).
