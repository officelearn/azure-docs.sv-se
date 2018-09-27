---
title: Importera en Power BI Desktop-fil till Azure Analysis Services | Microsoft Docs
description: Beskriver hur du importerar en Power BI Desktop-fil (pbix) med hjälp av Azure-portalen.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a4bb5f59e7aa99a676145ba7a6dbd1883cb860cf
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394341"
---
# <a name="import-a-power-bi-desktop-file"></a>Importera en Power BI Desktop-fil

Du kan importera en datamodell i Power BI Desktop-fil (pbix) till Azure Analysis Services. Modeller metadata, cachelagrade data och anslutningar till datakälla importeras. Rapporter och visualiseringar importeras inte. Importerade data modeller från Power BI Desktop är på kompatibilitetsnivå 1400.

**Begränsningar**   

- Importera från en pbix-fil använder webbdesignerfunktionen i portalen, som är **förhandsversion**. Funktionerna är begränsade. För mer avancerade modellen utveckling och testning är det bäst att använda Visual Studio (SSDT) och SQL Server Management Studio (SSMS).
- Om du får följande felmeddelande när du importerar har pbix-filen aktiverats förhandsversionsfunktioner som ännu inte stöds i Azure Analysis Services.

    ![Kompatibilitetsvarning för servicenivå](./media/analysis-services-import-pbix/aas-import-pbix-cl-warning.png)
- Du måste ha administratörsbehörighet för servern för att importera från en pbix-fil.
- Pbix-modellen kan ansluta till **Azure SQL Database** och **Azure SQL Data Warehouse** datakällor endast.
- Pbix-modellen kan inte ha live eller DirectQuery-anslutningar. 
- Importen misslyckas om datamodellen pbix innehåller metadata som inte stöds i Analysis Services.

## <a name="to-import-from-pbix"></a>Importera från pbix

1. På din server **översikt** > **webbdesignern**, klickar du på **öppna**.

    ![Skapa en modell i Azure-portalen](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. I **webbdesignern** > **modeller**, klickar du på **+ Lägg till**.

    ![Skapa en modell i Azure-portalen](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. I **nya modellen**, Skriv ett namn och välj sedan Power BI Desktop-fil.

    ![Dialogrutan ny modell i Azure-portalen](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. I **Import**letar du upp och välj din fil.

     ![Ansluta dialogrutan i Azure-portalen](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="change-credentials"></a>Ändra autentiseringsuppgifter

När du importerar en datamodell från en pbix-fil som standard konfigureras de autentiseringsuppgifter som används för att ansluta till en datakälla till tjänstkonto. När en modell har importerats från en pbix, kan du ändra autentiseringsuppgifterna med hjälp av följande metoder:

- Använd juli 2018 (version 17.8.1) eller senare version av SSMS för att redigera autentiseringsuppgifter. Det här är det enklaste sättet.
- Använd TMSL [Alter kommandot](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/alter-command-tmsl) på den [datakällor objektet](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/datasources-object-tmsl) att ändra egenskapen för anslutningssträngen. 
- Öppna modellen i Visual Studio, redigera autentiseringsuppgifter för anslutningen för datakällan och sedan distribuera modellen.

Ändra autentiseringsuppgifter med hjälp av SSMS. 

1. I SSMS, expanderar du databasen > **anslutningar**. 
2. Högerklicka på anslutningen till databasen och klicka sedan på **uppdatera autentiseringsuppgifterna**. 

    ![Uppdatera autentiseringsuppgifter](./media/analysis-services-import-pbix/aas-import-pbix-creds.png)

3. Välj en typ av autentiseringsuppgifter i dialogrutan autentiseringsuppgifter och anger autentiseringsuppgifter. Välj databas för SQL-autentisering. Välj Microsoft-konto för organisationskonto (OAuth).
    ![Redigera autentiseringsuppgifter](./media/analysis-services-import-pbix/aas-import-pbix-edit-creds.png)

Juli 2018-versionen av Power BI Desktop innehåller en ny funktion för att ändra behörigheter för datakällan. På den **Start** fliken **redigera frågor**  > **datakällsinställningar**. Välj datasource-anslutning och klicka sedan på **Redigera behörigheter**.


## <a name="see-also"></a>Se också

[Skapa en modell i Azure-portalen](analysis-services-create-model-portal.md)   
[Ansluta till Azure Analysis Services](analysis-services-connect.md)  
