---
title: "Kom igång med Azure logganalys-integration | Microsoft Docs"
description: "Lär dig mer om att installera tjänsten Azure logganalys-integrering och integrera loggar från Azure storage, Azure-granskningsloggarna och Azure Security Center-aviseringar."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 02/20/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 4555216950811960ccb42241bcade5ec892a77ce
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure logganalys-integrering med Azure Diagnostics loggning och vidarebefordran av Windows-händelser

Azure Log-integrering (AzLog) ger kunder med en alternativ i händelsen som en [Azure-Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) anslutningen är inte tillgänglig från deras SIEM-leverantör. Azure Log-integrering tillgängliggör Azure loggar till din SIEM och kan du skapa en instrumentpanel för enhetlig säkerhet för alla tillgångar.

>[!NOTE]
>Mer information om Azure-Monitor kan du granska [Kom igång med Azure-Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) Kontakta leverantören SIEM för mer information om status för en Azure-monitor-koppling.

>[!IMPORTANT]
>Om ditt primära intresse finns i insamling av loggar för virtuell dator, även de flesta SIEM-leverantörer detta i sitt lösning. Med SIEM ska leverantörens kopplingen alltid vara det bästa alternativet.

Den här artikeln hjälper dig att komma igång med Azure Log Integration genom att fokusera på installation av tjänsten AzLog och integrera tjänsten med Azure-diagnostik. Tjänsten Azure Log-integrering kommer sedan att kunna samla in Windows-händelseloggen information från den Windows-händelse säkerhetskanal från virtuella datorer i Azure IaaS. Detta liknar ”vidarebefordran av händelser” att du har använt lokalt.

>[!NOTE]
>Möjlighet att sätta utdata Azure inloggningen integrering med SIEM tillhandahålls av SIEM sig själv. Se artikeln [integrera Azure Log-integrering med lokala SIEM](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) för mer information.

Att du är klar - Azure Log-integrering-tjänsten körs på en fysisk eller virtuell dator som använder Windows Server 2008 R2 eller senare operativsystem (Windows Server 2012 R2 eller Windows Server 2016 rekommenderas).

Den fysiska datorn kan köra lokalt (eller på en webbplats som värd). Om du väljer att köra tjänsten Azure Log-integrering på en virtuell dator som den virtuella datorn kan finnas lokalt eller i ett offentligt moln, till exempel Microsoft Azure.

Den fysiska eller virtuella datorn som kör tjänsten Azure Log Integration kräver nätverksanslutning till det offentliga Azure-molnet. Stegen i den här artikeln innehåller detaljerad information om konfigurationen.

## <a name="prerequisites"></a>Förutsättningar

Installation av AzLog kräver minst följande:

* En **Azure-prenumeration**. Om du inte har en prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En **lagringskonto** som kan användas för Windows Azure diagnostikloggning (du kan använda en förkonfigurerad storage-konto eller skapa en ny – vi visar hur du konfigurerar storage-konto senare i den här artikeln)

  >[!NOTE]
  Beroende på scenario kanske inte ett lagringskonto krävs. Scenario som beskrivs i den här artikeln en krävs för Azure-diagnostik.

* **Två system**: en dator som kör tjänsten Azure Log-integrering och en dator som ska övervakas och har dess loggning informationen som skickas till den AzLog service-datorn.
   * En dator som du vill övervaka – det här är en virtuell dator som körs som en [Azure-dator](../virtual-machines/virtual-machines-windows-overview.md)
   * En dator som kör tjänsten Azure logganalys integration; den här datorn samlar in alla logginformation som senare ska importeras till din SIEM.
    * Systemet kan vara lokalt eller i Microsoft Azure.  
    * Den måste använda en x64 version av Windows server 2008 R2 SP1 eller senare och har .NET 4.5.1 har installerats. Du kan fastställa .NET-versionen som installerats i artikeln [så här: fastställa som .NET Framework-versioner är installerade](https://msdn.microsoft.com/library/hh925568)  
    Den måste ha anslutning till Azure storage-konto som används för Azure diagnostikloggning. Vi ger anvisningar nedan på hur du kan bekräfta den här anslutningen

* En **lagringskonto** som kan användas för Windows Azure diagnostikloggning. Du kan använda en förkonfigurerad storage-konto eller skapa en ny. Du kan konfigurera storage-konto senare i den här artikeln.
  >[!NOTE]
  Beroende på scenario kanske inte ett lagringskonto krävs. Scenario som beskrivs i den här artikeln en krävs för Azure-diagnostik.
* **Två system**: en dator som kör tjänsten Azure Log-integrering och en dator som övervakas och har dess loggningsinformation har skickats till Azlog service-datorn.
   * En dator som du vill övervaka – det här är en virtuell dator som körs som en [Azure-dator](../virtual-machines/virtual-machines-windows-overview.md)
   * En dator som kör tjänsten Azure logganalys integration; den här datorn samlar in alla logginformation som senare importeras till din SIEM.
    * Systemet kan vara lokalt eller i Microsoft Azure.  
    * Den måste använda en x64 version av Windows server 2008 R2 SP1 eller senare och har .NET 4.5.1 har installerats. Du kan fastställa .NET-versionen som installerats i artikeln [så här: fastställa som .NET Framework-versioner är installerade](https://msdn.microsoft.com/library/hh925568)  
    Den måste ha anslutning till Azure storage-konto som används för Azure diagnostikloggning. Hur du bekräfta att den här anslutningen beskrivs senare i den här artikeln.

För en snabb demonstration av processen att skapa en virtuell dator med hjälp av Azure portal, titta på videon nedan.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Distributionsöverväganden

Du kan använda alla system som uppfyller de minsta operativsystemkraven under testningen. För en produktionsmiljö måste belastningen du planera för att skala upp eller ned.

Du kan köra flera instanser av tjänsten Azure Log-integrering. Detta är begränsad till en instans per fysiska eller virtuella datorn. Dessutom kan kan du belastningsutjämna diagnostik för Azure storage-konton för Windows (BOMULLSTUSS) och antalet prenumerationer att förse instanserna ska baseras på din kapacitet.

>[!NOTE]
>För närvarande kan har vi inte specifika rekommendationer när du vill skala upp instanser av Azure logganalys integration datorer (datorer som kör tjänsten Azure logganalys-integrering) eller för storage-konton eller prenumerationer. Skalning beslut ska baseras på dina synpunkter prestanda i dessa olika områden.

Om händelsen volym är högt kan köra du flera instanser av tjänsten Azure Log-integrering (en instans per fysisk eller virtuell dator). Du kan dessutom ladda saldo Azure Diagnostics storage-konton för Windows (BOMULLSTUSS).

Du har också möjlighet att skala upp tjänsten Azure Log-integrering för att förbättra prestanda. Följande prestandamåtten kan hjälpa dig att ändra storlek på de datorer som du väljer att köra tjänsten Azure logganalys-integrering:

* På en dator med 8-processor (core) kan en enda instans av AzLog Integrator bearbeta cirka 24 miljoner händelser per dag (~1M/hour).
* En instans av AzLog Integrator kan bearbeta ungefär 1,5 miljoner händelser per dag (~62.5K/hour) på en dator i 4-processor (kärna).

## <a name="install-azure-log-integration"></a>Installera Azure logganalys-integrering

Om du vill installera Azure Log-integrering, måste du hämta den [Azure logganalys integration](https://www.microsoft.com/download/details.aspx?id=53324) installationsfilen. Kör via installationsrutinen och bestämma om du vill ange telemetri information till Microsoft.  

![Installationsskärmen med telemetri ikryssad](./media/security-azure-log-integration-get-started/telemetry.png)

> [!NOTE]
> Vi rekommenderar att du tillåter att Microsoft samlar in telemetridata. Du kan stänga av insamling av telemetridata genom att avmarkera det här alternativet.
>


Tjänsten Azure Log-integrering samlar in telemetridata från datorn som är installerad.  

I insamlade telemetridata är:

* Undantag som uppstår vid körning av Azure logganalys-integrering
* Mått om antalet frågor och händelser som har bearbetats
* Statistik om vilka Azlog.exe kommandoradsalternativ som används

Installationsprocessen ingår i videon nedan.
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]



## <a name="post-installation-and-validation-steps"></a>Efter installation och verifiering av steg

När du har slutfört rutinen grundläggande inställningar du är klar steg ska utföras efter installationen och validering steg:

1. Öppna ett upphöjt PowerShell-fönster och navigera till **c:\Program Files\Microsoft Azure Log-integrering**
2. Det första steget när du måste vidta är att hämta AzLog-Cmdlets som importeras. Du kan göra det genom att köra skriptet **LoadAzlogModule.ps1** (Lägg märke till de ”. \” i följande kommando). Typen **.\LoadAzlogModule.ps1** och tryck på **RETUR**. Du bör se något som liknar det som visas i bilden nedan. </br></br>
![Installationsskärmen med telemetri ikryssad](./media/security-azure-log-integration-get-started/loaded-modules.png) </br></br>
3. Nu måste du konfigurera AzLog om du vill använda en viss Azure-miljön. Ett ”Azure-miljön” är ”typ” för Azure-molndatacenter som du vill arbeta med. Det finns flera Azure miljöer just nu, alternativen för närvarande relevanta är antingen **AzureCloud** eller **AzureUSGovernment**.   I upphöjd PowerShell-miljön måste du kontrollera att du är i **c:\program files\Microsoft Azure Log-integrering\** </br></br>
    När det, kör kommandot: </br>
    ``Set-AzlogAzureEnvironment -Name AzureCloud`` (för Azure kommersiella)

      >[!NOTE]
      >När kommandot slutförs får inte feedback.  Om du vill använda US Government Azure-molnet använder du **AzureUSGovernment** (för variabeln-namn) för amerikanska myndigheter molnet. Andra Azure-moln stöds inte just nu.  
4. Innan du kan övervaka ett system behöver du namnet på lagringskontot används för Azure-diagnostik.  I Azure-portalen går du till **virtuella datorer** och leta efter den virtuella datorn som du övervakar. I den **egenskaper** väljer **diagnostikinställningar**.  Klicka på **Agent** och anteckna lagringskontots namn anges. Du behöver det här namnet på tjänstkontot för ett senare steg.

    ![Diagnostikinställningar för Azure](./media/security-azure-log-integration-get-started/storage-account-large.png) </br></br>

    ![Diagnostikinställningar för Azure](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

     >[!NOTE]
     >Om övervakning inte aktiverades under generering av virtuell dator, får du välja att aktivera den som ovan.

5. Nu ska vi växla våra uppmärksamhet tillbaka till Azure logganalys integration datorn. Vi behöver verifiera att du har anslutning till Lagringskontot från systemet där du installerade Azure Log-integrering. Den dator som kör tjänsten Azure Log-integrering behöver åtkomst till lagringskontot för att hämta information som loggas av Azure-diagnostik som konfigurerats på de övervakade systemen.  
    a. Du kan hämta Azure Lagringsutforskaren [här](http://storageexplorer.com/).
   b. Gå igenom installationen rutinunderhåll c. När installationen är klar klickar du på **nästa** och Låt kryssrutan **starta Microsoft Azure-Lagringsutforskaren** markerad.  
    d. Logga in på Azure.
   e. Kontrollera att du kan se det lagringskonto som du har konfigurerat för Azure-diagnostik. 
        ![Lagringskonton](./media/security-azure-log-integration-get-started/storage-account.jpg) </br></br>
6. Observera att det inte finns några alternativ under storage-konton. En av dem är **tabeller**. Under **tabeller** bör du se en kallas **WADWindowsEventLogsTable**. </br></br>

 Om övervakning inte aktiverades under generering av virtuell dator möjlighet du att aktivera den som ovan.
7. Nu ska vi växla våra uppmärksamhet tillbaka till Azure logganalys integration datorn. Vi behöver verifiera att du har anslutning till Lagringskontot från systemet där du installerade Azure Log-integrering. Den fysiska eller virtuella datorn kör tjänsten Azure Log-integrering behöver åtkomst till lagringskontot för att hämta information som loggas av Azure-diagnostik som konfigurerats på de övervakade systemen.  
  1. Du kan hämta Azure Lagringsutforskaren [här](http://storageexplorer.com/).
  2. Gå igenom installationsrutinen
  3. När installationen är klar klickar du på **nästa** och Låt kryssrutan **starta Microsoft Azure-Lagringsutforskaren** markerad.  
  4. Logga in på Azure.
  5. Kontrollera att du kan se det lagringskonto som du har konfigurerat för Azure-diagnostik.  
  6. Observera att det inte finns några alternativ under storage-konton. En av dem är **tabeller**. Under **tabeller** bör du se en kallas **WADWindowsEventLogsTable**. </br></br>
   ![Lagringskonton](./media/security-azure-log-integration-get-started/storage-explorer.png) 

## <a name="integrate-azure-diagnostic-logging"></a>Integrera Azure diagnostikloggning

I det här steget konfigurerar du den dator som kör tjänsten Azure Log-integrering för att ansluta till lagringskontot som innehåller loggfilerna.
För att slutföra det här steget måste några saker direkt.  
* **FriendlyNameForSource:** detta är ett eget namn som du kan använda till att du har konfigurerat den virtuella datorn för att lagra information från Azure-diagnostik-lagringskontot
* **StorageAccountName:** är namnet på lagringskontot som du angav när du konfigurerade Azure-diagnostik.  
* **StorageKey:** här sparas lagringsnyckeln för lagringskontot där informationen om Azure-diagnostik för den virtuella datorn.  

Utför följande steg för att hämta lagringsnyckeln:
 1. Bläddra till den [Azure-portalen](http://portal.azure.com).
 2. I navigeringsfönstret i Azure-konsolen klickar du på **alla tjänster.**
  3. Ange **lagring** i den **Filter** textruta. Klicka på **lagringskonton**.

       ![Skärmbild som visar lagringskonton i alla tjänster](./media/security-azure-log-integration-get-started/filter.png)
 4. Storage-konton visas, dubbelklicka på det konto som du tilldelat lagra loggen.

       ![lista över storage-konton](./media/security-azure-log-integration-get-started/storage-accounts.png)
 5. Klicka på **åtkomstnycklar** i den **inställningar** avsnitt.

      ![Snabbtangenter](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 6. Kopiera **key1** och placera den på en säker plats som du kan använda för nästa steg.

       ![två åtkomstnycklar](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 7. Öppna en upphöjd kommandotolk (Observera att vi använder ett upphöjt kommandotolksfönster här, inte en upphöjd PowerShell-konsol) på servern att du har installerat Azure Log-integrering.
 8. Navigera till **c:\Program Files\Microsoft Azure logganalys-integrering**
 9. Kör ``Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey> `` </br> Till exempel ``Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`` om du vill att prenumerations-ID visas i XML, Lägg till prenumerations-ID till ett eget namn: ``Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`` eller t.ex. ``Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==``

>[!NOTE]
>Upp till 60 minuter och sedan visa händelser som hämtas från lagringskontot. Om du vill visa, öppna **Loggboken > Windows-loggar > vidarebefordrade händelser** på AzLog Integrator.

Här kan du se en video som skickas via stegen som beskrivs ovan.

>[!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>Vad händer om data inte visas i mappen vidarebefordrade händelser?
Om en timme efter att data inte visas i den **vidarebefordrade händelser** mappen sedan:

1. Kontrollera den datorn som kör tjänsten Azure Log-integrering och bekräfta att den har åtkomst till Azure. Om du vill testa anslutningen för försök att öppna den [Azure-portalen](http://portal.azure.com) från webbläsaren.
2. Kontrollera att användarkontot **AzLog** har skrivbehörighet på mappen **users\Azlog**.
  <ol type="a">
   <li>Öppna **Utforskaren** </li>
  <li> Navigera till **c:\users** </li>
  <li> Högerklicka på **c:\users\Azlog** </li>
  <li> Klicka på **säkerhet**  </li>
  <li> Klicka på **NT Service\Azlog** och kontrollera behörigheterna för kontot. Om kontot saknas från den här fliken eller om de behörigheter som krävs för inte närvarande beviljar visar du kontorättigheter på den här fliken.</li>
  </ol>
3. Kontrollera att lagringskontot som lagts till i kommandot **Azlog källa lägga till** anges när du kör kommandot **Azlog källistan**.
4. Gå till **Loggboken > Windows-loggar > programmet** om det finns några fel rapporterats från Azure logganalys-integration.


Om du stöter på problem under installationen och konfigurationen, öppnar du en [supportbegäran](../azure-supportability/how-to-create-azure-support-request.md)väljer **loggen Integration** som tjänsten som du begär support.

Ett annat alternativ är den [Azure Log Integration MSDN-Forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Här kan gemenskapen stödja varandra med frågor, svar, tips och råd om hur du hämtar mest av Azure Log-integrering. Dessutom övervakar Azure Log-integrering teamet den här forum och hjälp när de kan.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Log-integrering finns i följande dokument:

* [Microsoft Azure Log-integrering för Azure loggar](https://www.microsoft.com/download/details.aspx?id=53324) – Download Center för ytterligare information, systemkrav, och installera instruktioner på Azure logganalys-integration.
* [Introduktion till Azure logganalys integration](security-azure-log-integration-overview.md) – det här dokumentet ger en introduktion till Azure logganalys integration, de viktigaste funktionerna och hur det fungerar.
* [Samarbeta konfigurationssteg](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – det här blogginlägget visar hur du konfigurerar Azure logganalys-integrering ska fungera med partnerlösningar Splunk, HP ArcSight och IBM QRadar. Det är vår aktuella vägledning om hur du konfigurerar SIEM-komponenter. Kontrollera med leverantören SIEM först för ytterligare information.
* [Azure logganalys Integration vanliga frågor (FAQ)](security-azure-log-integration-faq.md) – det här avsnittet får du svar frågor om Azure logganalys-integration.
* [Integrera Security Center aviseringar med Azure logga Integration](../security-center/security-center-integrating-alerts-with-log-integration.md) – det här dokumentet beskrivs hur du synkroniserar varningsmeddelanden, tillsammans med virtuella säkerhetshändelser samlas in av Azure-diagnostik och Azure aktivitetsloggar med din logganalys eller SIEM-lösning.
* [Nya funktioner för Azure-diagnostik och Azure-granskningsloggarna](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – det här blogginlägget ger en introduktion till Azure-granskningsloggarna och andra funktioner som hjälper dig få insikter om hur Azure-resurser.
