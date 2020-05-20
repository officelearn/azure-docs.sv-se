---
title: Använda antecknings böcker med Azure Sentinel för säkerhets jakt
description: Den här artikeln beskriver hur du använder antecknings böcker med funktionerna i Azure Sentinel-jakt.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: bf63d5c8cb46fd791508af40dcefd7b39d4ba9de
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652029"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Använda Jupyter-anteckningsböcker för att efter säkerhetshot

Stiftelsen i Azure Sentinel är data lagret. den kombinerar hög prestanda fråga, dynamiskt schema och skalar till enorma data volymer. Azure Portal och alla Azure Sentinel-verktyg använder ett gemensamt API för att få åtkomst till det här data lagret. Samma API är också tillgängligt för externa verktyg som [Jupyter](https://jupyter.org/) Notebooks och python. Många vanliga uppgifter kan utföras i portalen, och Jupyter utökar omfattningen av vad du kan göra med dessa data. Den kombinerar fullständig programmering med en enorm samling bibliotek för maskin inlärning, visualisering och data analys. Dessa attribut gör Jupyter till ett övertygande verktyg för säkerhets undersökning och jakt.

![exempel antecknings bok](./media/notebooks/sentinel-notebooks-map.png)

Vi har integrerat Jupyter-upplevelsen i Azure Portal, vilket gör det enkelt för dig att skapa och köra antecknings böcker för att analysera dina data. *Kqlmagic* -biblioteket ger ett lim som gör det möjligt att ta frågor från Azure Sentinel och köra dem direkt i en bärbar dator. Frågor använder [Kusto-frågespråket](https://kusto.azurewebsites.net/docs/query/index.html). Flera antecknings böcker, som har utvecklats av några av Microsofts säkerhetsanalytiker, paketeras med Azure Sentinel. Några av dessa antecknings böcker är utformade för ett visst scenario och kan användas som de är. Andra är avsedda som exempel för att illustrera tekniker och funktioner som du kan kopiera eller anpassa för användning i dina egna antecknings böcker. Andra antecknings böcker kan också importeras från community-GitHub för Azure Sentinel.

Den integrerade Jupyter-miljön använder [Azure Notebooks](https://notebooks.azure.com/) för att lagra, dela och köra antecknings böcker. Du kan också köra dessa antecknings böcker lokalt om du har en python-miljö och Jupyter på din dator eller i andra JupterHub-miljöer som Azure Databricks.

Antecknings böcker har två komponenter:

- Det webbläsarbaserade gränssnittet där du anger och kör frågor och kod och var resultatet av körningen visas.
- En *kernel* som ansvarar för parsning och körning av koden. 

I Azure Notebooks körs den här kärnan som standard i Azures *kostnads fri moln beräkning och lagring*. Om dina antecknings böcker innehåller komplexa Machine Learning-modeller eller visualiseringar bör du överväga att använda mer kraftfulla, dedikerade beräknings resurser som [data vetenskaps Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Antecknings böcker i ditt konto hålls privata såvida du inte väljer att dela dem.

Azure Sentinel-anteckningsbokarna använder många populära python-bibliotek som Pandas, matplotlib, bokeh och andra. Det finns många andra python-paket som du kan välja bland, som omfattar områden som:

- Visualiseringar och grafik
- Data bearbetning och analys
- Statistik och numerisk data behandling
- Maskin inlärning och djup inlärning

Vi har också släppt några Jupyter säkerhets verktyg med öppen källkod i ett paket med namnet [msticpy](https://github.com/Microsoft/msticpy/). Det här paketet används i många av de antecknings böcker som ingår. Msticpy-verktyg är särskilt utformade för att hjälpa dig att skapa antecknings böcker för jakt och undersökning och vi arbetar aktivt med nya funktioner och förbättringar.

De första antecknings böckerna är:

- **Guidad undersökning – process aviseringar**: gör att du snabbt kan prioritering aviseringar genom att analysera aktiviteten på den berörda värden eller värdarna.
- **Guidad jakt – Windows Host Explorer**: gör att du kan utforska konto aktivitet, bearbeta körningar, nätverks aktivitet och andra händelser på en värd.
- **Guidad jakt – Office365 – utforska**: söker efter misstänkt Office 365-aktivitet i flera Office 365-datauppsättningar.

[Azure Sentinel community GitHub-lagringsplatsen](https://github.com/Azure/Azure-Sentinel) är platsen för alla framtida Azure Sentinel-anteckningsböcker som skapats av Microsoft eller som har bidragit från communityn.

Om du vill använda antecknings böckerna måste du ha ett Azure Notebooks-konto. Mer information finns i [snabb start: Logga in och ange ett användar-ID](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) från Azure Notebooks-dokumentationen. Om du vill skapa det här kontot kan du använda alternativet **Registrera dig för Azure Notebooks** från kommando fältet i **Azure Sentinel-Notebooks**:

> [!div class="mx-imgBorder"]
>![Registrera dig för Azure Notebooks alternativ](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

Du kan köra en bärbar dator direkt från Azure Sentinel eller klona alla Azure Sentinel-anteckningsböcker till ett nytt Azure Notebooks projekt.

## <a name="run-a-notebook-from-azure-sentinel"></a>Köra en bärbar dator från Azure Sentinel
 
1. Från Azure Portal navigerar du till **Azure Sentinel**  >  **Threat management**  >  **antecknings böcker**för Azure Sentinel Threat Management, där du kan se antecknings böcker som Azure Sentinel tillhandahåller. 

2. Välj enskilda antecknings böcker för att läsa beskrivningar, nödvändiga data typer och data källor. Till exempel:
    
    > [!div class="mx-imgBorder"]
    > ![Starta Notebook](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. Välj den antecknings bok som du vill använda och välj sedan **Starta Notebook (för hands version)** för att klona och konfigurera antecknings boken i ett nytt Azure Notebooks projekt som ansluter till din Azure Sentinel-arbetsyta. När processen är klar öppnas antecknings boken i Azure Notebooks som du kan köra.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Klona Azure Sentinel-anteckningsböcker till ett nytt Azure Notebooks-projekt

Den här proceduren skapar ett Azure Notebooks-projekt åt dig, som innehåller Azure Sentinel-anteckningsböcker. Du kan sedan köra antecknings böckerna som de är eller ändra dem och sedan köra dem.

1. Från Azure Portal navigerar du till **Azure Sentinel**  >  **Threat management**  >  **antecknings böcker** för Azure Sentinel Threat Management och väljer sedan **klona antecknings böcker** från kommando fältet:
  
    > [!div class="mx-imgBorder"]
    >![Alternativet klona antecknings böcker](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. När följande dialog ruta visas väljer du **Importera** för att klona GitHub-lagrings platsen till ditt Azure Notebooks-projekt. Om du inte har ett befintligt Azure Notebooks-konto uppmanas du att skapa ett och logga in.

   ![Importera antecknings bok](./media/notebooks/sentinel-notebooks-clone.png)

3. I dialog rutan **Ladda upp GitHub-lagringsplats** väljer du **klon rekursivt** eftersom det här alternativet refererar till länkade GitHub-databaser. Använd standard namnet eller ange ett nytt som projekt namn. Klicka sedan på **Importera** för att börja klona innehållet i GitHub, vilket kan ta några minuter att slutföra.

   ![Importera antecknings bok](./media/notebooks/sentinel-create-project.png)

4. Öppna det projekt som du nyss skapade och öppna sedan mappen **antecknings böcker** för att se antecknings böckerna. Till exempel:

   ![Importera lagrings platsen](./media/notebooks/sentinel-open-notebook1.png)

Du kan sedan köra antecknings böckerna från Azure Notebooks. Om du vill återgå till dessa antecknings böcker från Azure Sentinel väljer du **gå till dina antecknings böcker** från kommando fältet i **Azure Sentinel-Notebooks**:

> [!div class="mx-imgBorder"]
>![Gå till alternativet antecknings böcker](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Använda antecknings böcker till jakt

Varje antecknings bok vägleder dig genom stegen för att genomföra en jakt eller undersökning. Bibliotek och andra beroenden som krävs av antecknings boken kan installeras från själva antecknings boken eller via en enkel konfigurations procedur. Konfigurationen som binder ditt Notebook-projekt tillbaka till din Azure Sentinel-prenumeration tillhandahålls automatiskt i föregående steg.

1. Om du inte redan är i Azure Notebooks kan du använda alternativet **gå till dina antecknings böcker** från kommando fältet i **Azure Sentinel – antecknings böcker**:
    
    > [!div class="mx-imgBorder"]
    >![Gå till alternativet antecknings böcker](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    I Azure Notebooks väljer du **Mina projekt**, sedan projektet som innehåller Azure Sentinel-anteckningsbokar och slutligen mappen **Notebooks** .
    
2. Innan du öppnar en bärbar dator bör du vara medveten om att som standard är den kostnads fria beräkningen markerad för att köra antecknings böckerna:
    
   ![Välj antecknings bok](./media/notebooks/sentinel-open-notebook2.png)
    
    Om du har konfigurerat en data vetenskaps Virtual Machines (DSVM) som ska användas som förklarad i introduktionen, väljer du DSVM och autentisera innan du öppnar den första antecknings boken. 

3. Välj en antecknings bok för att öppna den.
    
    Första gången du öppnar en antecknings bok kan du uppmanas att välja en kernel-version. Om du inte uppmanas kan du välja kernel-versionen från **kernel**  >   **change kernel**och sedan välja en version som är minst 3,6. Den valda kernel-versionen visas längst upp till höger i anteckningsbok-fönstret:
    
   ![Välj antecknings bok](./media/notebooks/sentinel-select-kernel.png)

4. Innan du gör några ändringar i den antecknings bok som du har hämtat är det en bra idé att göra en kopia av den ursprungliga antecknings boken och arbeta på kopian. Det gör du genom att välja **Arkiv**  >  **gör en kopia**. När du arbetar med kopior kan du på ett säkert sätt uppdatera framtida versioner av antecknings böcker utan att skriva över någon av dina data.
    
    Du är nu redo att köra eller redigera den valda antecknings boken.

Rekommenderade

- En snabb introduktion till att fråga data i Azure Sentinel finns i antecknings boken för [GetStarted](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/345cf9f7c8f6137f5af4593a3f9d7568acd6cbc2/DeprecatedNotebooks/Get%20Started.ipynb) i mappen med huvud **antecknings böcker** . 

- Du hittar fler exempel på antecknings böcker i undermappen **exempel – Notebooks** . De här exempel antecknings böckerna har sparats med data så att det blir enklare att se avsedda utdata. Vi rekommenderar att du visar dessa antecknings böcker i [nbviewer](https://nbviewer.jupyter.org/). 

- Mappen **howtos** innehåller antecknings böcker som beskriver, till exempel: ställa in standard-python-version, konfigurera en DSVM, skapa Azure Sentinel-bokmärken från en bärbar dator och andra ämnen.

De antecknings böcker som tillhandahålls är både användbara verktyg och som illustrationer och kod exempel som du kan använda i utvecklingen av dina egna antecknings böcker.

Vi välkomnar feedback, om förslag, förfrågningar om funktioner, antecknings böcker som har bidragit, fel rapporter eller förbättringar och tillägg till befintliga antecknings böcker. Gå till [Azure Sentinel community-GitHub](https://github.com/Azure/Azure-Sentinel) för att skapa ett ärende eller en förgrening och ladda upp ett bidrag.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du kommer igång med Jupyter-anteckningsböcker i Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- [Söker proaktivt efter hot](hunting.md)
- [Använd bok märken för att spara intressant information under jakt](bookmarks.md)
