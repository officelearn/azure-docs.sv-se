---
title: Konfigurera AWS-integrering med Azure Cost Management
description: Den här artikeln beskriver hur du konfigurerar integreringen av kostnads- och användningsrapporter från AWS med Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.openlocfilehash: e900d63ba7e521cbf7e63d8580d22b08726d1ef6
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517352"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Konfigurera integreringen av kostnads- och användningsrapporter från AWS

Med integreringen av kostnader och användningsrapporter från AWS (Amazon Web Services) kan du övervaka och styra dina AWS-utgifter i Azure Cost Management. Tack vare integreringen har du en enda plats i Azure-portalen där du övervakar och styr utgifterna för både Azure och AWS. I den här artikeln beskrivs hur du konfigurerar integreringen så att du kan använda Azure Cost Management-funktioner till att analysera kostnader och granska budgetar.

Cost Management bearbetar kostnads- och användningsrapporterna från AWS som lagras i en S3-bucket och använder dina AWS-autentiseringsuppgifter för att kunna hämta rapportdefinitioner och ladda ner rapportens GZIP CSV-filer.

Titta på videon [How to set up Connectors for AWS in Cost Management](https://www.youtube.com/watch?v=Jg5KC1cx5cA) (så konfigurerar du anslutningsprogram för AWS i Cost Management) om du vill veta mer om hur du konfigurerar AWS-rapportintegrering. Om du vill titta på andra videor går du till [YouTube-kanalen för Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/Jg5KC1cx5cA]

## <a name="create-a-cost-and-usage-report-in-aws"></a>Skapa en kostnads- och användningsrapport i AWS

Att använda en kostnads- och användningsrapport är det sätt som rekommenderas av AWS för att samla in och bearbeta AWS-kostnader. Mer information finns i dokumentationen om [kostnads- och användningsrapporter från AWS](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html).

Använd sidan **Kostnads- och användningsrapporter** i konsolen för fakturering och Cost Management i AWS till att skapa en kostnads- och användningsrapport med följande steg:

1. Logga in på hanteringskonsolen för AWS och öppna [konsolen för fakturering och Cost Management](https://console.aws.amazon.com/billing).
2. I navigeringsfönstret väljer du **Kostnads- och användningsrapporter** .
3. Välj **Skapa rapport** .
4. Ange ett namn på rapporten i **Rapportnamn** .
5. Under **Ytterligare rapportinformation** väljer du **Inkludera resurs-ID:n** .
6. I **Inställningar för datauppdatering** väljer du om du vill att kostnads- och användningsrapporterna från AWS ska uppdateras om AWS tillämpar återbetalning, kreditering eller supportavgifter på ditt konto efter att du har betalat din faktura. När en rapport uppdateras laddas en ny rapport upp på Amazon S3. Vi rekommenderar att du låter inställningen vara vald.
7. Välj **Nästa** .
8. I **S3-bucket** väljer du **Konfigurera** .
9. I dialogrutan Konfigurera S3-bucket anger du ett bucketnamn och den region där du vill skapa en ny bucket och väljer **Nästa** .
10. Välj **Jag har kontrollerat att principen är korrekt** och klicka sedan på **Spara** .
11. (Valfritt) Som prefix för rapportens sökväg anger du det prefix som du vill tillägga före namnet på rapporten.
Om du inte anger något prefix är standardprefixet det namn som du har angett för rapporten. Datumintervallet har formatet `/report-name/date-range/`.
12. Som **Tidsenhet** väljer du **Varje timme** .
13. Som **Rapportversion** väljer du om varje ny version av rapporten ska skriva över den tidigare versionen, eller om fler nya rapporter ska skapas.
14. I **Aktivera dataintegrering för** krävs inget val.
15. Välj **GZIP** som **Komprimering** .
16. Välj **Nästa** .
17. När du har granskat inställningarna för rapporten väljer du **Granska och slutför** .

    Anteckna rapportnamnet. Du kommer att använda det senare.

Det kan ta upp till 24 timmar innan AWS börjar leverera rapporter till Amazons S3-bucket. När leveransen har påbörjats uppdateras kostnads- och användningsrapportfilerna från AWS minst en gång om dagen. Du kan fortsätta att konfigurera AWS-miljön utan att vänta på att leveransen ska starta.

## <a name="create-a-role-and-policy-in-aws"></a>Skapa roller och principer i AWS

Azure Cost Management går till den S3-bucket där kostnads- och användningsrapporten finns flera gånger per dag. Tjänsten behöver åtkomst till autentiseringsuppgifterna för att kunna söka efter nya data. Du skapar en roll och en princip i AWS som ger Cost Management åtkomst till dem.

Om du vill aktivera rollbaserad åtkomst till ett AWS-konto i Cost Management, skapas rollen i AWS-konsolen. Du måste ha _roll-ARN_ och _externt ID_ från AWS-konsolen. Senare använder du dem på sidan **Skapa en AWS-koppling** i Cost Management.

Använd guiden Skapa en ny roll:

1. Logga in på AWS-konsolen och välj **Tjänster** .
2. Välj **IAM** i listan med tjänster.
3. Välj **Roller** och sedan **Skapa roll** .
4. På nästa sida väljer du **Ett annat AWS-konto** .
5. I **Konto-ID** anger du **432263259397** .
6. I **Alternativ** väljer du **Kräv externt ID (lämplig metod när en tredje part kommer att ha rollen)** .
7. I **Externt ID** anger du det externa ID som är ett delat lösenord mellan AWS-rollen och Azure Cost Management. Samma externa ID används också på sidan **Ny koppling** i Cost Management. Microsoft rekommenderar att du använder en stark lösenordsprincip när du anger det externa ID:t.
    > [!NOTE]
    > Ändra inte valet för **Kräv MFA** . Den bör förbli avmarkerad.
8. Välj **Nästa: Behörigheter** .
9. Välj **Skapa princip** . En ny webbläsarflik öppnas. Det är där du skapar principen.
10. Välj **Välj en tjänst** .

Konfigurera behörighet för kostnads- och användningsrapporten:

1. Ange **Kostnads- och användningsrapport** .
2. Välj **Åtkomstnivå** > **Läsa** > **DescribeReportDefinitions** . I det här steget kan Cost Management läsa vilka aktuella kostnads- och användningsrapporter som har definierats och avgöra om de matchar rapportdefinitionens krav.
3. Välj **Lägg till ytterligare behörigheter** .

Konfigurera behörigheten för din S3-bucket och dina objekt:

1. Välj **Välj en tjänst** .
2. Ange **S3** .
3. Välj **Åtkomstnivå** > **Lista** > **ListBucket** . Den här åtgärden hämtar listan med objekt i din S3-bucket.
4. Välj **Åtkomstnivå** > **Läsa** > **GetObject** . Med den här åtgärden kan du ladda ned faktureringsfiler.
5. Välj **Resurser** .
6. Välj **bucket – Lägg till ARN** .
7. I **Bucketnamn** anger du den bucket som används för att lagra kostnads- och användningsrapportfilerna.
8. Välj **objekt – Lägg till ARN** .
9. I **Bucketnamn** anger du den bucket som används för att lagra kostnads- och användningsrapportfilerna.
10. Välj **Alla** i **Objektnamn** .
11. Välj **Lägg till ytterligare behörigheter** .

Konfigurera behörighet för Cost Explorer:

1. Välj **Välj en tjänst** .
2. Ange **Cost Explorer-tjänst** .
3. Välj **Alla tjänståtgärder i Cost Explorer (ce:\*)** . Den här åtgärden kontrollerar att samlingen är korrekt.
4. Välj **Lägg till ytterligare behörigheter** .

Lägg till behörighet för AWS-organisationer:

1. Ange **Organisationer** .
2. Välj **Åtkomstnivå** > **Lista** > **ListAccounts** . Den här åtgärden hämtar namnen på kontona.
3. I **Granska princip** anger du ett namn på den nya principen. Kontrollera att du har angett rätt information och välj sedan **Skapa princip** .
4. Gå tillbaka till föregående flik och uppdatera webbläsarsidan. Sök efter din nya princip i sökfältet.
5. Välj **Nästa: Granskning** .
6. Ange ett namn på den nya rollen. Kontrollera att du har angett rätt information och välj sedan **Skapa roll** .

    Anteckna roll-ARN och det externa ID som användes i föregående steg när du skapade rollen. Du kommer att använda dem senare när du konfigurerar Azure Cost Management-kopplingen.

Principens JSON bör likna nedanstående exempel. Ersätt _bucketname_ med namnet på din S3-bucket.

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

## <a name="set-up-a-new-connector-for-aws-in-azure"></a>Konfigurera ett nytt anslutningsprogram för AWS i Azure

Använd följande information för att skapa en AWS-koppling och börja övervaka dina AWS-kostnader:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Gå till **Cost Management och fakturering** > **Cost Management** .
3. Välj **Anslutningsprogram för AWS** under **Inställningar** .  
4. Välj **+ Lägg till** överst på sidan för att skapa en koppling.  
    :::image type="content" source="./media/aws-integration-setup-configure/aws-connector.png" alt-text="Exempel som visar inställningen Anslutningsprogram för AWS" :::
1. På sidan **Skapa anslutning** i **Visningsnamn** anger du ett namn på anslutningsprogrammet.  
    :::image type="content" source="./media/aws-integration-setup-configure/create-aws-connector01.png" alt-text="Exempel som visar inställningen Anslutningsprogram för AWS" :::
1. Du kan välja standardhanteringsgrupp om du vill. I den lagras alla identifierade länkade konton. Du kan konfigurera detta senare.
1. I avsnittet **Fakturering** väljer du **På** för **Förnya automatiskt** om du vill säkerställa kontinuerlig drift. Om du väljer alternativet Automatisk måste du välja en faktureringsprenumeration.
1. I **Roll-ARN** anger du det värde som du använde när du konfigurerade rollen i AWS.
1. I **Externt ID** anger du det värde som du använde när du konfigurerade rollen i AWS.
1. I **Rapportnamn** anger du det namn som du skapade i AWS.
1. Välj **Nästa** och sedan **Skapa** .

Det kan ta några timmar innan de nya AWS-omfången, det AWS-konsoliderade kontot, AWS-länkade konton och deras kostnadsdata visas.

När du har skapat kopplingen rekommenderar vi att du tilldelar åtkomstkontroll till den. Användarna tilldelas behörighet till de nya identifierade omfången: Det AWS-konsoliderade kontot och AWS-länkade konton. Den användare som skapar kopplingen är ägare av kopplingen, det konsoliderade kontot och alla länkade konton.

Att kopplingsbehörighet tilldelas till användarna efter identifieringen ger inte behörighet till befintliga AWS-omfång. I stället tilldelas endast behörighet till nya länkade konton.

## <a name="take-additional-steps"></a>Ytterligare steg

- [Konfigurera hanteringsgrupper](../../governance/management-groups/overview.md#initial-setup-of-management-groups), om du inte redan har gjort det.
- Kontrollera att nya omfång läggs till i din omfångsväljare. Välj **Uppdatera** för att se den senaste informationen.
- På sidan **Molnanslutningsappar** väljer du din koppling och **Gå till faktureringskonto** för att tilldela det länkade kontot till hanteringsgrupper.

> [!NOTE]
> Hanteringsgrupper stöds för närvarande inte för kunder med Microsoft-kundavtal (MCA). MCA-kunder kan skapa anslutningsprogrammet och visa sina AWS-data. Däremot kan inte MCA-kunder se sina Azure-kostnader och AWS-kostnader tillsammans under en hanteringsgrupp.

## <a name="manage-aws-connectors"></a>Hantera AWS-anslutningsprogram

När du väljer ett anslutningsprogram på sidan **Anslutningsprogram för AWS** kan du:

- Välja **Gå till faktureringskonto** om du vill se information om det AWS-konsoliderade kontot.
- Välja **Access Control** för att hantera rolltilldelningen för kopplingen.
- Välja **Redigera** för att uppdatera kopplingen. Du kan inte ändra kontonumret för AWS, eftersom det visas i roll-ARN. Men du kan skapa en ny koppling.
- Välj **Verifiera** för att köra verifieringstestet igen och se att Cost Management kan samla in data med hjälp av kopplingsinställningarna.

:::image type="content" source="./media/aws-integration-setup-configure/aws-connector-details.png" alt-text="Exempel som visar inställningen Anslutningsprogram för AWS" :::

## <a name="set-up-azure-management-groups"></a>Konfigurera Azure-hanteringsgrupper

Placera dina Azure-prenumerationer och AWS-länkade konton i samma hanteringsgrupp för att skapa en enda plats där du kan se molntäckande providerinformation. Om du inte redan har konfigurerat din Azure-miljö med hanteringsgrupper, kan du läsa [Initial konfiguration av hanteringsgrupper](../../governance/management-groups/overview.md#initial-setup-of-management-groups).

Om du vill dela upp kostnader kan du skapa en hanteringsgrupp som bara innehåller AWS-länkade konton.

## <a name="set-up-an-aws-consolidated-account"></a>Konfigurera ett AWS-konsoliderat konto

Det AWS-konsoliderade kontot kombinerar fakturering och betalning för flera AWS-konton. Det fungerar också som ett AWS-länkat konto. Du kan visa informationen för ditt AWS-konsoliderade konto med hjälp av länken på sidan Anslutningsprogram för AWS. 

:::image type="content" source="./media/aws-integration-setup-configure/aws-consolidated-account01.png" alt-text="Exempel som visar inställningen Anslutningsprogram för AWS" :::

På sidan kan du:

- Välja **Uppdatera** för att massuppdatera associeringen av AWS-länkade konton med en hanteringsgrupp.
- Välja **Access Control** för att ange rolltilldelningen för omfånget.

### <a name="permissions-for-an-aws-consolidated-account"></a>Behörighet för ett AWS-konsoliderat konto

Som standard anges behörigheten för ett AWS-konsoliderat konto när kontot skapas, baserat på AWS-kopplingens behörighet. Den som skapar kopplingen är ägaren.

Du hanterar åtkomstnivån genom att använda sidan **Åtkomstnivå** på det AWS-konsoliderade kontot. AWS-länkade konton ärver dock inte någon behörighet till det AWS-konsoliderade kontot.

## <a name="set-up-an-aws-linked-account"></a>Konfigurera ett AWS-länkat konto

Det AWS-länkade kontot är den plats där AWS-resurser skapas och hanteras. Ett länkat konto fungerar också som en säkerhetsgräns.

På sidan kan du:

- Välja **Uppdatera** för att uppdatera associeringen av ett AWS-länkat konto med en hanteringsgrupp.
- Välja **Access Control** för att ange en rolltilldelning för omfånget.

:::image type="content" source="./media/aws-integration-setup-configure/aws-linked-account01.png" alt-text="Exempel som visar inställningen Anslutningsprogram för AWS" :::

### <a name="permissions-for-an-aws-linked-account"></a>Behörighet för ett AWS-länkat konto

Som standard anges behörigheten för ett AWS-länkat konto när kontot skapas, baserat på AWS-kopplingens behörighet. Den som skapar kopplingen är ägaren. Du hanterar åtkomstnivån genom att använda sidan **Åtkomstnivå** på det AWS-länkade kontot. AWS-länkade konton ärver dock inte någon behörighet från ett AWS-konsoliderat konto.

AWS-länkade konton ärver alltid behörighet från den hanteringsgrupp som de tillhör.

## <a name="next-steps"></a>Nästa steg

- Nu när du har konfigurerat integreringen av kostnads- och användningsrapporter från AWS, fortsätter du med att [Hantera AWS-kostnader och användning](aws-integration-manage.md).
- Om du inte är bekant med kostnadsanalys kan du gå till snabbstarten [Utforska och analysera kostnader med kostnadsanalys](quick-acm-cost-analysis.md).
- Om du inte är bekant med budgetar i Azure, kan du läsa [Skapa och hantera Azure-budgetar](tutorial-acm-create-budgets.md).
