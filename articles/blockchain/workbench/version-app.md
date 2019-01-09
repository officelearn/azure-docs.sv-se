---
title: Versionshantering av Blockchain-program i Azure Blockchain Workbench
description: Hur du använder programversioner i Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 1/8/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: c4c5dcf9c83b79158e3459e79db6dd066d982fc8
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54108260"
---
# <a name="azure-blockchain-workbench-application-versioning"></a>Versionshantering av Azure Blockchain Workbench-programmet

Du kan skapa och använda flera versioner av en app med Azure Blockchain Workbench. Om flera versioner av samma program laddas en versionshistorik är tillgänglig och användarna kan välja vilken version som de vill använda.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* En Blockchain Workbench-distribution. Mer information finns i [Azure Blockchain Workbench distribution](deploy.md) mer information om distribution
* En distribuerad blockchain-program i Blockchain Workbench. Se [skapa ett blockchain-program i Azure Blockchain Workbench](create-app.md)

## <a name="add-an-app-version"></a>Lägg till en appversion

Om du vill lägga till en ny version, överför du den nya konfigurationen och smarta kontraktsfiler till Blockchain Workbench.

1. I en webbläsare, navigerar du till webbadressen Blockchain Workbench. Till exempel `https://{workbench URL}.azurewebsites.net/` information om hur du hittar webbadressen för Blockchain Workbench finns i [Blockchain Workbench-Webbadress](deploy.md#blockchain-workbench-web-url)
2. Logga in som en [Blockchain Workbench administratör](manage-users.md#manage-blockchain-workbench-administrators).
3. Välj ett blockchain-program som du vill uppdatera med en annan version.
4. Välj **Lägg till version**. Den **Lägg till version** egenskapsruta visas.
5. Välj den nya version kontrakt konfigurationen och koda filer att ladda upp ett kontrakt. Konfigurationsfilen valideras automatiskt. Åtgärda eventuella verifieringsfel innan du distribuerar programmet.
6. Välj **Lägg till version** att lägga till den nya blockchain-programversionen.

    ![Lägga till en ny version](media/version-app/add-version.png)

Distribution av blockchain-program kan ta några minuter. När distributionen är klar kan du uppdatera appen på sidan. Välja program och välja den **versionshistorik** knapp, visar tidigare versioner av programmet.

> [!IMPORTANT]
> Tidigare versioner av programmet har inaktiverats. Du kan individuellt återaktivera tidigare versioner.
>
> Du kan behöva uppdatera lägga till medlemmar i programroller om ändringar har gjorts för programroller i den nya versionen.

## <a name="using-app-versions"></a>Med hjälp av appversioner

Som standard används den senaste aktiverad versionen av programmet i Blockchain Workbench. Om du vill använda en tidigare version av ett program måste du välja versionen från programsidan först.

1. Markera kryssrutan program som innehåller det kontrakt som du vill använda i Blockchain Workbench program i. Om tidigare versioner aktiveras är knappen tidigare version tillgänglig.
2. Välj den **versionshistorik** knappen.
3. I rutan version tidigare väljer du versionen av programmet genom att välja länken i den *ändrad* kolumn.

    ![Välj en tidigare version](media/version-app/use-version.png)

    Du kan skapa nya kontrakt eller utföra åtgärder på den föregående versionen kontrakt. Versionen av programmet ska visas efter namnet på programmet och en varning visas om den äldre versionen.

## <a name="next-steps"></a>Nästa steg

* [Azure Blockchain Workbench felsökning](troubleshooting.md)
