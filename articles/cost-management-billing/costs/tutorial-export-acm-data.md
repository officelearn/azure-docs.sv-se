---
title: Självstudie – Skapa och hantera exporterade data från Azure Cost Management
description: Den här artikeln visar hur du kan skapa en hantera exporterade Azure Cost Management-data så att du kan använda dem i externa system.
author: bandersmsft
ms.author: banders
ms.date: 08/05/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 69b7b4bff46ba2998ca931ba1cb6bc9e7c1d9096
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272228"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Självstudier: Skapa och hantera exporterade data

Om du har läst självstudien om kostnadsanalys är du bekant med att manuellt ladda ned Cost Management-data. Du kan dock skapa en återkommande uppgift som automatiskt exporterar Cost Management-data till Azure Storage varje dag, vecka eller månad. Exporterade data är i CSV-format och innehåller all information som samlas in av Cost Management. Du kan sedan använda exporterade data i Azure Storage med externa system och kombinera dem med dina egna anpassade data. Och du kan använda din exporterade data i ett externt system, till exempel en instrumentpanel eller något annat ekonomisystem.

Titta på videon [How to schedule exports to storage with Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) (Så här schemalägger du exporter till Storage med Azure Cost Management) om hur du skapar en schemalagd export av dina Azure-kostnadsdata till Azure Storage. Om du vill titta på andra videor går du till [YouTube-kanalen för Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

Exemplen i den här självstudien vägleder dig genom export av Cost Management-data och kontrollerar sedan att data har exporterats.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en daglig export
> * Kontrollera att data samlas in

## <a name="prerequisites"></a>Krav
Dataexport är tillgänglig för en mängd olika typer av Azure-konton, exempelvis för kunder med [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) och [Microsoft-kundavtal](get-started-partners.md). Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](understand-cost-mgt-data.md). Följande Azure-behörigheter, eller omfång, stöds per prenumeration för dataexport efter användare och grupp. Mer information om omfång finns i [Förstå och arbeta med omfång](understand-work-scopes.md).

- Ägare – kan skapa, ändra eller ta bort schemalagda exporter för en prenumeration.
- Deltagare – kan skapa, ändra eller ta bort sina egna schemalagda exporter. Kan ändra namnet på schemalagda exporter som skapats av andra.
- Läsare – kan schemalägga exporter som läsaren har behörighet till.

För Azure Storage-konton:
- Det krävs skrivbehörigheter för att ändra det konfigurerade lagringskontot, oavsett behörigheter för exporten.
- Ditt Azure Storage-konto måste konfigureras för blob- eller fillagring.

Om du har en ny prenumeration kan du inte använda Cost Management-funktioner direkt. Det kan ta upp till 48 timmar innan du kan använda alla Cost Management-funktioner.

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Skapa en daglig export

Om du vill skapa eller visa en dataexport, eller om du vill schemalägga en export, öppnar du önskat omfång i Azure-portalen och väljer **Kostnadsanalys** i menyn. Du kan till exempel gå till **Prenumerationer**, välja en prenumeration i listan och sedan välja **Kostnadsanalys** i menyn. Överst på sidan Kostnadsanalys väljer du **Inställningar** och **Exporter**.

> [!NOTE]
> - Utöver prenumerationer kan du skapa exporter för resursgrupper, hanteringsgrupper, avdelningar och registreringar. Mer information om omfång finns i [Förstå och arbeta med omfång](understand-work-scopes.md).
>- När du är inloggad som partner i faktureringskontots omfång eller i en kunds klientorganisation kan du exportera data till ett Azure Storage-konto som är länkat till ditt partnerlagringskonto. Du måste dock ha en aktiv prenumeration i din CSP-klientorganisation.

1. Välj **Lägg till** och skriv ett namn för exporten. 
1. Välj **Mått**:
    - **Verklig kostnad (användning och inköp)** – välj för att exportera standardanvändning och standardinköp
    - **Periodiserad kostnad (användning och inköp)** – välj för att exportera periodiserade kostnader för inköp som Azure-reservationer
1. Välj **Exporttyp**:
    - **Daglig export av kostnader hittills under månaden** – tillhandahåller en ny exportfil varje dag för dina kostnader hittills under månaden. Den senaste informationen sammanställs från tidigare dagliga exporter.
    - **Veckovis export av kostnad under de senaste 7 dagarna** – skapar en veckovis export av dina kostnader under de senaste sju dagarna från det valda startdatumet för exporten.  
    - **Månatlig export av den senaste månadens kostnader** – ger dig en export av den senaste månadens kostnader jämfört med den aktuella månad då du skapar exporten. Om vi ser framåt kör schemat en export den femte dagen i varje ny månad med kostnaderna för den senaste månaden.  
    - **Engångsexport** – du kan välja ett datumintervall för historiska data som ska exporteras till Azure Blob Storage. Du kan exportera högst 90 dagars historiska kostnader från den dag du väljer. Den här exporten körs omedelbart och är tillgänglig i ditt lagringskonto inom två timmar.  
        Beroende på exporttyp väljer du antingen ett startdatum eller väljer datum för **Från** och **Till**.
1. Ange prenumerationen för ditt Azure Storage-konto och välj sedan en resursgrupp eller skapa en ny resursgrupp. 
1. Välj lagringskontots namn eller skapa ett nytt lagringskonto. 
1. Välj plats (Azure-region).
1. Ange den lagringscontainer och den katalogsökväg som du vill att exportfilen ska gå till. 
    :::image type="content" source="./media/tutorial-export-acm-data/basics_exports.png" alt-text="Nytt exportexempel" lightbox="./media/tutorial-export-acm-data/basics_exports.png":::
1. Granska exportinformationen och välj **Skapa**.

Den nya exporten visas i listan över exporter. Som standard är nya exporter aktiverade. Om du vill inaktivera eller ta bort en schemalagd export väljer du valfritt objekt i listan och väljer sedan antingen **Inaktivera** eller **Ta bort**.

Först kan det ta 12–24 timmar innan exporten körs. Men det kan ta längre tid innan data visas i de exporterade filerna.

### <a name="export-schedule"></a>Exportera schema

Schemalagda exporter påverkas av tiden och dagen i veckan då du först skapade exporten. När du skapar en schemalagd export körs exporten med samma frekvens för varje efterföljande export. För till exempel en daglig export av kostnader hittills denna månad körs exporten varje dag. På liknande sätt körs exporten varje vecka för en veckovis export, samma dag som den schemaläggs. Den exakta leveranstiden för exporten garanteras inte, och exporterade data är tillgängliga inom fyra timmar från körningstiden.

Varje export skapar en ny fil, så äldre exporter skrivs inte över.

#### <a name="create-an-export-for-multiple-subscriptions"></a>Skapa en export för flera prenumerationer

Om du har ett Enterprise-avtal kan du använda en hanteringsgrupp för att aggregera information om prenumerationskostnader i en enda container. Sedan kan du exportera kostnadshanteringsdata för hanteringsgruppen.

Export av hanteringsgrupper av andra prenumerationstyper stöds inte.

1. Om du inte redan har skapat en hanteringsgrupp skapar du en hanteringsgrupp och tilldelar prenumerationer till den.
1. I kostnadsanalysen anger du omfattningen för hanteringsgruppen och väljer **Välj den här hanteringsgruppen**.  
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="Exempel som visar alternativet Välj den här hanteringsgruppen" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. Skapa en export i omfånget för att hämta kostnadshanteringsdata för prenumerationerna i hanteringsgruppen.  
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="Exempel som visar alternativet Skapa ny export med ett hanteringsgruppsomfång":::

## <a name="verify-that-data-is-collected"></a>Kontrollera att data samlas in

Du kan enkelt kontrollera att dina Cost Management-data samlas in och visa den exporterade CSV-filen med hjälp av Azure Storage Explorer.

I exportlistan väljer du lagringskontots namn. På sidan för lagringskontot väljer du Öppna i Explorer. Om du ser en bekräftelseruta väljer du **Ja** för att öppna filen i Azure Storage Explorer.

![Lagringskontosidan som visar exempelinformation och länkar till Öppna i Explorer](./media/tutorial-export-acm-data/storage-account-page.png)

I Storage Explorer navigerar du till den container som du vill öppna och väljer den mapp som motsvarar aktuell månad. En lista med CSV-filer visas. Välj en och välj sedan **Öppna**.

![Exempel på information som visas i Storage Explorer](./media/tutorial-export-acm-data/storage-explorer.png)

Filen öppnas med det program eller den app som är inställt på att öppna CSV-filnamnstillägg. Här är ett exempel i Excel.

![Exempel på exporterade CSV-data som visas i Excel](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>Ladda ned en exporterad CSV-datafil

Du kan också ladda ned den exporterade CSV-filen i Azure-portalen. Följande steg beskriver hur du hittar den från kostnadsanalys.

1. I kostnadsanalys väljer du **Inställningar** och sedan **Exporter**.
1. I listan över exporter väljer du lagringskontot för en export.
1. I ditt lagringskonto väljer du **Containers**.
1. Välj containern i listan över containrar.
1. Navigera genom katalogerna och lagringsblobar till det datum du vill ha.
1. Välj CSV-filen och välj sedan **Ladda ned**.

[![Exempel på exportnedladdning](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="view-export-run-history"></a>Visa körningshistorik för export  

Du kan visa körningshistoriken för din schemalagda export genom att välja en enskild export på sidan med exportlistor. På sidan med exportlistor kan du även snabbt se körningstiderna för dina tidigare exporter och se när en export kommer att köras nästa gång. Här är ett exempel som visar körningshistoriken.

:::image type="content" source="./media/tutorial-export-acm-data/run-history.png" alt-text="Exempel på körningshistorik för export":::

Välj en export för att se dess körningshistorik.

:::image type="content" source="./media/tutorial-export-acm-data/single-export-run-history.png" alt-text="Exempel som visar körningshistorik för export":::

## <a name="access-exported-data-from-other-systems"></a>Komma åt exporterade data från andra system

Ett av syftena med att exportera dina Cost Management-data är att komma åt data från externa system. Du använder kanske ett instrumentpanelssystem eller något annat ekonomisystem. Sådana system varierar mycket, så det vore inte praktiskt att visa ett exempel.  Dock kan du komma igång med att få åtkomst till data från dina program i [Introduktion till Azure Storage](../../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en daglig export
> * Kontrollera att data samlas in

Gå vidare till nästa självstudie för att optimera och förbättra effektiviteten genom att identifiera inaktiva och underutnyttjade resurser.

> [!div class="nextstepaction"]
> [Granska och arbeta med optimeringsrekommendationer](tutorial-acm-opt-recommendations.md)
