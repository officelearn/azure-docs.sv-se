---
title: Kom igång med Azure Log-integrering | Microsoft Docs
description: Lär dig mer om att installera tjänsten Azure Log-integrering och integrera loggar från Azure Storage, Azure-granskningsloggarna och aviseringar i Azure Security Center.
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
ms.date: 06/07/2018
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 5aab890340fcdd87e1b3788d8bcca903c43da1da
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235754"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure Log-integrering med Azure diagnostikloggning och vidarebefordran av Windows-händelser


>[!IMPORTANT]
> Funktionen Azure logganalys integration att bli inaktuell av 2019-06/01. Att kommer inaktiveras AzLog hämtningar av 27 Jun 2018. För information om vad du gör glidande vidarebefordra granskning efter [Använd Azure Övervakaren för integrering med SIEM-verktyg](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Du bör endast använda Azure logganalys integration om en [Azure-Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) anslutningen är inte tillgänglig från leverantören säkerhetsincident och händelsen Management (SIEM).

Azure Log-integrering tillgängliggör Azure loggar till din SIEM så att du kan skapa en enhetlig säkerhet instrumentpanel för alla tillgångar.
Mer information om status för en Azure-Monitor koppling försäljaren SIEM.

> [!IMPORTANT]
> Om ditt primära intresse samlar in loggar för virtuell dator, inkluderar det här alternativet i sitt lösning i de flesta SIEM-leverantörer. Med SIEM leverantörens connector är alltid bra alternativ.

Den här artikeln hjälper dig att komma igång med Azure Log-integrering. Den fokuserar på att installera tjänsten Azure Log-integrering och integrera tjänsten med Azure-diagnostik. Tjänsten Azure Log-integrering sedan samlar in Windows-händelseloggen information från Windows-säkerhetshändelse kanal från virtuella datorer i en Azure-infrastruktur som en tjänst. Detta liknar *vidarebefordran av händelser* som du kan använda i ett lokalt system.

> [!NOTE]
> Integrera utdata från Azure Log-integrering med en SIEM görs genom SIEM sig själv. Mer information finns i [integrera Azure Log-integrering med lokala SIEM](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/).

Tjänsten Azure Log-integrering körs på en fysisk eller en virtuell dator som kör Windows Server 2008 R2 eller senare (Windows Server 2016 eller Windows Server 2012 R2 är att föredra).

En fysisk dator kan köra lokalt eller på en värd plats. Om du väljer att köra tjänsten Azure Log-integrering på en virtuell dator, den virtuella datorn kan inte finnas lokalt eller i ett offentligt moln, exempelvis Microsoft Azure.

Den fysiska eller virtuella datorn som kör tjänsten Azure Log Integration kräver nätverksanslutning till det offentliga Azure-molnet. Den här artikeln innehåller information om konfigurationen.

## <a name="prerequisites"></a>Förutsättningar

Som minimum krävs installerar Azure Log-integrering följande:

* En **Azure-prenumeration**. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* En **lagringskonto** som kan användas för Windows Azure Diagnostics (BOMULLSTUSS) loggning. Du kan använda en förkonfigurerad storage-konto eller skapa ett nytt lagringskonto. Senare i den här artikeln beskriver vi hur du konfigurerar storage-konto.

  > [!NOTE]
  > Beroende på ditt scenario kanske inte ett lagringskonto krävs. Ett lagringskonto krävs för det Azure-diagnostik scenario som beskrivs i den här artikeln.

* **Två system**: 
  * En dator som kör tjänsten Azure Log-integrering. Den här datorn samlar in alla logginformation som senare importeras till din SIEM. Det här systemet:
    * Kan vara lokal eller värdbaserad i Microsoft Azure.  
    * Måste köra en x64 version av Windows Server 2008 R2 SP1 eller senare, och har Microsoft .NET 4.5.1 har installerats. Information om den installerade versionen av .NET finns [avgör vilka versioner av .NET Framework har installerats](https://msdn.microsoft.com/library/hh925568).  
    * Måste ha en anslutning till Azure Storage-konto som används för Azure-diagnostik loggning. Senare i den här artikeln beskriver vi hur du bekräfta anslutningar.
  * En dator som du vill övervaka. Detta är en virtuell dator som körs som en [virtuella Azure-datorn](../virtual-machines/virtual-machines-windows-overview.md). Loggningsinformation från den här datorn skickas till Azure Log Integration service-datorn.

Ta en titt på följande videoklipp för en snabb demonstration av hur du skapar en virtuell dator med hjälp av Azure portal:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Distributionsöverväganden

Du kan använda alla system som uppfyller de minsta operativsystemkraven under testningen. För en produktionsmiljö kan du planera för skala upp eller skala ut kräva belastningen.

Du kan köra flera instanser av tjänsten Azure Log-integrering. Du kan dock köra endast en instans av tjänsten per fysiska eller virtuella datorn. Dessutom kan du belastningsutjämna diagnostik för Azure storage-konton för BOMULLSTUSS. Antalet prenumerationer att förse instanserna är baserad på din kapacitet.

> [!NOTE]
> Vi har för närvarande inte specifika rekommendationer om när du ska skalas ut instanser av datorer i Azure Log-integrering (det vill säga datorer som kör tjänsten Azure Log-integrering) eller för storage-konton eller prenumerationer. Se skalning beslut baserat på dina synpunkter prestanda i dessa olika områden.

För att förbättra prestanda, har du också möjlighet att skala upp tjänsten Azure Log-integrering. Med hjälp av följande prestandamått kan du ändra storlek på de datorer som du väljer att köra tjänsten Azure Log-integrering:

* På en dator med 8-processor (core) kan en enda instans av Azure Log-integrering bearbeta cirka 24 miljoner händelser per dag (cirka 1 miljon händelser per timme).
* En instans av Azure Log-integrering kan bearbeta ungefär 1,5 miljoner händelser per dag (cirka 62,500 händelser per timme) på en dator i 4-processor (kärnor).

## <a name="install-azure-log-integration"></a>Installera Azure logganalys-integrering

Om du vill installera Azure Log-integrering hämta den [Azure Log-integrering](https://www.microsoft.com/download/details.aspx?id=53324) installationsfilen. Slutför installationen. Välj om du vill ange telemetri information till Microsoft.

Tjänsten Azure Log-integrering samlar in telemetridata från datorn som är installerad.  

Telemetridata som samlats in omfattar följande:

* Undantag som uppstår vid körning av Azure Log-integrering.
* Mått om antalet frågor och händelser som har bearbetats.
* Statistik om vilka Azlog.exe kommandoradsalternativ används. 

> [!NOTE]
> Vi rekommenderar att du tillåter att Microsoft samlar in telemetridata. Du kan stänga av insamling av telemetridata genom att avmarkera den **Tillåt Microsoft att samla in telemetridata** kryssrutan.
>

![Skärmbild av fönstret installation med telemetri kryssrutan är markerad](./media/security-azure-log-integration-get-started/telemetry.png)


Installationsprocessen beskrivs i följande video:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Efter installationen och validering steg

När du har slutfört grundinställning är du redo att utföra efter installation och validering steg:

1. Öppna PowerShell som administratör. Gå till C:\Program Files\Microsoft Azure Log-integrering.
2. Importera cmdlet: ar för Azure Log-integrering. Om du vill importera cmdlets, kör du skriptet `LoadAzlogModule.ps1`. Ange `.\LoadAzlogModule.ps1`, och tryck sedan på RETUR (Observera användningen av **.\\**  i det här kommandot). Du bör se något som liknar det som visas i följande bild:

  ![Skärmbild av utdata från kommandot LoadAzlogModule.ps1](./media/security-azure-log-integration-get-started/loaded-modules.png)
3. Konfigurera Azure Log-integrering för att använda en viss Azure-miljön. En *Azure-miljön* är den typ av Azure-molndatacenter som du vill arbeta med. Även om det finns flera Azure miljöer, relevanta alternativen är antingen **AzureCloud** eller **AzureUSGovernment**. Köra PowerShell som administratör, se till att du är i C:\Program Files\Microsoft Azure Log Integration\. Kör detta kommando:

  `Set-AzlogAzureEnvironment -Name AzureCloud` (för **AzureCloud**)
  
  Om du vill använda US Government Azure-molnet använder **AzureUSGovernment** för den **-namnet** variabeln. För närvarande stöds andra Azure-moln inte.  

  > [!NOTE]
  > Du får inte feedback när kommandot slutförs. 

4. Innan du kan övervaka ett system, måste namnet på lagringskontot som används för Azure-diagnostik. I Azure-portalen går du till **virtuella datorer**. Leta efter en Windows-dator som ska övervakas. I den **egenskaper** väljer **diagnostikinställningar**.  Markera **Agent**. Anteckna namnet på lagringskontot som har angetts. Du behöver det här namnet på tjänstkontot för ett senare steg.

  ![Skärmbild av Azure Diagnostics inställningsfönstret](./media/security-azure-log-integration-get-started/storage-account-large.png) 

  ![Skärmbild av aktivera övervakning gästnivå-knappen](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

  > [!NOTE]
  > Om övervakning inte är aktiverad när den virtuella datorn skapades, kan du aktivera det som visas i föregående bild.

5. Gå nu tillbaka till Azure Log-integrering-datorn. Kontrollera att du har anslutning till lagringskontot från systemet där du installerade Azure Log-integrering. Den dator som kör tjänsten Azure Log-integrering behöver åtkomst till lagringskontot för att hämta information som loggas av Azure-diagnostik på de övervakade systemen. Att verifiera anslutning: 
  1. [Hämta Azure Lagringsutforskaren](http://storageexplorer.com/).
  2. Installationen slutföras.
  3. När installationen är klar, Välj **nästa**. Lämna den **starta Microsoft Azure-Lagringsutforskaren** kryssrutan är markerad.  
  4. Logga in i Azure.
  5. Kontrollera att du kan se det lagringskonto som du har konfigurerat för Azure-diagnostik: 

   ![Skärmbild av storage-konton i Lagringsutforskaren](./media/security-azure-log-integration-get-started/storage-explorer.png)

  6. Några alternativ visas under storage-konton. Under **tabeller**, bör du se en tabell som kallas **WADWindowsEventLogsTable**.

  Om övervakning inte aktiverad när den virtuella datorn skapades, kan du aktivera den, enligt beskrivningen ovan.


## <a name="integrate-windows-vm-logs"></a>Integrera Windows VM-loggar

I det här steget konfigurerar du den dator som kör tjänsten Azure Log-integrering för att ansluta till lagringskontot som innehåller loggfilerna.

För att slutföra det här steget måste några saker:  
* **FriendlyNameForSource**: ett eget namn som du kan använda till att du har konfigurerat för den virtuella datorn att lagra information från Azure-diagnostik-lagringskontot.
* **StorageAccountName**: namnet på lagringskontot som du angav när du konfigurerade Azure-diagnostik.  
* **StorageKey**: lagringsnyckeln för lagringskontot där Azure diagnostikinformationen lagras för den virtuella datorn.  

Om du vill hämta nyckel för säkerhetslagring, gör du följande:
1. Gå till [Azure-portalen](http://portal.azure.com).
2. I navigeringsfönstret väljer **alla tjänster**.
3. I den **Filter** ange **lagring**. Markera **lagringskonton**.

  ![Skärmbild som visar lagringskonton i alla tjänster](./media/security-azure-log-integration-get-started/filter.png)

4. Storage-konton visas. Dubbelklicka på det konto som du tilldelat logga lagring.

  ![Skärmbild som visar en lista över storage-konton](./media/security-azure-log-integration-get-started/storage-accounts.png)

5. Under **Inställningar** klickar du på **Åtkomstnycklar**.

  ![Skärmbild som visar alternativet åtkomst nycklar i menyn](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)

6. Kopiera **key1**, och spara den på en säker plats som du kan använda för följande steg.
7. Öppna ett kommandotolksfönster som administratör på servern där du installerade Azure Log-integrering. (Glöm inte att öppna ett kommandotolksfönster som administratör och PowerShell inte).
8. Gå till C:\Program Files\Microsoft Azure logganalys-Integration.
9. Kör det här kommandot: `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
  Exempel:
  
  `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

  Lägg till prenumerations-ID i det egna namnet om du vill ska visas i XML prenumerations-ID:

  `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
  Exempel:
  
  `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Upp till 60 minuter och sedan visa händelser som hämtas från lagringskontot. Om du vill visa händelser, i Azure Log-integrering, Välj **Loggboken** > **Windows-loggar** > **vidarebefordrade händelser**.

Följande videoklipp beskriver de föregående steg:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Om data inte visas i mappen vidarebefordrade händelser
Om data inte visas i mappen vidarebefordrade händelser efter en timme, kan du utföra följande steg:

1. Kontrollera den dator som kör tjänsten Azure Log-integrering. Bekräfta att den har åtkomst till Azure. Om du vill testa anslutning i en webbläsare, försök att gå till den [Azure-portalen](http://portal.azure.com).
2. Kontrollera att användarkontot Azlog har skrivbehörighet för mappen users\Azlog.
  1. Öppna Utforskaren.
  2. Gå till C:\users.
  3. Högerklicka på C:\users\Azlog.
  4. Välj **säkerhet**.
  5. Välj **NT Service\Azlog**. Kontrollera behörigheterna för kontot. Om kontot saknas från den här fliken, eller om de behörigheter som krävs inte visas, kan du bevilja behörigheter på den här fliken.
3. När du kör kommandot `Azlog source list`, kontrollera att lagringen konto som har lagts till i kommandot `Azlog source add` visas i utdata.
4. Om några fel har rapporterats från tjänsten Azure Log-integrering, gå till **Loggboken** > **Windows-loggar** > **programmet**.

Om du stöter på problem under installationen och konfigurationen, kan du skapa en [supportbegäran](../azure-supportability/how-to-create-azure-support-request.md). Tjänsten, Välj **loggen Integration**.

Ett annat alternativ är den [Azure Log Integration MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). I MSDN-forum kan gemenskapen stödja genom att besvara frågor och dela tips och råd om hur du hämtar mest av Azure Log-integrering. Azure Log-integrering teamet övervakar även det här forumet. De hjälper när de kan.

## <a name="integrate-azure-activity-logs"></a>Integrera Azure aktivitetsloggar

Azure-aktivitetsloggen är en prenumerationslogg som ger inblick i prenumerationsnivån händelser som har inträffat i Azure. Detta omfattar en mängd data från Azure Resource Manager användningsdata till uppdateringar på händelser för Hälsotjänst. Azure Security Center-aviseringar ingår även i den här loggfilen.
> [!NOTE]
> Innan du försöker stegen i den här artikeln, bör du granska den [Kom igång](security-azure-log-integration-get-started.md) artikel och slutför stegen.

### <a name="steps-to-integrate-azure-activity-logs"></a>Stegen för att integrera Azure aktivitetsloggar

1. Öppna Kommandotolken och kör det här kommandot:  ```cd c:\Program Files\Microsoft Azure Log Integration```
2. Kör det här kommandot:  ```azlog createazureid```

    Det här kommandot uppmanas du att din Azure-inloggning. Kommandot skapar sedan ett Azure Active Directory tjänstens huvudnamn i Azure AD-klienter som är värdar för Azure-prenumerationer som den inloggade användaren är administratör, en medadministratör eller en ägare. Kommandot misslyckas om den inloggade användaren är endast gästanvändaren i Azure AD-klient. Autentisering till Azure sker via Azure AD. Skapa ett huvudnamn för tjänsten för Azure Log integrering skapar Azure AD-identitet som har behörighet att läsa från Azure-prenumerationer.
3.  Kör följande kommando för att auktorisera Azure Log-integrering tjänstens huvudnamn som skapats i föregående steg åtkomst till Läs aktivitetsloggen för prenumerationen. Du måste vara en ägare på prenumerationen för att köra kommandot.

    ```Azlog.exe authorize subscriptionId``` Exempel:

```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```
4.  Kontrollera följande mappar för att bekräfta att de Azure Active Directory audit JSON loggfilerna skapas i dem:
    - C:\Users\azlog\AzureResourceManagerJson
    - C:\Users\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> Specifika anvisningar för att hämta informationen i JSON-filerna till din säkerhetsinformation och Händelsehanteringssystem (SIEM), försäljaren SIEM.

Community-hjälp är tillgänglig via den [Azure Log Integration MSDN-Forum](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Detta forum kan andra i Azure Log-integrering webbgruppen att stödja varandra med frågor, svar, tips och råd. Dessutom övervakar det här forumet teamet Azure Log-integrering och hjälper när den kan.

Du kan även öppna en [supportbegäran](../azure-supportability/how-to-create-azure-support-request.md). Välj loggen Integration som tjänsten som du begär support.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Log-integrering finns i följande artiklar: innan du utför stegen i den här artikeln måste du granska få igång artikel och slutför stegen.

* [Azure Log-integrering för Azure loggar](https://www.microsoft.com/download/details.aspx?id=53324). Download Center innehåller information, systemkrav och Installationsinstruktioner för Azure Log-integrering.
* [Introduktion till Azure logganalys Integration](security-azure-log-integration-overview.md). Den här artikeln ger en introduktion till Azure Log Integration, de viktigaste funktionerna och hur det fungerar.
* [Samarbeta konfigurationssteg](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Det här blogginlägget visar hur du konfigurerar Azure Log-integrering ska fungera med partnerlösningar Splunk, HP ArcSight och IBM QRadar. Det beskriver våra aktuella vägledning om hur du konfigurerar SIEM-komponenter. Kontrollera med leverantören SIEM för ytterligare information.
* [Azure Log-integrering vanliga frågor (FAQ)](security-azure-log-integration-faq.md). Det här avsnittet får du svar vanliga frågor om Azure Log-integrering.
* [Integrera Azure Security Center-aviseringar med Azure Log-integrering](../security-center/security-center-integrating-alerts-with-log-integration.md). Den här artikeln visar hur du synkroniserar varningsmeddelanden och virtuella säkerhetshändelser som samlas in av Azure-diagnostik och Azure aktiviteten loggar. Du kan synkronisera loggar med hjälp av Azure logganalys eller SIEM-lösning.
* [Nya funktioner för Azure-diagnostik och Azure-granskningsloggar](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/). Det här blogginlägget ger en introduktion till Azure-granskningsloggarna och andra funktioner som kan hjälpa dig få insyn i hur Azure-resurser.
