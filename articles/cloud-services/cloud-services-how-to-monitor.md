---
title: "Så här övervakar du en tjänst i molnet | Microsoft Docs"
description: "Lär dig hur du övervakar molntjänster med hjälp av den klassiska Azure-portalen."
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: 5c48d2fb-b8ea-420f-80df-7aebe2b66b1b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2015
ms.author: adegeo
ms.openlocfilehash: c369b22cf068a473343b006eb1b06fdd350d31db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-monitor-cloud-services"></a>Så här övervakar du Cloud Services
[!INCLUDE [disclaimer](../../includes/disclaimer.md)]

Du kan övervaka `key` prestandastatistik för dina molntjänster i den klassiska Azure-portalen. Du kan ange nivån för övervakning och minimal utförlig för varje roll för tjänsten och kan anpassa övervakning visar. Utförlig övervakningsdata lagras i ett lagringskonto som du kan komma åt utanför portalen. 

Övervakning visas i den klassiska Azure-portalen kan konfigureras för hög. Du kan välja mått som du vill övervaka i listan mått på den **övervakaren** , och du kan välja vilka mått för att rita i mått diagram på den **övervakaren** sida och på instrumentpanelen. 

## <a name="concepts"></a>Koncept
Som standard ges minimal övervakning för en ny molntjänst med hjälp av prestandaräknare som samlats in från värdoperativsystemet för roller instanser (virtuella datorer). Minimal mått är begränsade till CPU-procent, Data i, utdata, Diskgenomflödet läsa och skriva Diskgenomflödet. Genom att konfigurera utförlig övervakning kan få du ytterligare mått baserat på prestandadata på de virtuella datorerna (rollinstanser). Utförlig mått aktivera närmare analys av problem som uppstår under program-åtgärder.

Som standard in prestandaräknardata från rollinstanser och överförs från rollinstansen på 3-minutersintervall. När du aktiverar utförlig övervakning sammanställs råa prestandadata för prestandaräknaren för varje rollinstans och över rollinstanser för varje roll med intervall på 5 minuter, 1 timme och 12 timmar. Sammanställda data tas bort efter 10 dagar.

När du aktiverar utförlig övervakning, lagras aggregerade övervakningsdata i tabeller i ditt lagringskonto. Om du vill aktivera utförlig övervakning för en roll måste du konfigurera en anslutningssträng för diagnostik som länkar till lagringskontot. Du kan använda olika lagringskonton för olika roller.

Aktivera utförlig övervakning ökar rör din lagringskostnader lagringsutrymme, dataöverföring och lagring transaktioner. Minimal övervakning kräver inte ett lagringskonto. Data för de mätvärden som är tillgängliga på den lägsta nivån för övervakning lagras inte i ditt lagringskonto, även om du ställa in övervakning på utförlig.

## <a name="how-to-configure-monitoring-for-cloud-services"></a>Så här: Konfigurera övervakning för molntjänster
Använd följande procedurer för att konfigurera utförlig eller minimalt övervakning i den klassiska Azure-portalen. 

### <a name="before-you-begin"></a>Innan du börjar
* Skapa en *klassiska* storage-konto för att lagra övervakningsdata. Du kan använda olika lagringskonton för olika roller. Mer information finns i [hur du skapar ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account).
* Aktivera Azure-diagnostik för dina molntjänstroller. Se [konfigurera diagnostik för molntjänster](cloud-services-dotnet-diagnostics.md).

Kontrollera att anslutningssträngen diagnostik finns i rollkonfigurationen. Du kan inte aktivera utförlig övervakning tills du aktiverar Azure-diagnostik och inkludera en anslutningssträng för diagnostik i rollkonfigurationen.   

> [!NOTE]
> Projekt riktad Azure SDK 2.5 innehöll inte automatiskt anslutningssträngen diagnostik i projektmallen. För dessa projekt måste du manuellt lägger till diagnostik anslutningssträngen rollkonfigurationen.
> 
> 

**Lägga till manuellt diagnostik anslutningssträngen rollkonfiguration**

1. Öppna Cloud Service-projekt i Visual Studio
2. Dubbelklicka på den **rollen** att öppna rollen designer och välj den **inställningar** fliken
3. Leta efter en inställning med namnet **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. 
4. Om den här inställningen inte finns, klickar du på den **Lägg till inställning** för att lägga till i konfigurationen och ändra typen för den nya inställningen **ConnectionString**
5. Ange värdet för anslutningssträngen den genom att klicka på den **...**  knappen. Då öppnas en dialogruta där du kan välja ett lagringskonto.
   
    ![Visual Studio-inställningar](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioDiagnosticsConnectionString.png)

### <a name="to-change-the-monitoring-level-to-verbose-or-minimal"></a>Ändra nivån övervakning till utförlig eller minimalt
1. I den [klassiska Azure-portalen](https://manage.windowsazure.com/)öppnar den **konfigurera** för cloud service-distributionen.
2. I **nivå**, klickar du på **utförlig** eller **Minimal**. 
3. Klicka på **Spara**.

När du aktiverar utförlig övervakning, bör du börja ser övervakningsdata i den klassiska Azure-portalen inom en timme.

Rådata prestandaräknardata och aggregerade övervakningsdata lagras i storage-konto i tabeller kvalificerat av distributions-ID för roller. 

## <a name="how-to-receive-alerts-for-cloud-service-metrics"></a>Så här: få aviseringar om cloud service mått
Du kan ta emot varningar baserat på din molntjänst övervakning mått. På den **hanteringstjänster** sidan i Azure klassiska portalen kan du skapa en regel för att utlösa en avisering när det mått som du når ett värde som du anger. Du kan också välja att ha e-post som skickas när en avisering utlöses. Mer information finns i [så här: ta emot aviseringar och hantera Aviseringsregler i Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

## <a name="how-to-add-metrics-to-the-metrics-table"></a>Så här: lägga till mått i tabellen mått
1. I den [klassiska Azure-portalen](http://manage.windowsazure.com/)öppnar den **övervakaren** för Molntjänsten.
   
    Som standard visar tabellen mått en delmängd av de tillgängliga måtten. Bilden visar standard utförlig mått för en molnbaserad tjänst som är begränsad till prestandaräknaren Minne\Tillgängliga megabyte med data som aggregeras på rollnivå. Använd **lägga till mätvärden** att välja ytterligare sammanställd och rollnivå mått för att övervaka i den klassiska Azure-portalen.
   
    ![Visa utförlig](./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png)
2. Lägga till mått i tabellen mått:
   
   1. Klicka på **lägga till mätvärden** att öppna **välja mått**, visas nedan.
      
       Det första tillgängliga måtten expanderas för att visa alternativ som är tillgängliga. Alternativet översta visar aggregerade övervakningsdata för alla roller för varje mått. Dessutom kan du välja enskilda roller för att visa data för.
      
       ![Lägg till mått](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png)
   2. Att välja mått att visa
      
      * Klicka på pilen av mått för att expandera alternativen under övervakning.
      * Markera kryssrutan för varje övervakningsalternativ som du vill visa.
        
        Du kan visa upp till 50 mått i tabellen mått.
        
        > [!TIP]
        > Utförlig övervakning på kan listan mått innehålla dussintals mått. Hovra över höger sida av dialogrutan för att visa en rullningslist. Klicka på sökikonen om du vill filtrera listan och skriva in text i sökrutan som visas nedan.
        > 
        > 
        
        ![Lägg till mått-sökning](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png)
3. Klicka på OK (markering) när du är klar med att välja mått.
   
    De valda måtten läggs till tabellen mått, enligt nedan.
   
    ![Övervakaren mått](./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png)
4. Om du vill ta bort ett mått från tabellen mått klickar du på mått för att markera den och klicka sedan på **ta bort måttet**. (Du kan bara se **ta bort måttet** när du har ett mått har valts.)

### <a name="to-add-custom-metrics-to-the-metrics-table"></a>Att lägga till anpassade mått i tabellen mått
Den **utförlig** övervakning nivå innehåller en lista över standard mått som du kan övervaka på portalen. Förutom dessa kan du övervaka alla anpassade mått eller en prestandaräknare som definierats av ditt program via portalen.

Följande steg förutsätter att du har aktiverat **utförlig** övervakning nivå och har konfigurerat ditt program för att samla in och överföra anpassade prestandaräknare. 

Om du vill visa de anpassade prestandaräknarna i portalen måste du uppdaterar konfigurationen i bomullstuss-kontroll-behållare:

1. Öppna bomullstuss-kontroll-container blob i din diagnostiklagringskonto. Du kan använda Visual Studio eller andra Lagringsutforskaren för att göra detta.
   
    ![Visual Studio Server Explorer](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioBlobExplorer.png)
2. Navigera i blob-sökväg med mönstret **RoleInstance-DeploymentId/RoleName** att hitta konfigurationen för din rollinstans. 
   
    ![Lagringsutforskaren för Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioStorage.png)
3. Hämta konfigurationsfilen för din rollinstans och uppdatera den om du vill inkludera alla anpassade prestandaräknare. Till exempel att övervaka *Disk-skrivna byte/s* för den *C-enheten* Lägg till följande under **PerformanceCounters\Subscriptions** nod
   
    ```xml
    <PerformanceCounterConfiguration>
    <CounterSpecifier>\LogicalDisk(C:)\Disk Write Bytes/sec</CounterSpecifier>
    <SampleRateInSeconds>180</SampleRateInSeconds>
    </PerformanceCounterConfiguration>
    ```
4. Spara ändringarna och skicka konfigurationsfilen till samma plats att skriva över den befintliga filen i blob.
5. Växla till utförligt läge i Azure klassiska portal konfigurationen. Om du redan fanns i utförligt läge måste du växla till minimal och tillbaka till utförlig.
6. Prestandaräknaren för anpassade att vara tillgängliga i den **lägga till mätvärden** dialogrutan. 

## <a name="how-to-customize-the-metrics-chart"></a>Så här: anpassa mått-diagram
1. Välja upp till 6 mått ska ritas diagrammet mått i tabellen mått. Klicka på kryssrutan på vänster sida om du vill markera ett mått. Avmarkera kryssrutan i tabellen mått för att ta bort ett mått från diagrammet mått.
   
    När du väljer mått i tabellen mått har mätvärdena lagts till mått-diagrammet. En smala visas en **n mer** listrutan innehåller mått huvuden som inte får plats visningen.
2. Välj relativ eller absolut överst i diagrammet om du vill växla mellan att visa relativa värden (sista värde endast för varje mått) och absoluta värden (Y-axeln visas).
   
    ![Relativ eller absolut](./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png)
3. Om du vill ändra tidsintervallet välja mått diagrammet visar, 1 timme, 24 timmar eller sju dagar överst i diagrammet.
   
    ![Övervakaren Visa period](./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png)
   
    Metoden för att rita upp mått skiljer sig på instrumentpanelen mått diagrammet. En standarduppsättning mått är tillgänglig och mått läggs till eller tas bort genom att välja mått huvudet.

### <a name="to-customize-the-metrics-chart-on-the-dashboard"></a>Anpassa mått diagram på instrumentpanelen
1. Öppna instrumentpanelen till Molntjänsten.
2. Lägg till eller ta bort mått i diagrammet:
   
   * Om du vill rita ett nytt mått, markerar du kryssrutan för måttet i diagrammet radrubrikerna. En smala visas klickar du på nedpilen av  ***n* ? mått** att rita ett mått sidhuvud diagramområde inte kan visas.
   * Avmarkera kryssrutan om du vill ta bort ett mått som ska ritas diagrammet av huvudet.
   
3. Växla mellan **relativa** och **absolut** visar.
4. Välj 1 timme, 24 timmar eller 7 dagar för data som ska visas.

## <a name="how-to-access-verbose-monitoring-data-outside-the-azure-classic-portal"></a>Så här: åtkomst utförlig övervakningsdata utanför den klassiska Azure-portalen
Utförlig övervakningsdata lagras i tabeller i storage-konton som du anger för varje roll. För varje cloud service-distribution skapas sex tabeller för rollen. Två tabeller skapas för varje (5 minuter, 1 timme och 12 timmar). En av dessa tabeller lagrar roll på objektnivå aggregeringar; den andra tabellen lagrar aggregeringar för rollinstanser. 

Tabellnamnen har följande format:

```
WAD*deploymentID*PT*aggregation_interval*[R|RI]Table
```

Där:

* *deploymentID* är GUID som tilldelats tjänsten molndistribution
* *aggregation_interval* = 5 M, 1 H eller 12 H
* rollen på objektnivå aggregeringar = R
* aggregeringar för rollinstanser = RI

Följande tabeller lagra exempelvis utförlig övervakningsdata samman med 1 timmars intervall:

```
WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)
```
