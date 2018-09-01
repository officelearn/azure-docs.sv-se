---
title: Konfigurera och använda Lagringsemulatorn med Visual Studio | Microsoft Docs
description: Konfigurera och använda Lagringsemulatorn med Visual Studio
services: visual-studio-online
author: ghogen
manager: douge
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: 4b14fa44f484735f2a5efee4d631a6f48e6a8bf6
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382644"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Konfigurera och använda Lagringsemulatorn med Visual Studio
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Översikt
Azure SDK-utvecklingsmiljö innehåller lagringsemulatorn, ett verktyg som simulerar Blob, Queue och Table storage tjänster som är tillgängliga i Azure på din lokala utvecklingsdator. Om du är att skapa en molntjänst som använder Azure storage-tjänster eller skrivs alla externa program som anropar lagringstjänsterna, du kan testa din kod lokalt mot storage-emulatorn. Azure Tools för Microsoft Visual Studio kan du integrera hanteringen av lagringsemulatorn i Visual Studio. Azure Tools initiera storage-emulatorn databasen vid första användning, som startar tjänsten storage-emulatorn när du kör eller felsöka från Visual Studio-kod och tillhandahåller skrivskyddad åtkomst till data för storage-emulatorn via Azure Storage Explorer.

Detaljerad information om storage-emulatorn, inklusive systemkrav och anpassade konfigurationsanvisningar finns i [använder Azure Storage-emulatorn för utveckling och testning](storage/common/storage-use-emulator.md).

> [!NOTE]
> Det finns vissa skillnader i funktionalitet mellan storage-emulatorn simulering och Azure storage-tjänster. Se [skillnader mellan Lagringsemulatorn och Azure Storage Services](storage/common/storage-use-emulator.md) i Azure SDK-dokumentationen för information om specifika skillnader.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Konfigurera en anslutningssträng för storage-emulatorn
Om du vill komma åt storage-emulatorn från kod i en roll måste vill du konfigurera en anslutningssträng som pekar på storage-emulatorn och som kan senare ändras för att peka mot ett Azure storage-konto. En anslutningssträng är en konfigurationsinställning som din roll kan läsa vid körning för att ansluta till ett lagringskonto. Mer information om hur du skapar anslutningssträngar finns i [konfigurera Azure Storage-anslutningssträngar](/azure/storage/common/storage-configure-connection-string).

> [!NOTE]
> Du kan returnera en referens till storage-emulatorn-kontot från din kod med hjälp av den **DevelopmentStorageAccount** egenskapen. Den här metoden fungerar korrekt om du vill komma åt storage-emulatorn från din kod, men om du planerar att publicera programmet till Azure måste du skapa en anslutningssträng för att få åtkomst till ditt Azure storage-konto och ändra koden om du vill använda denna anslutning sträng innan du publicerar den. Om du byter mellan storage-emulatorn-konto och ett Azure storage-konto ofta, kommer en anslutningssträng förenklar processen.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>Initiera och kör lagringsemulatorn
Du kan ange att när du kör eller felsöka din tjänst i Visual Studio, Visual Studio öppnas automatiskt av storage-emulatorn. I Solution Explorer, öppna snabbmenyn för din **Azure** programprojektet och väljer **egenskaper**. På den **utveckling** fliken den **starta Azure Storage-emulatorn** väljer **SANT** (om den inte har angetts till detta värde).

Första gången du kör eller felsöka din tjänst från Visual Studio, startar storage-emulatorn en initieringsprocessen. Den här processen reserverar lokala portar för storage-emulatorn och skapar databasen för storage-emulatorn. När du är klar behöver inte den här processen att köra igen såvida inte storage-emulatorn databasen tas bort.

> [!NOTE]
> Från och med juni 2012 av verktyg för Azure körs storage-emulatorn som standard i SQL Express LocalDB. I tidigare versioner av verktyg för Azure storage-emulatorn körs mot en standardinstans av SQL Express 2005 eller 2008 som du måste installera innan du kan installera Azure SDK. Du kan också köra lagringsemulatorn mot en namngiven instans av SQL Express eller en namngiven eller standardinstansen av Microsoft SQL Server. Om du vill konfigurera storage-emulatorn för att köra mot en instans än standardinstansen, se [använder Azure Storage-emulatorn för utveckling och testning](storage/common/storage-use-emulator.md).
> 
> 

Storage-emulatorn tillhandahåller ett användargränssnitt för att visa status för lokal lagring-tjänster och att starta, stoppa och återställa dem. När tjänsten storage-emulatorn har startats kan du visa användargränssnittet eller starta eller stoppa tjänsten genom att högerklicka på ikonen i meddelandefältet för Microsoft Azure-emulatorn i Windows-Aktivitetsfältet.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Visa storage-emulatorn data i Server Explorer
Azure Storage-noden i Server Explorer kan du visa data och ändra inställningarna för blob- och tabelldata i ditt storage-konton, inklusive storage-emulatorn. Se [hantera Azure Blob Storage-resurser med Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) för mer information.

