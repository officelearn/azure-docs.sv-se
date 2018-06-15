---
title: Använda ReportViewer på en webbplats | Microsoft Docs
description: Det här avsnittet beskriver hur du skapar en Microsoft Azure-webbplats med Visual Studio ReportViewer-kontrollen som visar en rapport som lagras på en Microsoft Azure-dator.
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
ms.openlocfilehash: af8a4a9c25005925bed3ddb78ced618e669f7f09
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31424626"
---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Använd ReportViewer på en webbplats i Azure
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Du kan skapa en Microsoft Azure-webbplats med Visual Studio ReportViewer-kontrollen som visar en rapport som lagras på en Microsoft Azure-dator. ReportViewer-kontrollen är i ett webbprogram som du skapar med mallen ASP.NET Web application.

> [!IMPORTANT]
> Webbprogram för ASP.NET MVC-mallar har inte stöd för ReportViewer-kontrollen.

Du måste utföra följande uppgifter för att tillämpa ReportViewer till Microsoft Azure-webbplatsen.

* **Lägg till** sammansättningar i distributionspaketet
* **Konfigurera** autentisering och auktorisering
* **Publicera** ASP.NET-webbprogram till Azure

## <a name="prerequisites"></a>Förutsättningar
Läs avsnittet ”allmänna rekommendationer och bästa praxis” i [SQL Server Business Intelligence i Azure Virtual Machines](../classic/ps-sql-bi.md).

> [!NOTE]
> Kontroller för ReportViewer levereras med Visual Studio, Standard Edition eller senare. Om du använder Web Developer Express Edition, måste du installera den [MICROSOFT REPORT VIEWER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) att använda funktioner i ReportViewer-körning.
> 
> ReportViewer som konfigurerats i lokalt läge stöds inte i Microsoft Azure.

## <a name="adding-assemblies-to-the-deployment-package"></a>Lägger till sammansättningar i distributionspaketet
När du är värd för ASP.NET-program lokalt, ReportViewer-sammansättningar installeras vanligtvis direkt i den globala sammansättningscachen (GAC) av IIS-servern under installationen av Visual Studio och kan nås direkt av programmet. Men när du är värd för ASP.NET-programmet i molnet, tillåter Microsoft Azure inte något som ska installeras i GAC, så du måste kontrollera ReportViewer-sammansättningar finns tillgängliga lokalt för ditt program. Du kan göra detta genom att lägga till referenser till dem i ditt projekt och konfigurera dem som ska kopieras lokalt.

ReportViewer-kontrollen använder följande sammansättningar i fjärrhantering läge:

* **Microsoft.ReportViewer.WebForms.dll**: innehåller ReportViewer-koden som du behöver använda ReportViewer på sidan. En referens för den här sammansättningen har lagts till ditt projekt när du släpper en ReportViewer-kontrollen på en ASP.NET-sida i projektet.
* **Microsoft.ReportViewer.Common.dll**: innehåller klasser som används av kontrollen ReportViewer vid körning. Det läggs inte automatiskt till ditt projekt.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Att lägga till en referens till Microsoft.ReportViewer.Common
* Högerklicka på ditt projekt **referenser** och välj **Lägg till referens**, Välj sammansättningen i .NET-fliken och klicka på **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Att göra sammansättningarna tillgängliga lokalt av ASP.NET-program
1. I den **referenser** mappen, klicka Microsoft.ReportViewer.Common sammansättningen så att dess egenskaper visas i fönstret Egenskaper.
2. I fönstret Egenskaper ange **kopiera lokala** till True.
3. Upprepa steg 1 och 2 för Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Att hämta ReportViewer Language Pack
1. Installera Microsoft Report Viewer 2012 Runtime lämpliga redistributable package från [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=317386).
2. Välj språk för i listrutan och sidan omdirigeras till sidan motsvarande download center.
3. Klicka på **hämta** att starta nedladdningen av ReportViewerLP.exe.
4. När du har hämtat ReportViewerLP.exe klickar du på **kör** installera omedelbart, eller klicka på **spara** spara den på datorn. Om du klickar på **spara**, ihåg namnet på mappen där du sparar filen.
5. Leta upp mappen där du sparade filen. Högerklicka på ReportViewerLP.exe, klickar du på **kör som administratör**, och klicka sedan på **Ja**.
6. När du har kört ReportViewerLP.exe, ser du c:\windows\assembly har resursfiler **Microsoft.ReportViewer.Webforms.Resources** och **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Om du vill konfigurera för lokaliserade ReportViewer-kontrollen
1. Hämta och installera Microsoft Report Viewer 2012 Runtime redistributable package enligt anvisningarna ovan angivna.
2. Skapa <language> mappen i projektet och kopiera associerad resurs sammansättningen det filer. Sammansättningen resursfiler som ska kopieras är: **Microsoft.ReportViewer.Webforms.Resources.dll** och **Microsoft.ReportViewer.Common.Resources.dll**. Välj sammansättningen resursfiler och i fönstret Egenskaper ange **kopiera till utdatakatalog** till ”**Kopiera alltid**”.
3. Ange kultur & UICulture för webbprojektet. Mer information om hur du anger kultur och kultur för Användargränssnittet för en ASP.NET-webbsida finns [så här: Ange kultur och kultur för Användargränssnittet för Globaliseringsinställningar för ASP.NET-webbsida](http://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Konfigurera autentisering och auktorisering
ReportViewer måste använda rätt autentiseringsuppgifter för att autentisera med rapportservern och autentiseringsuppgifterna måste godkännas av rapportservern att få åtkomst till rapporter som du vill. Information om autentisering finns i faktabladet [Reporting Services report viewer kontroll och Microsoft Azure virtuell dator baserat rapportservrar](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Publicera ASP.NET-webbprogram till Azure
Anvisningar om hur du publicerar ett ASP.NET-webbprogram till Azure finns [så här: migrera och publicera ett program till Azure från Visual Studio](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) och [Kom igång med Web Apps och ASP.NET](../../../app-service/app-service-web-get-started-dotnet.md).

> [!IMPORTANT]
> Om kommandot Lägg till Azure distributionsprojekt eller Lägg till Azure Molntjänstprojekt inte visas i snabbmenyn i Solution Explorer, kan du behöva ändra Målversionen av framework för projektet till .NET Framework 4.
> 
> Två kommandon ge i stort sett samma funktioner. En eller andra kommandot visas i snabbmenyn beroende på vilken version av Microsoft Azure SDK om du har installerat.
> 
> 

## <a name="resources"></a>Resurser
[Microsoft-rapporter](http://go.microsoft.com/fwlink/?LinkId=205399)

[SQL Server Business Intelligence i virtuella Azure-datorer](../classic/ps-sql-bi.md)

[Använd PowerShell för att skapa en virtuell Azure-dator med en rapportserver i enhetligt läge](../classic/ps-sql-report.md)

