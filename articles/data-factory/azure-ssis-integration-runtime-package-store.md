---
title: Hantera paket med Azure-SSIS Integration Runtime-paket arkiv
description: Lär dig hur du hanterar paket med Azure-SSIS Integration Runtime Package Store.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 05/25/2020
ms.openlocfilehash: fa1ab60ae5690bd3782e4c849ca7cb5c29d640c2
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84199058"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Hantera paket med Azure-SSIS Integration Runtime-paket arkiv

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Om du vill lyfta & flytta dina lokala arbets belastningar SQL Server Integration Services (SSIS) till molnet kan du etablera Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF). Mer information finns i [etablera en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure). En Azure-SSIS IR stöder:

- Köra paket som distribuerats i SSIS-katalogen (SSISDB) som hanteras av Azure SQL Database Server/hanterad instans (projekt distributions modell)
- Köra paket som distribuerats i fil systemet, Azure Files eller SQL Server databas (MSDB) som hanteras av Azure SQL-hanterad instans (paket distributions modell)

När du använder paket distributions modell kan du välja om du vill etablera Azure-SSIS IR med paket lager som tillhandahåller ett paket hanterings lager ovanpå fil systemet/Azure Files/MSDB som hanteras av en Azure SQL-hanterad instans. Med Azure-SSIS IR paket Store kan du importera/exportera/ta bort/köra paket och övervaka/stoppa paket som körs via SQL Server Management Studio (SSMS) som liknar det [äldre SSIS-paket lagret](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017). 

## <a name="connect-to-azure-ssis-ir"></a>Anslut till Azure-SSIS IR

När din Azure-SSIS IR har tillhandahållits kan du ansluta till den för att bläddra igenom paket arkiven på SSMS.

![Anslut till Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

Välj **Azure-SSIS integration runtime** på den nedrullningsbara menyn **Anslut** på **Object Explorers** fönstret för SSMS. Logga sedan in på Azure och välj relevant prenumeration, ADF och Azure-SSIS IR som paket arkiven är kopplade till. Din Azure-SSIS IR visas med noderna för att **köra paket** och **lagrade paket** under. Expandera noden **lagrade paket** för att se dina paket butiker under. Expandera paket arkiven för att se mappar och paket under. Du kan uppmanas att ange autentiseringsuppgifter för dina paket lager, om SSMS inte kan ansluta till dem automatiskt. Om du till exempel expanderar ett paket lager ovanpå MSDB kan du först uppmanas att ansluta till din Azure SQL-hanterade instans.

![Ansluta till en Azure SQL-hanterad instans](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Hantera mappar och paket

När du bläddrar Azure-SSIS IR på SSMS kan du högerklicka på alla paket Arkiv/mappar/paket för att popup-menyn och välja **ny mapp**, **Importera paket**, **Exportera paket**, **ta bort**eller **Uppdatera**.

   ![Hantera mappar och paket](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Välj **ny mapp** för att skapa en ny mapp för importerade paket.

   *  Välj **Importera paket** om du vill importera paket från **fil system**, **SQL Server** (msdb) eller det äldre **SSIS-paket arkivet** till ditt paket lager.

      ![Importera paket](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      Beroende på vilken **paket plats** som ska importeras från väljer du den relevanta **serverautentisering** / **Authentication type**, anger autentiseringsuppgifter vid behov, väljer **paket Sök vägen**och anger det nya **paket namnet**. När du importerar paket går det inte att ändra deras skydds nivå. Använd SQL Server Data Tools (SSDT) eller kommando rads verktyg för att ändra det `dtutil` .

   *  Välj **Exportera paket** för att exportera paket från ditt paket lager till **fil system**, **SQL Server** (msdb) eller det äldre **SSIS-paket arkivet**.

      ![Exportera paket](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      Beroende på vilken **paket plats** som exporten ska exporteras till väljer du **önskad** / **typ av serverautentisering**, anger autentiseringsuppgifter vid behov och väljer **paket Sök vägen**. När du exporterar paket, om de är krypterade, anger du lösen orden för att dekryptera dem först och sedan kan du ändra skydds nivån, till exempel inte spara känsliga data eller kryptera alla/känsliga data med användar nyckel/lösen ord.

   *  Välj **ta bort** för att ta bort befintliga mappar/paket från ditt paket lager.

   *  Välj **Uppdatera** för att visa nyligen tillagda mappar/paket i ditt paket lager.

## <a name="execute-packages"></a>Köra paket

När du bläddrar Azure-SSIS IR på SSMS kan du högerklicka på alla lagrade paket för att öppna en meny och välja **Kör paket**.  Då öppnas dialog rutan **verktyg för körning av paket** där du kan konfigurera dina paket körningar på Azure-SSIS IR som kör SSIS paket aktiviteter i ADF-pipeline.

![Verktyg för körning av paket sidor 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Verktyg för körning av paket Pages 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

Sidan **Allmänt**, **konfigurationer**, **körnings alternativ**och **loggnings** sidor i **verktyg för körning av paket** dialog rutan motsvarar fliken **Inställningar** för aktiviteten kör SSIS-paket där du kan ange krypterings lösen ordet för ditt paket, åtkomst information för paketets konfigurations fil, autentiseringsuppgifterna för paket körningen/egenskaper och åtkomst information för loggmappen.  Sidan **Ange värden** i **verktyg för körning av paket** dialog rutan motsvarar **egenskapen åsidosättningar** för aktiviteten kör SSIS-paket där du kan ange dina befintliga paket egenskaper för åsidosättning. Mer information finns i [köra SSIS-paket som kör SSIS-paket aktiviteter i ADF-pipeline](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

När du väljer knappen **Kör** i **verktyg för körning av paket** dialog rutan skapas och aktive ras en ny ADF-pipeline med kör SSIS-paket-aktivitet automatiskt. Om det redan finns en ADF-pipeline med samma paket körnings inställningar körs den igen och en ny pipeline skapas inte. Aktiviteten ADF pipeline och kör SSIS-paket får namnet respektive `Pipeline_SSMS_YourPackageName_HashString` `Activity_SSMS_YourPackageName` .

![verktyg för körning av paket knapp](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![Kör SSIS-paket-aktivitet](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>Övervaka och stoppa paket som körs

När du bläddrar i Azure-SSIS IR på SSMS kan du expandera noden **Kör paket** för att se de paket som körs för tillfället.  Högerklicka på någon av dem för att popup-menyn och välj **stoppa** eller **Uppdatera**.

   ![Övervaka och stoppa paket som körs](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  Välj **Avbryt om du vill** avbryta den pågående ADF-pipeline som kör paketet som EXECUTe SSIS-paket-aktivitet.

   *  Välj **Uppdatera** för att visa nyligen använda paket från dina paket lager.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Övervaka Azure-SSIS IR och redigera paket arkiv

När du bläddrar Azure-SSIS IR på SSMS kan du högerklicka på den för att öppna en meny och välja **gå till Azure Data Factory Portal** eller **Uppdatera**.

   ![Gå till ADF-portalen](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Välj **gå till Azure Data Factory Portal** för att öppna sidan **integrerings körningar** i ADF Monitoring Hub, där du kan övervaka Azure-SSIS IR. På panelen **paket arkiv** kan du se hur många paket lager som är kopplade till din Azure-SSIS IR.  Om du väljer det numret visas ett fönster där du kan redigera ADF-länkade tjänster som lagrar åtkomst informationen för dina paket lager.

      ![Redigera paket arkiv](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Välj **Uppdatera** om du vill visa nyligen tillagda mappar/paket i dina paket lager och köra paket från dina paket lager.

## <a name="next-steps"></a>Nästa steg

Du kan köra/redigera de automatiskt genererade ADF-pipelinen med kör SSIS-paket aktiviteter eller skapa nya på ADF-portalen. Mer information finns i [köra SSIS-paket som kör SSIS-paket aktiviteter i ADF-pipeline](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).