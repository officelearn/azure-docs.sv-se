---
title: Använda anteckningsböcker med Azure Sentinel för säkerhetsjakt
description: I den här artikeln beskrivs hur du använder anteckningsböcker med Azure Sentinel-jaktfunktionerna.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: 84b72a71ed2de910bce44b0c3f3309782f096680
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581845"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Använd Jupyter-anteckningsböcker för att jaga säkerhetshot

Grunden för Azure Sentinel är datalagret. Den kombinerar högpresterande frågor, dynamiskt schema och skalor till massiva datavolymer. Azure-portalen och alla Azure Sentinel-verktyg använder ett gemensamt API för att komma åt det här datalagret. Samma API är också tillgängligt för externa verktyg som [Jupyter-anteckningsböcker](https://jupyter.org/) och Python. Medan många vanliga uppgifter kan utföras i portalen, sträcker Jupyter omfattningen av vad du kan göra med dessa data. Den kombinerar full programmerbarhet med en enorm samling bibliotek för maskininlärning, visualisering och dataanalys. Dessa attribut gör Jupyter till ett övertygande verktyg för säkerhetsutredning och jakt.

![exempel på anteckningsbok](./media/notebooks/sentinel-notebooks-map.png)

Vi har integrerat Jupyter-upplevelsen i Azure-portalen, vilket gör det enkelt för dig att skapa och köra anteckningsböcker för att analysera dina data. *Kqlmagic-biblioteket* innehåller limmet som gör att du kan ta frågor från Azure Sentinel och köra dem direkt i en anteckningsbok. Frågor använder [Kusto Query Language](https://kusto.azurewebsites.net/docs/query/index.html). Flera anteckningsböcker, som utvecklats av några av Microsofts säkerhetsanalytiker, är paketerade med Azure Sentinel. Vissa av dessa anteckningsböcker är byggda för ett visst scenario och kan användas som de är. Andra är avsedda som exempel för att illustrera tekniker och funktioner som du kan kopiera eller anpassa för användning i dina egna anteckningsböcker. Andra anteckningsböcker kan också importeras från Azure Sentinel-communityn GitHub.

Den integrerade Jupyter-upplevelsen använder [Azure Notebooks](https://notebooks.azure.com/) för att lagra, dela och köra anteckningsböcker. Du kan också köra dessa anteckningsböcker lokalt om du har en Python-miljö och Jupyter på datorn, eller i andra JupterHub-miljöer som Azure Databricks.

Bärbara datorer har två komponenter:

- Det webbläsarbaserade gränssnittet där du anger och kör frågor och kod och där resultatet av körningen visas.
- En *kärna* som är ansvarig för tolkning och körning av själva koden. 

I Azure-anteckningsböcker körs som standard den här kärnan på Azure *Free Cloud Compute and Storage*. Om dina anteckningsböcker innehåller komplexa maskininlärningsmodeller eller visualiseringar kan du överväga att använda mer kraftfulla, dedikerade beräkningsresurser som virtuella datorer för [datavetenskap](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Anteckningsböcker i ditt konto hålls privata om du inte väljer att dela dem.

Azure Sentinel-anteckningsböckerna använder många populära Python-bibliotek som pandor, matplotlib, bokeh och andra. Det finns många andra Python-paket som du kan välja mellan, som täcker områden som:

- Visualiseringar och grafik
- Databehandling och analys
- Statistik och numerisk databehandling
- Maskininlärning och djupinlärning

Vi har också släppt några öppen källkod Jupyter säkerhetsverktyg i ett paket som heter [msticpy](https://github.com/Microsoft/msticpy/). Det här paketet används i många av de medföljande anteckningsböckerna. Msticpy verktyg är utformade speciellt för att hjälpa till med att skapa bärbara datorer för jakt och utredning och vi arbetar aktivt med nya funktioner och förbättringar.

De första anteckningsböckerna inkluderar:

- **Guidad undersökning - Processvarningar**: Gör att du snabbt kan triage aviseringar genom att analysera aktivitet på den drabbade värden eller värdar.
- **Guidad jakt - Utforskaren för Windows:** Gör att du kan utforska kontoaktivitet, processkörningar, nätverksaktivitet och andra händelser på en värd.
- **Guidad jakt – Office365-Exploring**: Jakt efter misstänkt Office 365-aktivitet i flera Office 365-datauppsättningar.

[Azure Sentinel Community GitHub-databasen](https://github.com/Azure/Azure-Sentinel) är platsen för alla framtida Azure Sentinel-anteckningsböcker som skapats av Microsoft eller bidragit från communityn.

Om du vill använda anteckningsböckerna måste du ha ett Azure Notebooks-konto. Mer information finns [i Snabbstart: Logga in och ange ett användar-ID](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) från dokumentationen till Azure Notebooks. Om du vill skapa det här kontot kan du använda alternativet **Registrera dig för Azure-anteckningsböcker** från kommandofältet i **Azure Sentinel - Anteckningsböcker:**

> [!div class="mx-imgBorder"]
>![Registrera dig för alternativet Azure Notebooks](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

Du kan köra en anteckningsbok direkt från Azure Sentinel eller klona alla Azure Sentinel-anteckningsböcker till ett nytt Azure Notebooks-projekt.

## <a name="run-a-notebook-from-azure-sentinel"></a>Köra en anteckningsbok från Azure Sentinel
 
1. Från Azure-portalen navigerar du till **Azure Sentinel** > **Threat management** > **Notebooks**, där du kan se anteckningsböcker som Azure Sentinel tillhandahåller. 

2. Välj enskilda anteckningsböcker för att läsa deras beskrivningar, nödvändiga datatyper och datakällor. Ett exempel:
    
    > [!div class="mx-imgBorder"]
    > ![starta anteckningsbok](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. Välj den anteckningsbok som du vill använda och välj sedan **Starta anteckningsbok (förhandsversion)** för att klona och konfigurera anteckningsboken till ett nytt Azure Notebooks-projekt som ansluter till din Azure Sentinel-arbetsyta. När processen är klar öppnas anteckningsboken i Azure-anteckningsböcker så att du kan köra den.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Klona Azure Sentinel-anteckningsböcker till ett nytt Azure Notebooks-projekt

Den här proceduren skapar ett Azure Notebooks-projekt åt dig, som innehåller Azure Sentinel-anteckningsböckerna. Du kan sedan köra anteckningsböckerna som de är, eller göra ändringar i dem och sedan köra dem.

1. Från Azure-portalen navigerar du till **Azure Sentinel** > **Threat management** > **Notebooks** och väljer sedan **Klona anteckningsböcker** i kommandofältet:
  
    > [!div class="mx-imgBorder"]
    >![Klona anteckningsböcker](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. När följande dialogruta visas väljer du **Importera** för att klona GitHub-repoen i ditt Azure Notebooks-projekt. Om du inte har ett befintligt Azure Notebooks-konto uppmanas du att skapa ett och logga in.

   ![Importera anteckningsbok](./media/notebooks/sentinel-notebooks-clone.png)

3. Välj inte **Klona rekursivt** i dialogrutan **Ladda upp GitHub-databas** eftersom det här alternativet refererar till länkade GitHub-repos. För projektnamnet använder du standardnamnet eller typen i ett nytt. Klicka sedan på **Importera** för att börja klona GitHub-innehållet, vilket kan ta några minuter att slutföra.

   ![Importera anteckningsbok](./media/notebooks/sentinel-create-project.png)

4. Öppna projektet som du just skapade och öppna sedan mappen **Anteckningsböcker** för att se anteckningsböckerna. Ett exempel:

   ![Importera repo](./media/notebooks/sentinel-open-notebook1.png)

Du kan sedan köra anteckningsböcker från Azure Notebooks. Om du vill återgå till dessa anteckningsböcker från Azure Sentinel väljer du **Gå till dina anteckningsböcker** i kommandofältet i **Azure Sentinel - Anteckningsböcker:**

> [!div class="mx-imgBorder"]
>![Gå till alternativet Anteckningsböcker](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Använda anteckningsböcker för att jaga

Varje anteckningsbok går igenom stegen för att utföra en jakt eller undersökning. Bibliotek och andra beroenden som behövs av den bärbara datorn kan installeras från själva anteckningsboken eller via en enkel konfigurationsprocedur. Konfiguration som binder tillbaka ditt anteckningsboksprojekt till din Azure Sentinel-prenumeration etableras automatiskt i föregående steg.

1. Om du inte redan finns i Azure Notebooks kan du använda alternativet **Gå till dina anteckningsböcker** från kommandofältet i **Azure Sentinel - Anteckningsböcker:**
    
    > [!div class="mx-imgBorder"]
    >![Gå till alternativet Anteckningsböcker](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    I Azure-anteckningsböcker väljer du **Mina projekt**, sedan projektet som innehåller Azure Sentinel-anteckningsböckerna och slutligen mappen **Anteckningsböcker.**
    
2. Innan du öppnar en anteckningsbok bör du tänka på att kostnadsfri beräkning som standard är markerad för att köra anteckningsböckerna:
    
   ![välj anteckningsbok](./media/notebooks/sentinel-open-notebook2.png)
    
    Om du har konfigurerat en virtuella datavetenskapsdatorer (DSVM) så att den används som förklaras i inledningen väljer du DSVM och autentiserar innan du öppnar den första anteckningsboken. 

3. Välj en anteckningsbok för att öppna den.
    
    Första gången du öppnar en anteckningsbok kan du bli ombedd att välja en kärnversion. Om du inte uppmanas kan du välja kärnversionen från **Kernel** >  **Change-kärnan**och sedan välja en version som är minst 3.6. Den valda kärnversionen visas längst upp till höger i det bärbara fönstret:
    
   ![välj anteckningsbok](./media/notebooks/sentinel-select-kernel.png)

4. Innan du gör några ändringar i anteckningsboken som du har hämtat är det en bra idé att göra en kopia av den ursprungliga anteckningsboken och arbeta med kopian. Det gör du genom att välja **Gör** > **en kopia**. Genom att arbeta med kopior kan du på ett säkert sätt uppdatera till framtida versioner av anteckningsböcker utan att skriva över någon av dina data.
    
    Nu är du redo att köra eller redigera den markerade anteckningsboken.

Rekommendationer:

- En snabb introduktion till att fråga data i Azure Sentinel finns i anteckningsboken [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) i huvudmappen **För anteckningsböcker.** 

- Du hittar ytterligare exempel anteckningsböcker i undermappen **Exempelböcker.** Dessa exempel anteckningsböcker har sparats med data, så att det är lättare att se den avsedda utdata. Vi rekommenderar att du visar dessa anteckningsböcker i [nbviewer](https://nbviewer.jupyter.org/). 

- **HowTos-mappen** innehåller anteckningsböcker som beskriver till exempel: Ange standardversion av Python- versioner, konfigurera en DSVM, skapa Azure Sentinel-bokmärken från en anteckningsbok och andra ämnen.

De bärbara datorerna är avsedda både som användbara verktyg och som illustrationer och kodexempel som du kan använda i utvecklingen av dina egna anteckningsböcker.

Vi välkomnar feedback, oavsett om det gäller förslag, förfrågningar om funktioner, bidragande anteckningsböcker, felrapporter eller förbättringar och tillägg till befintliga anteckningsböcker. Gå till [Azure Sentinel Community GitHub](https://github.com/Azure/Azure-Sentinel) för att skapa ett problem eller en gaffel och ladda upp ett bidrag.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du kommer igång med Jupyter-anteckningsböcker i Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- [Proaktivt jakt efter hot](hunting.md)
- [Använd bokmärken för att spara intressant information under jakt](bookmarks.md)
