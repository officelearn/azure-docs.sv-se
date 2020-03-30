---
title: Utöka Azure DevTest Labs med Azure Functions | Microsoft-dokument
description: Lär dig hur du utökar Azure DevTest Labs med Hjälp av Azure Functions.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: dd1fc4c1076d89c12b25837db9fa6a0ac3e1f3a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70014365"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Använda Azure Functions för att utöka DevTest Labs
Du kan använda Azure Functions för att stödja ytterligare scenarier utöver de som redan stöds av DevTest Labs. Azure Functions kan användas för att utöka den inbyggda funktionen för tjänsten för att uppfylla dina företagsspecifika behov. Följande lista innehåller några av de möjliga scenarierna. Den här artikeln visar hur du implementerar något av dessa exempelscenarier.

- Ge en sammanfattning på den översta nivån av virtuella datorer i labbet
- [Konfigurera ett labb för användning med en fjärrskrivbords-gateway](configure-lab-remote-desktop-gateway.md)
- Efterlevnadsrapportering på den interna supportsidan
- Gör det möjligt för användare att slutföra åtgärder som kräver ökade behörigheter i prenumerationen
- [Starta arbetsflöden baserat på DevTest Labs-händelser](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Översikt
[Azure Functions](../azure-functions/functions-overview.md) är en serverlös datorplattform i Azure. Genom att använda Azure Functions i en lösning med DevTest Labs kan vi utöka de befintliga funktionerna med vår egen anpassade kod. Mer information om Azure Functions finns i [Azure Functions-dokumentationen](../azure-functions/functions-overview.md). För att illustrera hur Azure Functions kan hjälpa dig att uppfylla dina krav eller slutföra scenarier i DevTest Labs, använder den här artikeln ett exempel på att tillhandahålla en sammanfattning på den översta nivån av virtuella datorer i labbet enligt följande:

**Exempel på krav/scenario**: Användare kan se information om alla virtuella datorer i ett labb, inklusive operativsystem, ägare och alla tillämpade artefakter.  Om artefakten **Använd Windows-uppdateringar** inte nyligen har tillämpats finns det dessutom ett enkelt sätt att använda den.

För att slutföra scenariot använder du två funktioner enligt beskrivningen i följande diagram:  

![Totalt flöde](./media/extend-devtest-labs-azure-functions/flow.png)

Källkoden för dessa exempelfunktioner finns i [DevTest Labs GitHub-databasen](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) (både C# och PowerShell-implementeringar är tillgängliga).

- **UpdateInternalSupportPage**: Den här funktionen frågar DevTest Labs och uppdaterar den interna supportsidan direkt med information om de virtuella datorerna.
- **ApplyWindowsUpdateArtifact**: För en virtuell dator i ett labb tillämpar den här funktionen Windows Update-artefakten. **Windows update**

## <a name="how-it-works"></a>Hur det fungerar
När användare väljer sidan **Intern support** i DevTest Labs har de en förifylld sida med information om virtuella datorer, labbägare och supportkontakter.  

När du väljer knappen **Välj här för att uppdatera** anropas den första Azure-funktionen: **UpdateInternalSupportPage**. Funktionen frågar DevTest Labs efter information och skriver sedan om den **interna supportsidan** med den nya informationen.

Det finns ytterligare en åtgärd som kan vidtas, för alla virtuella datorer där Windows Update-artefakterna inte har tillämpats nyligen, kommer det att finnas en knapp för att tillämpa Windows-uppdateringar på den virtuella datorn. När du väljer knappen ***Kör Windows update** för en virtuell dator anropas den andra Azure-funktionen: **ApplyWindowsUpdateArtifact**. Den här funktionen kontrollerar om den virtuella datorn körs och i så fall tillämpas [windows update-artefakten](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) direkt.

## <a name="step-by-step-walkthrough"></a>Steg-för-steg-genomgång
Det här avsnittet innehåller steg-för-steg-instruktioner för att konfigurera Azure-resurser som behövs för att uppdatera den **interna supportsidan.** Den här genomgången är ett exempel på hur du utökar DevTest Labs. Du kan använda det här mönstret för andra scenarier.

### <a name="step-1-create-a-service-principal"></a>Steg 1: Skapa ett huvudnamn för tjänsten 
Det första steget är att få ett tjänsthuvudnamn med behörighet till prenumerationen som innehåller labbet. Tjänstens huvudnamn måste använda den lösenordsbaserade autentiseringen. Det kan göras med [Azure CLI,](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)eller [Azure-portalen](../active-directory/develop/howto-create-service-principal-portal.md). Om du redan har ett huvudnamn för tjänsten att använda kan du hoppa över det här steget.

Anteckna **program-ID,** **nyckel**och **klient-ID** för tjänstens huvudnamn. Du kommer att behöva dem senare i denna genomgång. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>Steg 2: Ladda ned exemplet och öppna i Visual Studio 2019
Hämta en kopia av [exemplet C# Azure Functions](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) lokalt (antingen genom att klona databasen eller hämta databasen [härifrån).](https://github.com/Azure/azure-devtestlab/archive/master.zip)  

1. Öppna exempellösningen med Visual Studio 2019.  
1. Installera **Azure-utvecklingsarbetsbelastningen** för Visual Studio om du inte redan har installerat den. Den kan installeras via **Verktyg** -> **Få verktyg och funktioner menyalternativ).**

    ![Azure-utvecklingsarbetsbelastning](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Skapa lösningen. Välj **Bygg** och sedan **skapa lösningsmenyalternativ.**

### <a name="step-3-deploy-the-sample-to-azure"></a>Steg 3: Distribuera exemplet till Azure
Högerklicka på **AzureFunctions-projektet** i Fönstret **Lösningsutforskare** i Visual Studio och välj sedan **Publicera**. Följ guiden för att slutföra publiceringen till antingen en ny eller en befintlig Azure Function App. Detaljerad information om hur du utvecklar och distribuerar Azure-funktioner med Visual Studio finns i [Utveckla Azure-funktioner med Visual Studio](../azure-functions/functions-develop-vs.md).

![Dialogrutan Publicera](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>Steg 4: Samla in programinställningar
När funktionerna har publicerats måste du hämta webbadresser för dessa funktioner från Azure-portalen. 

1. Navigera till [Azure-portalen](https://portal.azure.com). 
1. Hitta funktionsappen.
1. Välj funktionen på sidan **Funktionsappar.** 
1. Välj **Hämta funktions-URL** som visas i följande bild. 

    ![Url:er för Azure-funktioner](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Kopiera och spara webbadressen. Upprepa dessa steg för den andra Azure-funktionen. 

Du behöver också ytterligare information om tjänstens huvudnamn, till exempel program-ID, nyckel och klient-ID.


### <a name="step-5--update-application-settings"></a>Steg 5: Uppdatera programinställningar
I Visual Studio, när du har publicerat Azure-funktionen, väljer du **inställningarna för Redigera Azure App Service** under **Åtgärder**. Uppdatera följande programinställningar (fjärr):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateTillåteradedagar (standard till 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Programinställningar](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>Steg 6: Testa Azure-funktionen
Det sista steget i den här genomgången är att testa Azure-funktionen.  

1. Navigera till funktionen **UpdateInternalSupportPage** i funktionsappen som skapades i steg 3. 
1. Välj **Testa** till höger på sidan. 
1. Ange i flödesegenskaperna (LABNAME, RESOURCEGROUPNAME och SUBSCRIPTIONID).
1. Välj **Kör** för att köra funktionen.  

    Den här funktionen uppdaterar den interna supportsidan för det angivna labbet. Den innehåller också en knapp för användare att direkt ringa funktionen nästa gång

    ![Testfunktion](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Nästa steg
Azure Functions kan hjälpa till att utöka funktionaliteten i DevTest Labs utöver vad som redan är inbyggt och hjälpa kunderna att uppfylla sina unika krav för sina team. Detta mönster kan utökas & utökas ytterligare för att täcka ännu mer.  Mer information om DevTest Labs finns i följande artiklar: 

- [DevTest Labs Företagsreferensarkitektur](devtest-lab-reference-architecture.md)
- [Vanliga frågor och svar](devtest-lab-faq.md)
- [Skala upp DevTest Labs](devtest-lab-guidance-scale.md)
- [Automatisera DevTest Labs med PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








