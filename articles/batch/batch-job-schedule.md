---
title: Schemalägg dina jobb
description: Använd finplanering för att hantera dina aktiviteter.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 02/20/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 55ea8fb4cc0e65deaa89d718c4a46513716dcf54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672438"
---
# <a name="schedule-jobs-for-efficiency"></a>Schemalägga jobb för effektivitet

Med schemaläggning av batchjobb kan du prioritera de jobb som du vill köra först med hänsyn till aktiviteter som har beroenden på andra aktiviteter. Genom att schemalägga dina jobb kan du se till att du använder minst resurser. Noder kan inaktiveras när det inte behövs, uppgifter som är beroende av andra uppgifter snurras upp precis i tid optimera arbetsflödena. Endast ett jobb i taget körs. En ny startar inte förrän den tidigare är klar. Du kan ställa in jobbet så att det fylls i automatiskt. 

## <a name="benefit-of-job-scheduling"></a>Fördelar med finplanering

Fördelen med att schemalägga jobb är att du kan ange ett schema för att skapa jobb. De aktiviteter som du schemalägger med jobbhanterarens aktivitet är kopplade till ett jobb. Jobbansvarige skapar aktiviteter för jobbet. För att göra det måste jobbhanterarens uppgift autentiseras med batch-kontot. Använd AZ_BATCH_AUTHENTICATION_TOKEN åtkomsttoken. Token ger åtkomst till resten av jobbet. 

## <a name="use-the-portal-to-schedule-a-job"></a>Använda portalen för att schemalägga ett jobb

   1. Logga in på [Azure Portal](https://portal.azure.com/).

   2. Välj det batchkonto som du vill schemalägga jobb i.

   3. Välj **Lägg till** om du vill skapa ett nytt jobbschema och fylla i formuläret **Grundläggande**.



![Schemalägga ett jobb][1]

**Jobbschema-ID:** Den unika identifieraren för det här jobbschemat.

**Visningsnamn**: Visningsnamnet för jobbet behöver inte vara unikt men har en maximal längd på 1024 tecken.

**Kör inte förrän:** Anger den tidigaste tiden jobbet ska köras. Om du inte anger detta blir schemat redo att köra jobb direkt.

**Kör inte efter**: Inga jobb körs efter den tid du anger här. Om du inte anger någon tid skapar du ett återkommande jobbschema som förblir aktivt tills du uttryckligen avslutar det.

**Återkommande intervall:** Du kan ange hur lång tid mellan jobben. Du kan bara ha ett jobb i taget schemalagt, så om det är dags att skapa ett nytt jobb under ett jobbschema, men det föregående jobbet fortfarande körs, skapar batch-tjänsten inte det nya jobbet förrän det föregående jobbet är klart.  

**Startfönster**: Här anger du tidsintervallet, från den tidpunkt då schemat anger att ett jobb ska skapas, tills det ska slutföras. Om det aktuella jobbet inte slutförs under fönstret startar inte nästa jobb.

Längst ned i det grundläggande formuläret anger du den pool som du vill att jobbet ska köras på. Om du vill hitta pool-ID-informationen väljer du **Uppdatera**. 

![Ange pool][2]


**Pool-ID:** Identifiera poolen som du ska köra jobbet på.

**Jobbkonfigurationsuppgift:** Välj **Uppdatera** för att namnge jobbhanterarens uppgift samt jobbförberedelse- och utgivningsuppgifter, om du använder dem.

**Prioritet**: Ge jobbet prioritet.

**Max väggklocka tid:** Ställ in den maximala tid jobbet kan köras för. Om det inte slutförs inom tidsramen avslutar Batch jobbet. Om du inte ställer in detta, så finns det ingen tidsgräns för jobbet.

**Antal tecken på maxaktivitetsförsök**: Ange hur många gånger en aktivitet kan göras om upp till högst fyra gånger. Detta är inte samma sak som antalet försök ett API-anrop kan ha.

**När alla aktiviteter är klara**: Standardvärdet är ingen åtgärd.

**När en aktivitet misslyckas:** Standardvärdet är ingen åtgärd. En aktivitet misslyckas om antalet försök på nytt är uttömt eller om det uppstod ett fel när aktiviteten startades. 

När du har valt **Spara**kan du spåra körningen av jobbet om du går till **Jobbscheman** i den vänstra navigeringen genom att välja **Körningsinformation**.


## <a name="for-more-information"></a>Mer information

Information om hur du hanterar ett jobb med Azure CLI finns [i az batch job-schedule](https://docs.microsoft.com/cli/azure/batch/job-schedule?view=azure-cli-latest).

## <a name="next-steps"></a>Nästa steg

[Skapa aktivitetssamband för att köra aktiviteter som är beroende av andra aktiviteter](batch-task-dependencies.md).





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


