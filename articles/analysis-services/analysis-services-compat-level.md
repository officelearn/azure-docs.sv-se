---
title: Datamodell kompatibilitetsnivån i Azure Analysis Services | Microsoft Docs
description: Förstå tabelldata modellen kompatibilitetsnivå.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 231a7ea42560aac3fba0ccf27e27123320f219a3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Kompatibilitetsnivån för Analysis Services-tabellmodeller

*Kompatibilitetsnivån* refererar till version-specifika funktioner i Analysis Services-motorn. Ändringar av kompatibilitetsnivån sammanfaller normalt med större versioner av SQL Server. De här ändringarna implementeras också i Azure Analysis Services för att upprätthålla paritet mellan båda plattformar. Kompatibilitet ändras även påverka funktionerna i din tabellmodeller. Till exempel har DirectQuery och tabular objektmetadata olika implementeringar beroende på kompatibilitetsnivån. 

Azure Analysis Services har stöd för tabellmodeller på 1200 eller över 1400 kompatibilitet.

Senaste kompatibilitetsnivå är 1400. Den här nivån sammanfaller med SQL Server 2017 Analysis Services. Viktiga funktioner i kompatibilitetsnivå 1400 omfattar:

*  Ny infrastruktur för dataanslutning och importeras till tabellmodeller med stöd för TOM APIs och TMSL skript. Den här nya funktionen aktiverar stöd för andra datakällor, till exempel Azure Blob storage.
*  Dataomvandling av och data mashup-funktioner med hjälp av hämta Data och M uttryck.
*  Åtgärder som stöd för en detaljrader egenskap med ett DAX-uttryck. Den här egenskapen gör klientverktyg som Microsoft Excel för att öka detaljnivån till detaljerad data från en sammansatt rapport. När användare visar total försäljning för en region och månad, kan de visa associerade orderinformationen. 
*  Säkerhet på objektnivå för tabell- och namn, utöver informationen i dem.
*  Förbättrat stöd för ojämna hierarkier.
*  Prestanda och förbättringar i övervakning.
  
## <a name="set-compatibility-level"></a>Ange kompatibilitetsnivån 
 När du skapar ett nytt tabellmodell-projekt i SSDT, du kan ange kompatibilitetsnivån på den **tabellmodell designer** dialogrutan. 
  
 ![ssas_tabularproject_compat1200](./media/analysis-services-compat-level/aas-tabularproject-compat.png)  
  
 Om du väljer den **Visa inte detta meddelande igen** , alla efterföljande projekt använder kompatibilitetsnivå som du angav som standard. Du kan ändra kompatibilitetsnivån standard i SSDT i **verktyg** > **alternativ**.  
  
 Om du vill uppgradera ett befintligt tabellmodell-projekt i SSDT, ange den **kompatibilitetsnivå** egenskap i modellen **egenskaper** fönster. Håll i åtanke, uppgradera kompatibilitetsnivån går inte att ångra.
  
## <a name="check-compatibility-level-for-a-tabular-model-database-in-sql-server-management-studio"></a>Kontrollera kompatibilitetsnivån för en tabellmodelldatabas i SQL Server Management Studio 
 Högerklicka på namnet på databasen i SSMS, > **egenskaper** > **kompatibilitetsnivå**.  
  
## <a name="check-supported-compatibility-level-for-a-server-in-ssms"></a>Kontrollera stöds kompatibilitetsnivån för en server i SSMS  
 Högerklicka på servernamnet i SSMS, > **egenskaper** > **stöds kompatibilitetsnivå**.  
  
 Den här egenskapen anger högsta kompatibilitetsnivån för en databas som körs på servern (förutom preview). Stöds kompatibilitetsnivån kan inte ändras.  

## <a name="next-steps"></a>Nästa steg
  [Skapa en modell i Azure-portalen](analysis-services-create-model-portal.md)   
  [Hantera Analysis Services](analysis-services-manage.md)  
