---
title: "Kontinuerlig leverans för cloud services med TFS i Azure | Microsoft Docs"
description: "Lär dig hur du ställer in kontinuerlig leverans för appar i Azure-molnet. Kodexempel för kommandoraden MSBuild-satser och PowerShell-skript."
services: cloud-services
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 4f3c93c6-5c82-4779-9d19-7404a01e82ca
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/12/2017
ms.author: kraigb
ms.openlocfilehash: 0979722b9ec715e91825c7aba74657451df6e83f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="continuous-delivery-for-cloud-services-in-azure"></a>Kontinuerlig leverans för molntjänster i Azure
Processen som beskrivs i den här artikeln visar hur du ställer in kontinuerlig leverans för appar i Azure-molnet. Med processen kan du automatiskt skapa paket och distribuera dem till Azure varje gång du checkar in ny kod. Paketet build-processen som beskrivs i den här artikeln motsvarar den **paketet** kommandot i Visual Studio och publishing stegen är likvärdiga med den **publicera** i Visual Studio.
Artikeln täcker de metoder som du använder för att skapa en build-server med kommandoradsverktyget MSBuild-satser och Windows PowerShell-skript och visar även hur kan du konfigurera Visual Studio Team Foundation Server - teamet skapa definitioner för att använda MSBuild-kommandon och PowerShell-skript. Processen kan anpassas för build-miljön och Azure mål-miljöer.

Du kan också använda Visual Studio Team Services, en version av TFS som finns i Azure för att göra det enklare. 

Innan du börjar bör du publicera programmet från Visual Studio.
Detta säkerställer att alla resurser är tillgängliga och initierad när du försöker automatisera publikationen.

## <a name="1-configure-the-build-server"></a>1: Konfigurera Build-servern
Innan du kan skapa ett Azure-paket med hjälp av MSBuild, måste du installera nödvändig programvara och verktyg på build-servern.

Visual Studio behöver inte installeras på build-servern. Om du vill använda Byggtjänsten för Team Foundation för att hantera build-servern, följ instruktionerna i den [Byggtjänsten för Team Foundation] [ Team Foundation Build Service] dokumentation.

1. Build-servern installerar den [.NET Framework 4.5.2][.NET Framework 4.5.2], som innehåller MSBuild.
2. Installera senaste [Azure redigering av verktyg för .NET](https://azure.microsoft.com/develop/net/).
3. Installera den [Azure-bibliotek för .NET](http://go.microsoft.com/fwlink/?LinkId=623519).
4. Kopiera filen Microsoft.WebApplication.targets från en installation av Visual Studio till build-servern.

   På en dator med Visual Studio installerat kan den här filen finns i katalogen C:\\Program Files(x86)\\MSBuild\\Microsoft\\VisualStudio\\v14.0\\WebApplications. Du bör kopiera den till samma katalog på build-servern.
5. Installera den [Azure Tools för Visual Studio](https://www.visualstudio.com/features/azure-tools-vs.aspx).

## <a name="2-build-a-package-using-msbuild-commands"></a>2: skapa ett paket med hjälp av MSBuild-kommandon
Det här avsnittet beskriver hur du skapar ett MSBuild-kommando som skapar ett Azure-paket. Kör det här steget på build-servern för att kontrollera att allt är korrekt konfigurerat och att kommandot MSBuild utför vad du vill göra. Du kan antingen lägga till den här kommandoraden befintliga build-skript på build-servern eller använda kommandoraden i en TFS skapa Definition, enligt beskrivningen i nästa avsnitt. Mer information om kommandoradsparametrar och MSBuild finns [MSBuild Kommandoradsreferens](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

1. Om Visual Studio är installerat på build-server, leta upp och välj **Kommandotolken Visual Studio** i den **Visual Studio Tools** -mappen i Windows.

   Om Visual Studio inte är installerat på build-servern, öppna en kommandotolk och kontrollera att MSBuild.exe är tillgängligt på sökvägen. MSBuild installeras med .NET Framework i sökvägen % WINDIR %\\Microsoft.NET\\Framework\\*Version*. Till exempel om du vill lägga till MSBuild.exe i PATH-miljövariabeln när du har .NET Framework 4 har installerats, skriver du följande kommando i Kommandotolken:

       set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"
2. I Kommandotolken, navigera till mappen som innehåller filen Azure-projekt som du vill skapa.
3. Kör MSBuild med de/target: publicera alternativet som i följande exempel:

       MSBuild /target:Publish

   Det här alternativet kan vara förkortas /t: publicera. Alternativet /t:Publish i MSBuild ska inte förväxlas med publicera kommandon som är tillgängliga i Visual Studio när du har installerat Azure SDK. /T: publicera alternativet endast versioner Azure-paket. Den distribuerar inte paket som kommandona publicera i Visual Studio.

   Alternativt kan ange du projektets namn som en MSBuild-parameter. Om inget anges används den aktuella katalogen. Mer information om MSBuild kommandoradsalternativ finns [MSBuild Kommandoradsreferens](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).
4. Leta upp utdata. Som standard det här kommandot skapar en katalog i förhållande till rotmappen för projektet som *ProjectDir*\\bin\\*Configuration*\\app.publish\\. När du skapar ett Azure-projekt kan skapa du två filer, paketfilen sig själv och tillhörande konfigurationsfilen:

   * Project.cspkg
   * ServiceConfiguration. *TargetProfile*.cscfg

   Som standard varje Azure-projekt inkluderar en service konfigurationsfilen (.cscfg-filen) för lokala (felsökning) versioner och ett annat för molnet (mellanlagring eller produktion) versioner, men du kan lägga till eller ta bort service configuration-filer efter behov. När du skapar ett paket i Visual Studio kan blir du ombedd vilka tjänstkonfigurationsfilen att inkludera tillsammans med paketet.
5. Ange tjänstens konfigurationsfil. När du skapar ett paket med hjälp av MSBuild med lokal tjänstkonfigurationsfilen som standard. Om du vill inkludera en annan tjänstkonfigurationsfil egenskapen TargetProfile för MSBuild-kommandot, som i följande exempel:

       MSBuild /t:Publish /p:TargetProfile=Cloud
6. Ange platsen för utdata. Ställa in sökvägen med hjälp av /p:PublishDir =*Directory* \\ alternativ, inklusive avslutande omvänt avgränsare, som i följande exempel:

       MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

   När du har skapats och testas en lämplig MSBuild-kommandorad för att bygga projektet och kombineras till ett Azure-paket, kan du lägga till den här kommandoraden build-skript. Om din build-servern använder anpassade skript, beror den här processen på specifika anpassade build-processen. Om du använder TFS som en kompileringsmiljö, kan du följa anvisningarna i nästa steg för att lägga till Azure-paketet build till build-processen.

## <a name="3-build-a-package-using-tfs-team-build"></a>3: skapa ett paket med TFS-teamet skapa
Om du har servern TFS (Team Foundation) har angetts som en build-styrenhet och build-server konfigurerad som en TFS build-dator och du kan du konfigurera en automatisk build för Azure-paket. Information om hur du konfigurerar och använder Team Foundation server som ett build-system finns [skala ut ditt system][Scale out your build system]. I synnerhet följande procedur förutsätter att du har konfigurerat build-server enligt beskrivningen i [distribuera och konfigurera en build-server][Deploy and configure a build server], skapas ett molntjänstprojekt och att du har skapat ett grupprojekt i grupprojektet.

Utför följande steg för att konfigurera TFS för att skapa Azure-paket:

1. I Visual Studio på utvecklingsdatorn på Visa-menyn väljer du **Team Explorer**, eller välj Ctrl +\\, Ctrl + M. I fönstret Team Explorer expanderar den **bygger** nod eller Välj den **bygger** , och väljer **ny skapa Definition**.

   ![Nytt skapa Definition alternativ][0]
2. Välj den **utlösaren** och ange önskade villkor när du vill att paketet skapas. Till exempel ange **kontinuerlig Integration** att skapa paketet när ett källkontroll checka in inträffar.
3. Välj den **inställningar för datakälla** fliken och kontrollera att din projektmapp ingår i den **kontroll källmapp** kolumn och status är **Active**.
4. Välj den **skapa standardvärden** och under Build-styrenhet, kontrollera namnet på build-server.  Dessutom väljer du alternativet **kopiera skapa utdata för följande avlämningsmappen** och ange önskade målplatsen.
5. Välj den **processen** fliken. På fliken processen väljer standardmallen under **skapa**, välj projektet om den inte redan är markerad och expandera den **Avancerat** i avsnittet den **skapa** avsnitt i rutnätet.
6. Välj **MSBuild-argument**, och ange lämpliga MSBuild kommandoradsargumenten som beskrivs i steg 2 ovan. Ange till exempel **/t: Publicera /p:PublishDir =\\\\minserver\\släpper\\**  att skapa ett paket och kopiera paketfilerna dit \\ \\minserver\\släpper\\:

   ![MSBuild-argument][2]

   > [!NOTE]
   > Kopiera filerna till en delad plats gör det enklare att distribuera paketen på utvecklingsdatorn manuellt.
7. Testa genomförandet av build-steg genom att söka i en ändring i projektet eller Köa en ny version. För att Köa en ny version i Team-Explorer högerklickar du på **alla skapa definitioner** och välj sedan **kön nya skapa**.

## <a name="4-publish-a-package-using-a-powershell-script"></a>4: publicera ett paket med hjälp av ett PowerShell-skript
Det här avsnittet beskriver hur du skapar ett Windows PowerShell-skript som ska publicera Cloud app paketet utdata till Azure med valfria parametrar. Det här skriptet kan anropas efter bygga steg i din anpassade build-automation. Det kan också anropas från processmall arbetsflödesaktiviteter i Visual Studio TFS Team skapa.

1. Installera den [Azure PowerShell-cmdlets] [ Azure PowerShell cmdlets] (v0.6.1 eller högre).
   Välj att installera som en snapin-modulen under installationsfasen cmdlet. Observera att den versionen som stöds officiellt ersätter den tidigare versionen som erbjuds via CodePlex, även om tidigare versioner har numrerade 2.x.x.
2. Börjar Azure PowerShell med hjälp av Start-menyn eller sidan. Om du startar i det här sättet kommer att laddas Azure PowerShell-cmdlets.
3. I PowerShell-Kommandotolken, kontrollera att PowerShell-cmdlets laddas genom att ange kommandot partiell `Get-Azure` och sedan trycka på TABB-tangenten för instruktionen slutfördes.

   Om du trycker på TABB-tangenten flera gånger, bör du se olika Azure PowerShell-kommandon.
4. Kontrollera att du kan ansluta till din Azure-prenumeration genom att importera din prenumerationsinformation från .publishsettings-fil.

   `Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings`

   Ange kommandot

   `Get-AzureSubscription`

   Detta visar information om din prenumeration. Kontrollera att allt är korrekt.
5. Spara skriptet mallen anges i slutet av den här artikeln till mappen skript c:\\skript\\WindowsAzure\\**PublishCloudService.ps1**.
6. Läs avsnittet Parametrar för skriptet. Lägg till eller ändra alla standardvärden. Dessa värden kan alltid åsidosättas genom att passera i explicit parametrar.
7. Se till att det är giltigt moln tjänsten och storage-konton skapas i din prenumeration kan vara mål med skriptet publicera. Storage-konto (blobblagring) används för att ladda upp och tillfälligt lagra distributionsfilen av paket och config medan distributionen skapas.

   * Om du vill skapa en ny molntjänst som du kan anropa den här skript eller använda den [Azure-portalen](https://portal.azure.com). Molntjänstnamnet kommer att användas som ett prefix i ett fullständigt kvalificerat domännamn och därför det måste vara unikt.

         New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
   * Om du vill skapa ett nytt lagringskonto som du kan anropa den här skript eller använda den [Azure-portalen](https://portal.azure.com). Lagringskontonamn används som ett prefix i ett fullständigt kvalificerat domännamn och därför det måste vara unikt. Du kan försöka med samma namn som Molntjänsten.

         New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
8. Anropa skriptet direkt från Azure PowerShell eller tråd in det här skriptet till din värd build automation efter paket-version.

   > [!IMPORTANT]
   > Skriptet kommer alltid att ta bort eller ersätta din befintliga distributioner som standard om de upptäcks. Detta är nödvändigt för att aktivera kontinuerlig leverans från automation när inga användare att fråga är möjligt.
   >
   >

   **Exempelscenario 1:** kontinuerlig distribution till mellanlagringsmiljön för en tjänst:

       PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

   Detta är vanligtvis följas upp av test kör verifieringen och en VIP-växling. VIP-växling kan göras den [Azure-portalen](https://portal.azure.com) eller med hjälp av cmdleten Move-distribution.

   **Exempelscenario 2:** kontinuerlig distribution till produktionsmiljön för en dedikerad test-tjänst

       PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

   **Fjärrskrivbord:**

   Om Fjärrskrivbord har aktiverats i ditt Azure projekt som du behöver utföra ytterligare enstaka steg för att säkerställa rätt Cloud Service certifikat har överförts till alla molntjänster som mål för det här skriptet.

   Leta upp certifikatet tumavtrycket värden som förväntades av rollerna. Tumavtryck för värden visas i avsnittet certifikat i molnet config-fil (d.v.s. ServiceConfiguration.Cloud.cscfg). Det är också synliga i dialogrutan konfiguration av fjärrskrivbord i Visual Studio när du visar alternativen och visa det valda certifikatet.

       <Certificates>
             <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
       </Certificates>

   Överför certifikat för fjärrskrivbord som ett enstaka installationen steg med hjälp av följande cmdlet-skript:

       Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

   Exempel:

       Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

   Du kan också exportera certifikatfilen PFX med privat nyckel och överför certifikat för varje mål cloud service med hjälp av den [Azure-portalen](https://portal.azure.com).

   <!---
   Fixing broken links for Azure content migration from ACOM to DOCS. I'm unable to find a replacement links, so I'm commenting out this reference for now. The author can investigate in the future. "Read the following article to learn more: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx.
   -->
   **Uppgradera jämfört med distributionen. Ta bort distributionen -\> ny distribution**

   Skriptet som standard utför en Uppgraderingsdistribution ($enableDeploymentUpgrade = 1) när ingen parameter som skickas eller värdet 1 skickas explicit. Detta har fördelen med att göra kortare tid än en fullständig distribution för enskild instanser. För instanser som kräver hög tillgänglighet som också har fördelen att lämna vissa instanser körs samtidigt som andra uppgraderas (går din uppdateringsdomän), plus din VIP tas inte bort.

   Uppgradera distributionen kan inaktiveras i skriptet ($enableDeploymentUpgrade = 0) eller genom att skicka *- enableDeploymentUpgrade 0* som en parameter som ändrar beteendet skriptet att först ta bort alla befintliga distributionen och sedan skapa en ny distribution.

   > [!IMPORTANT]
   > Skriptet kommer alltid att ta bort eller ersätta din befintliga distributioner som standard om de upptäcks. Detta är nödvändigt för att aktivera kontinuerlig leverans från automation om ingen användare/operator fråga är möjligt.
   >
   >

## <a name="5-publish-a-package-using-tfs-team-build"></a>5: publicera ett paket med TFS-teamet skapa
Det här valfria steget ansluter TFS Team bygga till det skript du skapade i steg 4, som hanterar publicering av paketet build till Azure. Detta innebär att ändra processmall som används av build-definition så att det körs en publicera aktivitet i slutet av arbetsflödet. Publicera körs aktiviteten Skicka parametrar från bygga din PowerShell-kommando. Utdata från MSBuild mål och publicera skript ska skickas till standard build-utdata.

1. Redigera ansvarar för att skapa Definition kontinuerlig distribution.
2. Välj den **processen** fliken.
3. Följ [instruktionerna](http://msdn.microsoft.com/library/dd647551.aspx) för att lägga till en aktivitet projekt för processmall build hämta standardmallen, lägga till den i projektet och kontrollera i. Ge processmall bygga ett nytt namn, till exempel AzureBuildProcessTemplate.
4. Gå tillbaka till den **processen** fliken och använda **Visa detaljer** att visa en lista över tillgängliga build processmallar. Välj den **nya...**  knappen och navigera till projektet du lagt till och checkats in. Leta upp den mall du just skapade och välj **OK**.
5. Öppna den valda mallen processen för redigering. Du kan öppna direkt i Arbetsflödesdesignern eller i XML-redigerare för att arbeta med XAML.
6. Lägg till följande lista över nya argument som separata poster på fliken argument i Arbetsflödesdesignern. Alla argument bör ha riktningen = i och Skriv = sträng. Dessa används för att flöda parametrar från build-definition i arbetsflödet, som sedan får användas för att anropa publicera skript.

       SubscriptionName
       StorageAccountName
       CloudConfigLocation
       PackageLocation
       Environment
       SubscriptionDataFileLocation
       PublishScriptLocation
       ServiceName

   ![Listan med argument][3]

   Motsvarande XAML ser ut så här:

       <Activity  _ />
         <x:Members>
           <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
           <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
           <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
           <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
           <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
           <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
           <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
           <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
           <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
           <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
           <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
           <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
           <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
           <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
           <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
           <x:Property Name="GetVersion" Type="InArgument(x:String)" />
           <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
           <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
           <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
           <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
           <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
           <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
           <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
           <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
           <x:Property Name="Environment" Type="InArgument(x:String)" />
           <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
           <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
           <x:Property Name="ServiceName" Type="InArgument(x:String)" />
         </x:Members>

         <this:Process.MSBuildArguments>
7. Lägg till en ny sekvens i slutet av körs på agenten:

   1. Starta genom att lägga till en aktivitet för instruktion om att söka efter en giltigt skriptfil. Ange villkoret till det här värdet:

          Not String.IsNullOrEmpty(PublishScriptLocation)
   2. Sedan fall i instruktionen om att lägga till en ny sekvensaktivitet. Ange visningsnamn Start publicera
   3. Med början publicera sekvens fortfarande markerat, Lägg till följande lista över nya variabler som separata poster på fliken variabler i Arbetsflödesdesignern. Alla variabler ska ha variabeltyp = sträng och omfånget = Start publicera. Dessa används för att flöda parametrar från build-definition i arbetsflödet, som sedan får användas för att anropa publicera skript.

      * SubscriptionDataFilePath av typen String
      * PublishScriptFilePath av typen String

        ![Nya variabler][4]
   4. Om du använder TFS 2012 eller tidigare, kan du lägga till en ConvertWorkspaceItem aktivitet i början av den nya sekvensen. Om du använder TFS 2013 eller senare kan du lägga till en GetLocalPath aktivitet i början av den nya sekvensen. För en ConvertWorkspaceItem ange egenskaper på följande sätt: riktning = ServerToLocal, visningsnamn = 'Konvertera publicera skript filename', indata = PublishScriptLocation, resultat = PublishScriptFilePath, arbetsytan = 'Arbetsytan'. Ange egenskapen IncomingPath till 'PublishScriptLocation' och 'PublishScriptFilePath' resultatet för en GetLocalPath aktivitet. Den här aktiviteten konverterar sökvägen till skriptet publicera från TFS serverplatser (om tillämpligt) till en lokal disk som standard sökväg.
   5. Om du använder TFS 2012 eller tidigare, kan du lägga till en annan ConvertWorkspaceItem aktivitet i slutet av den nya sekvensen. Riktning = ServerToLocal DisplayName = konvertera prenumerationen filnamn, indata = SubscriptionDataFileLocation, resultat = SubscriptionDataFilePath, arbetsytan = 'Arbetsytan'. Om du använder TFS 2013 eller senare, lägga till en annan GetLocalPath. IncomingPath = SubscriptionDataFileLocation, och resultatet = SubscriptionDataFilePath.
   6. Lägga till en InvokeProcess aktivitet i slutet av den nya sekvensen.
      Den här aktiviteten anropar PowerShell.exe med argument som skickades av Build-Definition.

      + Argument = String.Format (”-filen” ”{0}” ”- serviceName {1} - storageAccountName {2} - anges i packageLocation” ”{3}” ”- cloudConfigLocation” ”{4}” ”- subscriptionDataFile” ”{5}” ”- selectedSubscription {6}-miljön” ”{7}” ””, PublishScriptFilePath, ServiceName, StorageAccountName, anges i PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, miljön)
      + DisplayName = Execute publicera skript
      + FileName = ”PowerShell” (inklusive citattecken)
      + OutputEncoding = System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)
   7. I den **hantera standardutdata** avsnittet textruta för InvokeProcess kan du ange värdet för textrutan ”data”. Det här är en variabel för att lagra data standardutdata.
   8. Lägga till en WriteBuildMessage aktivitet precis nedanför den **hantera standardutdata** avsnitt. Ange vikten = 'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High' och meddelandet = ”data”. Detta säkerställer att standardutdata av skriptet kommer skrivs till build-utdata.
   9. I den **hantera Felutdata** avsnittet textruta för InvokeProcess kan du ange värdet för textrutan ”data”. Det här är en variabel för att lagra data standardfel.
   10. Lägga till en WriteBuildError aktivitet precis nedanför den **hantera Felutdata** avsnitt. Ställa in meddelandet = ”data”. Detta säkerställer att standardfel av skriptet kommer skrivs till build Felutdata.
   11. Korrigera eventuella fel identifieras med blå utropstecken märken. Hovra över utropstecken markerar att hämta en ledtråd om felet. Spara arbetsflöde om du vill rensa fel.

   Slutresultatet av arbetsflödesaktiviteter publicera ser ut så här i designer:

   ![Arbetsflödesaktiviteter][5]

   Slutresultatet av arbetsflödesaktiviteter publicera ser ut så här i XAML:

       <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
           <If.Then>
             <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
               <Sequence.Variables>
                 <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                 <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
               </Sequence.Variables>
               <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
               <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
               <mtbwa:InvokeProcess Arguments="[String.Format(&quot; -File &quot;&quot;{0}&quot;&quot; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation &quot;&quot;{3}&quot;&quot;&#xD;&#xA;            -cloudConfigLocation &quot;&quot;{4}&quot;&quot; -subscriptionDataFile &quot;&quot;{5}&quot;&quot;&#xD;&#xA;            -selectedSubscription {6} -environment &quot;&quot;{7}&quot;&quot;&quot;,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
                 <mtbwa:InvokeProcess.ErrorDataReceived>
                   <ActivityAction x:TypeArguments="x:String">
                     <ActivityAction.Argument>
                       <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                     </ActivityAction.Argument>
                     <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
                   </ActivityAction>
                 </mtbwa:InvokeProcess.ErrorDataReceived>
                 <mtbwa:InvokeProcess.OutputDataReceived>
                   <ActivityAction x:TypeArguments="x:String">
                     <ActivityAction.Argument>
                       <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                     </ActivityAction.Argument>
                     <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                   </ActivityAction>
                 </mtbwa:InvokeProcess.OutputDataReceived>
               </mtbwa:InvokeProcess>
             </Sequence>
           </If.Then>
         </If>
       </Sequence>
8. Spara build processen mall arbetsflödet och checka In den här filen.
9. Redigera build-definition (Stäng den om den redan är öppen), och välj den **ny** knappen om du inte ser den nya mallen i listan över processmallar ännu.
10. Ange parametern egenskapsvärden i avsnittet Övrigt på följande sätt:

    1. CloudConfigLocation = ”c:\\släpper\\app.publish\\ServiceConfiguration.Cloud.cscfg' *det här värdet är härledd från: ($PublishDir)ServiceConfiguration.Cloud.cscfg*
    2. Anges i PackageLocation = ' c:\\släpper\\app.publish\\ContactManager.Azure.cspkg' *det här värdet är härledd från: ($PublishDir)($ProjectName) .cspkg*
    3. PublishScriptLocation = ”c:\\skript\\WindowsAzure\\PublishCloudService.ps1'
    4. ServiceName = 'mycloudservicename' *använda lämpliga molntjänstnamnet här*
    5. Miljö = ”mellanlagring”
    6. StorageAccountName = 'mystorageaccountname' *använda lämpliga lagringskontonamnet här*
    7. SubscriptionDataFileLocation = ”c:\\skript\\WindowsAzure\\Subscription.xml'
    8. SubscriptionName = 'default'

    ![Parametern egenskapsvärden][6]
11. Spara ändringarna i Skapa-Definition.
12. Kön är en version för att köra båda paketet bygga och publicera. Om du har en utlösare som har angetts till kontinuerlig Integration, körs det här problemet på varje incheckning.

### <a name="publishcloudserviceps1-script-template"></a>PublishCloudService.ps1 skript mall
```
Param(  $serviceName = "",
        $storageAccountName = "",
        $packageLocation = "",
        $cloudConfigLocation = "",
        $environment = "Staging",
        $deploymentLabel = "ContinuousDeploy to $servicename",
        $timeStampFormat = "g",
        $alwaysDeleteExistingDeployments = 1,
        $enableDeploymentUpgrade = 1,
        $selectedsubscription = "default",
        $subscriptionDataFile = ""
     )


function Publish()
{
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue
    if ($a[0] -ne $null)
    {
        Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
    }
    #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
    if ($deployment.Name -ne $null)
    {
        switch ($alwaysDeleteExistingDeployments)
        {
            1
            {
                switch ($enableDeploymentUpgrade)
                {
                    1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
                        UpgradeDeployment
                    }
                    0  #Delete then create new deployment
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
                        DeleteDeployment
                        CreateNewDeployment

                    }
                } # switch ($enableDeploymentUpgrade)
            }
            0
            {
                Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
                exit
            }
        } #switch ($alwaysDeleteExistingDeployments)
    } else {
            CreateNewDeployment
    }
}

function CreateNewDeployment()
{
    write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

    $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"

    StartInstances
}

function UpgradeDeployment()
{
    write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

    # perform Update-Deployment
    $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
}

function DeleteDeployment()
{

    write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

    #WARNING - always deletes with force
    $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

    write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"

}

function StartInstances()
{
    write-progress -id 4 -activity "Starting Instances" -status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $runstatus = $deployment.Status

    if ($runstatus -ne 'Running')
    {
        $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
    }
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $oldStatusStr = @("") * $deployment.RoleInstanceList.Count

    while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
    {
        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
            }

            write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
            $i = $i + 1
        }

        sleep -Seconds 1

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    }

    $i = 1
    foreach ($roleInstance in $deployment.RoleInstanceList)
    {
        $instanceName = $roleInstance.InstanceName
        $instanceStatus = $roleInstance.InstanceStatus

        if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
        {
            $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
            Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
        }

        $i = $i + 1
    }

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $opstat = $deployment.Status

    write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
}

function AllInstancesRunning($roleInstanceList)
{
    foreach ($roleInstance in $roleInstanceList)
    {
        if ($roleInstance.InstanceStatus -ne "ReadyRole")
        {
            return $false
        }
    }

    return $true
}

#configure powershell with Azure 1.7 modules
Import-Module Azure

#configure powershell with publishsettings for your subscription
$pubsettings = $subscriptionDataFile
Import-AzurePublishSettingsFile $pubsettings
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

#set remaining environment variables for Azure cmdlets
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#main driver - publish & write progress to activity log
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."
```

## <a name="next-steps"></a>Nästa steg
För att aktivera fjärrfelsökning när du använder kontinuerlig leverans, se [aktivera fjärrfelsökning när kontinuerlig leverans för att publicera till Azure](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md).

[Team Foundation Build Service]: https://msdn.microsoft.com/library/ee259687.aspx
[.NET Framework 4]: https://www.microsoft.com/download/details.aspx?id=17851
[.NET Framework 4.5]: https://www.microsoft.com/download/details.aspx?id=30653
[.NET Framework 4.5.2]: https://www.microsoft.com/download/details.aspx?id=42643
[Scale out your build system]: https://msdn.microsoft.com/library/dd793166.aspx
[Deploy and configure a build server]: https://msdn.microsoft.com/library/ms181712.aspx
[Azure PowerShell cmdlets]: /powershell/azureps-cmdlets-docs
[the .publishsettings file]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0
[0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01bc.png
[2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
[3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
[4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
[5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
[6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
