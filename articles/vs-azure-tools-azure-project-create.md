---
title: Skapa ett Azure cloud service-projekt med Visual Studio | Microsoft Docs
description: Lär dig att skapa ett Azure cloud service-projekt med Visual Studio
services: visual-studio-online
author: ghogen
manager: douge
assetId: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 3e6ab2078c6b9233360d7cabdc49eddf557b9b57
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189248"
---
# <a name="creating-an-azure-cloud-service-project-with-visual-studio"></a>Skapa ett Azure cloud service-projekt med Visual Studio
Azure Tools för Visual Studio tillhandahåller en projektmall där du kan skapa en [Azure-molntjänst](/azure/cloud-services/cloud-services-choose-me), vilket är en enkel allmänna Azure-tjänst. När projektet har skapats, kan du konfigurera, felsöka och distribuera Molntjänsten till Azure i Visual Studio.

## <a name="steps-to-create-an-azure-cloud-service-project-in-visual-studio"></a>Steg för att skapa ett Azure cloud service-projekt i Visual Studio
Det här avsnittet visar hur du skapar ett Azure cloud service-projekt i Visual Studio med en eller flera web-roller.  

1. Starta Visual Studio som administratör.

1. På huvudmenyn väljer **filen** > **New** > **projekt**.

1. Välj **molnet** från Visual C# eller Visual Basic mall noder, och välj **Azure Cloud Service** i listan med mallar.

    ![Nya Azure-molntjänst](./media/vs-azure-tools-azure-project-create/new-project-wizard-for-cloud-service.png)

1. Ange vilken version av .NET Framework som du vill använda för att utveckla ditt projekt.

1. Ange ett namn och plats för ditt projekt och ett namn för lösningen. 

1. Välj **OK**.

1. I den **nya Microsoft Azure Cloud Service** dialogrutan, Välj de roller som du vill lägga till och klicka på högerpilen för att lägga till dem i din lösning.

    ![Välj Azure cloud service-roller](./media/vs-azure-tools-azure-project-create/new-cloud-service.png)

1. Byt namn på en roll som du har lagt till genom att hovra över rollen i den **nya Microsoft Azure Cloud Service** dialogrutan och välj sedan från snabbmenyn **Byt namn på**. Du kan också byta namn på en roll i din lösning (i den **Solution Explorer**) när den har lagts till.

    ![Byt namn på Azure cloud service-roll](./media/vs-azure-tools-azure-project-create/new-cloud-service-rename.png)

Visual Studio Azure-projekt har kopplingar till rollen-projekt i lösningen. Projektet innehåller också de *tjänstdefinitionsfilen* och *tjänstkonfigurationsfilen*:

- **Tjänstdefinitionsfilen** -definierar körningsinställningar för dina program, inklusive vilka roller är obligatoriska, slutpunkter och VM-storlek. 
- **Tjänstkonfigurationsfilen** -konfigurerar hur många instanser av en roll är körnings- och värdena för inställningarna för en roll. 

Mer information om dessa filer finns i [Konfigurera roller för en Azure-molntjänst med Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

## <a name="next-steps"></a>Nästa steg
- [Hantera roller i Azure cloud service-projekt med Visual Studio](./vs-azure-tools-cloud-service-project-managing-roles.md)
