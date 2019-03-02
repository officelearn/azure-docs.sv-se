---
title: Jakt funktioner med hjälp av bärbara datorer i Azure-Sentinel-förhandsversionen | Microsoft Docs
description: Den här artikeln beskriver hur du använder anteckningsböcker med funktioner för Azure Sentinel jakt.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 83bbb7f6a683265e4f2ed20801d0c7541a54e9f9
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244969"
---
# <a name="use-notebooks-to-hunt-for-anomalies"></a>Använda anteckningsböcker för jakt efter avvikelser

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel utnyttjar kraften i interaktiva Jupyter-anteckningsböcker för att ge insikter och åtgärder för att undersöka eller hitta säkerhetsavvikelser i din miljö. Azure Sentinel förinstalleras med anteckningsböcker som har utvecklats av Microsofts säkerhetsanalytiker. Varje anteckningsboken är ändamål skapats med en självständig arbetsflöde för specifika användningsfall. Visualiseringar som ingår i varje anteckningsboken för snabbare datagranskning och threat jakt. Du kan anpassa de inbyggda anteckningsböckerna för att uppfylla dina behov, skapa nya anteckningsböcker från grunden eller importera anteckningsböcker från Azure Sentinel ”GitHub-communityn. Jupyter-anteckningsböcker importeras som ett projekt på sidan för Azure-datorer – detta gör det möjligt för användare att få åtkomst till alla sina Sentinel-Azure-datorer på samma ställe. Bärbara datorer kan köras genom att klicka på en knapp eller kan konfigureras av användaren så att de matchar deras miljö.

Fullständigt integrerad upplevelse kan anteckningsböcker för körning på molnet och lagring utan underliggande merkostnader för underhåll. Möjlighet att utnyttja befintliga Jupyter Notebooks ekosystemet kan du mängden källa modeller, utan delning av kunddata. 


Varje anteckningsboken finns på Azure-datorer (för närvarande i förhandsversion), en interaktiv utvecklingsmiljö i Azure-molnet. Bärbara datorer är alltid tillgänglig och alltid tillgänglig från valfri webbläsare, var som helst i världen.  Azure Sentinel' inbyggda anteckningsböcker för undersökning och jakt klonas till ett projekt som tillhör dig, och som du kan ändra och skräddarsy din miljö. Några av de mest populära inbyggda bärbara datorerna är:

- **Avisera undersökningar och jakt**: Interaktiv anteckningsboken möjliggör snabb prioritering av olika typer av aviseringar genom att hämta relaterad aktivitet och berikas aviseringen med tillhörande aktivitet och data som aviseringen genererades.

- **Slutpunktsvärd guidad jakt**: Kan du hitta loggar från en säkerhetsöverträdelse genom att gå nedåt i de relevanta säkerhetsaktiviteter kopplad till en slutpunkt-värden.  

- **Office logga in interaktiva jakt**: Gör det möjligt för dig att hitta misstänkta inloggningar genom att visualisera geografiska platser av misstänkta loggar samt visa ovanliga inloggningsmönster härleds från Office 365-data.

## <a name="run-a-notebook"></a>Kör en anteckningsbok
I följande exempel kör vi inbyggda anteckningsboken för att söka efter djup börjar med plats avvikelser för att se om någon i en oväntad plats gör något i nätverket.

1. Sentinel-Azure-portalen klickar du på **anteckningsböcker** och välj sedan något av de inbyggda bärbara datorerna.
  
   ![Välj anteckningsbok](./media/notebooks/select-notebook.png)

3.  Klicka på **Import** att klona GitHub-lagringsplatsen till ditt projekt i Azure-anteckningsböcker.
  ![Import-anteckningsbok](./media/notebooks/import1.png)
4.  Varje notebook vägleder dig genom stegen för att utföra en hunt eller undersökning. Modeller, bibliotek, och andra beroenden och konfiguration för anslutning till Azure Sentinel importeras automatiskt om du vill aktivera en körning. Alla kod och bibliotek som krävs för att köra en anteckningsbok läses in i förväg. Du kan omedelbart börja kör en bärbar dator mot Log Analytics-arbetsytan utan konfiguration.

   ![Importera lagringsplats](./media/notebooks/import2.png)

5.  Utforska, ändra och köra alla exempelanteckningsböcker som tillhandahålls. Dessa kan användas som byggblock för många olika scenarier.

  ![Välj anteckningsbok](./media/notebooks/import3.png)



## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du kör en jakt undersökning med anteckningsböcker i Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- [Hitta proaktivt hot](hunting.md)
- [Använda bokmärken för att spara intressant information vid jakt](bookmarks.md)