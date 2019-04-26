---
title: Ta bort och exportera personliga data från Azure DevTest Labs | Microsoft Docs
description: Lär dig ta bort och exportera personliga data från tjänsten Azure DevLast Labs för att stödja dina skyldigheter under den allmänna Dataskyddsförordningen GDPR (Data Protection).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: e681652c13e521bd33524e247db65088f47a794c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60395007"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Exportera eller ta bort personliga data från Azure DevTest Labs
Den här artikeln innehåller steg för att ta bort och exportera personliga data från tjänsten Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Vilka personliga data samlar DevTest Labs?
DevTest Labs samlar in de två viktigaste delarna av personliga data från användaren. De är: användares e-postadress och användarobjekts-ID. Den här informationen är viktigt för tjänsten för att ange av funktioner lab administratörer och labbanvändare.

### <a name="user-email-address"></a>Användares e-postadress
DevTest Labs använder användarens e-postadress för att skicka e-postmeddelanden för automatisk avstängning till labbanvändare. E-postmeddelandet meddelar användare av deras datorn stängts ned. Användarna kan skjuta upp eller hoppa över avstängningen om de vill göra detta. Du kan konfigurera e-postadress på labb eller på VM-nivå.

**Ange e-post i labbet:**

![Ange e-post på nivån lab](./media/personal-data-delete-export/lab-user-email.png)

**Ange e-post på den virtuella datorn:**

![Ange e-post på VM-nivå](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>Användarens objekt-ID
DevTest Labs använder objekt-ID för användaren för att visa månad för månad kostnadstrender och kostnad genom resursinformation till lab administratörer. Det gör att de kan spåra kostnader och hantera tröskelvärden för sina labb. 

**Beräknad kostnad trenden för den aktuella kalendermånaden:**
![uppskattad kostnad trenden för den aktuella kalendermånaden](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Beräknad kostnad för månad hittills efter resurs:**
![beräknad kostnad för månad hittills efter resurs](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Varför behöver vi dessa personliga data?
DevTest Labs-tjänsten använder personliga data för operativa åtgärder. Dessa data är viktigt för tjänsten att leverera viktiga funktioner. Om du anger en bevarandeprincip för användarens e-postadress får inte labbanvändare tid automatisk avstängning av e-postmeddelanden när e-postadressen har tagits bort från vårt system. På samma sätt kan kan inte administratören lab Visa månad månad trender och kostnaden per resurs för datorer i sina labs om användarobjektet ID: N tas bort baserat på en bevarandeprincip. Därför måste dessa data sparas så länge användarens resursen är aktiv i laboratoriet.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Hur kan jag ha systemet att glömma min personliga data?
Som en lab-användare om du vill ha dessa personliga data tas bort, kan du göra det genom att ta bort motsvarande resurs i labbet. Tjänsten DevTest Labs anonymiseras har tagits bort personliga data 30 dagar efter att den tas bort av användaren.

Exempel: Om du tar bort den virtuella datorn eller ta bort din e-postadress, tjänsten DevTest Labs anonymiseras dessa data 30 dagar när resursen har tagits bort. 30-dagars bevarandeprincipen när borttagningen är att se till att vi ger en rättvisande månad för månad kostnadsprognoser labb-administratören.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Hur kan jag begära en export med min personliga data?
Som en lab-användare kan du begära en export på dina personliga data som lagras i DevTest Labs. Om du vill begära för en export, navigera till den **personuppgifter** alternativet på den **översikt** sidan över ditt labb. Välj den **begära export** knappen startar skapandet av en nedladdningsbar excel-fil i labb-administratörens storage-konto. Du kan kontakta labb-administratören om du vill visa dessa data.

1. Välj **personuppgifter** på den vänstra menyn. 

    ![Sidan för personliga data](./media/personal-data-delete-export/personal-data-page.png)
2. Välj den **resursgrupp** som innehåller labbet.

    ![Välj resursgrupp](./media/personal-data-delete-export/select-resource-group.png)
3. Välj den **lagringskonto** i resursgruppen.
4. På den **lagringskonto** väljer **Blobar**.

    ![Välj Blobar panel](./media/personal-data-delete-export/select-blobs-tile.png)
5. Välj behållaren med namnet **labresourceusage** i listan över behållare.

    ![Välj blobcontainer](./media/personal-data-delete-export/select-blob-container.png)
6. Välj den **mappen** namnges efter ditt labb. Du hittar **csv** filer för **diskar** och **virtuella datorer** i labbet i den här mappen. Du kan hämta dessa csv-filer, filtrera innehåll för övningen användaren begär en åtkomst och dela den med dem.

    ![Hämta CSV-fil](./media/personal-data-delete-export/download-csv-file.png)

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Ange principer för ett labb](devtest-lab-get-started-with-lab-policies.md)
- [Vanliga frågor och svar](devtest-lab-faq.md)
