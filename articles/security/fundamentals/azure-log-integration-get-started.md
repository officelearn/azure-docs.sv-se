---
title: Kom igång med Azure Log Integration | Microsoft Docs
description: Lär dig hur du installerar tjänsten Azure Log Integration och integrerar loggar från Azure Storage, Azures gransknings loggar och Azure Security Center aviseringar.
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 861a72b6abdc3d06cc4737630c92d34eee2e8545
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727675"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure Log Integration med Azure-diagnostik loggning och vidarebefordran av Windows-händelser


>[!IMPORTANT]
> Funktionen Azure logg integrering kommer att föråldras med 06/15/2019. AzLog hämtningar inaktiverades den 27 juni 2018. Information om vad du kan göra när du flyttar framåt finns i [använda Azure Monitor för att integrera med SIEM-verktyg](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Du bör endast använda Azure logg integrering om en [Azure Monitor](/azure/monitoring-and-diagnostics/monitoring-get-started) koppling inte är tillgänglig från din Siem-leverantör (security incident and Event Management).

Azure Log Integration gör Azure-loggar tillgängliga för din SIEM så att du kan skapa en enhetlig säkerhets instrument panel för alla dina till gångar.
Kontakta din SIEM-leverantör om du vill ha mer information om statusen för en Azure Monitor-anslutning.

> [!IMPORTANT]
> Om ditt primära intresse samlar in loggar för virtuella datorer inkluderar de flesta SIEM-leverantörer det här alternativet i sin lösning. Att använda SIEM-leverantörens anslutning är alltid det bästa alternativet.

Den här artikeln hjälper dig att komma igång med Azure Log Integration. Den fokuserar på att installera Azure Log Integration tjänsten och integrera tjänsten med Azure-diagnostik. Tjänsten Azure Log Integration samlar sedan in information om Windows-händelseloggen från Windows säkerhets händelse kanal från virtuella datorer som distribueras i en Azure-infrastruktur som en tjänst. Detta liknar *händelse vidarebefordring* som du kan använda i ett lokalt system.

> [!NOTE]
> Att integrera utdata från Azure Log Integration med en SIEM görs av själva SIEM. Mer information finns i [integrera Azure log integration med din lokala Siem](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/).

Azure Log Integrations tjänsten körs på antingen en fysisk eller en virtuell dator som kör Windows Server 2008 R2 eller senare (Windows Server 2016 eller Windows Server 2012 R2 rekommenderas).

En fysisk dator kan köras lokalt eller på en värd plats. Om du väljer att köra Azure Log Integration tjänsten på en virtuell dator kan den virtuella datorn finnas lokalt eller i ett offentligt moln, till exempel i Microsoft Azure.

Den fysiska eller virtuella dator som kör tjänsten Azure Log Integration måste ha nätverks anslutning till det offentliga Azure-molnet. Den här artikeln innehåller information om den konfiguration som krävs.

## <a name="prerequisites"></a>Förutsättningar

Att installera Azure Log Integration kräver minst följande objekt:

* En **Azure-prenumeration**. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Ett **lagrings konto** som kan användas för loggning av Windows-Azure-diagnostik (wad). Du kan använda ett förkonfigurerat lagrings konto eller skapa ett nytt lagrings konto. Senare i den här artikeln beskriver vi hur du konfigurerar lagrings kontot.

  > [!NOTE]
  > Beroende på ditt scenario kanske det inte krävs något lagrings konto. För Azure-diagnostik scenariot som beskrivs i den här artikeln krävs ett lagrings konto.

* **Två system**: 
  * En dator som kör Azure Log Integrations tjänsten. Den här datorn samlar in all logg information som senare importeras till din SIEM. Det här systemet:
    * Kan vara lokal eller värdbaserad i Microsoft Azure.  
    * Måste köra en x64-version av Windows Server 2008 R2 SP1 eller senare och ha Microsoft .NET 4.5.1 installerat. Information om hur du fastställer vilken .NET-version som är installerad finns i [avgöra vilka .NET Framework-versioner som är installerade](https://msdn.microsoft.com/library/hh925568).  
    * Måste ha anslutning till det Azure Storage konto som används för Azure-diagnostik loggning. Senare i den här artikeln beskriver vi hur du bekräftar anslutningen.
  * En dator som du vill övervaka. Det här är en virtuell dator som körs som en [virtuell Azure-dator](/azure/virtual-machines/virtual-machines-windows-overview). Loggnings informationen från den här datorn skickas till Azure Log Integration tjänst datorn.

För en snabb demonstration av hur du skapar en virtuell dator med hjälp av Azure Portal, ta en titt på följande videoklipp:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Distributionsöverväganden

Under testningen kan du använda alla system som uppfyller minimi kraven för operativ system. För en produktions miljö kan belastningen kräva att du planerar att skala upp eller ut ur drift.

Du kan köra flera instanser av tjänsten Azure Log Integration. Du kan dock bara köra en instans av tjänsten per fysisk eller virtuell dator. Dessutom kan du belastningsutjämna Azure-diagnostik lagrings konton för WAD. Antalet prenumerationer som ska tillhandahållas för instanserna baseras på din kapacitet.

> [!NOTE]
> För närvarande har vi inga rekommendationer om när du ska skala ut instanser av Azure Log Integration datorer (det vill säga datorer som kör tjänsten Azure Log Integration) eller för lagrings konton eller prenumerationer. Fatta skalnings beslut baserat på dina prestanda observationer i vart och ett av dessa områden.

För att förbättra prestanda kan du också välja att skala upp Azure Log Integration tjänsten. Följande prestanda mått kan hjälpa dig att ändra storlek på de datorer som du väljer att köra tjänsten Azure Log Integration:

* På en dator med 8 processorer (Core) kan en enda instans av Azure Log Integration bearbeta cirka 24 000 000 händelser per dag (cirka 1 000 000 händelser per timme).
* På en dator med 4 processorer (Core) kan en enda instans av Azure Log Integration bearbeta cirka 1 500 000 händelser per dag (cirka 62 500 händelser per timme).

## <a name="install-azure-log-integration"></a>Installera Azure Log Integration

Kör genom att ställa in rutinen. Välj om du vill lämna telemetri information till Microsoft.

Tjänsten Azure Log Integration samlar in telemetridata från den dator som den är installerad på.  

Telemetri data som samlas in innehåller följande:

* Undantag som inträffar vid körning av Azure Log Integration.
* Mått om antalet bearbetade frågor och händelser.
* Statistik om vilka kommando rads alternativ för Azlog. exe som används. 

> [!NOTE]
> Vi rekommenderar att du tillåter Microsoft att samla in telemetridata. Du kan stänga av insamling av telemetridata genom att avmarkera kryss rutan **Tillåt Microsoft att samla in telemetridata** .
>

![Skärm bild av installations fönstret med kryss rutan telemetri markerad](./media/azure-log-integration-get-started/telemetry.png)


Installations processen beskrivs i följande videoklipp:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Steg efter installationen och valideringen

När du har slutfört den grundläggande installationen är du redo att utföra åtgärder efter installation och verifiering:

1. Öppna PowerShell som administratör. Gå sedan till C:\Program Files\Microsoft Azure Log Integration.
2. Importera Azure Log Integration-cmdletar. Importera cmdletarna genom att köra skriptet `LoadAzlogModule.ps1`. Ange `.\LoadAzlogModule.ps1`och tryck sedan på RETUR (observera användningen av **.\\**  i det här kommandot). Du bör se något som liknar vad som visas i följande figur:

   ![Skärm bild av utdata från kommandot LoadAzlogModule. ps1](./media/azure-log-integration-get-started/loaded-modules.png)
3. Konfigurera sedan Azure Log Integration att använda en speciell Azure-miljö. En *Azure-miljö* är den typ av Azure Cloud-datacenter som du vill arbeta med. Även om det finns flera Azure-miljöer, är de relevanta alternativen antingen **AzureCloud** eller **azureusgovernment eller**. Kör PowerShell som administratör, se till att du är i C:\Program Files\Microsoft Azure log Integration\. Kör sedan följande kommando:

   `Set-AzlogAzureEnvironment -Name AzureCloud` (for **AzureCloud**)
  
   Använd **azureusgovernment eller** för variabeln **-Name** om du vill använda Azure-molnet för amerikanska myndigheter. För närvarande stöds inte andra Azure-moln.  

   > [!NOTE]
   > Du får ingen feedback när kommandot har slutförts. 

4. Innan du kan övervaka ett system behöver du namnet på det lagrings konto som används för Azure-diagnostik. I Azure Portal går du till **virtuella datorer**. Leta efter en virtuell Windows-dator som du vill övervaka. I avsnittet **Egenskaper** väljer du **diagnostikinställningar**.  Välj sedan **agent**. Anteckna det angivna lagrings konto namnet. Du behöver det här konto namnet för ett senare steg.

   ![Skärm bild av fönstret Azure-diagnostik inställningar](./media/azure-log-integration-get-started/storage-account-large.png) 

   ![Skärm bild av knappen Aktivera övervakning på gästnivå](./media/azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

   > [!NOTE]
   > Om övervakningen inte var aktive rad när den virtuella datorn skapades, kan du aktivera den så som visas i föregående bild.

5. Nu går du tillbaka till Azure Log Integration datorn. Kontrol lera att du har anslutning till lagrings kontot från systemet där du installerade Azure Log Integration. Datorn som kör Azure Log Integration tjänsten behöver åtkomst till lagrings kontot för att hämta information som loggas av Azure-diagnostik på alla övervakade system. Så här kontrollerar du anslutningen: 
   1. [Ladda ned Azure Storage Explorer](https://storageexplorer.com/).
   2. Slutför installationen.
   3. När installationen är färdig väljer du **Nästa**. Låt kryss rutan **starta Microsoft Azure Storage Explorer** vara markerad.  
   4. Logga in i Azure.
   5. Kontrol lera att du kan se det lagrings konto som du har konfigurerat för Azure-diagnostik: 

   ![Skärm bild av lagrings konton i Storage Explorer](./media/azure-log-integration-get-started/storage-explorer.png)

   1. Några alternativ visas under lagrings konton. Under **tabeller**bör du se en tabell med namnet **WADWindowsEventLogsTable**.

   Om övervakningen inte var aktive rad när den virtuella datorn skapades, kan du aktivera den, enligt beskrivningen ovan.


## <a name="integrate-windows-vm-logs"></a>Integrera Windows VM-loggar

I det här steget konfigurerar du datorn som kör Azure Log Integration-tjänsten för att ansluta till det lagrings konto som innehåller loggfilerna.

För att slutföra det här steget behöver du några saker:  
* **FriendlyNameForSource**: Ett eget namn som du kan använda på det lagrings konto som du har konfigurerat för den virtuella datorn för att lagra information från Azure-diagnostik.
* **StorageAccountName**: Namnet på det lagrings konto som du angav när du konfigurerade Azure-diagnostik.  
* **StorageKey**: Lagrings nyckeln för lagrings kontot där Azure-diagnostik information lagras för den här virtuella datorn.  

Utför följande steg för att hämta lagrings nyckeln:
1. Gå till [Azure-portalen](https://portal.azure.com).
2. I navigerings fönstret väljer du **alla tjänster**.
3. I rutan **filter** anger du **Storage**. Välj sedan **lagrings konton**.

   ![Skärm bild som visar lagrings konton i alla tjänster](./media/azure-log-integration-get-started/filter.png)

4. En lista över lagrings konton visas. Dubbelklicka på det konto som du tilldelade logg lagringen för.

   ![Skärm bild som visar en lista över lagrings konton](./media/azure-log-integration-get-started/storage-accounts.png)

5. Under **Inställningar** klickar du på **Åtkomstnycklar**.

   ![Skärm bild som visar alternativet åtkomst nycklar på menyn](./media/azure-log-integration-get-started/storage-account-access-keys.png)

6. Kopiera **KEY1**och spara den på en säker plats som du har åtkomst till i följande steg.
7. Öppna ett kommando tolks fönster som administratör på den server där du installerade Azure Log Integration. (Glöm inte att öppna ett kommando tolks fönster som administratör och inte PowerShell).
8. Gå till C:\Program\Microsoft Azure Log Integration.
9. Kör det här kommandot `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`:.
 
   Exempel:
  
   `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

   Om du vill att prenumerations-ID: t ska visas i händelse-XML lägger du till prenumerations-ID: t i det egna namnet:

   `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
   Exempel:
  
   `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Vänta upp till 60 minuter och Visa sedan de händelser som hämtas från lagrings kontot. Om du vill visa händelserna väljer du **Loggboken** > **Windows loggar** > **vidarebefordrade händelser**i Azure log integration.

I följande video beskrivs föregående steg:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Om data inte visas i mappen vidarebefordrade händelser
Om data inte visas i mappen vidarebefordrade händelser efter en timme slutför du följande steg:

1. Kontrol lera datorn som kör Azure Log Integrations tjänsten. Bekräfta att det går att komma åt Azure. Om du vill testa anslutningen går du till webbläsaren och försöker gå till [Azure Portal](https://portal.azure.com).
2. Kontrol lera att användar kontot Azlog har Skriv behörighet för mappen users\Azlog.
   1. Öppna Utforskaren.
   2. Gå till C:\Users.
   3. Högerklicka på C:\users\Azlog.
   4. Välj **säkerhet**.
   5. Välj **NT-Service\Azlog**. Kontrol lera behörigheterna för kontot. Om kontot saknas på den här fliken, eller om rätt behörigheter inte visas, kan du bevilja konto behörigheterna på den här fliken.
3. När du kör kommandot `Azlog source list`kontrollerar du att lagrings kontot som lades till i kommandot `Azlog source add` visas i utdata.
4. Om du vill se om några fel rapporteras från Azure log integration tjänsten går du till **Loggboken** > **Windows-loggar** >  **.**

Om du stöter på problem under installationen och konfigurationen kan du skapa en [support förfrågan](../../azure-supportability/how-to-create-azure-support-request.md). För tjänsten väljer du **logg integrering**.

Ett annat support alternativ är [Azure log integration MSDN-forumet](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). I MSDN-forumet kan communityn ge support genom att besvara frågor och dela tips och råd om hur du får ut mesta möjliga av Azure Log Integration. Azure Log Integrations teamet övervakar också det här forumet. De hjälper när de kan.

## <a name="integrate-azure-activity-logs"></a>Integrera Azure-aktivitets loggar

Azure-aktivitetsloggen är en prenumerationslogg som ger insikt i händelser på prenumerationsnivå som har inträffat i Azure. Detta omfattar en mängd data, från Azure Resource Manager användnings data till uppdateringar på Service Health händelser. De Azure Security Center aviseringarna ingår också i den här loggen.
> [!NOTE]
> Innan du försöker utföra stegen i den här artikeln måste du läsa artikeln [Kom igång](azure-log-integration-get-started.md) och slutföra stegen där.

### <a name="steps-to-integrate-azure-activity-logs"></a>Steg för att integrera Azure-aktivitets loggar

1. Öppna kommando tolken och kör det här kommandot:```cd c:\Program Files\Microsoft Azure Log Integration```
2. Kör det här kommandot:```azlog createazureid```

    Med det här kommandot uppmanas du att ange din Azure-inloggning. Kommandot skapar sedan ett Azure Active Directory tjänstens huvud namn i Azure AD-klienterna som är värdar för de Azure-prenumerationer där den inloggade användaren är administratör, en gemensam administratör eller en ägare. Kommandot fungerar inte om den inloggade användaren bara är en gäst användare i Azure AD-klienten. Autentisering till Azure görs via Azure AD. När du skapar ett huvud namn för tjänsten för Azure Log Integration skapas den Azure AD-identitet som har åtkomst att läsa från Azure-prenumerationer.
3. Kör följande kommando för att auktorisera Azure Log Integration tjänstens huvud namn som skapades i föregående steg åtkomst till Läs aktivitets loggen för prenumerationen. Du måste vara ägare till prenumerationen för att köra kommandot.

   ```Azlog.exe authorize subscriptionId```Exempel

   ```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```

4. Kontrol lera följande mappar för att kontrol lera att JSON-filerna för Azure Active Directory gransknings loggen skapas i dem:
   - C:\Users\azlog\AzureResourceManagerJson
   - C:\Users\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> Om du vill ha mer information om hur du hämtar informationen i JSON-filerna till SIEM-systemet (Security information and Event Management) kontaktar du din SIEM-leverantör.

Community-assistans är tillgängligt via [Azure log integration MSDN-forumet](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Det här forumet gör det möjligt för personer i Azure Log Integration community att stödja varandra med frågor, svar, tips och trick. Dessutom övervakar Azure Log Integration-teamet det här forumet och ser när det kan.

Du kan också öppna en [support förfrågan](../../azure-supportability/how-to-create-azure-support-request.md). Välj logg integrering som den tjänst som du begär support för.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Log Integration finns i följande artiklar: Innan du försöker utföra stegen i den här artikeln måste du läsa artikeln kom igång och slutföra stegen där.

* [Introduktion till Azure log integration](azure-log-integration-overview.md). Den här artikeln beskriver hur du Azure Log Integration, dess viktiga funktioner och hur det fungerar.
* [Konfigurations steg för partner](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Det här blogg inlägget visar hur du konfigurerar Azure Log Integration att arbeta med partner Solutions Splunk, HP ArcSight och IBM QRadar. Här beskrivs vår aktuella vägledning om hur du konfigurerar SIEM-komponenterna. Kontakta din SIEM-leverantör om du vill ha mer information.
* Vanliga [frågor och svar om Azure log integration vanliga frågor och svar](azure-log-integration-faq.md). Vanliga frågor och svar om Azure Log Integration.
* [Integrera Azure Security Center aviseringar med Azure log integration](/azure/security-center/security-center-integrating-alerts-with-log-integration). Den här artikeln visar hur du synkroniserar Security Center aviseringar och säkerhets händelser för virtuella datorer som samlas in av Azure-diagnostik och Azure aktivitets loggar. Du synkroniserar loggarna genom att använda Azure Monitor loggar eller SIEM-lösning.
* [Nya funktioner för Azure-diagnostik och gransknings loggar i Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/). I det här blogg inlägget får du en introduktion till Azures gransknings loggar och andra funktioner som kan hjälpa dig att få insikt i driften av dina Azure-resurser.
