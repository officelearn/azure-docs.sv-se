<properties
    pageTitle="Automatisera borttagning av resursgrupper | Microsoft Azure"
    description="PowerShells arbetsflödesversion av ett Azure Automation-scenario, inklusive runbooks för att ta bort alla resursgrupper i din prenumeration."
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""
    />
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="magoedte"/>


# Azure Automation-scenario – automatiserad borttagning av resursgrupper

Många kunder skapar flera resursgrupper där vissa är avsedda för hantering av produktionsprogram och andra kan är avsedda att användas som utvecklings-, test- och mellanlagringsmiljöer. Att automatisera distributionen av dessa resurser är en sak, men att kunna ta en resursgrupp ur drift genom att klicka på en knapp är en helt annan sak.  Det här är ett perfekt användningsområde för Automation och ett ypperligt tillfälle att strömlinjeforma vanliga arbetsuppgifter inom administration. Detta är också användbart om du arbetar med en Azure-prenumeration som har en utgiftsgräns som fastställts i ett medlemserbjudande som till exempel MSDN eller programmet Microsoft Partner Network Cloud Essentials. 

Det här scenariot baseras på en PowerShell-runbook och är utformat för att ta bort en eller flera resursgrupper som du anger i prenumerationshanteringen.  Runbook har stöd för testning innan du utför åtgärden, vilket är standardinställningen.  På det här sättet kan du inte radera den av misstag innan du har kontrollerat att du verkligen vill slutföra åtgärden.   

## Hämta scenariot

Det här scenariot består av en PowerShell-runbook som du kan ladda ned från [PowerShell-galleriet](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript) eller också kan du importera det direkt från [Runbook-galleriet](automation-runbook-gallery.md) i Azure-portalen.<br><br> 

Runbook | Beskrivning|
----------|------------|
Remove-ResourceGroup | Tar bort en eller flera Azure-resursgrupper och dess resurser från prenumerationen.  
<br>
Följande indataparametrar har definierats för denna runbook:

Parameter | Beskrivning|
----------|------------|
NameFilter (obligatorisk) | Gör det möjligt att ange ett namnfilter som begränsar de resursgrupper som markeras för borttagning. Du kan använda flera värden med hjälp av en kommaavgränsad lista.<br>Filtret är inte skiftlägeskänsligt och kommer att matcha alla resursgrupper som innehåller strängen i fråga.|
PreviewMode (valfritt) | Kör en runbook för att se vilka resursgrupper som kan komma att tas bort, men vidta inga åtgärder.<br>Standardvärdet är **Sant** för att undvika oönskad borttagning av en eller flera resursgrupper som godkänts för en runbook.  

## Installera och konfigurera det här scenariot

### Krav

Denna runbook autentiserar med hjälp av ett [Kör som-konto i Azure](automation-sec-configure-azure-runas-account.md).    

### Installera och publicera runbooks

När du har hämtat en runbook importerar du den med hjälp av anvisningarna i [Importing runbook procedures (Procedurer för att importera runbooks)](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-Azure-Automation).  Publicera en runbook när den har importerats till ditt Automation-konto.


## Med hjälp av en runbook

Följande steg hjälper dig genom körning av denna runbook och visar dig hur allt fungerar.  Vi kommer endast att testa en runbook i det här exemplet – vi tar inte ta bort resursgruppen på riktigt.  

1. Öppna Automation-kontot i Azure-portalen och klicka på **Runbooks**.
2. Välj den runbook som heter **Remove-ResourceGroup** och klicka på **Starta**.
3. När du startar en runbook öppnas bladet **Starta Runbook** och du får möjlighet att konfigurera följande värden för parametrarna.  Ange namnet på en eller flera resursgrupper i din prenumeration som du vill testa, och som inte skulle ställa till alltför stor skada om du skulle råka radera.<br> ![Parametrar för Remove-ResouceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-input-parameters.png)
    
    >[AZURE.NOTE] Kontrollera att alternativet **Previewmode** är inställt på **Sant** för att förhindra att du tar bort de markerade resursgrupperna av misstag.  **Observera** att denna runbook inte tar bort resursgruppen som innehåller Automation-kontot som kör denna runbook.  

4. När du har konfigurerat parametervärdena klickar du på **OK** för att ställa denna runbook i kö för körning.  

Om du vill visa information om runbook-jobbet **Remove-ResourceGroup (Ta bort resursgrupp)** i Azure-portalen väljer du rutan **Jobb** för runbook. Jobbsammanfattningen visar indataparametrarna och utdataströmmen samt allmän information om jobbet och eventuella undantag som uppstår.<br> ![Status för runbook-jobbet Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png).

**Jobbsammanfattning** innehåller meddelanden från strömmarna utdata, varning och fel. Välj **Utdata** om du vill visa detaljerade resultat från runbook-körningen.<br> ![Utdataresultat från Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png) 

## Nästa steg

- Kom igång med att skapa egna runbooks genom att läsa [Creating or importing a runbook in Azure Automation (Skapa eller importera en runbook med Azure Automation)](automation-creating-importing-runbook.md)
- Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)


<!--HONumber=Sep16_HO4-->


