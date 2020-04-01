---
title: Samla in resursloggar från en Azure-resurs och analysera med Azure Monitor
description: Självstudiekurs för att konfigurera diagnostikinställningar för att samla in resursloggar från en Azure-resurs till en Log Analytics-arbetsyta där de kan analyseras med en loggfråga.
ms.subservice: ''
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: d356042d65c419163de4951e64a635a22ea90e6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78269197"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Självstudiekurs: Samla in och analysera resursloggar från en Azure-resurs

Resursloggar ger insikt i den detaljerade åtgärden för en Azure-resurs och är användbara för att övervaka deras hälsa och tillgänglighet. Azure-resurser genererar resursloggar automatiskt, men du måste konfigurera var de ska samlas in. Den här självstudien tar dig igenom processen att skapa en diagnostikinställning för att samla in resursloggar för en resurs i din Azure-prenumeration och analysera den med en loggfråga.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en log analytics-arbetsyta i Azure Monitor
> * Skapa en diagnostikinställning för att samla in resursloggar 
> * Skapa en enkel loggfråga för att analysera loggar


## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du en Azure-resurs för att övervaka. Du kan använda alla resurser i din Azure-prenumeration som stöder diagnostikinställningar. Om du vill ta reda på om en resurs stöder diagnostikinställningar går du till dess meny i Azure-portalen och kontrollerar att det finns ett alternativ **för diagnostikinställningar** i avsnittet **Övervakning** på menyn.


## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-workspace"></a>Skapa en arbetsyta
En Log Analytics-arbetsyta i Azure Monitor samlar in och indexerar loggdata från en mängd olika källor och möjliggör avancerad analys med hjälp av ett kraftfullt frågespråk. Arbetsytan Log Analytics måste finnas innan du skapar en diagnostikinställning för att skicka data till den. Du kan använda en befintlig arbetsyta i din Azure-prenumeration eller skapa en med följande procedur. 

> [!NOTE]
> Även om du kan arbeta med data i Log Analytics-arbetsytor på **Azure Monitor-menyn** skapar och hanterar du arbetsytor på menyn **Logganalysarbetsytor.**

1. Välj **Log Analytics-arbetsytor**från **Alla tjänster**.
2. Klicka på **Lägg till** högst upp på skärmen och ange följande information för arbetsytan:
   - **Log Analytics-arbetsyta:** Namn på den nya arbetsytan. Det här namnet måste vara globalt unikt för alla Azure Monitor-prenumerationer.
   - **Prenumeration**: Välj prenumerationen för att lagra arbetsytan. Detta behöver inte vara samma prenumeration som den resurs som övervakas.
   - **Resursgrupp:** Välj en befintlig resursgrupp eller klicka på **Skapa ny** om du vill skapa en ny. Detta behöver inte vara samma resursgrupp som den resurs som övervakas.
   - **Plats**: Välj en Azure-region eller skapa en ny. Detta behöver inte vara samma plats som den resurs som övervakas.
   - **Prisnivå**: Välj *Betala per användning* som prisnivå. Du kan ändra den här prisnivån senare. Klicka på **prislänken Log Analytics** om du vill veta mer om olika prisnivåer.

    ![Ny arbetsyta](media/tutorial-resource-logs/new-workspace.png)

3. Klicka på **OK** för att skapa arbetsytan.

## <a name="create-a-diagnostic-setting"></a>Skapa en diagnostikinställning
[Diagnostikinställningar](../platform/diagnostic-settings.md) definierar var resursloggar ska skickas för en viss resurs. En enda diagnostikinställning kan ha flera [mål,](../platform/diagnostic-settings.md#destinations)men vi använder bara en Log Analytics-arbetsyta i den här självstudien.

1. Välj **Diagnostikinställningar**under avsnittet **Övervakning** på resursens meny .
2. Du bör ha meddelandet "Inga diagnostikinställningar definierade". Klicka på **Lägg till diagnostikinställning**.

    ![Diagnostikinställningar](media/tutorial-resource-logs/diagnostic-settings.png)

3. Varje diagnostisk inställning har tre grundläggande delar:
 
   - **Namn**: Detta har ingen signifikant effekt och bör helt enkelt vara beskrivande för dig.
   - **Destinationer**: En eller flera destinationer för att skicka loggarna. Alla Azure-tjänster delar samma uppsättning av tre möjliga destinationer. Varje diagnostikinställning kan definiera en eller flera mål men inte mer än ett mål av en viss typ. 
   - **Kategorier**: Kategorier av loggar som ska skickas till var och en av destinationerna. Uppsättningen kategorier varierar för varje Azure-tjänst.

4. Välj **Skicka till Log Analytics-arbetsyta och** välj sedan arbetsytan som du skapade.
5. Välj de kategorier som du vill samla in. Se dokumentationen för varje tjänst för en definition av dess tillgängliga kategorier.

    ![Diagnostikinställning](media/tutorial-resource-logs/diagnostic-setting.png)

6. Spara diagnostikinställningarna genom att klicka på **Spara.**

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Använda en loggfråga för att hämta loggar
Data hämtas från en Log Analytics-arbetsyta med hjälp av en loggfråga som skrivits i Kusto Query Language (KQL). Insikter och lösningar i Azure Monitor tillhandahåller loggfrågor för att hämta data för en viss tjänst, men du kan arbeta direkt med loggfrågor och deras resultat i Azure-portalen med Log Analytics. 

1. Välj **Loggar**under avsnittet **Övervakning** på resursens meny .
2. Log Analytics öppnas med ett tomt frågefönster med scopet inställt på din resurs. Alla frågor innehåller endast poster från den resursen.

    > [!NOTE]
    > Om du öppnade loggar från Azure Monitor-menyn anges scopet till log analytics-arbetsytan. I det här fallet kommer alla frågor att inkludera alla poster på arbetsytan.
   
    ![Loggar](media/tutorial-resource-logs/logs.png)

4. Tjänsten som visas i exemplet skriver resursloggar till tabellen **AzureDiagnostics,** men andra tjänster kan skriva till andra tabeller. Se [Tjänster, scheman och kategorier som stöds för Azure Resource Logs](../platform/diagnostic-logs-schema.md) för tabeller som används av olika Azure-tjänster.

    > [!NOTE]
    > Flera tjänster skriver resursloggar till tabellen AzureDiagnostics. Om du startar Log Analytics från Azure Monitor-menyn `where` måste du `ResourceProvider` lägga till ett uttryck med kolumnen för att ange din tjänst. När du startar Log Analytics från en resurs meny anges scopet endast till poster från den här resursen så att den här kolumnen inte krävs. Se tjänstens dokumentation för exempelfrågor.


5. Skriv in en fråga och klicka på **Kör** för att granska resultaten. 
6. Se [Komma igång med loggfrågor i Azure Monitor](../log-query/get-started-queries.md) för en självstudiekurs om hur du skriver loggfrågor.

    ![Loggfråga](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du samlar in resursloggar på en Log Analytics-arbetsyta slutför du en självstudiekurs om hur du skriver loggfrågor för att analysera dessa data.

> [!div class="nextstepaction"]
> [Komma igång med loggfrågor i Azure Monitor](../log-query/get-started-queries.md)
