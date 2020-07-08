---
title: inkludera fil
description: inkludera fil
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: include
ms.date: 08/09/2018
ms.author: vashan, cynthn, rajsqr
ms.custom: include file
ms.openlocfilehash: 57f557a812ec5e4eea75b76ca1394ca360a85d30
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187282"
---
Azure Virtual Machines (VM) går igenom olika tillstånd som kan kategoriseras i *etablerings* -och *energi* tillstånd. Syftet med den här artikeln är att beskriva dessa tillstånd och särskilt Markera när kunder faktureras för användning av instanser. 

## <a name="power-states"></a>Energinivåer

Energi läget motsvarar det senaste kända läget för den virtuella datorn.

![Diagram över virtuellt dator energi tillstånd](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
Följande tabell innehåller en beskrivning av varje instans tillstånd och indikerar om det debiteras för användning av instanser eller inte.

<table>
<tr>
<th>
Stat
</th>
<th>
Beskrivning
</th>
<th>
Fakturering av instans användning
</th>
</tr>
<tr>
<td>
<p><b>Startar</b></p>
</td>
<td>
<p>Den virtuella datorn startas.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Ej fakturerat</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Körs</b></p>
</td>
<td>
<p>Normalt arbets läge för en virtuell dator</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Debiteras</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Stoppas</b></p>
</td>
<td>
<p>Detta är ett över gångs tillstånd. När det är slutfört visas det som **stoppad**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Debiteras</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Stoppad</b></p>
</td>
<td>
<p>Den virtuella datorn har stängts av från gäst operativ systemet eller använder avstängnings läge-API: er.</p>
<p>Maskin varan är fortfarande allokerad till den virtuella datorn och den finns kvar på värden. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Fakturerad&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Frigör</b></p>
</td>
<td>
<p>Över gångs tillstånd. När den är klar visas den virtuella datorn som **frigjord**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Ej fakturerat&#42;</b></p>
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
<p><b>Ej fakturerat</b></p>
</td>
</tr>
</tbody>
</table>


&#42;vissa Azure-resurser, till exempel diskar och nätverk, debiteras. Program varu licenser på instansen debiteras inte.

## <a name="provisioning-states"></a>Etablerings tillstånd

Ett etablerings tillstånd är status för en användarinitierad, kontroll Plans åtgärd på den virtuella datorn. Dessa tillstånd är skilda från den virtuella datorns energi tillstånd.

- **Skapa** – Skapa virtuell dator.

- **Update** – uppdaterar modellen för en befintlig virtuell dator. Vissa ändringar av den virtuella datorn som inte är modeller, till exempel starta/starta om, omfattas också av Update.

- **Ta bort** – borttagning av virtuell dator.

- **Frigör** – är där en virtuell dator stoppas och tas bort från värden. Om du avallokerar en virtuell dator betraktas den som en uppdatering, så den visar etablerings tillstånd som är relaterade till uppdatering.



Här följer över gångs åtgärds tillstånd efter att plattformen har accepterat en åtgärd som initieras av användaren:

<br>

<table>
<tbody>
<tr>
<td width="162">
<p><b>Tillstånd</b></p>
</td>
<td width="366">
<p>Description</p>
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
<p><b>Tas bort</b></p>
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
<p><b>Etablerings tillstånd för operativ system</b></p>
</td>
<td width="366">
<p>Om en virtuell dator skapas med en operativ system avbildning och inte med en specialiserad avbildning kan följande under tillstånd observeras:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; Den virtuella datorn körs och installationen av gäst operativ systemet pågår. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; Status för kort livs längd. Den virtuella datorn övergår snabbt till **lyckad** om inga tillägg behöver installeras. Det kan ta tid att installera tillägg. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Obs!</b>OS-etableringen kan övergå till **fel** om ett operativ system fel uppstår eller om operativ systemet inte installeras i tid. Kunderna debiteras för den distribuerade virtuella datorn i infrastrukturen.</p>
</td>
</tr>
</table>


När åtgärden har slutförts övergår den virtuella datorn till något av följande tillstånd:

- **Lyckades** – åtgärderna som initierades av användaren har slutförts.

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

 

- **Misslyckades** – representerar en misslyckad åtgärd. Läs fel koderna för att få mer information och möjliga lösningar.

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



## <a name="vm-instance-view"></a>Vy för VM-instans

Instans visnings-API: et tillhandahåller information om den virtuella datorns körnings tillstånd. Mer information finns i dokumentationen för [Virtual Machines-instans Visa](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) API.

Azures resurs Utforskaren innehåller ett enkelt användar gränssnitt för att visa den virtuella datorn som kör tillstånd: [Resursläsaren](https://resources.azure.com/).

Etablerings tillstånd visas på VM-egenskaper och instans visning. Energi spar lägen är tillgängliga i instans visning av virtuell dator. 

