---
title: Jakt funktioner med hjälp av antecknings böcker i Azure Sentinel Preview | Microsoft Docs
description: Den här artikeln beskriver hur du använder antecknings böcker med funktionerna i Azure Sentinel-jakt.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 6372a7958caf108903321e5ee87ea6bf1a42271c
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689533"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Använda Jupyter-anteckningsböcker för att efter säkerhetshot

> [!IMPORTANT]
> Azure Sentinel är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Stiftelsen i Azure Sentinel är data lagret. den kombinerar hög prestanda fråga, dynamiskt schema och skalar till enorma data volymer. Azure Sentinel-portalen och alla Azure Sentinel-verktyg använder ett gemensamt API för att få åtkomst till det här data lagret. Samma API är också tillgängligt för externa verktyg som [Jupyter](https://jupyter.org/) Notebooks och python. Många vanliga uppgifter kan utföras i portalen, och Jupyter utökar omfattningen av vad du kan göra med dessa data. Den kombinerar fullständig programmering med en enorm samling bibliotek för maskin inlärning, visualisering och data analys. Dessa attribut gör Jupyter till ett övertygande verktyg för säkerhets undersökning och jakt.

![exempel antecknings bok](./media/notebooks/sentinel-notebooks-map.png)

Vi har integrerat Jupyter-upplevelsen i Azure Sentinel-portalen, vilket gör det enkelt för dig att skapa och köra antecknings böcker för att analysera dina data. *Kqlmagic* -biblioteket ger ett lim som gör det möjligt att ta frågor från Azure Sentinel och köra dem direkt i en bärbar dator. Frågor använder [Kusto-frågespråket](https://kusto.azurewebsites.net/docs/query/index.html). Flera antecknings böcker, som har utvecklats av några av Microsofts säkerhetsanalytiker, paketeras med Azure Sentinel. Några av dessa antecknings böcker är utformade för ett visst scenario och kan användas som de är. Andra är avsedda som exempel för att illustrera tekniker och funktioner som du kan kopiera eller anpassa för användning i dina egna antecknings böcker. Andra antecknings böcker kan också importeras från community-GitHub för Azure Sentinel.

Den integrerade Jupyter-miljön använder [Azure Notebooks](https://notebooks.azure.com/) för att lagra, dela och köra antecknings böcker. Du kan också köra dessa antecknings böcker lokalt (om du har en python-miljö och Jupyter på datorn) eller i andra JupterHub-miljöer som Azure Databricks.

Antecknings böcker har två komponenter:

- det webbläsarbaserade gränssnittet där du anger och kör frågor och kod och var resultatet av körningen visas.
- en *kernel* som ansvarar för parsning och körning av koden. 

I Azure Notebooks körs den här kärnan på Azures *kostnads fri moln beräkning och lagring* som standard. Om dina antecknings böcker innehåller komplexa Machine Learning-modeller eller visualiseringar bör du överväga att använda mer kraftfulla, dedikerade beräknings resurser som [data vetenskaps Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Antecknings böcker i ditt konto hålls privata såvida du inte väljer att dela dem.

Azure Sentinel-anteckningsbokarna använder många populära python-bibliotek som Pandas, matplotlib, bokeh och andra. Det finns ett stort antal andra python-paket som du kan välja bland, som omfattar områden som:

- visualiseringar och grafik
- data bearbetning och analys
- statistik och numerisk data behandling
- maskin inlärning och djup inlärning

Vi har också släppt några Jupyter säkerhets verktyg med öppen källkod i ett paket med namnet [msticpy](https://github.com/Microsoft/msticpy/). Det här paketet används i många av de antecknings böcker som ingår. Msticpy-verktyg är särskilt utformade för att hjälpa dig att skapa antecknings böcker för jakt och undersökning och vi arbetar aktivt med nya funktioner och förbättringar.

De första antecknings böckerna är:

- **Guidad undersökning – process aviseringar**: Gör att du snabbt kan prioritering aviseringar genom att analysera aktivitet på den eller de berörda värdarna.
- **Guidad jakt – Windows värd Explorer**: Gör att du kan utforska konto aktivitet, bearbeta körningar, nätverks aktivitet och andra händelser på en värd.  
- **Guidad jakt – Office365 – utforska**: Söker efter misstänkt Office 365-aktivitet i flera O365-datauppsättningar.

[Azure Sentinel community GitHub](https://github.com/Azure/Azure-Sentinel) -lagringsplatsen är platsen för alla framtida Azure Sentinel-anteckningsböcker som skapats av Microsoft eller som har bidragit från communityn.

## <a name="run-a-notebook"></a>Köra en bärbar dator

I följande exempel skapar vi ett Azure Notebooks-projekt från Azure Sentinel-portalen och fyller projektet med antecknings böcker. Innan du använder de här antecknings böckerna är det en bra idé att göra en kopia av den bärbara datorn och arbeta på kopian. När du arbetar med kopior kan du på ett säkert sätt uppdatera framtida versioner av antecknings böcker utan att skriva över någon av dina data.

1. I Azure Sentinel-portalen klickar du på antecknings **böcker** på navigerings menyn. Skapa ett nytt Azure Notebooks projekt genom att klicka på **klona Azure Sentinel-anteckningsböcker** eller öppna dina befintliga Notebook-projekt Klicka på **gå till dina antecknings böcker**.
  
   ![Välj antecknings böcker](./media/notebooks/sentinel-azure-notebooks-home.png)

2. Om du väljer **klona Azure Sentinel-anteckningsböcker** i föregående steg så visas följande dialog ruta. Klicka på **Importera** för att klona GitHub-lagrings platsen i Azure Notebooks-projektet. Om du inte har ett befintligt Azure Notebooks-konto uppmanas du att skapa ett och logga in.

   ![Importera antecknings bok](./media/notebooks/sentinel-notebooks-clone.png)

3. När du skapar ett nytt projekt måste du namnge projektet – Använd standard namnet eller ange ett nytt namn. Markera inte alternativet **klona rekursivt** – det här alternativet refererar till länkade GitHub-databaser. Om du klickar på **Importera** börjar klona innehållet i GitHub, vilket kan ta några minuter att slutföra.

   ![Importera antecknings bok](./media/notebooks/sentinel-create-project.png)

4. Öppna mappen **Notebooks** om du vill se antecknings böckerna. Varje antecknings bok vägleder dig genom stegen för att genomföra en jakt eller undersökning. Bibliotek och andra beroenden som krävs av antecknings boken kan installeras från själva antecknings boken eller via en enkel konfigurations procedur. Konfigurationen som binder ditt Notebook-projekt tillbaka till din Azure Sentinel-prenumeration tillhandahålls automatiskt i föregående steg. Dina antecknings böcker är klara att köras mot din Azure Sentinel Log Analytics-arbetsyta.

   ![Importera lagrings platsen](./media/notebooks/sentinel-open-notebook1.png)

5. Öppna en bärbar dator. Den kostnads fria beräkningen är markerad som standard för att köra antecknings böckerna (markerat). Om du har konfigurerat en DSVM att använda (se ovan) väljer du DSVM och autentisera innan du öppnar den första antecknings boken. Klicka på en antecknings bok för att öppna den.

   ![Välj antecknings bok](./media/notebooks/sentinel-open-notebook2.png)

6. Välja python-versionen. När du först öppnar en antecknings bok kan du bli ombedd att välja en kernel-version. Om inte väljer du den kernel som ska användas på följande sätt. Python 3,6 eller senare bör vara den valda kärnan (längst upp till höger i fönstret Notebook).

   ![Välj antecknings bok](./media/notebooks/sentinel-select-kernel.png)

En snabb introduktion till att fråga data i Azure Sentinel finns i antecknings boken för [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) i mappen med huvud antecknings böcker. Ytterligare exempel antecknings böcker finns i undermappen **exempel-Notebooks** . Exempel antecknings böckerna har sparats med data så att det blir enklare att se de avsedda utdata (vi rekommenderar att du visar dem i [nbviewer](https://nbviewer.jupyter.org/)). Mappen **howtos** innehåller antecknings böcker som beskriver, till exempel: ställa in standard-python-version, konfigurera en DSVM, skapa Azure Sentinel-bokmärken från en bärbar dator och andra ämnen.

Dessa antecknings böcker är avsedda som både användbara verktyg och som illustrationer och kod exempel som du kan använda i utvecklingen av dina egna antecknings böcker.

Vi välkomnar feedback, om förslag, förfrågningar om funktioner, antecknings böcker som har bidragit, fel rapporter eller förbättringar och tillägg till befintliga antecknings böcker. Gå till [Azure Sentinel community-GitHub](https://github.com/Azure/Azure-Sentinel) för att skapa ett ärende eller en förgrening och ladda upp ett bidrag.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du kommer igång med Jupyter-anteckningsböcker i Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- [Söker proaktivt efter hot](hunting.md)
- [Använd bok märken för att spara intressant information under jakt](bookmarks.md)
