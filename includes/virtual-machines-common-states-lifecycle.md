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
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124995"
---
Azure-datorer (VM) som går igenom olika tillstånd kan grupperas i *etablering* och *power* tillstånd. Syftet med den här artikeln är att beskriva tillståndet och markera specifikt när kunder faktureras till exempel användning. 

## <a name="power-states"></a>Energinivåer

Energinivån representerar den senast kända statusen för den virtuella datorn.

![Diagram över VM power-tillstånd](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
I följande tabell innehåller en beskrivning av varje instanstillstånd och anger om debiteras för användningen av instanser eller inte.

<table>
<tr>
<th>
Status
</th>
<th>
Beskrivning
</th>
<th>
Fakturering för användning av instans
</th>
</tr>
<tr>
<td>
<p><b>Startar</b></p>
</td>
<td>
<p>Virtuell dator startas.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Inte faktureras</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Körs</b></p>
</td>
<td>
<p>Normal fungerande tillstånd för en virtuell dator</p>
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
<p><b>Stoppar</b></p>
</td>
<td>
<p>Det här är ett övergående tillstånd. När du är klar visas den som **stoppad**.</p>
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
<p>Den virtuella datorn har stängts ned från i gästoperativsystemet eller med hjälp av the PowerOff APIs.</p>
<p>Maskinvara fortfarande är tilldelad till den virtuella datorn och den kvar på värden. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Billed&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Frigörs</b></p>
</td>
<td>
<p>Övergångsstadium. När du är klar visas den virtuella datorn som **frigjord**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Inte faktureras&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Har frigjorts</b></p>
</td>
<td>
<p>Den virtuella datorn har stoppats och tas bort från värden. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Inte faktureras</b></p>
</td>
</tr>
</tbody>
</table>


&#42;Vissa Azure-resurser, till exempel diskar och nätverk, avgifter. Programvarulicenser på instansen avgifter.

## <a name="provisioning-states"></a>Etableringsstatus

Etableringsläge är statusen för en användarinitierad, kontrollplanet åtgärd på den virtuella datorn. Dessa tillstånd är separat från av en virtuell dator.

- **Skapa** – skapa en virtuell dator.

- **Uppdatera** – uppdaterar modellen för en befintlig virtuell dator. Vissa icke-modellen ändrar till virtuell dator som Start/omstart omfattas också uppdatering.

- **Ta bort** – borttagning av virtuell dator.

- **Frigör** – är där en virtuell dator stoppas och tas bort från värden. Frigörs en virtuell dator betraktas som en uppdatering, så den visar etablering tillstånd som handlar om att uppdatera.



Här är övergående åtgärden tillstånd när plattformen har godkänt en åtgärd som initieras av användaren:

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
<p><b>Skapa</b></p>
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
<p><b>Uppdaterar</b></p>
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
<p><b>Tar bort</b></p>
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
<p><b>OS-etablering tillstånd</b></p>
</td>
<td width="366">
<p>Om en virtuell dator har skapats med en OS-avbildning och inte med en specialiserad avbildning, kan följande deltillstånd observeras:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; den virtuella datorn körs och installationen av gäst-OS pågår. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; tillfällig tillstånd. Den virtuella datorn övergår snabbt till **lyckades** såvida inte några tillägg måste vara installerade. Installera tillägg kan ta tid. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Obs!</b> OS-etableringen kan övergå till **misslyckades** om det uppstår ett OS-fel eller Operativsystemet inte installeras i tid. Kunder kommer att faktureras för den distribuerade virtuella datorn på infrastrukturen.</p>
</td>
</tr>
</table>


När åtgärden har slutförts, övergår den virtuella datorn i något av följande tillstånd:

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

 

- **Det gick inte** – representerar en misslyckad åtgärd. Referera till felkoder för att få mer information och möjliga lösningar.

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



## <a name="vm-instance-view"></a>Instansvy för virtuell dator

Instansvy API innehåller VM-Körstatus information. Mer information finns i den [VM - instansvyn](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) API-dokumentationen.

Azure resurser explorer ger ett enkelt gränssnitt för att visa den virtuella datorn körs: [Resursläsaren](https://resources.azure.com/).

Etablering tillstånd är synliga på VM-egenskaperna och instansvyn. Energinivåer är tillgängliga i instansvyn för VM. 

