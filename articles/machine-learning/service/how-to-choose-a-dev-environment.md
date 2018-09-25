---
title: Utvecklingsmiljöer för Azure Machine Learning | Microsoft Docs
description: En översikt över utvecklingsmiljöer som du kan använda med Azure Machine Learning-tjänsten. Python 3 är det enda kravet för utvecklingsmiljön, men vi rekommenderar att du även använder Conda-miljöer. Vi rekommenderar för utveckling, av verktyg Jupyter Notebooks, Azure-anteckningsböcker och IDE/kod redigerare.
services: machine-learning
author: rastala
ms.author: roastala
manager: cgronlun
ms.service: machine-learning
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 9/24/2018
ms.openlocfilehash: 260e209bcf00396ee545851684038578c4fd148a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971050"
---
# <a name="development-environment-for-azure-machine-learning"></a>Utvecklingsmiljö för Azure Machine Learning 

Läs mer om utvecklingsmiljöer som du kan använda med Azure Machine Learning-tjänsten. 

Azure Machine Learning-tjänsten är plattformsoberoende och kräver inte en specifik utvecklingsmiljö. Det krävs __Python 3__, och vi rekommenderar att du använder __Conda__ skapa isolerade miljöer. __Om du redan har en utvecklingsmiljö som uppfyller dessa krav__, du kan hoppa över det här dokumentet och gå till den [Konfigurera utvecklingsmiljön](how-to-configure-environment.md) dokumentet.

Resten av det här dokumentet beskrivs utvecklingsmiljöer som vi rekommenderar:

* __Jupyter Notebook__
* __Azure-anteckningsböcker__
* __Integrated development Environment (IDE) och kod redigerare__
* __Virtuell dator för datavetenskap__

En jämförelse mellan dessa miljöer är svårt eftersom både anteckningsböcker och IDE: er kan utökas. Till exempel kan vissa IDE: er användas som klienter till Jupyter Notebooks. Generellt sett __anteckningsböcker__ är utformade för __interaktiva experimentering__ och __visualisering__. __IDE: er och Kodredigerare__ tillhandahåller verktyg för att __förbättra kodkvaliteten__ och __integrera med externa system__ , till exempel versionskontroll.

> [!TIP]
> Med hjälp av en miljö låser inte sig med hjälp av den andra. Anteckningsböcker IDE: er finns bara verktyg och kan blandas efter behov. Du kan till exempel börja experimentera på en bärbar dator och sedan exportera till ett python-skript för redigering och felsökning i IDE.
>
> Det är därför den virtuella datorn för datavetenskap tillhandahåller både Jupyter-anteckningsböcker och flera populära Python IDE: er.

## <a name="jupyter-notebooks"></a>Jupyter Notebook

Jupyter Notebooks är en del av den [Jupyter projekt](https://jupyter.org/). De fokuserar på att tillhandahålla en interaktiv kodningserfarenheter där du skapar dokument som blandar live-koden med löpande text och grafik. Du kan installera Jupyter Notebooks på en rad olika plattformar.

Med Jupyter-anteckningsbok installationen kan du installera och konfigurera miljön efter behov. Men du är ansvarig för att underhålla systemet.

## <a name="azure-notebooks"></a>Azure Notebooks

[Azure anteckningsböcker](https://notebooks.azure.com) (förhandsversion) är en miljö för anteckningsböcker i Azure-molnet. Den är baserad på Jupyter och ger en miljö som redan har lästs in med populära bibliotek. Azure Machine Learning SDK finns redan i Azure-datorer, så att du kan börja experimentera med nästan ingen installationen.

Azure anteckningsböcker förenklar också processen med att dela dina anteckningsböcker. Du kan dela en URL till dina anteckningsböcker, offentliggöra biblioteket eller dela i sociala medier från Azure-anteckningsböcker-gränssnittet.

Nackdel med Azure-datorer är att du inte har fullständig kontroll över miljön och kanske inte kan installera egen programvara som du behöver. Det är också en delad miljö, så att dina anteckningsböcker kan köras långsammare än på en dedikerad Jupyter Notebook-installation.

## <a name="ides-and-code-editors"></a>IDE: er och Kodredigerare

Det finns många IDE: er och Kodredigerare som fungerar med Azure Machine Learning. Det enda programvara kravet är Azure Machine Learning SDK, som kan installeras med den `pip` verktyget.

Vi rekommenderar att [Visual Studio Code](https://code.visualstudio.com/), eftersom det ger dig verktyg för att arbeta med både Python språk och Azure Machine Learning. Det är tillgängligt för Linux, macOS och Windows-plattformar.

## <a name="data-science-virtual-machine"></a>Virtuell dator för datavetenskap

Den virtuella datorn på datavetenskap (DSVM) är en kombination av de föregående miljöerna. Det är en virtuell dator på Azure-plattformen med Jupyter Notebooks, Visual Studio Code och Azure Machine Learning SDK förinstallerat. Skapa den virtuella datorn är mer komplexa än Azure Notebooks, men mindre komplex än att konfigurera en virtuell dator från grunden. Eftersom nödvändig programvara är förinstallerat i avbildningen, kan du börja experimentera med Azure Machine Learning snabbt när den virtuella datorn har skapats.

DSVM kan du välja beräkningsresurser som du behöver, till exempel CPU, GPU och minne. Det är också förinstallerat med andra redigerare som PyCharm samt populära machine learning-programvara som TensorFlow, Keras och PyTorch. Om programvara som måste inte installeras, kan du installera det själv.

Läs mer på vad som är förinstallerade på [vad är den virtuella datorn för datavetenskap](../data-science-virtual-machine/overview.md) dokumentet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om utvecklingsmiljöer, lär du dig [så här konfigurerar du en utvecklingsmiljö](how-to-configure-environment.md).

