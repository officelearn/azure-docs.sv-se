---
title: Azure Snabbstart – Köra Batch-jobb – Portal
description: Lär dig snabbt att köra ett Batch-jobb på Azure-portalen.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.devlang: na
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: d478307ff4393d84a854fcd3b2ea0efd77de7135
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087847"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Snabbstart: Köra ditt första Batch-jobb i Azure-portalen

Den här snabbstarten visar hur du använder Azure-portalen för att skapa ett Batch-konto, en *pool* med beräkningsnoder (virtuella datorer) och ett *jobb* som kör grundläggande *aktiviteter* på poolen. När du har slutfört den här snabbstarten kommer du att förstå huvudbegreppen för Batch-tjänsten och vara redo att testa Batch med mer realistiska arbetsbelastningar i större skala.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-batch-account"></a>Skapa ett Batch-konto

Följ dessa steg om du vill skapa ett Batch-konto som ska användas som exempel för testning. Du behöver ett Batch-konto för att skapa pooler och jobb. Som du ser här kan du länka ett Azure-lagringskonto till Batch-kontot. Även om det inte krävs för den här snabbstarten är lagringskontot användbart för att distribuera program och lagra indata och utdata för de flesta verkliga arbetsbelastningarna.


1. Välj **Skapa en resurs** > **Beräkna** > **Batch-tjänst**. 

   ![Batch på Marketplace][marketplace_portal]

2. Ange värden för **Kontonamn** och **Resursgrupp**. Kontonamnet måste vara unikt inom den **Azureplats** som valts, får endast använda små bokstäver eller siffror och innehålla 3 till 24 tecken. 

3. Gå till **Lagringskonto** och välj ett befintligt lagringskonto eller skapa ett nytt.

4. Behåll standardinställningarna för återstående inställningar och välj **Skapa** för att skapa kontot.

   ![Skapa ett Batch-konto][account_portal]  

När meddelandet **Distributionen lyckades** visas ska du gå till Batch-kontot i portalen.

## <a name="create-a-pool-of-compute-nodes"></a>Skapa en pool med beräkningsnoder

Nu när du har ett Batch-konto kan du skapa en exempelpool med Windows beräkningsnoder för testning. Poolen för det här snabba exemplet består av 2 noder som kör en Windows Server 2012 R2-avbildning från Azure Marketplace.


1. I Batch-kontot klickar du på **Pooler** > **Lägg till**.

2. Ange ett **Pool-ID** som kallas *mypool*. 

3. Vid **Operativsystem** väljer du följande inställningar (du kan utforska andra alternativ).
  
   |Inställning  |Värde  |
   |---------|---------|
   |**Avbildningstyp**|Marketplace (Linux/Windows)|
   |**Utgivare**     |MicrosoftWindowsServer|
   |**Erbjudande**     |WindowsServer|
   |**Sku**     |2012-R2-Datacenter-smalldisk|

   ![Välj ett pooloperativsystem][pool_os] 

4. Rulla nedåt för att ange inställningar för **Nodstorlek** och **Skala**. Föreslagen nodstorlek erbjuder en bra balans mellan prestanda och kostnad för det här snabba exemplet.
  
   |Inställning  |Värde  |
   |---------|---------|
   |**Nodprisnivå**     |Standard_A1|
   |**Reserverade målnoder**     |2|

   ![Välj en poolstorlek][pool_size] 

5. Behåll standardinställningarna för återstående inställningar och välj **OK** för att skapa poolen.

Batch skapar poolen omedelbart, men det tar några minuter att allokera och starta beräkningsnoderna. Under denna tid visar poolens **Allokeringstillstånd** som **Ändrar storlek**. Du kan gå vidare och skapa ett jobb och aktiviteter medan poolen ändrar storlek. 

![Pool i tillståndet Ändrar storlek][pool_resizing]

Efter några minuter är tillståndet för poolen **Konstant** och noderna startar. Klicka på **Noder** för att kontrollera tillståndet för noderna. När en nods tillstånd anges som **Inaktiv** är noden redo att köra aktiviteter. 

## <a name="create-a-job"></a>Skapa ett jobb

Nu när du har en pool ska du skapa ett jobb att köra på den. Ett Batch-jobb är en logisk grupp för en eller flera aktiviteter. Ett jobb omfattar inställningar som är gemensamma för aktiviteter, till exempel prioritet och vilken pool som aktiviteterna ska köras på. Från början har jobbet inga aktiviteter. 

1. I vyn Batch-konto väljer du **Jobb** > **Lägg till**. 

2. Ange ett **Jobb-ID** som kallas *myjob*. I **Pool**väljer du *mypool*. Behåll standardinställningarna för återstående inställningar och välj **OK**.

   ![Skapa ett jobb][job_create]

När jobbet har skapats öppnas sidan **Aktiviteter**.

## <a name="create-tasks"></a>Skapa aktiviteter

Nu ska du skapa exempelaktiviteter som ska köras i jobbet. Vanligtvis kan du skapa flera aktiviteter som Batch köar och distribuerar för att köras på beräkningsnoderna. I det här exemplet skapar du två identiska aktiviteter. Varje aktivitet kör en kommandorad för att visa Batch-miljövariablerna på en beräkningsnod och väntar sedan i 90 sekunder. 

När du använder Batch är det på kommandoraden som du anger din app eller ditt skript. Batch tillhandahåller ett antal sätt att distribuera appar och skript till beräkningsnoder. 

Så här skapar du den första aktiviteten:

1. Välj **Lägg till**.

2. Ange ett **Aktivitets-ID** som kallas *mytask*. 

3. På **Kommandoraden** anger du `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. Behåll standardinställningarna för återstående inställningar och välj **OK**.

   ![Skapa en uppgift][task_create]

När du har skapat en aktivitet köar Batch den så att den körs på poolen. När en nod kan köra den, körs aktiviteten.

Gå tillbaka till steg 1 för att skapa en till aktivitet. Ange ett annat **Aktivitets-ID**, men ange en identisk kommandorad. Om den första aktiviteten fortfarande körs startar Batch den andra aktiviteten på den andra noden i poolen.

## <a name="view-task-output"></a>Visa aktivitetens utdata

Föregående aktivitetsexempel slutförs på ett par minuter. Om du vill visa resultatet av en slutförd aktivitet väljer du **Filer på noden** och markerar sedan filen `stdout.txt`. Den här filen innehåller standardutdata för aktiviteten. Informationen liknar följande:

![Visa aktivitetens utdata][task_output]

Innehållet visar Azure Batch-miljövariabler som ställts in på noden. När du skapar dina egna Batch-jobb och aktiviteter kan du referera till dessa miljövariabler i aktivitetens kommandorader och i de appar och skript som körs av kommandoraderna.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta med Batch-självstudier och -exempel ska du använda Batch-kontot och det länkade lagringskontot som skapats i denna snabbstart. Själva Batch-kontot kostar ingenting.

Du debiteras för poolen medan noderna körs, även om inga jobb är schemalagda. Ta bort poolen när du inte längre behöver den. I kontovyn väljer du **Pooler** och namnet på poolen. Välj sedan **Ta bort**.  När du tar bort poolen raderas alla aktivitetsutdata på noderna. 

Ta bort resursgruppen, Batch-kontot och alla relaterade resurser när de inte längre behövs. Om du vill göra det väljer du resursgruppen för Batch-kontot och sedan **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Batch-konto, en Batch-pool och ett Batch-jobb. Jobbet körde exempelaktiviteter och du visade utdata som skapats på en av noderna. Nu när du förstår nyckelbegreppen för Batch-tjänsten är du redo att testa Batch med mer realistiska arbetsbelastningar i större skala. Om du vill veta mer om Azure Batch ska du fortsätta med Azure Batch-självstudierna. 

> [!div class="nextstepaction"]
> [Azure Batch-självstudier](./tutorial-parallel-dotnet.md)

[marketplace_portal]: ./media/quick-create-portal/marketplace-batch.png

[account_portal]: ./media/quick-create-portal/batch-account-portal.png

[account_keys]: ./media/quick-create-portal/batch-account-keys.png

[pool_os]: ./media/quick-create-portal/pool-operating-system.png

[pool_size]: ./media/quick-create-portal/pool-size.png

[pool_resizing]: ./media/quick-create-portal/pool-resizing.png

[job_create]: ./media/quick-create-portal/job-create.png

[task_create]: ./media/quick-create-portal/task-create.png

[task_output]: ./media/quick-create-portal/task-output.png