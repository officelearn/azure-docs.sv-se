---
title: "Autoskala Azure-resurser baserat på prestanda eller ett schema | Microsoft Docs"
description: "Skapa en autoskalningsinställning för en app service-plan med hjälp av mätvärden och ett schema"
author: anirudhcavale
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: ancav
ms.custom: mvc
ms.openlocfilehash: 7e8d97657e03b0eaff76365d3988f51c773e3b55
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-autoscale-setting-for--azure-resources-based-on-performance-data-or-a-schedule"></a>Skapa en Autoskalningsinställning för Azure-resurser baserat på prestanda eller ett schema

Autoskala inställningar kan du lägga till/ta bort instanser av tjänsten baserat på förinställda villkor. De här inställningarna kan skapas via portalen. Den här metoden ger ett webbläsarbaserat användargränssnitt för att skapa och konfigurera en autoskalningsinställning. Den här kursen går igenom:

1. Skapa en Apptjänst
2. Konfigurera en autoskalningsinställning
3. Utlöser en skalbar åtgärd
4. Utlösa en åtgärd för skalan

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-web-app-and-app-service-plan"></a>Skapa en Webbapp och Programtjänstplanen
1. Klicka på den **ny** alternativet från det vänstra navigeringsfönstret
2. Söka efter och välja den *Webbapp* och klickar på **skapa**
3. Välj en appnamn, till exempel *MyTestScaleWebApp*. Skapa en ny resursgrupp * myResourceGroup' och placera det i resursgruppen du väljer.
4. Dina resurser ska etableras inom några minuter. Vi referera till webbprogram och motsvarande App Service-Plan som precis har skapats i resten av den här kursen.

    ![Skapa en ny app i portalen](./media/monitor-tutorial-autoscale-performance-schedule/Web-App-Create.png)

## <a name="navigate-to-autoscale-settings"></a>Navigera till Autoskala inställningar
1. I det vänstra navigeringsfönstret väljer du den **övervakaren** alternativet. När sidan läses in väljer du den **Autoskala** fliken.
2. En lista över resurser i din prenumeration som stöder Autoskala visas här. Identifiera den App Service-Plan som skapades tidigare i kursen och klicka på den.

    ![Navigera till Autoskala inställningar](./media/monitor-tutorial-autoscale-performance-schedule/monitor-blade-autoscale.png)

3. Autoskalningsinställningen på den **aktivera Autoskala** knappen

Nästa några steg hjälp du fyllde skärmen Autoskala ska se ut som på bilden nedan:

   ![Spara autoskalningsinställning](./media/monitor-tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

 ## <a name="configure-default-profile"></a>Konfigurera standardprofil
1. Ange en **namn** för autoskalningsinställningen
2. Standardprofilen, se till att den **skala läge** är inställd på 'Skala till specifika instanser'
3. Ange instansantalet 1. Den här inställningen garanterar att när inga andra profilen är aktiv eller i själva verket standardprofilen returnerar instansantalet 1.

  ![Navigera till Autoskala inställningar](./media/monitor-tutorial-autoscale-performance-schedule/autoscale-setting-profile.png)


## <a name="create-recurrence-profile"></a>Skapa profil för upprepning

1. Klicka på den **Lägg till ett villkor för skala** länken under standardprofil

2. Redigera den **namn** i den här profilen ska måndag-fredag profil

3. Se till att den **skala läge** är inställd på 'Skala baserat på ett mått'

4. För **instansen gränser** ange den **minsta** som '1', den **maximala** som '2' och **standard** som '1'. Detta säkerställer att den här profilen har inte Autoskala serviceplan som har mindre än 1, eller mer än 2 instanser. Om profilen inte har tillräckliga data kan besluta, använder standardvärdet för antalet instanser (i det här fallet 1).

5. För **schema** Välj 'Upprepa särskilda dagar ”

6. Ange profil ska upprepas måndag-fredag från 09:00 PST till 18:00 PST. Detta garanterar att den här profilen är bara aktiva och tillämpliga 9: 00 till 18: 00, måndag-fredag. Vid andra tider är 'Default' profilen den autoskalningsinställning som använder profilen.

## <a name="create-a-scale-out-rule"></a>Skapa en skalbar regel

1. I måndag till fredag profil

2. Klicka på den **lägga till en regel** länk

3. Ange den **mått källa** ska vara en annan resurs. Ange den **resurstypen** som App-tjänster och **resurs** som Webbappen skapade tidigare i den här kursen.

4. Ange den **tid aggregering** som summa, den **måttnamnet** as-begäranden, och **tid grain statistik** som ”Sum”

5. Ange den **operatorn** som ”större än', den **tröskelvärdet** som” 10 ”och **varaktighet** '5' minuter.

6. Välj den **åtgärden** som 'Count ökning av', den **instansen antal** som '1', och **kall ned** '5' minuter

7. Klicka på den **Lägg till** knappen

Den här regeln säkerställer att om ditt webbprogram tar emot mer än 10 förfrågningar inom 5 minuter eller mindre, en ytterligare instans läggs till i din App Service-Plan att hantera belastningen.

   ![Skapa en skalbar regel](./media/monitor-tutorial-autoscale-performance-schedule/Scale-Out-Rule.png)

## <a name="create-a-scale-in-rule"></a>Skapa en regel för skalan
Vi rekommenderar att du alltid har en skala i regel som medföljer en skalbar regel. Båda garanterar att dina resurser över inte tillhandahålls. Du har flera instanser än vad som behövs för att hantera den aktuella belastningen över etablering innebär. 

1. I måndag till fredag profil

2. Klicka på den **lägga till en regel** länk

3. Ange den **mått källa** ska vara en annan resurs. Ange den **resurstypen** som App-tjänster och **resurs** som Webbappen skapade tidigare i den här kursen.

4. Ange den **tid aggregering** som summa, den **måttnamnet** as-begäranden, och **tid grain statistik** som 'Genomsnitt'

5. Ange den **operatorn** som ”mindre än”, den **tröskelvärdet** som '5' och **varaktighet** '5' minuter.

6. Välj den **åtgärden** som 'Minska count by', den **instansen antal** som '1', och **kall ned** '5' minuter

7. Klicka på den **Lägg till** knappen

    ![Skapa en regel för skalan](./media/monitor-tutorial-autoscale-performance-schedule/Scale-In-Rule.png)

8. **Spara** autoskalningsinställningen

    ![Spara autoskalningsinställning](./media/monitor-tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="trigger-scale-out-action"></a>Skalbar utlösaråtgärd
Webbprogrammet måste ha fler än 10 begäranden på mindre än 5 minuter för att utlösa skalbar villkor i den autoskalningsinställning som du nyss skapade.

1. Öppna ett webbläsarfönster och navigera till det webbprogram som du skapade tidigare i den här kursen. Du hittar URL: en för Webbappen i Azure Portal genom att gå till resursen Web App och klicka på den **Bläddra** knappen på fliken 'Översikt'.

2. I snabb följd läsa in sidan fler än 10 gånger

3. I det vänstra navigeringsfönstret väljer du den **övervakaren** alternativet. När sidan läses in väljer du den **Autoskala** fliken.

4. I listan väljer du den Apptjänstplan som används i den här självstudiekursen

5. Autoskalningsinställningen, klicka på den **kör historik** fliken

6. Du ser ett diagram som avspeglar instansantalet med den App Service-Plan över tid

7. Om några minuter bör instansantalet upphov från 1 till 2.

8. Under diagrammet visas aktiviteten loggposter för varje skala åtgärd som den här autoskalningsinställning.

## <a name="trigger-scale-in-action"></a>Skala i utlösaråtgärd
Skala i villkoret i utlösare för autoskalningsinställning, om det finns färre än 5 begäranden till Webbappen under en period om 10 minuter. 

1. Kontrollera att inga begäranden skickas till ditt webbprogram

2. Läsa in Azure-portalen

3. I det vänstra navigeringsfönstret väljer du den **övervakaren** alternativet. När sidan läses in väljer du den **Autoskala** fliken.

4. I listan, Välj Apptjänstplan används i den här självstudiekursen

5. Autoskalningsinställningen, klicka på den **kör historik** fliken

6. Du ser ett diagram som avspeglar instansantalet med den App Service-Plan över tid.

7. Om några minuter instansantalet bör ta bort från 2, 1. Processen tar minst tio minuter.  

8. Under diagrammet är motsvarande uppsättning aktivitet loggposter för varje skala åtgärd som den här automatiska måttinställningarna

    ![Visa skala i åtgärder](./media/monitor-tutorial-autoscale-performance-schedule/Scale-In-Chart.png)

## <a name="clean-up-resources"></a>Rensa resurser

1. I den vänstra menyn i Azure-portalen klickar du på **alla resurser** och välj sedan den Webbapp som skapats i den här självstudiekursen.

2. På din resurs klickar du på **ta bort**, bekräfta borttagningen genom att skriva **Ja** i textrutan och klicka sedan på **ta bort**.

3. Välj Apptjänstplan resursen och klicka sedan **ta bort**

4. Bekräfta borttagningen genom att skriva **Ja** i textrutan och klicka sedan på **ta bort**.

## <a name="next-steps"></a>Nästa steg

I kursen får skapat du en enkel Webbapp och App Service-Plan. Du sedan skapar en autoskalningsinställning som skala i App Service-Plan baserat på antalet begäranden mottagning av webbprogrammet. Mer information om automatiska fortsätter inställningar du med Autoskala Översikt.

> [!div class="nextstepaction"]
> [Arkivera övervakningsdata](./monitor-tutorial-archive-monitoring-data.md)
