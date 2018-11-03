---
title: Konfigurera ett Azure cloud service-projekt med Visual Studio | Microsoft Docs
description: Lär dig hur du konfigurerar ett Azure cloud service-projekt i Visual Studio, beroende på dina krav för projektet.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 609d6965-05cc-47b1-82dc-c76a92d4f295
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/06/2017
ms.author: ghogen
ms.openlocfilehash: f119f4c758fcc7d89e5790ba9e3a223dc3a726c5
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969246"
---
# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>Konfigurera ett Azure cloud service-projekt med Visual Studio
Du kan konfigurera ett Azure cloud service-projekt, beroende på dina krav för projektet. Du kan ange egenskaper för projektet i följande kategorier:

- **Publicera en molnbaserad tjänst på Azure** – du kan ange en egenskap för att se till att en befintlig molntjänst som distribuerats till Azure inte raderas av misstag.
- **Kör eller felsöka en molntjänst på den lokala datorn** – du kan välja en tjänstkonfiguration att använda och ange om du vill starta Azure storage-emulatorn.
- **Verifiera ett molntjänstpaket när den har skapats** – du kan välja att behandla alla varningar som fel så att du kan se till att molntjänstpaketet distribuerar utan problem. 

## <a name="steps-to-configure-an-azure-cloud-service-project"></a>Steg för att konfigurera ett Azure cloud service-projekt
1. Öppna eller skapa en cloud service-projekt i Visual Studio

1. I **Solution Explorer**, högerklicka på projektet och, från snabbmenyn väljer **egenskaper**.
   
1. Egenskapssidan för projektet, Välj den **utveckling** fliken.

    ![Projekt egenskaper-menyn](./media/vs-azure-tools-configuring-an-azure-project/solution-explorer-project-properties-menu.png)

1. Ange **fråga innan du tar bort en befintlig distribution** till **SANT**. Den här inställningen hjälper till att se till att du inte råkar ta bort en befintlig distribution i Azure

1. Välj önskat **tjänstkonfiguration** för att ange vilken tjänstkonfiguration som du vill använda när du kör eller felsöka din molntjänst lokalt. Läs mer om hur du ändrar en tjänstkonfiguration för en roll, [hur du konfigurerar roller för en Azure-molntjänst med Visual Studio](./vs-azure-tools-configure-roles-for-cloud-service.md).

1. Ange **starta Azure storage-emulatorn** till **SANT** att starta Azure storage-emulatorn när du kör eller felsöka din molntjänst lokalt.

1. Ange **behandla varningar som fel** till **SANT** att kontrollera att du inte kan publicera om det finns verifieringsfel i paketet.

1. Ange **använda projekt webbportar** till **SANT** för att se till att din webbroll använder samma port varje gång det startar lokalt i IIS Express.

1. Välj Visual Studio-verktygsfältet **spara**.

## <a name="next-steps"></a>Nästa steg
- [Konfigurera ett Azure-projekt med hjälp av flera tjänstkonfiguration](vs-azure-tools-multiple-services-project-configurations.md)

