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
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: aae3ec8ff713959c5cc2485951aba025a6f89a1e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113290"
---
# <a name="validate-oem-packages"></a>Verifiera OEM-paket

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Du kan testa ett nytt OEM-paket när det har skett en ändring av inbyggd programvara eller drivrutiner för en färdiga lösningen verifiering. När paketet har passerat testet, är det signerat av Microsoft. Testet måste innehålla det uppdaterade OEM-tilläggspaket med drivrutiner och inbyggd programvara som har klarat logotyp för Windows Server och datorer tester.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Innan du överför eller skickar paket, granska [skapa ett OEM-paket](azure-stack-vaas-create-oem-package.md) för förväntade paketet format och innehåll.

## <a name="managing-packages-for-validation"></a>Hantera paket för verifiering

När du använder den **Paketvalideringen** arbetsflöde för att verifiera ett paket måste du ange en URL till en **Azure Storage-blob**. Den här bloben är testet signerade OEM-paket som installeras som en del av uppdateringen. Skapa blob med hjälp av Azure Storage-konto som du skapade under installationen (se [ställa in din validering som en tjänstresurser](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>Förutsättning: Etablera en lagringsbehållare

Skapa en behållare i ditt storage-konto för paketet BLOB. Den här behållaren kan användas för alla dina Paketvalideringen körs.

1. I den [Azure-portalen](https://portal.azure.com)går du till det lagringskonto som skapats i [ställa in din validering som en tjänstresurser](azure-stack-vaas-set-up-resources.md).

2. På bladet vänstra under **Blobtjänsten**väljer **behållare**.

3. Välj **+ behållare** på menyraden.
    1. Ange ett namn för behållaren, till exempel `vaaspackages`.
    1. Välj önskad åtkomstnivå för oautentiserade klienter, till exempel VaaS. Mer information om hur du ger VaaS åtkomst till paket i varje scenario finns i [hantering av behållare åtkomstnivå](#handling-container-access-level).

### <a name="upload-package-to-storage-account"></a>Ladda upp paketet till lagringskonto

1. Förbered det paket som du vill validera. Det här är en `.zip` filen vars innehåll måste matcha strukturen som beskrivs i [skapa ett OEM-paket](azure-stack-vaas-create-oem-package.md).

    > [!NOTE]
    > Se till att den `.zip` innehållet är placerade i roten på den `.zip` filen. Det bör finnas några underordnade mappar i paketet.

1. I den [Azure-portalen](https://portal.azure.com), Välj paket-behållaren och ladda upp paketet genom att välja på **överför** på menyraden.

1. Välj paketet `.zip` fil att ladda upp. Behåll standardinställningarna för **typ av Blob** (det vill säga **Blockblob**) och **blockstorlek**.

### <a name="generate-package-blob-url-for-vaas"></a>Generera paketet blob-Webbadressen för VaaS

När du skapar en **Paketvalideringen** arbetsflöde i VaaS-portalen, måste du ange en URL till Azure Storage-blob som innehåller ditt paket. Vissa *interaktiva* tester, inklusive **månatliga AzureStack Update verifiering** och **OEM-tillägget paketet verifiering**, kräver också en URL till BLOB-paketet.

#### <a name="handling-container-access-level"></a>Åtkomstnivå för hantering av behållare

Den minsta åtkomstnivå som krävs av VaaS beror på om du skapar ett arbetsflöde för Paketvalideringen eller schemalägga en *interaktiva* testa.

I fall med **privata** och **Blob** åtkomstnivåer, tillfälligt måste du ge åtkomst till bloben paketet genom att ge VaaS en [signatur för delad åtkomst](https://docs.microsoft.com/en-us/azure/storage/common/storage-dotnet-shared-access-signature-part-1?) (SAS). Den **behållare** åtkomstnivå kräver inte att generera SAS URL: er, men tillåter oautentiserad åtkomst till behållaren och dess blobbar.

|Åtkomstnivå | Arbetsflödeskrav | Test krav |
|---|---------|---------|
|Privat | Skapa en SAS-Webbadressen per paket blob ([alternativ 1](#option-1-generate-a-blob-sas-url)). | Generera en SAS-URL på kontonivå och manuellt lägger till blob paketnamn ([alternativ 2](#option-2-construct-a-container-sas-url)). |
|Blob | Ange URL-egenskapen för blob ([alternativ 3](#option-3-grant-public-read-access)). | Generera en SAS-URL på kontonivå och manuellt lägger till blob paketnamn ([alternativ 2](#option-2-construct-a-container-sas-url)). |
|Container | Ange URL-egenskapen för blob ([alternativ 3](#option-3-grant-public-read-access)). | Ange URL-egenskapen för blob ([alternativ 3](#option-3-grant-public-read-access)).

Alternativ för att bevilja åtkomst till dina paket sorteras från minst åtkomst till största åtkomst.

#### <a name="option-1-generate-a-blob-sas-url"></a>Alternativ 1: Generera en blob SAS-URL

Använd det här alternativet om åtkomstnivån för lagringsbehållaren har angetts till **privata**, där behållaren inte aktivera offentlig läsbehörighet till behållaren eller dess blobar.

> [!NOTE]
> Den här metoden fungerar inte för *interaktiva* tester. Se [alternativ 2: Skapa en behållare SAS-Webbadressen](#option-2-construct-a-container-sas-url).

1. I den [Azure-portalen](https://portal.azure.com/), gå till ditt storage-konto och navigera till .zip som innehåller ditt paket.

2. Välj **generera SAS** på snabbmenyn.

3. Välj **Läs** från **behörigheter**.

4. Ange **starttid** till aktuell tid och **sluttid** minst 48 timmar från **starttid**. Om du skapar andra arbetsflöden med samma paket, överväga att öka **sluttid** för längden på testet.

5. Välj **Generera blob-SAS-token och URL**.

Använd den **Blob SAS-Webbadressen** när tillhandahåller paketet blob-URL: er till portalen.

#### <a name="option-2-construct-a-container-sas-url"></a>Alternativ 2: Skapa en behållare SAS-URL

Använd det här alternativet om åtkomstnivån för lagringsbehållaren har angetts till **privata** och måste du ange en blob-URL för paketet till en *interaktiva* testa. Den här URL: en kan även användas på arbetsflödesnivå.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Välj **Blob** från **tillåtna tjänster alternativ**. Avmarkera eventuella återstående alternativen.

1. Välj **behållare** och **objekt** från **tillåtna resurstyper**.

1. Välj **Läs** och **lista** från **behörigheter**. Avmarkera eventuella återstående alternativen.

1. Välj **starttid** som aktuell tid och **sluttid** minst 14 dagar från **starttid**. Om du ska köra andra tester med samma paket måste du överväga att öka **sluttid** för längden på testet. Alla tester som schemalagts via VaaS efter **sluttid** kommer misslyckas och en ny SAS behöver ska genereras.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    Formatet ska visas på följande sätt: `https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. Ändra den genererade SAS-URL och inkludera behållaren paketet `{containername}`, och namnet på din paketet blob `{mypackage.zip}`, enligt följande:  `https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    Använd det här värdet när tillhandahåller paketet blob-URL: er till portalen.

#### <a name="option-3-grant-public-read-access"></a>Alternativ 3: Ge offentlig läsbehörighet

Använd det här alternativet om du accepterar att tillåta åtkomst till enskilda blobbar, eller i fall med oautentiserade klienter *interaktiva* testar behållaren.

> [!CAUTION]
> Det här alternativet öppnas din BLOB(ar) för anonym åtkomst för skrivskyddat läge.

1. Ange åtkomstnivån för paketet behållaren **Blob** eller **behållare** genom att följa anvisningarna i avsnittet [bevilja anonyma användare till behållare och blobbar](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

    > [!NOTE]
    > Om du tillhandahåller en paket-URL till en *interaktiva* test, måste du ge **fullständig offentlig läsbehörighet** till behållaren för att fortsätta med testning.

1. I behållaren paket väljer du blobben som paketet ska öppna egenskapsfönstret.

1. Kopiera den **URL**. Använd det här värdet när tillhandahåller paketet blob-URL: er till portalen.

## <a name="create-a-package-validation-workflow"></a>Skapa ett arbetsflöde för verifiera paketet

1. Logga in på den [VaaS portal](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Välj **starta** på den **Paketvalideringen** panelen.

    ![Paketet verifieringar arbetsflöde panel](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Ange Azure Storage blob URL: en till test-signerade OEM-paketet som kräver en signatur från Microsoft. Anvisningar finns i [generera paketet blob-Webbadressen för VaaS](#generate-package-blob-url-for-vaas).

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Miljö-parametrar kan inte ändras när du har skapat ett arbetsflöde.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Du omdirigeras till sammanfattningssidan tester.

## <a name="required-tests"></a>Tester som krävs

Följande tester krävs för att verifiera för OEM-paketet:

- Verifiering av OEM-tillägg-paketet
- Cloud Simulation Engine

## <a name="run-package-validation-tests"></a>Köra verifieringstester för paketet

1. I den **Paketvalideringen testar sammanfattning** kan du köra en delmängd av de listade testerna som är lämpligt för ditt scenario.

    I arbetsflöden verifiering **schemaläggning** ett test använder de gemensamma parametrarna för arbetsflödet på servernivå som du angav under arbetsflödet skapats (se [arbetsflödets gemensamma parametrar för Azure Stack-verifiering som en tjänst](azure-stack-vaas-parameters.md)). Om någon av test parametervärden blir ogiltiga måste du resupply dem som finns beskrivet i [ändra arbetsflödesparametrar](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

    > [!NOTE]
    > Schemalägga ett verifieringstest över en befintlig instans skapas en ny instans i stället för den gamla instansen i portalen. Loggar för den gamla instansen kommer att hållas kvar, men är inte tillgängliga från portalen.  
    > När ett test har slutförts, den **schema** åtgärden inaktiveras.

2. Välj den agent som ska köra testet. Information om att lägga till lokala webbtestagenter körning, se [distribuera lokal agent](azure-stack-vaas-local-agent.md).

3. För att slutföra verifieringen för OEM-tillägg-paketet, Välj **schema** från snabbmenyn för att öppna en kommandotolk för att schemalägga test-instans.

4. Granska de test-parametrarna och välj sedan **skicka** att schemalägga OEM-tillägget paketet verifiering för körning.

    OEM-tillägget paketet verifiering är uppdelat i två manuella steg: Azure Stack-uppdatering och OEM-uppdatering.

   1. **Välj** ”kör” i Användargränssnittet för att köra skriptet precheck. Det här är ett automatiserat test som tar cirka 5 minuter för att slutföra och kräver ingen åtgärd.

   1. När precheck skriptet har slutförts kan du utföra den manuella åtgärden: **installera** den senaste tillgängliga Azure Stack uppdateringen med hjälp av Azure Stack-portalen.

   1. **Kör** Test-AzureStack på stämpeln. Om något fel uppstår, Fortsätt inte med test- och kontakta [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

       Information om hur du kör kommandot Test-AzureStack finns i [Validera Azure Stack systemtillstånd](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test).

   1. **Välj** ”nästa” för att köra skriptet postcheck. Detta är ett automatiserat test och markerar slutet av uppdateringsprocessen för Azure Stack.

   1. **Välj** ”kör” för att köra skriptet precheck för OEM-uppdatering.

   1. När kravkontrollen har slutförts kan utföra den manuella åtgärden: **installera** OEM-tilläggspaket via portalen.

   1. **Kör** Test-AzureStack på stämpeln.

      > [!NOTE]
      > Som tidigare Fortsätt inte med test- och kontakta [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) om det misslyckas. Det här steget är kritiskt eftersom kan du spara en omdistributionen.

   1. **Välj** ”nästa” för att köra skriptet postcheck. Detta markerar slutet av OEM update steg.

   1. Svara på eventuella återstående frågor i slutet av testet och **Välj** ”skicka”.

   1. Detta markerar slutet av det interaktiva testet.

5. Granska resultatet för OEM-tillägget paketet verifiering. När testet har slutförts kan du schemalägga molnet simulering motorn för körning.

Om du vill skicka ett paket som förfrågan, skicka [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) Lösningsnamnet och verifiera paketet namn som är associerade med den här körningen.

## <a name="next-steps"></a>Nästa steg

- [Övervaka och hantera tester i VaaS-portalen](azure-stack-vaas-monitor-test.md)
