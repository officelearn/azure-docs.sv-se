---
title: Version av Blockchain-appen – Azure Blockchain Workbench
description: Så här använder du programversioner i förhandsversionen av Azure Blockchain Workbench.
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 45219790cf0cd064e0fcd456e262b2f93aa03ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74323916"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Förhandsversion av Azure Blockchain Workbench-program

Du kan skapa och använda flera versioner av en Azure Blockchain Workbench Preview-app. Om flera versioner av samma program överförs är en versionshistorik tillgänglig och användarna kan välja vilken version de vill använda.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

* En Blockchain Workbench-distribution. Mer information finns i [Azure Blockchain Workbench-distributionen](deploy.md) för mer information om distribution
* Ett distribuerat blockkedjeprogram i Blockchain Workbench. Mer information finns i avsnittet om hur du [skapar ett blockkedjeprogram i Azure Blockchain Workbench](create-app.md)

## <a name="add-an-app-version"></a>Lägga till en appversion

Om du vill lägga till en ny version laddar du upp de nya konfigurations- och smarta kontraktsfilerna till Blockchain Workbench.

1. I en webbläsare går du till webbadressen för Blockchain Workbench. `https://{workbench URL}.azurewebsites.net/` Mer information om hur du hittar din blockchain Workbench-webbadress finns till exempel i Webbadressen till Blockchain [Workbench](deploy.md#blockchain-workbench-web-url)
2. Logga in som [Blockchain Workbench-administratör](manage-users.md#manage-blockchain-workbench-administrators).
3. Välj det blockchain-program som du vill uppdatera med en annan version.
4. Välj **Lägg till version**. Fönstret **Lägg till version** visas.
5. Välj den nya version kontrakt konfiguration och kontrakt kodfiler att ladda upp. Konfigurationsfilen verifieras automatiskt. Åtgärda eventuella valideringsfel innan du distribuerar programmet.
6. Välj **Lägg till version** om du vill lägga till den nya blockchain-programversionen.

    ![Lägga till en ny version](media/version-app/add-version.png)

Distribution av blockchain-programmet kan ta några minuter. När distributionen är klar uppdaterar du programsidan. Om du väljer programmet och väljer knappen **Versionshistorik** visas programmets versionshistorik.

> [!IMPORTANT]
> Tidigare versioner av programmet är inaktiverade. Du kan aktivera tidigare versioner individuellt.
>
> Du kan behöva lägga till medlemmar i programrollerna igen om ändringar har gjorts i programrollerna i den nya versionen.

## <a name="using-app-versions"></a>Använda appversioner

Som standard används den senast aktiverade versionen av programmet i Blockchain Workbench. Om du vill använda en tidigare version av ett program måste du välja den version från programsidan först.

1. I programavsnittet Blockchain Workbench markerar du programrutan som innehåller det kontrakt du vill använda. Om tidigare versioner är aktiverade är knappen versionshistorik tillgänglig.
2. Välj knappen **Versionshistorik.**
3. I versionshistorikfönstret väljer du versionen av programmet genom att välja länken i kolumnen *Datummodifierad.*

    ![Välj en tidigare version](media/version-app/use-version.png)

    Du kan skapa nya kontrakt eller vidta åtgärder för tidigare versionskontrakt. Versionen av programmet visas efter programnamnet och en varning visas om den äldre versionen.

## <a name="next-steps"></a>Nästa steg

* [Felsökning av Azure Blockchain Workbench](troubleshooting.md)
