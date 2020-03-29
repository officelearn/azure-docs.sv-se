---
title: Uppdatera Visual Studios malldistributionsskript för att använda Az PowerShell
description: Uppdatera distributionsskriptet för Visual Studio-mallen från AzureRM till Az PowerShell
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963877"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>Uppdatera distributionsskript för Visual Studio-mall för att använda Az PowerShell-modul

Visual Studio 16.4 stöder användning av Az PowerShell-modulen i malldistributionsskriptet. Visual Studio installerar dock inte modulen automatiskt. Om du vill använda Az-modulen måste du ta fyra steg:

1. [Avinstallera AzureRM-modul](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Installera Az-modul](/powershell/azure/install-az-ps)
1. Uppdatera Visual Studio till 16,4
1. Uppdatera distributionsskriptet i projektet.

## <a name="update-visual-studio-to-164"></a>Uppdatera Visual Studio till 16,4

Uppdatera installationen av Visual Studio till version 16.4 eller senare. Kontrollera att Azure PowerShell-komponenten inte är markerad under uppgraderingen. Eftersom du har installerat Az-modulen via galleriet vill du inte installera om AzureRM-modulen.

Om du redan har uppgraderat till 16.4 och Azure PowerShell-komponenten har kontrollerats kan du avinstallera den genom att köra Visual Studio Installer. Välj inte Azure PowerShell-komponenten i Azure-arbetsbelastningen eller på sidan enskilda komponenter.

## <a name="update-the-deployment-script-in-your-project"></a>Uppdatera distributionsskriptet i projektet

Ersätt alla förekomster av strängen "AzureRm" med Az i distributionsskriptet. Du kan referera till ändringar i den här [kontentan](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) för att se ändringarna. Mer information om hur du uppgraderar skript till Az-modulen finns i [Migrera Azure PowerShell från AzureRM till Az](/powershell/azure/migrate-from-azurerm-to-az).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Visual Studio-projektet finns i [Skapa och distribuera Azure-resursgruppsprojekt via Visual Studio](create-visual-studio-deployment-project.md).
