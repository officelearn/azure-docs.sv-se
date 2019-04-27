---
title: Använd ReportViewer på en webbplats | Microsoft Docs
description: Det här avsnittet beskriver hur du skapar en Microsoft Azure-webbplats med Visual Studio ReportViewer-kontroll som visar en rapport som lagras på en Microsoft Azure-dator.
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 78b76318-d9bf-48ef-9d9e-d1b7d8cf3042
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: b554dc1fa33519d87aa0c9c5ba9130b47cbea142
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580076"
---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Använd ReportViewer på en webbplats i Azure
> [!IMPORTANT]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Du kan skapa en Microsoft Azure-webbplats med Visual Studio ReportViewer-kontroll som visar en rapport som lagras på en Microsoft Azure-dator. ReportViewer-kontrollens är i ett webbprogram som du skapar med hjälp av ASP.NET-webbprogrammet.

> [!IMPORTANT]
> ASP.NET MVC-webbprogram-mallar stöder inte ReportViewer-kontrollens.

För att införliva ReportViewer i Microsoft Azure-webbplatsen, måste du slutföra följande uppgifter.

* **Lägg till** sammansättningar till distributionspaketet
* **Konfigurera** autentisering och auktorisering
* **Publicera** ASP.NET-webbapp till Azure

## <a name="prerequisites"></a>Nödvändiga komponenter
Läs avsnittet ”allmänna rekommendationer och metodtips” i [SQL Server Business Intelligence i Azure Virtual Machines](../classic/ps-sql-bi.md).

> [!NOTE]
> ReportViewer kontroller levereras med Visual Studio, Standard Edition eller senare. Om du använder Web Developer Express Edition, måste du installera den [MICROSOFT REPORT VIEWER 2012 körning](https://www.microsoft.com/download/details.aspx?id=35747) använda ReportViewer runtime-funktioner.
>
> ReportViewer som konfigurerats i lokalt läge stöds inte i Microsoft Azure.

## <a name="adding-assemblies-to-the-deployment-package"></a>Att lägga till sammansättningar i distributionspaketet
Om du agerar värd för ASP.NET-program på plats, ReportViewer-sammansättningar installeras vanligtvis direkt i den globala sammansättningscachen (GAC) av IIS-servern under installationen av Visual Studio och kan användas direkt av programmet. Men om du agerar värd för ASP.NET-program i molnet, tillåter Microsoft Azure inte att installeras i GAC, så måste du kontrollera ReportViewer-sammansättningar är tillgängliga lokalt för ditt program. Du kan göra detta genom att lägga till referenser till dem i ditt projekt och konfigurera dem så kopieras lokalt.

ReportViewer-kontrollens använder följande sammansättningar i fjärrbearbetning läge:

* **Microsoft.ReportViewer.WebForms.dll**: Innehåller ReportViewer-kod, som du behöver använda ReportViewer på din sida. Referens för den här sammansättningen har lagts till ditt projekt när du släpper en kontroll för ReportViewer på en ASP.NET-sida i projektet.
* **Microsoft.ReportViewer.Common.dll**: Innehåller klasser som används av ReportViewer-kontrollens vid körning. Det läggs inte automatiskt till ditt projekt.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Att lägga till en referens till Microsoft.ReportViewer.Common
* Högerklicka på ditt projekt **referenser** noden och väljer **Lägg till referens**, Välj sammansättningen i .NET-fliken och klicka på **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Att göra sammansättningarna lokalt tillgängligt genom ASP.NET-program
1. I den **referenser** mappen, klicka Microsoft.ReportViewer.Common sammansättningen så att dess egenskaper visas i fönstret Egenskaper.
2. I fönstret Egenskaper anger **kopiera lokala** till True.
3. Upprepa steg 1 och 2 för Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Att hämta ReportViewer Language Pack
1. Installera Microsoft Report Viewer 2012 körning lämplig redistributable package från [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=317386).
2. Välj språk i listrutan och sidan omdirigeras till sidan motsvarande download center.
3. Klicka på **hämta** att starta nedladdningen av ReportViewerLP.exe.
4. När du har hämtat ReportViewerLP.exe klickar du på **kör** installeras direkt, eller klicka på **spara** att spara det på din dator. Om du klickar på **spara**, Kom ihåg namnet på mappen där du sparar filen.
5. Leta reda på mappen där du sparade filen. Högerklicka på ReportViewerLP.exe, klickar du på **kör som administratör**, och klicka sedan på **Ja**.
6. När du har kört ReportViewerLP.exe, ser du c:\windows\assembly har resursfiler **Microsoft.ReportViewer.Webforms.Resources** och **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Om du vill konfigurera för lokaliserade ReportViewer-kontrollens
1. Hämta och installera Microsoft Report Viewer 2012 körning distributionspaketet genom att följa ovanstående angivna anvisningar.
2. Skapa \<språk\> mappen i projektet och kopiera den associerade resurs sammansättningen det filer. Sammansättningen resursfiler som ska kopieras är: **Microsoft.ReportViewer.Webforms.Resources.dll** och **Microsoft.ReportViewer.Common.Resources.dll**. Markera sammansättningen resursfiler och i fönstret Egenskaper ange **kopiera till utdatakatalog** till ”**Kopiera alltid**”.
3. Ange kultur & UICulture för webbprojektet. Läs mer om hur du anger kultur och kultur för Användargränssnittet för en ASP.NET-webbsida [så här: Ange Culture och kultur för Användargränssnittet för ASP.NET-webbsida globalisering](https://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Konfigurera autentisering och auktorisering
ReportViewer måste använda rätt autentiseringsuppgifter för att autentisera med rapportservern och autentiseringsuppgifterna måste godkännas av rapportservern att få åtkomst till rapporter som du vill. Information om autentisering finns i faktabladet [Reporting Services rapportera viewer-kontrollen och Microsoft Azure virtuell dator baserat rapportservrar](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Publicera ASP.NET-webbapp till Azure
Anvisningar om hur du publicerar ett ASP.NET-webbprogram till Azure finns i [så här: Migrera och publicera en Webbapp till Azure från Visual Studio](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) och [Kom igång med Web Apps och ASP.NET](../../../app-service/app-service-web-get-started-dotnet.md).

> [!IMPORTANT]
> Om kommandot Lägg till projekt för distribution i Azure eller lägga till Azure Cloud Service-projekt inte visas i snabbmenyn i Solution Explorer, kan du behöva ändra målramverk för projektet till .NET Framework 4.
>
> De två kommandona ge i stort sett samma funktioner. En eller andra kommandot visas i snabbmenyn beroende på vilken version av Microsoft Azure SDK som du har installerat.
>
>

## <a name="resources"></a>Resurser
[Microsoft-rapporter](https://go.microsoft.com/fwlink/?LinkId=205399)

[SQL Server Business Intelligence på virtuella Azure-datorer](../classic/ps-sql-bi.md)

[Använd PowerShell för att skapa en virtuell Azure-dator med en rapportserver i enhetligt läge](../classic/ps-sql-report.md)
