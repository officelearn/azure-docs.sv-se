---
title: Så här tar du bort och exporterar personuppgifter från Azure DevTest Labs
description: Lär dig hur du tar bort och exporterar personuppgifter från Azure DevLast Labs-tjänsten för att stödja dina skyldigheter enligt den allmänna dataskyddsförordningen (GDPR).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: c87e2fb534480bbf9bbe625d67782e5a11eda18c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169689"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Exportera eller ta bort personuppgifter från Azure DevTest Labs
Den här artikeln innehåller steg för att ta bort och exportera personuppgifter från Azure DevTest Labs-tjänsten. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Vilka personuppgifter samlar DevTest Labs in?
DevTest Labs samlar in två huvudsakliga delar av personuppgifter från användaren. De är: användarens e-postadress och användarobjekt-ID. Den här informationen är viktig för att tjänsten ska kunna tillhandahålla funktioner i tjänsten till labbadministratörer och labbanvändare.

### <a name="user-email-address"></a>Användarens e-postadress
DevTest Labs använder användarens e-postadress för att skicka e-postmeddelanden med automatisk avstängning till labbanvändare. E-postmeddelandet meddelar användarna om att deras maskin stängs av. Användarna kan antingen skjuta upp eller hoppa över avstängningen om de vill göra det. Du konfigurerar e-postadressen på labbnivå eller på VM-nivå.

**Ställa in e-post på labbet:**

![Ställa in e-post på labbnivå](./media/personal-data-delete-export/lab-user-email.png)

**Ställa in e-post på den virtuella datorn:**

![Ställa in e-post på VM-nivå](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>ID för användarobjekt
DevTest Labs använder användarobjekt-ID för att visa kostnadstrender och kostnad per resursinformation per månad för labbadministratörer. Det gör det möjligt för dem att spåra kostnader och hantera trösklar för deras Lab. 

**Uppskattad kostnadstrend för den aktuella kalendermånaden:**
![Uppskattad kostnadstrend för den aktuella kalendermånaden](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Uppskattad kostnad per kostnad per månad per resurs:**
![Beräknad kostnad per resurs per månad](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Varför behöver vi dessa personuppgifter?
DevTest Labs-tjänsten använder personuppgifterna för operativa ändamål. Dessa data är avgörande för att tjänsten ska kunna leverera viktiga funktioner. Om du anger en bevarandeprincip på användarens e-postadress får labbanvändare inte e-postmeddelanden om automatisk avstängning i tid efter att deras e-postadress har tagits bort från vårt system. På samma sätt kan labbadministratören inte visa kostnadstrender och kostnad per resurs månad för datorer i sina labb om användarobjekt-ID:t tas bort baserat på en bevarandeprincip. Därför måste dessa data behållas så länge användarens resurs är aktiv i labbet.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Hur kan jag ha systemet för att glömma mina personuppgifter?
Som labbanvändare kan du göra det genom att ta bort motsvarande resurs i labbet om du vill att dessa personuppgifter ska tas bort. DevTest Labs-tjänsten anonymiserar de borttagna personuppgifterna 30 dagar efter att de har tagits bort av användaren.

Om du till exempel tar bort den virtuella datorn eller tar bort din e-postadress anonymiserar devtest labs-tjänsten dessa data 30 dagar efter att resursen har tagits bort. 30-dagars lagringsprincipen efter borttagningen är att se till att vi tillhandahåller en korrekt kostnadsprognos månad för månad till labbadministratören.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Hur kan jag begära export av mina personuppgifter?
Du kan exportera personliga och labbanvändningsdata med hjälp av Azure-portalen eller PowerShell. Data exporteras som två olika CSV-filer:

- **disks.csv** - innehåller information om diskarna som används av de olika virtuella datorerna
- **virtualmachines.csv** - innehåller information om de virtuella datorerna i labbet.

### <a name="azure-portal"></a>Azure Portal
Som labbanvändare kan du begära export på de personuppgifter som DevTest Labs-tjänsten lagrar. Om du vill begära en export navigerar du till alternativet **Personuppgifter** på **sidan Översikt i** ditt labb. Välj knappen **Begär export** startar skapandet av en nedladdningsbar Excel-fil i labbadministratörens lagringskonto. Du kan sedan kontakta labbadministratören för att visa dessa data.

1. Välj **Personuppgifter** på den vänstra menyn. 

    ![Sida med personuppgifter](./media/personal-data-delete-export/personal-data-page.png)
2. Välj den **resursgrupp** som innehåller labbet.

    ![Välj resursgrupp](./media/personal-data-delete-export/select-resource-group.png)
3. Välj **lagringskontot** i resursgruppen.
4. Välj **Blobbar**på sidan **Lagringskonto** .

    ![Markera panelen Blobbar](./media/personal-data-delete-export/select-blobs-tile.png)
5. Välj behållaren **labresourceusage** i listan över behållare.

    ![Välj blob-behållare](./media/personal-data-delete-export/select-blob-container.png)
6. Markera **mappen** som är uppkallad efter labbet. Du hittar **CSV-filer** för **diskar** och **virtuella datorer** i labbet i den här mappen. Du kan ladda ner dessa CSV-filer, filtrera innehållet för labbanvändaren som begär en åtkomst och dela det med dem.

    ![Ladda ner CSV-fil](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell"></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage="The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

De viktigaste komponenterna i ovanstående prov är:

- Kommandot Anropa-AzureRmResourceAction.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Två åtgärdsparametrar
    - **blobStorageAbsoluteSasUri** - Lagringskontot URI med SAS-token (Shared Access Signature). I PowerShell-skriptet kan det här värdet skickas in i stället för lagringsnyckeln.
    - **usageStartDate** - Startdatum för att hämta data, med slutdatum som det aktuella datumet då åtgärden utförs. Granulariteten är på dagnivå, så även om du lägger till tidsinformation ignoreras den.

### <a name="exported-data---a-closer-look"></a>Exporterade data - en närmare titt
Nu ska vi ta en närmare titt på de exporterade data. Som tidigare nämnts, när data har exporterats, kommer det att finnas två CSV-filer. 

**Virtualmachines.csv** innehåller följande datakolumner:

| Kolumnnamn | Beskrivning |
| ----------- | ----------- | 
| SubscriptionId | Prenumerationsidentifieraren som labbet finns i. |
| LabUId (på) | Unik GUID-identifierare för labbet. |
| LabName (LabName) | Labbets namn. |
| LabResourceId | Fullständigt kvalificerat labbresurs-ID. |
| ResourceGroupName | Namn på resursgruppen som innehåller den virtuella datorn | 
| ResourceId | Fullständigt kvalificerat resurs-ID för den virtuella datorn. |
| ResourceUId (På) | GUID för den virtuella datorn |
| Namn | Namn på virtuell dator. |
| CreatedTime | Den datumtid då den virtuella datorn skapades. |
| DeletedDate | Den datumtid då den virtuella datorn togs bort. Om den är tom har borttagningen inte skett ännu. |
| Resursägare | Ägare av den virtuella datorn. Om värdet är tomt är det antingen en anspråksbar virtuell dator eller skapad av ett huvudnamn för tjänsten. |
| PriserTier | Prisnivå för den virtuella datorn |
| Resursstatus | Tillgänglighetstillstånd för den virtuella datorn. Aktiv, om det fortfarande finns eller inaktiv, om den virtuella datorn har tagits bort. |
| ComputeResourceId | Fullständigt kvalificerad beräkningsresursidentifierare för virtuella datorer. |
| Kan begäras | Ange till true om den virtuella datorn är en anspråksbar virtuell dator | 
| MiljöDid | Den miljöresursidentifierare som den virtuella datorn skapades i. Den är tom när den virtuella datorn inte skapades som en del av en miljöresurs. |
| UtgångsdatumDatum | Utgångsdatum för den virtuella datorn. Den är inställd på tom, om ett utgångsdatum inte har angetts.
| GalleriImageReferenceVersion |  Version av vm-basavbildningen. |
| GalleriImageReferenceOffer | Erbjudande av vm-basavbildningen. |
| GalleriBildReferencePublisher | Utgivare av vm-basavbildningen. |
| GalleriImageReferenceSku | Sku för vm-basavbildningen |
| GalleriBildReferenceOsTyp | OS-typ av vm-basavbildning |
| CustomImageId | Fullständigt kvalificerat ID för den anpassade avbildningen av den virtuella datorns bas. |

Datakolumnerna i **disks.csv** visas nedan:

| Kolumnnamn | Beskrivning | 
| ----------- | ----------- | 
| SubscriptionId | ID för prenumerationen som innehåller labbet |
| LabUId (på) | GUID för labbet |
| LabName (LabName) | Labbets namn | 
| LabResourceId | Fullständigt kvalificerat resurs-ID för labbet | 
| ResourceGroupName | Namn på resursgruppen som innehåller labbet | 
| ResourceId | Fullständigt kvalificerat resurs-ID för den virtuella datorn. |
| ResourceUId (På) | GUID för den virtuella datorn |
 |Namn | Namnet på den bifogade disken |
| CreatedTime |Datum och tid då datadisken skapades. |
| DeletedDate | Datum och tid då datadisken togs bort. |
| Resursstatus | Resursens status. Aktiv, om resursen finns. Inaktiv, när den tas bort. |
| DiskBlobName | Blob-namn för datadisken. |
| DiskSizeGB | Storleken på datadisken. |
| DiskType | Typ av datadisk. 0 för Standard, 1 för Premium. |
| ArrenderadeByVmId | Resurs-ID för den virtuella dator som datadisken har kopplats till. |


> [!NOTE]
> Om du har att göra med flera labb och vill få övergripande information, de två viktigaste kolumnerna är **LabUID** och **ResourceUId**, som är de unika id:na över prenumerationer.

Exporterade data kan manipuleras och visualiseras med hjälp av verktyg, som SQL Server, Power BI, etc. Den här funktionen är särskilt användbar när du vill rapportera användning av ditt labb till din ledningsgrupp som kanske inte använder samma Azure-prenumeration som du gör.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Ange principer för ett labb](devtest-lab-get-started-with-lab-policies.md)
- [Vanliga frågor och svar](devtest-lab-faq.md)
