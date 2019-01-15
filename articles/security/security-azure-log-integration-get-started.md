---
title: Kom igång med Azure Log Integration | Microsoft Docs
description: Lär dig hur du installerar Azure Log Integration-tjänsten och integrera loggar från Azure Storage, Azure-granskningsloggar och aviseringar i Azure Security Center.
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
ms.date: 01/14/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: bf39e22c0edc23d2e452d96103c8933e0a47af9e
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304419"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure Log Integration med Azure Diagnostisk loggning och vidarebefordran av Windows-händelser


>[!IMPORTANT]
> Funktionen Azure Log integration upphör att gälla genom 06/01/2019. AzLog hämtningar har inaktiverats på den 27 juni 2018. För information om vad du gör Flytta framåt granska inlägget [Använd Azure monitor för att integrera med SIEM-verktyg](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Du bör endast använda Azure-loggintegrering om en [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) anslutningen inte är tillgänglig från leverantören av säkerhetsincidenter och händelsehantering (SIEM).

Azure Log Integration gör Azure loggar tillgängliga för din siem-Server så att du kan skapa en enhetlig säkerhets-instrumentpanel för alla tillgångar.
Kontakta leverantören av SIEM för mer information om status för en Azure Monitor-koppling.

> [!IMPORTANT]
> Om din primära intresse samlar in virtuella datorer, loggar, inkludera detta alternativ i de flesta SIEM-leverantörer i sin lösning. Med hjälp av SIEM leverantörens connector är alltid bra alternativ.

Den här artikeln hjälper dig att komma igång med Azure Log Integration. Den fokuserar på installation av Azure Log Integration-tjänsten och integrera tjänsten med Azure Diagnostics. Tjänsten Azure Log Integration samlar sedan in Windows-händelseloggen information från Windows säkerhetshändelse kanal från virtuella datorer som distribueras i en Azure-infrastruktur som en tjänst. Detta liknar *vidarebefordran* som du kan använda i ett lokalt system.

> [!NOTE]
> Integrera utdata från Azure Log Integration med en SIEM görs med SIEM själva. Mer information finns i [integrera Azure Log Integration med lokala SIEM](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/).

Azure Log Integration-tjänsten körs på en fysisk eller en virtuell dator som kör Windows Server 2008 R2 eller senare (Windows Server 2016 eller Windows Server 2012 R2 är att föredra).

En fysisk dator kan köras lokalt eller på en värd plats. Om du väljer att köra tjänsten Azure Log Integration på en virtuell dator måste den virtuella datorn kan vara finnas lokalt eller i ett offentligt moln, till exempel i Microsoft Azure.

Fysisk eller virtuell dator som kör tjänsten Azure Log Integration kräver nätverksanslutning till det offentliga Azure-molnet. Den här artikeln innehåller information om konfigurationen som krävs.

## <a name="prerequisites"></a>Förutsättningar

Som minimum följer installerar Azure Log Integration du följande objekt:

* En **Azure-prenumeration**. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En **lagringskonto** som kan användas för Windows Azure Diagnostics SÄKERHETSSPECIFIKA loggar. Du kan använda en förkonfigurerad storage-konto eller skapa ett nytt lagringskonto. Senare i den här artikeln beskriver vi hur du konfigurerar storage-konto.

  > [!NOTE]
  > Beroende på ditt scenario kanske inte ett storage-konto krävs. Azure Diagnostics-scenariot som beskrivs i den här artikeln krävs ett storage-konto.

* **Två system**: 
  * En dator som kör tjänsten Azure Log Integration. Den här datorn samlar in alla logginformation som senare importeras till din SIEM. Det här systemet:
    * Kan finnas lokalt eller i Microsoft Azure.  
    * Måste köra en x64 version av Windows Server 2008 R2 SP1 eller senare, och har Microsoft .NET 4.5.1 har installerats. Information om den installerade versionen av .NET finns [avgöra vilka .NET Framework-versioner som installeras](https://msdn.microsoft.com/library/hh925568).  
    * Måste ha en anslutning till Azure Storage-kontot som har använt för Azure Diagnostics-loggning. Senare i den här artikeln beskriver vi hur du bekräfta anslutningar.
  * En dator som du vill övervaka. Det här är en virtuell dator som körs som en [Azure-dator](../virtual-machines/virtual-machines-windows-overview.md). Loggningsinformation från den här datorn skickas till Azure Log Integration service-datorn.

Ta en titt på följande videoklipp för en snabb demonstration av hur du skapar en virtuell dator med hjälp av Azure portal:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Distributionsöverväganden

Du kan använda alla system som uppfyller de minsta operativsystemkraven under testningen. För en produktionsmiljö kan belastningen måste du planera för att skala upp eller skala ut.

Du kan köra flera instanser av tjänsten Azure Log Integration. Du kan dock köra endast en instans av tjänsten per fysisk eller virtuell dator. Dessutom kan du belastningsutjämna Azure Diagnostics-lagringskonton för WAD. Antalet prenumerationer att förse instanserna baseras på din kapacitet.

> [!NOTE]
> Vi har för närvarande inga specifika rekommendationer om hur du skalar ut instanser av datorer i Azure Log Integration (det vill säga datorer som kör tjänsten Azure Log Integration), eller för storage-konton eller prenumerationer. Kontrollera skalning beslut baserat på dina prestanda observationer i var och en av dessa områden.

För att förbättra prestanda, har också möjlighet att skala upp tjänsten Azure Log Integration. Med hjälp av följande prestandamått kan du ändra storlek på de datorer som du vill köra Azure Log Integration-tjänsten:

* En enda instans av Azure Log Integration kan bearbeta ungefär 24 miljoner händelser per dag (cirka 1 miljon händelser per timme) på en dator med 8-processor (kärnor).
* En enda instans av Azure Log Integration kan bearbeta ungefär 1,5 miljoner händelser per dag (cirka 62,500 händelser per timme) på en 4-processor (kärna)-dator.

## <a name="install-azure-log-integration"></a>Installera Azure-Loggintegrering

Gå igenom Ställ in rutinen. Välj om du vill ange telemetriinformation till Microsoft.

Azure Log Integration-tjänsten samlar in telemetridata från den dator där den är installerad.  

Telemetridata som samlats in omfattar följande:

* Undantag som uppstår vid körning av Azure Log Integration.
* Mått om antalet frågor och händelser som bearbetas.
* Statistik om vilka Azlog.exe kommandoradsalternativ används. 

> [!NOTE]
> Vi rekommenderar att du tillåter att Microsoft samlar in telemetridata. Du kan stänga av insamling av telemetridata genom att avmarkera den **Tillåt Microsoft att samla in telemetridata** markerar du kryssrutan.
>

![Skärmbild av fönstret installation med kryssrutan telemetri markerad](./media/security-azure-log-integration-get-started/telemetry.png)


Installationsprocessen beskrivs i följande video:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Efter installation och verifiering

När du har slutfört grundinställning är du redo att utföra efter installation och verifiering steg:

1. Öppna PowerShell som administratör. Gå sedan till C:\Program Files\Microsoft Azure Log Integration.
2. Importera Azure Log Integration-cmdletar. Om du vill importera cmdletarna, kör du skriptet `LoadAzlogModule.ps1`. Ange `.\LoadAzlogModule.ps1`, och tryck sedan på RETUR (Observera användningen av **.\\**  i det här kommandot). Du bör se något som liknar det som visas i följande bild:

  ![Skärmbild av utdata från kommandot LoadAzlogModule.ps1](./media/security-azure-log-integration-get-started/loaded-modules.png)
3. Konfigurera Azure Log Integration för att använda en specifik Azure-miljön. En *Azure-miljön* är den typ av Azure-molndatacenter som du vill arbeta med. Även om det finns flera Azure-miljöer, för närvarande, relevanta alternativ är antingen **AzureCloud** eller **azureusgovernment eller**. Kör PowerShell som administratör, se till att du är i C:\Program Files\Microsoft Azure Log Integration\. Kör det här kommandot:

  `Set-AzlogAzureEnvironment -Name AzureCloud` (for **AzureCloud**)
  
  Om du vill använda US Government Azure-molnet använder **azureusgovernment eller** för den **-namnet** variabeln. För närvarande stöds andra Azure-moln inte.  

  > [!NOTE]
  > Du får inte feedback när kommandot lyckas. 

4. Innan du kan övervaka ett system, behöver du namnet på det lagringskonto som används för Azure-diagnostik. I Azure-portalen går du till **virtuella datorer**. Leta efter en Windows-dator som ska övervakas. I den **egenskaper** väljer **diagnostikinställningar**.  Välj **agenten**. Anteckna namnet på lagringskontot som har angetts. Du behöver det här kontonamnet för ett senare steg.

  ![Skärmbild av fönstret Azure Diagnostics-inställningar](./media/security-azure-log-integration-get-started/storage-account-large.png) 

  ![Skärmbild av aktivera övervakning på gästnivå-knappen](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

  > [!NOTE]
  > Om övervakning inte aktiverad när den virtuella datorn skapades, kan du aktivera den som visas i föregående bild.

5. Gå nu tillbaka till Azure Log Integration-datorn. Kontrollera att du har anslutning till lagringskontot från systemet där du installerade Azure Log Integration. Den dator som kör tjänsten Azure Log Integration behöver åtkomst till lagringskontot för att hämta information som loggas av Azure Diagnostics på alla övervakade system. För att verifiera anslutningen: 
  1. [Hämta Azure Storage Explorer](http://storageexplorer.com/).
  2. Slutföra installationen.
  3. När installationen är klar, väljer **nästa**. Lämna den **starta Microsoft Azure Storage Explorer** kryssrutan är markerad.  
  4. Logga in i Azure.
  5. Kontrollera att du kan se det lagringskonto som du har konfigurerat för Azure-diagnostik: 

   ![Skärmbild av storage-konton i Storage Explorer](./media/security-azure-log-integration-get-started/storage-explorer.png)

  6. Ett par alternativ visas under storage-konton. Under **tabeller**, bör du se en tabell med namnet **WADWindowsEventLogsTable**.

  Om övervakning inte aktiverad när den virtuella datorn skapades, kan du aktivera den, enligt beskrivningen ovan.


## <a name="integrate-windows-vm-logs"></a>Integrera Windows VM-loggar

I det här steget konfigurerar du den dator som kör tjänsten Azure Log Integration för att ansluta till lagringskontot som innehåller filerna.

För att slutföra det här steget behöver du några saker:  
* **FriendlyNameForSource**: Ett eget namn som du kan använda till att du har konfigurerat för den virtuella datorn att lagra information från Azure Diagnostics-lagringskontot.
* **StorageAccountName**: Namnet på lagringskontot som du angav när du konfigurerade Azure Diagnostics.  
* **StorageKey**: Lagringskontots åtkomstnyckel för lagringskontot där Azure Diagnostics-information lagras för den virtuella datorn.  

Utför följande steg för att hämta lagringsnyckeln:
1. Gå till [Azure-portalen](http://portal.azure.com).
2. I navigeringsfönstret väljer **alla tjänster**.
3. I den **Filter** anger **Storage**. Välj **lagringskonton**.

  ![Skärmbild som visar lagringskonton i alla tjänster](./media/security-azure-log-integration-get-started/filter.png)

4. En lista över storage-konton visas. Dubbelklicka på det konto som du tilldelats logga lagring.

  ![Skärmbild som visar en lista över storage-konton](./media/security-azure-log-integration-get-started/storage-accounts.png)

5. Under **Inställningar** klickar du på **Åtkomstnycklar**.

  ![Skärmbild som visar alternativet åtkomst nycklar i menyn](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)

6. Kopiera **key1**, och spara den på en säker plats som du kan komma åt för följande steg.
7. Öppna ett kommandotolksfönster som administratör på den server där du installerade Azure Log Integration. (Glöm inte att öppna ett kommandotolksfönster som administratör och inte PowerShell).
8. Gå till C:\Program Files\Microsoft Azure Log Integration.
9. Kör det här kommandot: `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
  Exempel:
  
  `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

  Lägg till prenumerations-ID till det egna namnet om du vill att prenumerations-ID visas i XML:

  `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
  Exempel:
  
  `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Vänta upp till 60 minuter och sedan visa de händelser som hämtas från lagringskontot. Om du vill visa händelser, i Azure Log Integration, Välj **Loggboken** > **Windows loggar** > **vidarebefordrade händelser**.

Följande videoklipp beskriver de föregående stegen:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Om data inte visas i mappen vidarebefordrade händelser
Om data inte visas i mappen vidarebefordrade händelser efter en timme, kan du utföra följande steg:

1. Kontrollera den dator som kör tjänsten Azure Log Integration. Bekräfta att den har åtkomst till Azure. Om du vill testa anslutningen, i en webbläsare, försök att gå till den [Azure-portalen](https://portal.azure.com).
2. Kontrollera att användarkontot Azlog har skrivbehörighet för mappen users\Azlog.
  1. Öppna Utforskaren.
  2. Gå till C:\users.
  3. Högerklicka på C:\users\Azlog.
  4. Välj **Security**.
  5. Välj **NT Service\Azlog**. Kontrollera behörigheter för kontot. Om kontot saknas från den här fliken, eller om de behörigheter som krävs inte visas, kan du bevilja behörigheterna på den här fliken.
3. När du kör kommandot `Azlog source list`, se till att lagringskontot som har lagts till i kommandot `Azlog source add` är listad i utdata.
4. Om du vill se om några fel har rapporterats från tjänsten Azure Log Integration, gå till **Loggboken** > **Windows loggar** > **program**.

Om du stöter på problem under installation och konfiguration, kan du skapa en [supportförfrågan](../azure-supportability/how-to-create-azure-support-request.md). Tjänsten, Välj **Loggintegrering**.

Ett annat alternativ är den [Azure Log Integration MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). I MSDN-forum ger communityn support genom att besvara frågor och dela tips och råd om hur du får ut mest av Azure Log Integration. Azure Log Integration-teamet övervakar även det här forumet. De hjälper att varje gång som de kan.

## <a name="integrate-azure-activity-logs"></a>Integrera Azure-aktivitetsloggar

Azure-aktivitetsloggen är en prenumerationslogg som ger insikt i händelser på prenumerationsnivå som har inträffat i Azure. Detta omfattar en mängd data från Azure Resource Manager driftdata uppdateringar på Service Health-händelser. Azure Security Center-aviseringar ingår även i den här loggen.
> [!NOTE]
> Innan du försöker utföra stegen i den här artikeln måste du granska den [börjar](security-azure-log-integration-get-started.md) artikel och slutför stegen.

### <a name="steps-to-integrate-azure-activity-logs"></a>Stegen för att integrera Azure-aktivitetsloggar

1. Öppna Kommandotolken och kör det här kommandot:  ```cd c:\Program Files\Microsoft Azure Log Integration```
2. Kör följande kommando:  ```azlog createazureid```

    Det här kommandot uppmanas du att Azure-autentiseringsuppgifter. Sedan skapar kommandot en Azure Active Directory-tjänstens huvudnamn i Azure AD-klienter som är värdar för de Azure-prenumerationer som den inloggade användaren är administratör, en delad administratör eller en ägare. Kommandot misslyckas om den inloggade användaren är bara en gästanvändare i Azure AD-klient. Autentisering till Azure görs via Azure AD. Skapa ett huvudnamn för tjänsten för Azure Log Integration skapar Azure AD-identitet som har behörighet att läsa från Azure-prenumerationer.
3.  Kör följande kommando för att ge Azure Log Integration-tjänstens huvudnamn som skapades i föregående steg-åtkomst till Läs aktivitetsloggen för prenumerationen. Du måste vara ägare för prenumerationen för att köra kommandot.

    ```Azlog.exe authorize subscriptionId``` Exempel:

```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```

4.  Kontrollera följande mappar för att bekräfta att Azure Active Directory granskningsloggfilerna JSON skapas i dem:
    - C:\Users\azlog\AzureResourceManagerJson
    - C:\Users\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> Kontakta leverantören av SIEM specifika anvisningar för att hämta informationen i JSON-filerna till din säkerhetsinformation och Händelsehanteringssystem (SIEM).

Community-hjälp är tillgänglig via den [MSDN-Forum för Azure Log Integration](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Detta forum kan andra i Azure Log Integration-webbgruppen för varandra med frågor, svar, tips och råd. Dessutom övervakar det här forumet Azure Log Integration-teamet och hjälper när det är möjligt.

Du kan även öppna en [supportförfrågan](../azure-supportability/how-to-create-azure-support-request.md). Välj Loggintegrering som tjänsten som du begär support.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Log Integration finns i följande artiklar: Innan du försöker utföra stegen i den här artikeln måste du granska Kom igång-artikel och slutför stegen.

* [Introduktion till Azure-Loggintegrering](security-azure-log-integration-overview.md). Den här artikeln ger en introduktion till Azure Log Integration, de viktigaste funktionerna och hur det fungerar.
* [Partner konfigurationssteg](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Det här blogginlägget visar hur du konfigurerar Azure Log Integration att arbeta med partnerlösningar Splunk, HP ArcSight och IBM QRadar. Det beskriver våra aktuella vägledning om hur du konfigurerar SIEM-komponenter. Kontrollera med leverantören SIEM för ytterligare information.
* [Vanliga frågor (och svar FAQ) om Azure Log Integration](security-azure-log-integration-faq.md). HÄR får du svar på vanliga frågor om Azure Log Integration.
* [Integrera Azure Security Center-aviseringar med Azure Log Integration](../security-center/security-center-integrating-alerts-with-log-integration.md). Den här artikeln visar hur du synkroniserar Security Center-aviseringar och VM-säkerhetshändelser som samlas in av Azure Diagnostics och Azure-aktivitetsloggar loggar. Du kan synkronisera loggarna genom att använda din Azure Log Analytics eller SIEM-lösning.
* [Nya funktioner för Azure Diagnostics och Azure-granskningsloggar](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/). Det här blogginlägget ger en introduktion till Azure-granskningsloggar och andra funktioner som kan hjälpa dig att få insyn i driften av dina Azure-resurser.
