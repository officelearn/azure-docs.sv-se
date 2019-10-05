---
title: Konfigurera och använda Storage-emulatorn med Visual Studio | Microsoft Docs
description: Konfigurera och använda Storage-emulatorn med Visual Studio
services: visual-studio-online
author: ghogen
manager: jillfra
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: 08e3f5d5bb32d15b9d8d164c898d2b8d7a90108c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969715"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Konfigurera och använda Storage-emulatorn med Visual Studio
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Översikt
Azure SDK utvecklings miljön innehåller Storage-emulatorn, ett verktyg som simulerar de BLOB-, kö-och tabell lagrings tjänster som är tillgängliga i Azure på din lokala utvecklings dator. Om du skapar en moln tjänst som använder Azure Storage-tjänster, eller om du skriver ett externt program som anropar lagrings tjänsterna, kan du testa koden lokalt mot lagringsprovidern. Azure Tools för Microsoft Visual Studio integrerar hantering av lagringsprovidern i Visual Studio. Azure-verktygen initierar Storage emulator-databasen vid första användning, startar tjänsten Storage emulator när du kör eller felsöker din kod från Visual Studio och ger skrivskyddad åtkomst till lagrings-emulatorns data via Azure Storage Explorer.

Mer detaljerad information om Storage-emulatorn, inklusive system krav och anpassade konfigurations anvisningar finns i [använda Azure Storage emulatorn för utveckling och testning](storage/common/storage-use-emulator.md).

> [!NOTE]
> Det finns vissa skillnader i funktionaliteten mellan Storage emulator-simuleringen och Azure Storage-tjänsterna. Se [skillnader mellan Storage-emulatorn och Azure Storage tjänster](storage/common/storage-use-emulator.md) i Azure SDK-dokumentationen för information om de olika skillnaderna.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Konfigurera en anslutnings sträng för Storage-emulatorn
För att få åtkomst till lagringsprovidern från kod i en roll, vill du konfigurera en anslutnings sträng som pekar på lagringsprovidern och som senare kan ändras så att de pekar på ett Azure Storage-konto. En anslutnings sträng är en konfigurations inställning som din roll kan läsa vid körning för att ansluta till ett lagrings konto. Mer information om hur du skapar anslutnings strängar finns i [Konfigurera anslutnings strängar för Azure Storage](/azure/storage/common/storage-configure-connection-string).

> [!NOTE]
> Du kan returnera en referens till kontot för lagrings emulatorn från din kod med hjälp av egenskapen **DevelopmentStorageAccount** . Den här metoden fungerar korrekt om du vill ha åtkomst till lagringsprovidern från din kod, men om du planerar att publicera ditt program till Azure måste du skapa en anslutnings sträng för att få åtkomst till ditt Azure Storage-konto och ändra koden för att använda den anslutningen sträng innan du publicerar den. Om du växlar mellan Storage-emulatorns konto och ett Azure Storage-konto ofta, kommer en anslutnings sträng att förenkla processen.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>Initiera och köra Storage-emulatorn
Du kan ange att om du vill köra eller felsöka tjänsten i Visual Studio startar Visual Studio automatiskt Storage-emulatorn. Öppna snabb menyn för ditt **Azure** -projekt i Solution Explorer och välj **Egenskaper**. På fliken **utveckling** i listan **Starta Azure Storage emulator** väljer du **Sant** (om den inte redan är inställd på det värdet).

Första gången du kör eller felsöker tjänsten från Visual Studio startar Storage-emulatorn en initierings process. Den här processen reserverar lokala portar för Storage-emulatorn och skapar Storage emulator-databasen. När processen är klar behöver den här processen inte köras igen om inte Storage mula-databasen tas bort.

> [!NOTE]
> Från och med juni 2012-versionen av Azure-verktygen körs Storage-emulatorn som standard i SQL Express-LocalDB. I tidigare versioner av Azure-verktygen körs Storage-emulatorn mot en standard instans av SQL Express 2005 eller 2008, som du måste installera innan du kan installera Azure SDK. Du kan också köra Storage-emulatorn mot en namngiven instans av SQL Express eller en namngiven eller standard instans av Microsoft SQL Server. Om du behöver konfigurera Storage-emulatorn så att den körs mot en annan instans än standard instansen kan du läsa [använd Azure Storage emulatorn för utveckling och testning](storage/common/storage-use-emulator.md).
> 
> 

Storage-emulatorn tillhandahåller ett användar gränssnitt för att visa statusen för de lokala lagrings tjänsterna och för att starta, stoppa och återställa dem. När tjänsten Storage emulator har startats kan du Visa användar gränssnittet eller starta eller stoppa tjänsten genom att högerklicka på ikonen i meddelande fältet för Microsoft Azure emulatorn i aktivitets fältet i Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Visa Storage mula data i Server Explorer
Azure Storage-noden i Server Explorer gör att du kan visa data och ändra inställningarna för blob-och tabell data i dina lagrings konton, inklusive Storage-emulatorn. Mer information finns i [Hantera Azure Blob Storage-resurser med Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) .

