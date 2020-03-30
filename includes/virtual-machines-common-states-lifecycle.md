---
title: ta med fil
description: ta med fil
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: include
ms.date: 08/09/2018
ms.author: vashan, cynthn, rajsqr
ms.custom: include file
ms.openlocfilehash: 57f557a812ec5e4eea75b76ca1394ca360a85d30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187282"
---
Virtuella Azure-datorer (VMs) går igenom olika tillstånd som kan kategoriseras i *etablerings-* och *energitillstånd.* Syftet med den här artikeln är att beskriva dessa tillstånd och specifikt markera när kunder faktureras för till exempel användning. 

## <a name="power-states"></a>Energinivåer

Energitillståndet representerar det senast kända tillståndet för den virtuella datorn.

![Diagram över vm-energitillstånd](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
Följande tabell innehåller en beskrivning av varje förekomsttillstånd och anger om det faktureras för instansanvändning eller inte.

<table>
<tr>
<th>
Status
</th>
<th>
Beskrivning
</th>
<th>
Fakturering för instansanvändning
</th>
</tr>
<tr>
<td>
<p><b>Start</b></p>
</td>
<td>
<p>DEN VIRTUELLA DATORN startar.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Faktureras inte</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Körs</b></p>
</td>
<td>
<p>Normalt arbetstillstånd för en virtuell dator</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Faktureras</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Stoppas</b></p>
</td>
<td>
<p>Detta är en övergångsstat. När den är klar visas den som **Stoppad**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Faktureras</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Stoppad</b></p>
</td>
<td>
<p>Den virtuella datorn har stängts av från gästoperativsystemet eller med PowerOff-API:erna.</p>
<p>Maskinvaran allokeras fortfarande till den virtuella datorn och den finns kvar på värden. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Faktureras&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Frigör</b></p>
</td>
<td>
<p>Övergångsstat. När den virtuella datorn är klar visas den som **deallocated**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Faktureras inte&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Frigjord</b></p>
</td>
<td>
<p>Den virtuella datorn har stoppats och tagits bort från värden. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Faktureras inte</b></p>
</td>
</tr>
</tbody>
</table>


&#42;Vissa Azure-resurser, till exempel diskar och nätverk, medför avgifter. Programvarulicenser på instansen medför inga avgifter.

## <a name="provisioning-states"></a>Etableringstillstånd

Ett etableringstillstånd är statusen för en användarinitierad kontrollplansåtgärd på den virtuella datorn. Dessa tillstånd är åtskilda från en virtuell dators energitillstånd.

- **Skapa** – skapande av virtuella datorer.

- **Uppdatering** – uppdaterar modellen för en befintlig virtuell dator. Vissa ändringar som inte är modelländringar på den virtuella datorn, till exempel Start/Omstart, uppdateras också.

- **Ta bort** – borttagning av virtuella datorer.

- **Deallocate** – är där en virtuell dator stoppas och tas bort från värden. Att frigöra en virtuell dator betraktas som en uppdatering, så den visar etableringstillstånd relaterade till uppdatering.



Här är övergångsåtgärden efter att plattformen har accepterat en användarinitierad åtgärd:

<br>

<table>
<tbody>
<tr>
<td width="162">
<p><b>Tillstånd</b></p>
</td>
<td width="366">
<p>Beskrivning</p>
</td>
</tr>
<tr>
<td width="162">
<p><b>Skapar</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating",<br>
 "level": "Info",<br>
 "displayStatus": "Creating"<br>
 }</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Uppdatera</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/updating",<br>
 "level": "Info",<br>
 "displayStatus": "Updating"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Ta bort</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/deleting",<br>
 "level": "Info",<br>
 "displayStatus": "Deleting"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Tillstånd för etablering av operativsystemet</b></p>
</td>
<td width="366">
<p>Om en virtuell dator skapas med en OS-avbildning och inte med en specialiserad avbildning kan följande undertillstånd observeras:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; Den virtuella datorn körs och installation av gäst-OS pågår. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; Kortlivade tillstånd. Den virtuella datorn övergår snabbt till **Framgång** om inte några tillägg behöver installeras. Det kan ta tid att installera tillägg. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Operativsystemets</b>etablering kan övergå till **Misslyckades** om det uppstår ett os-fel eller om operativsystemet inte installeras i tid. Kunder debiteras för den distribuerade virtuella datorn på infrastrukturen.</p>
</td>
</tr>
</table>


När åtgärden är klar övergår den virtuella datorn till något av följande tillstånd:

- **Lyckades** – de användarinitierade åtgärderna har slutförts.

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **Misslyckades** – representerar en misslyckad åtgärd. Se felkoderna för att få mer information och möjliga lösningar.

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>Vyn VM-instans

Instansvy API ger VM-körtillståndsinformation. Mer information finns i [api-dokumentationen för virtuell dator - instansvy.](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview)

Azure Resources Explorer tillhandahåller ett enkelt användargränssnitt för visning av den virtuella datorns körtillstånd: [Resource Explorer](https://resources.azure.com/).

Etableringstillstånd visas på vm-egenskaper och instansvy. Energitillstånd är tillgängliga i instansvy av virtuell dator. 

