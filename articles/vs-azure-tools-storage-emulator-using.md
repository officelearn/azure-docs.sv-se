---
title: Konfigurera och använda Lagringsemulatorn med Visual Studio | Microsoft Docs
description: Konfigurera och använda Lagringsemulatorn med Visual Studio
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: c8e7996f-6027-4762-806e-614b93131867
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: ecf9df0d94f34df69750a8baa7f5502764cf9b06
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Konfigurera och använda Lagringsemulatorn med Visual Studio
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Översikt
Azure SDK-utvecklingsmiljö innehåller storage-emulatorn, ett verktyg som simulerar Blob, köer och tabellen lagringstjänster som är tillgängliga i Azure på utvecklingsdatorn lokala. Om du skapar en molnbaserad tjänst som använder Azure-lagringstjänster eller skriva alla externa program som anropar storage-tjänster, du kan testa din kod lokalt mot storage-emulatorn. Azure-verktyg för Microsoft Visual Studio integrera hanteringen av lagringsemulatorn i Visual Studio. Azure-verktyg initiera storage-emulatorn databasen vid första användning, startar tjänsten storage-emulatorn när du kör eller felsöka din kod från Visual Studio och tillhandahåller skrivskyddad åtkomst till storage-emulatorn data via Azure Lagringsutforskaren.

Detaljerad information om storage-emulatorn, inklusive systemkrav och anpassade konfigurationsanvisningar finns [Använd Azure Storage-emulatorn för utveckling och testning](storage/common/storage-use-emulator.md).

> [!NOTE]
> Det finns vissa skillnader i funktionalitet mellan storage-emulatorn simuleringen och Azure storage-tjänster. Se [skillnader mellan Lagringsemulatorn och Azure Storage-tjänster](storage/common/storage-use-emulator.md) i Azure SDK-dokumentationen för mer information om specifika skillnader.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Konfigurera en anslutningssträng för storage-emulatorn
För att komma åt lagringsemulatorn från kod inom en roll, kommer du vill konfigurera en anslutningssträng som pekar på storage-emulatorn och som kan senare ändras för att peka till en Azure storage-konto. En anslutningssträng är en konfigurationsinställning som din roll kan läsa vid körning kan ansluta till ett lagringskonto. Mer information om hur du skapar anslutningssträngar finns [konfigurera Azure-program](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

> [!NOTE]
> Du kan returnera en referens till storage-emulatorn konto från din kod med hjälp av den **DevelopmentStorageAccount** egenskapen. Den här metoden fungerar om du vill använda lagringsemulatorn från din kod, men om du planerar att publicera programmet till Azure, behöver du skapa en anslutningssträng för att komma åt ditt Azure storage-konto och ändra din kod för att använda den anslutningssträngen innan du publicerar den. Om du byter mellan storage-emulatorn-konto och ett Azure storage-konto ofta, förenklar en anslutningssträng processen.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>Initiera och kör storage-emulatorn
Du kan ange att när du kör eller felsöka din tjänst i Visual Studio, Visual Studio startas automatiskt storage-emulatorn. I Solution Explorer, öppna snabbmenyn för din **Azure** projektet och välj **egenskaper**. På den **Development** fliken den **starta Azure Storage-emulatorn** Välj **SANT** (om den inte har angetts till värdet).

Första gången du kör eller felsöka din tjänst från Visual Studio startar storage-emulatorn en initieringsprocessen. Den här processen reserverar lokala portar för storage-emulatorn och storage-emulatorn databas skapas. När du är klar, behöver den här processen inte köras igen om storage-emulatorn databasen tas bort.

> [!NOTE]
> Från och med juni 2012 verktyg för Azure körs storage-emulatorn som standard i SQL Express LocalDB. I tidigare versioner av verktyg för Azure storage-emulatorn körs mot en standardinstans av SQL Express 2005 eller 2008, som du måste installera innan du kan installera Azure SDK. Du kan också köra storage-emulatorn mot en namngiven instans av SQL Express eller en namngiven eller standardinstansen av Microsoft SQL Server. Om du behöver konfigurera storage-emulatorn att köra mot en instans än standardinstansen finns [Använd Azure Storage-emulatorn för utveckling och testning](storage/common/storage-use-emulator.md).
> 
> 

Storage-emulatorn ger ett användargränssnitt för att visa status för lokal lagring-tjänster och för att starta, stoppa och återställa dem. När tjänsten storage-emulatorn har startats, kan du visa användargränssnittet eller starta eller stoppa tjänsten genom att högerklicka på ikonen i meddelandefältet för Microsoft Azure-emulatorn i Aktivitetsfältet.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Visa storage-emulatorn data i Server Explorer
Azure Storage-nod i Server Explorer kan du visa data och ändra inställningar för blob- och tabellen i storage-konton, inklusive storage-emulatorn. Se [hantera Azure Blob Storage-resurser med Lagringsutforskaren (förhandsversion)](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) för mer information.

