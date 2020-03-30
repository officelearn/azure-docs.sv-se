---
title: Självstudie – Skapa och hantera exporterade data från Azure Cost Management
description: Den här artikeln visar hur du kan skapa en hantera exporterade Azure Cost Management-data så att du kan använda dem i externa system.
author: bandersmsft
ms.author: banders
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: b950235eccbd41b1dfa46c23985e804212d9e8f2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80155910"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Självstudier: Skapa och hantera exporterade data

Om du har läst självstudien om kostnadsanalys är du bekant med att manuellt ladda ned Cost Management-data. Du kan dock skapa en återkommande uppgift som automatiskt exporterar Cost Management-data till Azure Storage varje dag, vecka eller månad. Exporterade data är i CSV-format och innehåller all information som samlas in av Cost Management. Du kan sedan använda exporterade data i Azure Storage med externa system och kombinera dem med dina egna anpassade data. Och du kan använda din exporterade data i ett externt system, till exempel en instrumentpanel eller något annat ekonomisystem.

Titta på videon [How to schedule exports to storage with Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) (Så här schemalägger du exporter till Storage med Azure Cost Management) om hur du skapar en schemalagd export av dina Azure-kostnadsdata till Azure Storage.

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

Om du vill skapa eller visa en dataexport, eller om du vill schemalägga en export, öppnar du önskat omfång i Azure-portalen och väljer **Kostnadsanalys** i menyn. Du kan till exempel gå till **Prenumerationer**, välja en prenumeration i listan och sedan välja **Kostnadsanalys** i menyn. Överst på sidan Kostnadsanalys väljer du **Exportera** och väljer sedan ett exportalternativ. Välj till exempel **Schemalägg export**.  

> [!NOTE]
> - Utöver prenumerationer kan du skapa exporter för resursgrupper, konton, avdelningar och registreringar. Mer information om omfång finns i [Förstå och arbeta med omfång](understand-work-scopes.md).
>- När du är inloggad som partner i faktureringskontots omfång eller i en kunds klientorganisation kan du exportera data till ett Azure Storage-konto som är länkat till ditt partnerlagringskonto. Du måste dock ha en aktiv prenumeration i din CSP-klientorganisation.
>


Välj **Lägg till**, skriv ett namn för exporten och välj sedan alternativet **Daily export of month-to-date costs** (Daglig export av kostnader hittills under månaden). Välj **Nästa**.

![Nytt exempel som visar exporttyp](./media/tutorial-export-acm-data/basics_exports.png)

Ange prenumerationen för ditt Azure Storage-konto och välj sedan ditt lagringskonto.  Ange den lagringscontainer och den katalogsökväg som du vill att exportfilen ska gå till. Välj **Nästa**.

![Nytt exportexempel som visar information om lagringskonto](./media/tutorial-export-acm-data/storage_exports.png)

Granska exportinformationen och välj **Skapa**.

Den nya exporten visas i listan över exporter. Som standard är nya exporter aktiverade. Om du vill inaktivera eller ta bort en schemalagd export väljer du valfritt objekt i listan och väljer sedan antingen **Inaktivera** eller **Ta bort**.

Först kan det ta en till två timmar innan exporten körs. Dock kan det ta upp till fyra timmar innan data visas i de exporterade filerna.

### <a name="export-schedule"></a>Exportera schema

Schemalagda exporter påverkas av tiden och dagen i veckan då du först skapade exporten. När du skapar en schemalagd export körs exporten med samma frekvens för varje efterföljande export. För till exempel en månad till datum-export som anges till daglig frekvens körs exporten varje dag. På liknande sätt körs exporten varje vecka för en veckovis export, samma dag som den schemaläggs. Den exakta leveranstiden för exporten är inte garanterad, och exporterade data är tillgängliga inom fyra timmar från körningstiden.
Varje export skapar en ny fil, vilket betyder att äldre exporter inte skrivs över.

Det finns två typer av exportalternativ:

**Daglig export av kostnader hittills under månaden** – Den första exporten körs direkt. Efterföljande exporter körs nästa dag vid samma tid som den första exporten. Den senaste informationen sammanställs från tidigare dagliga exporter.

**Anpassad** – Gör att du kan schemalägga vecko- och månadsexporter med alternativ för ”hittills den här veckan” och ”hittills den här månaden”. *Den första exporten körs direkt.*

Om du har en prenumeration av typen Betala per användning, MDSN eller Visual Studio är det inte säkert att faktureringsperioden justeras till kalendermånaden. För dessa typer av prenumerationer och resursgrupper kan du skapa en export som är justerad till din fakturaperiod eller till kalendermånader. Om du vill skapa en export som är justerad till din fakturamånad går du till **Anpassad** och väljer **Billing-period-to-date** (Faktureringsperiod hittills).  Om du vill skapa en export som är justerad till kalendermånaden väljer du **Month-to-date** (Hittills under månaden).
>
>

![Ny export – Fliken Grundläggande inställningar visar ett anpassat alternativ för ”hittills den här veckan” för varje vecka](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Kontrollera att data samlas in

Du kan enkelt kontrollera att dina Cost Management-data samlas in och visa den exporterade CSV-filen med hjälp av Azure Storage Explorer.

I exportlistan väljer du lagringskontots namn. På sidan för lagringskontot väljer du Öppna i Explorer. Om du ser en bekräftelseruta väljer du **Ja** för att öppna filen i Azure Storage Explorer.

![Lagringskontosidan som visar exempelinformation och länkar till Öppna i Explorer](./media/tutorial-export-acm-data/storage-account-page.png)

I Storage Explorer navigerar du till den container som du vill öppna och väljer den mapp som motsvarar aktuell månad. En lista med CSV-filer visas. Välj en och välj sedan **Öppna**.

![Exempel på information som visas i Storage Explorer](./media/tutorial-export-acm-data/storage-explorer.png)

Filen öppnas med det program eller den app som är inställt på att öppna CSV-filnamnstillägg. Här är ett exempel i Excel.

![Exempel på exporterade CSV-data som visas i Excel](./media/tutorial-export-acm-data/example-export-data.png)


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
