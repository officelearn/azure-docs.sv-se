---
title: Uppdatera Visual Studios skript för mall distribution för att använda AZ PowerShell
description: Uppdatera Visual Studio Template Deployment-skriptet från AzureRM till AZ PowerShell
author: cweining
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: cweining
ms.openlocfilehash: c34cde5593b48c301826be3dd2641dc2490ee88d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149061"
---
# <a name="updating-the-visual-studio-template-deployment-script-to-use-az-powershell"></a>Uppdatera Visual Studio Template Deployment-skriptet för att använda AZ PowerShell

Visual Studio 16,4 stöder användning av AZ PowerShell i skriptet för mall-distribution. Visual Studio installerar inte AZ PowerShell eller uppdaterar inte distributions skriptet i resurs grupps projektet automatiskt. För att kunna använda den nyare AZ PowerShell måste du göra följande fyra saker:
1. Avinstallera AzureRM PowerShell
1. Installera AZ PowerShell
1. Uppdatera till Visual Studio 16,4
1. Uppdatera distributions skriptet i projektet.

## <a name="uninstall-azurerm-powershell"></a>Avinstallera AzureRM PowerShell
Följ dessa [anvisningar](https://docs.microsoft.com/powershell/azure/uninstall-az-ps?view=azps-2.7.0#uninstall-the-azurerm-module) för att avinstallera AzureRM PowerShell.

## <a name="install-az-powershell"></a>Installera AZ PowerShell
Följ de här [anvisningarna](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.7.0) för att installera AZ PowerShell.

## <a name="update-visual-studio-to-164"></a>Uppdatera Visual Studio till 16,4
Uppdatera till Visual Studio 16,4 när det är tillgängligt. Kontrol lera att Azure PowerShell-komponenten inte är markerad under uppgraderingen. Eftersom du har installerat AZ PowerShell via galleriet vill du inte att den AzureRM-version av PowerShell som installeras med Visual Studio.

Om du redan har uppgraderat till 16,4 och Azure PowerShell-komponenten har marker ATS kan du avinstallera den genom att köra Visual Studio Installer och avmarkera Azure PowerShell-komponenten i Azure-arbetsbelastningen eller på sidan enskilda komponenter.

## <a name="update-the-deployment-script-in-your-project"></a>Uppdatera distributions skriptet i projektet
Ersätt alla förekomster av strängen "AzureRm" med "AZ" i distributions skriptet. Du kan referera till revisioner i det här [registret](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) för att se ändringarna. Mer information om hur du uppgraderar skript till AZ PowerShell hittar du i den här [dokumentationen](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-2.5.0) .


