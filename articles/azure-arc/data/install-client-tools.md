---
title: Installera klientverktyg
description: Installera azdata, kubectl, Azure CLI, psql, Azure Data Studio (Insiders) och Arc-tillägget för Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d61bc99e851b28712262dba8512c06b6e8872c0e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108226"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Installera klientverktyg för att distribuera och hantera Azure Arc-aktiverade datatjänster

> [!IMPORTANT]
> Om du uppdaterar till en ny månads utgåva måste du också uppdatera till den senaste versionen av Azure Data Studio, verktyget Azure Data CLI (azdata) och Azure Data CLI och Azure Arc-tilläggen för Azure Data Studio.

Det här dokumentet vägleder dig genom stegen för att installera Azure Data CLI (azdata), Azure Data Studio, Azure CLI (AZ) och Kubernetes CLI-verktyget (kubectl) på klient datorn.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Verktyg för att skapa och hantera Azure Arc-aktiverade data tjänster 

I följande tabell visas vanliga verktyg som krävs för att skapa och hantera Azure Arc-aktiverade data tjänster och hur du installerar verktygen:

| Verktyg | Krävs | Beskrivning | Installation |
|---|---|---|---|
| Azure Data CLI (azdata) | Ja | Kommando rads verktyg för att installera och hantera ett stort data kluster. Azure Data CLI innehåller också ett kommando rads verktyg för att ansluta till och fråga Azure SQL och SQL Server instanser och postgres-servrar med hjälp av kommandona `azdata sql query` (köra en enda fråga från kommando raden), `azdata sql shell` (ett interaktivt gränssnitt) `azdata postgres query` och `azdata postgres shell` . | [Installera](/sql/azdata/install/deploy-install-azdata?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json) |
| Azure Data Studio | Ja | Omfattande upplevelse verktyg för att ansluta till och fråga en rad olika databaser, inklusive Azure SQL, SQL Server, PostrgreSQL och MySQL. Tillägg till Azure Data Studio ger en administrations upplevelse för Azure Arc-aktiverade data tjänster. | [Installera](/sql/azure-data-studio/download-azure-data-studio) |
| Azure Data CLI-tillägg för Azure Data Studio | Ja | Tillägg för Azure Data Studio som ska installera Azure Data CLI om du inte redan har det.| Installera från tilläggs galleriet i Azure Data Studio.|
| Azure Arc-tillägg för Azure Data Studio | Ja | Tillägg för Azure Data Studio som ger en hanterings upplevelse för Azure Arc-aktiverade data tjänster. Det finns ett beroende av Azure Data CLI-tillägget för Azure Data Studio. | Installera från tilläggs galleriet i Azure Data Studio.|
| PostgreSQL-tillägg i Azure Data Studio | Nej | PostgreSQL-tillägg för Azure Data Studio som tillhandahåller hanterings funktioner för PostgreSQL. | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Installera från tilläggs galleriet i Azure Data Studio.|
| Azure CLI (AZ)<sup>1</sup> | Ja | Modernt kommando rads gränssnitt för att hantera Azure-tjänster. Används med AKS-distributioner och för att överföra Azure Arc-aktiverade data Services-inventering och fakturerings data till Azure. ([Mer information](/cli/azure/?view=azure-cli-latest&preserve-view=true)). | [Installera](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) |
| Kubernetes CLI (kubectl)<sup>2</sup> | Ja | Kommando rads verktyg för att hantera Kubernetes-klustret ([Mer information](https://kubernetes.io/docs/tasks/tools/install-kubectl/)). | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| Sväng <sup>3</sup> | Krävs för vissa exempel skript. | Kommando rads verktyg för att överföra data med URL: er. | [Windows](https://curl.haxx.se/windows/) \| Linux: installera ett sväng paket |
| oc | Krävs för redhat-distributioner med Red Hat OpenShift och Azure. |`oc` är kommando rads gränssnittet för öppen Skift (CLI). | [Installera CLI](https://docs.openshift.com/container-platform/4.4/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> du måste använda Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen om det behövs.

<sup>2</sup> du måste använda `kubectl` version 1,13 eller senare. Dessutom bör versionen av `kubectl` vara plus eller minus en lägre version av Kubernetes-klustret. Om du vill installera en enskild version på `kubectl` klienten, se [installera `kubectl` Binary via sväng](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl) (på Windows 10, Använd cmd.exe och inte Windows PowerShell för att köra sväng).

<sup>3</sup> om du använder PowerShell, är svängen ett alias till Invoke-WebRequest-cmdleten.

## <a name="next-steps"></a>Nästa steg

[Skapa data styrenheten för Azure-bågen](create-data-controller.md)