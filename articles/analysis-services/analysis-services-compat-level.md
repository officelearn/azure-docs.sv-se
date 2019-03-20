---
title: Datamodelldata för kompatibilitetsnivå i Azure Analysis Services | Microsoft Docs
description: Förstå tabelldata modellen kompatibilitetsnivå.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9f2eae4160dbef164ec70e8fa4d7b3a83706ae96
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880134"
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Kompatibilitetsnivån för Analysis Services-tabellmodeller

*Kompatibilitetsnivån* refererar till release-specifika beteenden i Analysis Services-motorn. Ändringar i kompatibilitetsnivå sammanfalla vanligtvis med större versioner av SQL Server. Dessa ändringar är också implementerade i Azure Analysis Services för att upprätthålla paritet mellan båda plattformarna. Kompatibilitetsnivån ändras även gälla funktionerna i dina tabellmodeller. DirectQuery och tabular objektmetadata du till exempel ha olika implementeringar beroende på kompatibilitetsnivå. Kompatibilitetsnivån har angetts i projekt för tabellmodeller i Visual Studio (SSDT).

Azure Analysis Services stöder tabellmodeller på kompatibilitetsnivå 1200 och 1400. Senaste kompatibilitetsnivå är 1400. Den här nivån är i linje med SQL Server 2017 Analysis Services. Huvudfunktioner i kompatibilitetsnivån 1400 omfattar:

*  Nya funktioner för dataanslutning och import med stöd för TOM APIs och TMSL-skript. 
*  Transformering av data och funktioner för kombinationsprogram genom att använda hämta Data och M-uttryck.
*  Mått stöd för en egenskap för rader med detaljerad information med ett DAX-uttryck. Den här egenskapen gör klientverktyg som Microsoft Excel för att öka detaljnivån till detaljerade data från en sammansatt rapport. När användarna visar total försäljning för en region och månad, kan de visa associerade orderinformationen. 
*  Säkerhet på objektnivå för tabeller och kolumner som ett komplement till data i dem.
*  Förbättrat stöd för ojämna hierarkier.
*  Övervakning av prestanda och förbättringar.
 
## <a name="set-compatibility-level"></a>Ange kompatibilitetsnivån

 När du skapar ett nytt projekt för tabellmodeller i SSDT, du kan ange kompatibilitetsnivån på den **tabellmodelldesigner** dialogrutan. 
  
 ![ssas_tabularproject_compat1200](./media/analysis-services-compat-level/aas-tabularproject-compat.png)  
  
 Om du väljer den **Visa inte det här meddelandet igen** alternativet alla efterföljande projekt använder kompatibilitetsnivå du angav som standard. Du kan ändra standardkompatibilitetsnivån i SSDT i **verktyg** > **alternativ**.  
  
 Om du vill uppgradera ett befintligt projekt för tabellmodeller i SSDT, ange den **kompatibilitetsnivå** -egenskapen i modellen **egenskaper** fönster. Kom ihåg, uppgradera kompatibilitetsnivån kan inte ångras.
  
## <a name="check-compatibility-level-for-a-tabular-model-database-in-sql-server-management-studio"></a>Kontrollera kompatibilitetsnivån för en databas för tabellmodeller i SQL Server Management Studio 

 I SSMS högerklickar du på namnet på databasen > **egenskaper** > **kompatibilitetsnivå**.  
  
## <a name="check-supported-compatibility-level-for-a-server-in-ssms"></a>Kontrollera stöds kompatibilitetsnivån för en server i SSMS  

 Högerklicka på servernamnet i SSMS > **egenskaper** > **stöds kompatibilitetsnivå**.  
  
 Denna egenskap Anger högsta kompatibilitetsnivån för en databas som körs på servern (förutom förhandsversion). Stöds kompatibilitetsnivån kan inte ändras.  

## <a name="next-steps"></a>Nästa steg

  [Manage Analysis Services](analysis-services-manage.md)  
