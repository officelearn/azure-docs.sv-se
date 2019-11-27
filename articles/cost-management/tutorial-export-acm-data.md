---
title: Självstudie – Skapa och hantera exporterade data från Azure Cost Management
description: Den här artikeln visar hur du kan skapa och hantera exporterade Azure Cost Management data så att du kan använda dem i externa system.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/12/2019
ms.topic: tutorial
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a462b3d165a596673049abbbb8b5b8d346f5fc9d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229818"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Självstudie: Skapa och hantera exporterade data

Om du har läst självstudien om kostnadsanalys är du bekant med att manuellt ladda ned Cost Management-data. Du kan dock skapa en återkommande uppgift som automatiskt exporterar Cost Management-data till Azure Storage varje dag, vecka eller månad. Exporterade data är i CSV-format och innehåller all information som samlas in av Cost Management. Du kan sedan använda exporterade data i Azure Storage med externa system och kombinera dem med dina egna anpassade data. Och du kan använda din exporterade data i ett externt system, till exempel en instrumentpanel eller något annat ekonomisystem.

Se [hur du schemalägger export till lagring med Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) video om hur du skapar en schemalagd export av dina Azure Cost-data till Azure Storage.

Exemplen i den här självstudien vägleder dig genom export av Cost Management-data och kontrollerar sedan att data har exporterats.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en daglig export
> * Kontrollera att data samlas in

## <a name="prerequisites"></a>Krav
Dataexport är tillgänglig för en mängd olika typer av Azure-konton, exempelvis för [EA-kunder (Enterprise Agreement)](https://azure.microsoft.com/pricing/enterprise-agreement/). Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](understand-cost-mgt-data.md). Följande Azure-behörigheter, eller omfattningar, stöds per prenumeration för data export av användare och grupp. Mer information om omfattningar finns i [förstå och arbeta med omfattningar](understand-work-scopes.md).

- Ägare – kan skapa, ändra eller ta bort schemalagda exporter för en prenumeration.
- Deltagare – kan skapa, ändra eller ta bort sina egna schemalagda exporter. Kan ändra namnet på schemalagda exporter som skapats av andra.
- Läsare – kan schemalägga exporter som läsaren har behörighet till.

För Azure Storage-konton:
- Det krävs skrivbehörigheter för att ändra det konfigurerade lagringskontot, oavsett behörigheter för exporten.
- Ditt Azure Storage-konto måste konfigureras för blob- eller fillagring.

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Skapa en daglig export

Om du vill skapa eller Visa en data export eller om du vill schemalägga en export öppnar du önskad omfattning i Azure Portal och väljer **kostnads analys** i menyn. Du kan till exempel navigera till **prenumerationer**, välja en prenumeration i listan och sedan välja **kostnads analys** i menyn. Klicka på **Exportera** längst upp på sidan kostnads analys och välj sedan ett export alternativ. Klicka till exempel på **Schemalägg export**.  

> [!NOTE]
> - Förutom prenumerationer kan du skapa exporter för resurs grupper, konton, avdelningar och registreringar. Mer information om omfattningar finns i [förstå och arbeta med omfattningar](understand-work-scopes.md).
>- När du är inloggad som en partner i fakturerings kontots omfattning eller på en kunds klient kan du exportera data till ett Azure Storage-konto som är länkat till ditt partner lagrings konto. Du måste dock ha en aktiv prenumeration i din CSP-klient.
>


Klicka på **Lägg till**, Skriv ett namn för exporten och välj sedan alternativet **daglig export av månad till datum** . Klicka på **Nästa**.

![Nytt exempel som visar exporttyp](./media/tutorial-export-acm-data/basics_exports.png)

Ange prenumerationen för ditt Azure Storage-konto och välj sedan ditt lagringskonto.  Ange den lagringscontainer och den katalogsökväg som du vill att exportfilen ska gå till.  Klicka på **Nästa**.

![Nytt exportexempel som visar information om lagringskonto](./media/tutorial-export-acm-data/storage_exports.png)

Granska exportinformationen och klicka på **Skapa**.

Den nya exporten visas i listan över exporter. Som standard är nya exporter aktiverade. Om du vill inaktivera eller ta bort en schemalagd export klickar du på valfritt objekt i listan och klickar sedan på antingen **Inaktivera** eller **Ta bort**.

Först kan det ta en till två timmar innan exporten körs. Dock kan det ta upp till fyra timmar innan data visas i de exporterade filerna.

### <a name="export-schedule"></a>Exportera schema

Schemalagda exporter påverkas av tiden och dagen i veckan då du först skapade exporten. När du skapar en schemalagd export körs exporten samma tid på dagen för varje efterföljande export. Anta exempelvis att du skapar en daglig export klockan 13:00. Nästa export körs klockan 13:00 följande dag. Den aktuella tiden påverkar alla andra exporttyper på samma sätt – de körs alltid samma tid på dagen som då du först skapade exporten. I ett annat exempel skapar du en veckovis export kl. 16:00 på måndag. Nästa rapport körs kl. 16:00 följande måndag. *Exporterade data är tillgängliga inom fyra timmar efter körningstiden.*

Varje export skapar en ny fil, vilket betyder att äldre exporter inte skrivs över.

Det finns tre typer av exportalternativ:

**Daglig export av kostnader hittills under månaden** – Den första exporten körs direkt. Efterföljande exporter körs nästa dag vid samma tid som den första exporten. Den senaste informationen sammanställs från tidigare dagliga exporter.

**Veckokostnader för export de senaste 7 dagarna** – Den första exporten körs direkt. Efterföljande exporter körs på samma dag i veckan och vid samma tid som den första exporten. Kostnaderna avser de senaste sju dagarna.

**Anpassad** – Gör att du kan schemalägga vecko- och månadsexporter med alternativ för ”hittills den här veckan” och ”hittills den här månaden”. *Den första exporten körs direkt.*

Om du har en prenumeration enligt principen betala per användning, MSDN eller Visual Studio kan fakturerings fakturerings perioden inte justeras till kalender månaden. För dessa typer av prenumerationer och resurs grupper kan du skapa en export som är justerad till din faktura period eller kalender månader. Om du vill skapa en export justerad till din faktura månad navigerar du till **anpassad**och väljer sedan **fakturerings period-till-datum**.  Om du vill skapa en export justerad till kalender månaden väljer du **månad-hittills**.
>
>

![Ny export – Fliken Grundläggande inställningar visar ett anpassat alternativ för ”hittills den här veckan” för varje vecka](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Kontrollera att data samlas in

Du kan enkelt kontrollera att dina Cost Management-data samlas in och visa den exporterade CSV-filen med hjälp av Azure Storage Explorer.

I exportlistan klickar du på lagringskontots namn. På sidan för lagringskontot klickar du på Öppna i Explorer. Om du ser en bekräftelseruta klickar du på **Ja** för att öppna filen i Azure Storage Explorer.

![Lagringskontosidan som visar exempelinformation och länkar till Öppna i Explorer](./media/tutorial-export-acm-data/storage-account-page.png)

I Storage Explorer navigerar du till den container som du vill öppna och väljer den mapp som motsvarar aktuell månad. En lista med CSV-filer visas. Välj en och klicka sedan på **Öppna**.

![Exempel på information som visas i Storage Explorer](./media/tutorial-export-acm-data/storage-explorer.png)

Filen öppnas med det program eller den app som är inställt på att öppna CSV-filnamnstillägg. Här är ett exempel i Excel.

![Exempel på exporterade CSV-data som visas i Excel](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>Komma åt exporterade data från andra system

Ett av syftena med att exportera dina Cost Management-data är att komma åt data från externa system. Du använder kanske ett instrumentpanelssystem eller något annat ekonomisystem. Sådana system varierar mycket, så det vore inte praktiskt att visa ett exempel.  Dock kan du komma igång med att få åtkomst till data från dina program i [Introduktion till Azure Storage](../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en daglig export
> * Kontrollera att data samlas in

Gå vidare till nästa självstudie för att optimera och förbättra effektiviteten genom att identifiera inaktiva och underutnyttjade resurser.

> [!div class="nextstepaction"]
> [Granska och arbeta med optimeringsrekommendationer](tutorial-acm-opt-recommendations.md)
