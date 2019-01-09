---
title: Verifiera OEM-tillverkare (original equipment manufacturer)-paket i Azure Stack-verifiering som en tjänst | Microsoft Docs
description: Lär dig hur du validerar OEM-tillverkare (original equipment manufacturer)-paket med verifiering som en tjänst.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: e3b0de577186cb7eb032a2042d234a0ffa2e3bb9
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105555"
---
# <a name="validate-oem-packages"></a>Verifiera OEM-paket

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Du kan testa ett nytt OEM-paket när det har skett en ändring av inbyggd programvara eller drivrutiner för en färdiga lösningen verifiering. När paketet har passerat testet, är det signerat av Microsoft. Testet måste innehålla det uppdaterade OEM-tilläggspaket med drivrutiner och inbyggd programvara som har klarat logotyp för Windows Server och datorer tester.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Innan du överför eller skickar paket, granska [skapa ett OEM-paket](azure-stack-vaas-create-oem-package.md) för förväntade paketet format och innehåll.

## <a name="managing-packages-for-validation"></a>Hantera paket för verifiering

När du använder den **Paketvalideringen** arbetsflöde för att verifiera ett paket måste du ange en URL till en **Azure Storage-blob**. Den här bloben är OEM-paket som har installerats på lösningen vid tidpunkten för distribution. Skapa blob med hjälp av Azure Storage-konto som du skapade under installationen (se [ställa in din validering som en tjänstresurser](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>Förutsättning: Etablera en lagringsbehållare

Skapa en behållare i ditt storage-konto för paketet BLOB. Den här behållaren kan användas för alla dina Paketvalideringen körs.

1. I den [Azure-portalen](https://portal.azure.com)går du till det lagringskonto som skapats i [ställa in din validering som en tjänstresurser](azure-stack-vaas-set-up-resources.md).
2. På bladet vänstra under **Blobtjänsten**väljer på **behållare**.
3. Välj **+ behållare** på menyn menyraden och ange ett namn för behållaren, t.ex. `vaaspackages`.

### <a name="upload-package-to-storage-account"></a>Ladda upp paketet till lagringskonto

1. Förbered det paket som du vill validera. Om ditt paket har flera filer, komprimera den i en `.zip` fil.
2. I den [Azure-portalen](https://portal.azure.com), Välj paket-behållaren och ladda upp paketet genom att välja på **överför** på menyraden.
3. Välj paketet `.zip` fil att ladda upp. Behåll standardinställningarna för **typ av Blob** (d.v.s. **Blockblob**) och **blockstorlek**.

> [!NOTE]
> Se till att den `.zip` innehållet är placerade i roten på den `.zip` filen. Det bör finnas några underordnade mappar i paketet.

### <a name="generate-package-blob-url-for-vaas"></a>Generera paketet blob-Webbadressen för VaaS

När du skapar en **Paketvalideringen** arbetsflöde i VaaS-portalen, måste du ange en URL till Azure Storage-blob som innehåller ditt paket.

#### <a name="option-1-generating-an-account-sas-url"></a>Alternativ 1: Generera en kontots SAS-URL

1. I den [Azure-portalen](https://portal.azure.com/), gå till ditt storage-konto och navigera till .zip som innehåller ditt paket

2. Välj **generera SAS** på snabbmenyn

3. Välj **Läs** från **behörigheter**

4. Ange **starttid** till aktuell tid och **sluttid** minst 48 timmar från **starttid**. Om du ska köra andra tester med samma paket måste du överväga att öka **sluttid** för längden på testet. Alla tester som schemalagts via VaaS efter **sluttid** kommer misslyckas och en ny SAS behöver ska genereras.

5. Välj **generera blob SAS-token och URL: en**

Använd **Blob SAS-Webbadressen** när startar en ny **Paketvalideringen** arbetsflöde i VaaS-portalen.

#### <a name="option-2-using-public-read-container"></a>Alternativ 2: Offentliga behållare som skrivskyddad

> [!CAUTION]
> Det här alternativet öppnas din behållare för anonym åtkomst för skrivskyddat läge.

1. Bevilja **offentlig läsbehörighet endast för blobbar** till behållaren paketet genom att följa anvisningarna i avsnittet [bevilja anonyma användare till behållare och blobbar](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

2. I behållaren paketet väljer du på paketet blob i behållaren för att öppna egenskapsfönstret.

3. Kopiera den **URL**. Använd det här värdet när du startar en ny **Paketvalideringen** arbetsflöde i VaaS-portalen.

## <a name="apply-monthly-update"></a>Tillämpa månatliga uppdatering

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-package-validation-workflow"></a>Skapa ett arbetsflöde för verifiera paketet

1. Logga in på den [VaaS portal](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Välj **starta** på den **Paketvalideringen** panelen.

    ![Paketet verifieringar arbetsflöde panel](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Ange URL: en för Azure Storage blob till OEM-paketet som har installerats på lösningen vid tidpunkten för distribution. Anvisningar finns i [generera paketet blob-Webbadressen för VaaS](#generate-package-blob-url-for-vaas).

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Miljö-parametrar kan inte ändras när du har skapat ett arbetsflöde.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Du omdirigeras till sammanfattningssidan tester.

## <a name="run-package-validation-tests"></a>Köra verifieringstester för paketet

1. I den **Paketvalideringen testar sammanfattning** sidan visas en lista över de tester som krävs för att slutföra verifieringen. Tester i det här arbetsflödet kör för ungefär 24 timmar.

    I arbetsflöden verifiering **schemaläggning** ett test använder de gemensamma parametrarna för arbetsflödet på servernivå som du angav under arbetsflödet skapats (se [arbetsflödets gemensamma parametrar för Azure Stack-verifiering som en tjänst](azure-stack-vaas-parameters.md)). Om någon av test parametervärden blir ogiltiga måste du resupply dem som finns beskrivet i [ändra arbetsflödesparametrar](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

    > [!NOTE]
    > Schemalägga ett verifieringstest över en befintlig instans skapas en ny instans i stället för den gamla instansen i portalen. Loggar för den gamla instansen kommer att hållas kvar, men är inte tillgängliga från portalen.  
    När ett test har slutförts, den **schema** åtgärden inaktiveras.

2. Välj den agent som ska köra testet. Information om att lägga till lokala webbtestagenter körning, se [distribuera lokal agent](azure-stack-vaas-local-agent.md).

3. För var och en av följande test steg fyra och fem:
    - Verifiering av OEM-tillägg-paketet
    - Simulering Molnteknik

4. Välj **schema** från snabbmenyn för att öppna en kommandotolk för att schemalägga test-instans.

5. Granska de test-parametrarna och välj sedan **skicka** att schemalägga testet för körning.

När alla tester har slutförts, skicka namnet på din VaaS lösning och verifiera paketet till [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) till Begär signering av paketet.

## <a name="next-steps"></a>Nästa steg

- [Övervaka och hantera tester i VaaS-portalen](azure-stack-vaas-monitor-test.md)