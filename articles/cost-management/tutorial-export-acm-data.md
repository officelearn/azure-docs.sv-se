---
title: Självstudie – Skapa och hantera exporterade data från Azure Cost Management | Microsoft Docs
description: Den här artikeln visar hur du kan skapa och hantera exporterade Azure Cost Management-data så att du kan använda den i externa system.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/10/2019
ms.topic: tutorial
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 6975bb872e67650b8b5729a4ac66f0825fb9a80f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61021563"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Självstudier: Skapa och hantera exporterade data

Om du har läst självstudien om kostnadsanalys är du bekant med att manuellt ladda ned Cost Management-data. Du kan dock skapa en återkommande uppgift som automatiskt exporterar Cost Management-data till Azure Storage varje dag, vecka eller månad. Exporterade data är i CSV-format och innehåller all information som samlas in av Cost Management. Du kan sedan använda exporterade data i Azure Storage med externa system och kombinera dem med dina egna anpassade data. Och du kan använda din exporterade data i ett externt system, till exempel en instrumentpanel eller något annat ekonomisystem.

Exemplen i den här självstudien vägleder dig genom export av Cost Management-data och kontrollerar sedan att data har exporterats.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en daglig export
> * Kontrollera att data samlas in

## <a name="prerequisites"></a>Nödvändiga komponenter
Dataexport är tillgänglig för en mängd olika typer av Azure-konton, exempelvis för [EA-kunder (Enterprise Agreement)](https://azure.microsoft.com/pricing/enterprise-agreement/). Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](understand-cost-mgt-data.md). Följande Azure-behörigheter eller scope, stöds per prenumeration för export av data för användare och grupp. Mer information om scope finns [förstå och arbeta med omfattningar](understand-work-scopes.md).

- Ägare – kan skapa, ändra eller ta bort schemalagda exporter för en prenumeration.
- Deltagare – kan skapa, ändra eller ta bort sina egna schemalagda exporter. Kan ändra namnet på schemalagda exporter som skapats av andra.
- Läsare – kan schemalägga exporter som läsaren har behörighet till.

För Azure Storage-konton:
- Det krävs skrivbehörigheter för att ändra det konfigurerade lagringskontot, oavsett behörigheter för exporten.
- Ditt Azure Storage-konto måste konfigureras för blob- eller fillagring.

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Skapa en daglig export

Att skapa eller visa en export av data eller för att schemalägga en export, öppna det önskade omfånget i Azure portal och väljer **analys av kostnader** på menyn. Till exempel Gå till **prenumerationer**, Välj en prenumeration i listan och välj sedan **analys av kostnader** på menyn. Längst upp på sidan Cost analysis, klickar du på **exportera** och välj sedan ett exportalternativ för. Klicka till exempel **schemalägga export**.  

> [!NOTE]
> Förutom prenumerationer, kan du skapa export på resursgrupper, konton, avdelningar och registreringar. Mer information om scope finns [förstå och arbeta med omfattningar](understand-work-scopes.md).
> 
> 


Klicka på **Lägg till**, Skriv ett namn för exporten och välj sedan den **dagliga export av månad hittills kostnader** alternativet. Klicka på **Nästa**.

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

Om du har en betala per användning, MSDN eller Visual Studio-prenumeration, kanske inte din faktura faktureringsperiod justeras per kalendermånad. Du kan skapa en export som justeras till din faktureringsperiod eller kalendermånaderna för dessa typer av prenumerationer och resursgrupper. Skapa en export justeras till din Fakturamånad, gå till **anpassade**och välj sedan **fakturering-punkt-till-date**.  Om du vill skapa en export justerad per kalendermånad, Välj **månad hittills**.
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
