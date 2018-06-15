---
title: Skapa ett Azure cloud service-projekt i Visual Studio | Microsoft Docs
description: Lär dig att skapa ett Azure cloud service-projekt i Visual Studio
services: visual-studio-online
author: ghogen
manager: douge
assetId: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 06213ecabf3669bf3b8cf2b8d73a4e8def359536
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31791570"
---
# <a name="creating-an-azure-cloud-service-project-with-visual-studio"></a>Skapa ett Azure cloud service-projekt i Visual Studio
Azure-verktyg för Visual Studio tillhandahåller en projektmall som låter dig skapa ett Azure-molntjänst. När projektet har skapats kan du konfigurera, felsöka och distribuera Molntjänsten till Azure i Visual Studio.

## <a name="steps-to-create-an-azure-cloud-service-project-in-visual-studio"></a>Steg för att skapa ett Azure cloud service-projekt i Visual Studio
Det här avsnittet vägleder dig genom att skapa ett Azure cloud service-projekt i Visual Studio med en eller flera webbroller.  

1. Starta Visual Studio som administratör.

1. På huvudmenyn, Välj **filen** > **ny** > **projekt**.

1. Välj **moln** projektet mallen noder från Visual C# eller Visual Basic och välj **Azure Cloud Service** från listan över mallar.

    ![Nya Azure-molntjänst](./media/vs-azure-tools-azure-project-create/new-project-wizard-for-cloud-service.png)

1. Ange vilken version av .NET Framework som du vill använda för att utveckla ditt projekt.

1. Ange ett namn och plats för ditt projekt och ett namn för lösningen. 

1. Välj **OK**.

1. I den **nya Microsoft Azure Cloud Service** dialogrutan Välj roller som du vill lägga till och klicka på högerpilen för att lägga till dem i din lösning.

    ![Välj Azure cloud service-roller](./media/vs-azure-tools-azure-project-create/new-cloud-service.png)

1. Byt namn på en roll som du har lagt till genom hovra över rollen i den **nya Microsoft Azure Cloud Service** dialogrutan, och på snabbmenyn Välj **Byt namn på**. Du kan också byta namn på en roll i din lösning (i det **Solution Explorer**) när den har lagts till.

    ![Byt namn på Azure-molnet rolltjänst](./media/vs-azure-tools-azure-project-create/new-cloud-service-rename.png)

Azure för Visual Studio-projekt har kopplingar till rollen projekt i lösningen. Projektet innehåller också den *tjänstdefinitionsfilen* och *tjänstkonfigurationsfilen*:

- **Tjänstdefinitionsfilen** -definierar körningsinställningar för programmet, inklusive vilka roller är obligatoriska, slutpunkter och storlek på virtuell dator. 
- **Tjänstkonfigurationsfilen** -konfigurerar hur många instanser av en roll är körningen och värdena för inställningarna för en roll. 

Mer information om dessa filer finns [Konfigurera roller för en Azure-molntjänst med Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

## <a name="next-steps"></a>Nästa steg
- [Hantera roller i Azure cloud service-projekt i Visual Studio](./vs-azure-tools-cloud-service-project-managing-roles.md)
