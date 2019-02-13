---
title: Azure Stack lägga till noder i skala | Microsoft Docs
description: Lägga till noder om du vill skala enheter i Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 4c9c4bce0b63bd6d45522e2e069d1ae884916e09
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172407"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack"></a>Lägga till ytterligare skalning enhet noder i Azure Stack

Azure Stack-operatörer kan öka den totala kapaciteten i en befintlig enhet för skala genom att lägga till en ytterligare fysisk dator. Den fysiska datorn kallas också en scale unit-nod. Varje ny skala enhet nod som du lägger till måste vara homogena i CPU-typ, minne, och disknumret och storleken på noderna som redan finns i skalningsenheten.

Om du vill lägga till en nod för skala enhet, fungerar i Azure Stack och kör verktyg från utrustning tillverkaren (OEM). OEM-verktyg körs på maskinvara livscykel värden (HLH) för att se till att den nya fysiska datorn matchar firmware samma nivå som befintliga noderna.

Följande flöde diagram visar den allmänna processen att lägga till en nod för skala enhet.

![Lägg till skala enhet flöde](media/azure-stack-add-scale-node/add-node-flow.png) &#42; *om maskinvaruleverantören OEM utfärdar fysisk server rack placering och uppdaterar den inbyggda programvaran varierar beroende på ditt supportkontrakt för.*

Åtgärden att lägga till en ny nod kan ta flera timmar eller dagar att slutföra.

> [!Note]  
> Försök inte någon av följande åtgärder när en Lägg till enhet noden åtgärden pågår redan:
>
>  - Uppdatera Azure Stack
>  - Rotera certifikat
>  - Stoppa Azure Stack
>  - Reparera skala enhet nod


## <a name="add-scale-unit-nodes"></a>Lägg till skala enhet noder

Följande steg är en översikt över hur du lägger till en nod. Inte Följ dessa steg utan att första referera till dokumentationen för expansion OEM-tillverkarens kapacitet.

1. Placera den nya fysiska servern racken och kabelansluta den på rätt sätt. 
2. Aktivera fysiska växelportar och justera åtkomstkontrollistor (ACL) om det är tillämpligt.
3. Konfigurera rätt IP-adress i huvudkortshantering (BMC) och tillämpa alla BIOS-inställningarna per din OEM-tillverkarens dokumentation.
4. Gäller den aktuella baslinjen för inbyggd programvara för alla komponenter med hjälp av verktygen som tillhandahålls av tillverkaren som körs på HLH.
5. Kör sedan Lägg till nod i Azure Stack-administratörsportalen.
6. Verifiera att lägga till noden åtgärden lyckas. Du gör detta genom att kontrollera den [ **Status** av Skalningsenheten](#monitor-add-node-operations). 

## <a name="add-the-node"></a>Lägga till noden

Du kan använda administrationsportalen eller PowerShell för att lägga till nya noder. Lägga till noden åtgärden först lägger till den nya noden i skala enhet som tillgängliga beräkningskapacitet och utökas lagringskapaciteten automatiskt. Kapaciteten expanderar automatiskt eftersom Azure Stack är ett hyperkonvergerat system där *compute* och *storage* skalas tillsammans.

### <a name="use-the-admin-portal"></a>Använda administrationsportalen

1. Logga in på administrationsportalen för Azure Stack som en Azure Stack-operator.
2. Gå till **+ skapa en resurs** > **kapacitet** > **skala enhet noden**.
   ![Skala enhet nod](media/azure-stack-add-scale-node/select-node1.png)
3. På den **Lägg till nod** väljer den *Region*, och välj sedan den *skalningsenhet* att du vill lägga till noden. Ange den *BMC IP-adress* för skala enhet nod som du lägger till. Du kan bara lägga till en nod i taget.
   ![Lägg till nodinformation](media/azure-stack-add-scale-node/select-node2.png)
 

### <a name="use-powershell"></a>Använd PowerShell

Använd den **New AzsScaleUnitNodeObject** cmdlet för att lägga till en nod.  

Innan du använder något av följande exempelskript för PowerShell, ersätter du värdena *nodnamn* och *IP-adresser* med värden från Azure Stack-miljön.

  > [!Note]  
  > När du namnger en nod måste namnet vara mindre än 15 tecken långt. Du kan också använda ett namn som innehåller ett blanksteg eller inte innehåller något av följande tecken: `\`, `/`, `:`, `*`, `?`, `"`, `<`, `>`, `|`, `\`, `~`, `!`, `@`, `#`, `$`, `%`, `^`, `&`, `(`, `)`, `{`,` }`, `_`.

**Lägg till en nod:**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

## <a name="monitor-add-node-operations"></a>Övervaka lägga till noden åtgärder 
Du kan använda administrationsportalen eller PowerShell för att hämta status för åtgärden Lägg till noden. Lägg till nod-åtgärder kan ta flera timmar och dagar att slutföra.

### <a name="use-the-admin-portal"></a>Använda administrationsportalen 
För att övervaka att lägga till en ny nod, granska skalningsenheten i admin portal eller skala rotnodobjekt enhet. Du gör detta genom att gå till **regionshantering** > **Skalningsenheter**. Välj sedan den skalningsenhet eller skala enhet nod som du vill granska. 

### <a name="use-powershell"></a>Använd PowerShell
Status för skalningsenheten och skala enhet noder kan hämtas med hjälp av PowerShell på följande sätt:
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>Status för åtgärden Lägg till nod 
**För en skalningsenhet:**

|Status               |Beskrivning  |
|---------------------|---------|
|Körs              |Alla noder deltar aktivt i skalningsenheten.|
|Stoppad              |Skala enhet nod är antingen i eller kan inte nås.|
|Expandera            |En eller flera skala enhet noder läggs för närvarande som beräkningskapacitet.|
|Konfigurera lagring  |Beräkningskapaciteten har expanderats och lagringskonfigurationen körs.|
|Kräver reparation |Ett fel har identifierats som kräver en eller flera skala enhet noder repareras.|


**För en nod för skala enhet:**

|Status                |Beskrivning  |
|----------------------|---------|
|Körs               |Noden deltar aktivt i skalningsenheten.|
|Stoppad               |Noden är inte tillgänglig.|
|Lägger till                |Noden läggs aktivt till skalningsenheten.|
|Reparerar             |Noden är aktivt repareras.|
|Underhåll           |Noden har pausats och ingen aktiv användare arbetsbelastningen körs. |
|Kräver reparation  |Ett fel har identifierats som kräver noden repareras.|


## <a name="troubleshooting"></a>Felsökning
Här följer några vanliga problem som kan uppstå när du lägger till en nod. 

**Scenario 1:**  Lägg till enhet noden åtgärden misslyckas, men en eller flera noder visas med statusen stoppad.  
- Reparation: Använd reparationen för att reparera en eller flera noder. En enda reparationen kan köras i taget.

**Scenario 2:** En eller flera skala enhet noder har lagts till men utökad lagring misslyckades. I det här scenariot skalobjektet enhet noden rapporterar statusen körs men uppgiften konfigurera lagring är inte igång.  
- Reparation: Använda privileged slutpunkten för att granska hälsostatusen lagring genom att köra följande PowerShell-cmdlet:
  ```powershell
     Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
  ```
 
**Scenario 3:** Du har fått en avisering som anger storage skalbar-jobbet misslyckades.  
- Reparation: I det här fallet misslyckades konfigurationsåtgärd storage. Det här problemet måste du kontakta supporten.


## <a name="next-steps"></a>Nästa steg 
[Lägg till offentliga IP-adresser](azure-stack-add-ips.md) 
