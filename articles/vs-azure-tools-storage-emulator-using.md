---
title: Konfigurera och använda lagringsemulatorn med Visual Studio | Microsoft-dokument
description: Konfigurera och använda lagringsemulatorn, ett verktyg som simulerar blob-, kö- och tabelllagringstjänster som är tillgängliga i Azure på din lokala utvecklingsdator.
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
ms.openlocfilehash: a6f853924416cce2440ca15767044029b20e651f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75450732"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Konfigurera och använda lagringsemulatorn med Visual Studio

[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Översikt

Azure SDK-utvecklingsmiljön innehåller lagringsemulatorn, ett verktyg som simulerar Blob-, kö- och Tabelllagringstjänster som är tillgängliga i Azure på din lokala utvecklingsdator. Om du skapar en molntjänst som använder Azure-lagringstjänsterna eller skriver externa program som anropar lagringstjänsterna, kan du testa koden lokalt mot lagringsemulatorn. Azure Tools för Microsoft Visual Studio integrerar hanteringen av lagringsemulatorn i Visual Studio. Azure Tools initierar lagringsemulatordatabasen vid första användningen, startar lagringsemulatortjänsten när du kör eller felsöker koden från Visual Studio och ger skrivskyddad åtkomst till lagringsemulatordata via Azure Storage Explorer.

Detaljerad information om lagringsemulatorn, inklusive systemkrav och anpassade konfigurationsinstruktioner, finns i [Använda Azure Storage Emulator för utveckling och testning](storage/common/storage-use-emulator.md).

> [!NOTE]
> Det finns vissa skillnader i funktionalitet mellan lagringsemulatorsimuleringen och Azure-lagringstjänsterna. Se [Skillnader mellan lagringsemulatorn och Azure Storage Services](storage/common/storage-use-emulator.md) i Azure SDK-dokumentationen för information om specifika skillnader.

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Konfigurera en anslutningssträng för lagringsemulatorn

Om du vill komma åt lagringsemulatorn från koden i en roll vill du konfigurera en anslutningssträng som pekar på lagringsemulatorn och som senare kan ändras så att det pekar på ett Azure-lagringskonto. En anslutningssträng är en konfigurationsinställning som din roll kan läsa vid körning för att ansluta till ett lagringskonto. Mer information om hur du skapar anslutningssträngar finns i [Konfigurera Azure Storage-anslutningssträngar](/azure/storage/common/storage-configure-connection-string).

> [!NOTE]
> Du kan returnera en referens till lagringsemulatorkontot från din kod med hjälp av egenskapen **DevelopmentStorageAccount.** Den här metoden fungerar korrekt om du vill komma åt lagringsemulatorn från din kod, men om du planerar att publicera ditt program till Azure måste du skapa en anslutningssträng för att komma åt ditt Azure-lagringskonto och ändra koden för att använda den anslutningen innan du publicerar den. Om du växlar mellan lagringsemulatorkontot och ett Azure-lagringskonto ofta, förenklar en anslutningssträng den här processen.

## <a name="initializing-and-running-the-storage-emulator"></a>Initiera och köra lagringsemulatorn

Du kan ange att när du kör eller felsöker tjänsten i Visual Studio startar Visual Studio lagringsemulatorn automatiskt. Öppna snabbmenyn för Ditt **Azure-projekt** i Solution Explorer och välj **Egenskaper**. På fliken **Utveckling** i listan **Starta Azure Storage Emulator** väljer du **Sant** (om det inte redan är inställt på det värdet).  Vissa projekttyper har inte fliken **Utveckling.** Om så är fallet kan du aktivera eller inaktivera lagringsemulatorns start genom att ange elementet `StartDevelopmentStorage` i projektfilen. Ställ in den på **True** för att aktivera den, eller **Falskt** för att inaktivera den.  I ett Azure Functions-projekt öppnar du till exempel projektfilen för redigering och ändring av XML-koden på följande sätt:

```xml
  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
    <StartDevelopmentStorage>True</StartDevelopmentStorage>
  </PropertyGroup>
```

Första gången du kör eller felsöker tjänsten från Visual Studio startar lagringsemulatorn en initieringsprocess. Den här processen reserverar lokala portar för lagringsemulatorn och skapar lagringsemulatordatabasen. När den här processen är klar behöver den inte köras igen om inte lagringsemulatorns databas tas bort.

> [!NOTE]
> Från och med versionen av Azure Tools i juni 2012 körs lagringsemulatorn som standard i SQL Express LocalDB. I tidigare versioner av Azure Tools körs lagringsemulatorn mot en standardinstans av SQL Express 2005 eller 2008, som du måste installera innan du kan installera Azure SDK. Du kan också köra lagringsemulatorn mot en namngiven instans av SQL Express eller en namngiven eller standardinstans av Microsoft SQL Server. Om du behöver konfigurera lagringsemulatorn så att den körs mot en annan instans än standardinstansen läser [du Använda Azure Storage Emulator för utveckling och testning](storage/common/storage-use-emulator.md).

Lagringsemulatorn tillhandahåller ett användargränssnitt för att visa status för de lokala lagringstjänsterna och starta, stoppa och återställa dem. När lagringsemulatorn har startats kan du visa användargränssnittet eller starta eller stoppa tjänsten genom att högerklicka på meddelandefältets ikon för Microsoft Azure Emulator i Aktivitetsfältet i Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Visa lagringsemulatordata i Server Explorer

Med Azure Storage-noden i Server Explorer kan du visa data och ändra inställningar för blob- och tabelldata i dina lagringskonton, inklusive lagringsemulatorn. Mer information [finns i Hantera Azure Blob Storage-resurser med Storage Explorer.](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)

