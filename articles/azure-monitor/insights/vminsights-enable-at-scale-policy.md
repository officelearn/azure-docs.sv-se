---
title: Aktivera Azure Monitor för virtuella datorer med Azure Policy | Microsoft Docs
description: Den här artikeln beskrivs hur du aktiverar Azure Monitor för virtuella datorer för flera virtuella Azure-datorer eller VM-skalningsuppsättningar med Azure Policy.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: magoedte
ms.openlocfilehash: 9664ad5272ffeb55bd85e3d4c4f4b70d05e97702
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524152"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-policy"></a>Aktivera Azure Monitor för virtuella datorer (förhandsversion) med Azure Policy

Den här artikeln förklarar hur du aktiverar Azure Monitor för virtuella datorer (förhandsversion) för Azure-datorer eller VM-skalningsuppsättningar med Azure Policy. I slutet av den här processen kommer du har konfigurerats att aktivera Log Analytics och beroendeagenter, och identifiera virtuella datorer som inte är kompatibla.

Om du vill identifiera, hantera och aktivera Azure Monitor för virtuella datorer för alla dina Azure-datorer eller VM-skalningsuppsättningar kan använda du Azure Policy eller Azure PowerShell. Azure Policy är den rekommenderade metoden eftersom du kan hantera principdefinitioner för att effektivt reglera dina prenumerationer för att säkerställa konsekvent efterlevnad och automatisk aktivering av nyligen etablerade virtuella datorer. Dessa definitioner:

* Distribuera Log Analytics-agenten och beroendeagenten.
* Rapport om kompatibilitetsresultat.
* Åtgärda problemet för icke-kompatibla virtuella datorer.

Om du är intresserad av att åstadkomma detta med Azure PowerShell eller Azure Resource Manager-mall finns i [aktivera med hjälp av Azure PowerShell eller Resource Manager-mall](vminsights-enable-at-scale-powershell.md). 

## <a name="manage-policy-coverage-feature-overview"></a>Hantera Principtäckning funktionsöversikt

Ursprungligen har erfarenhet av Azure Policy för att hantera och distribuera principdefinitioner för Azure Monitor för virtuella datorer utförts uteslutande av Azure Policy. Med den **hantera Principtäckning** funktionen gör det enklare att identifiera, hantera och aktivera i stor skala den **aktivera Azure Monitor för virtuella datorer** initiativet, som innehåller principdefinitionerna Vi nämnde tidigare. Du kan komma åt den här nya funktionen från den **börjar** fliken i Azure Monitor för virtuella datorer genom att välja **hantera Principtäckning**. Det öppnar sidan Principtäckning för virtuella datorer. 

![Azure Monitor från virtuella datorer komma igång-fliken](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

Härifrån du kan kontrollera och hantera täckning för initiativet över dina hanteringsgrupper och prenumerationer, samt förstå hur många virtuella datorer finns på båda hanteringsgrupperna och prenumerationer och deras efterlevnadsstatus.   

![Azure Monitor för virtuella datorer Hantera princip](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Den här informationen är användbar för att planera och utföra styrningsåtgärder scenariot för Azure Monitor för virtuella datorer från en central plats. Medan Azure Policy ger en Kompatibilitetsvyn när en princip/initiativ har tilldelats ett scope, med den nya sidan kan du identifiera där principen/initiativet är inte tilldelad och tilldela den på plats. Alla åtgärder (tilldela, visa, redigera) omdirigera direkt till Azure Policy. Azure Monitor för virtuella datorer Principtäckning sidan är en utökad och integrerad upplevelse för endast initiativet **aktivera Azure Monitor för virtuella datorer**. 

Från den här sidan kan du också konfigurera Log Analytics-arbetsytan för Azure Monitor för virtuella datorer som utför följande:

- Installerar lösningar installerar Service Map och Infrastruktursinsikter
- Gör det möjligt för operativsystemet prestandaräknare som används av prestandadiagram, arbetsböcker, och den anpassade loggfrågor och aviseringar.

![Konfigurera arbetsyta för Azure Monitor för virtuella datorer](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Det här alternativet inte är relaterade till alla åtgärder och är tillgängliga för att tillhandahålla ett enkelt sätt att uppfylla den [krav](vminsights-enable-overview.md) krävs för att aktivera Azure Monitor för virtuella datorer.  

### <a name="what-information-is-available-on-this-page"></a>Vilken information är tillgänglig på den här sidan?
Följande tabell innehåller en detaljerad analys av vilken information som visas på sidan täckning och hur du tolkar det.

| Funktion | Beskrivning | 
|----------|-------------| 
| **Omfång** | Hanteringsgrupp och prenumerationer som du har eller ärvs åtkomst för att gruppera hierarkin med möjlighet att öka detaljnivån i hanteringen.|
| **Roll** | Din roll i omfånget kan vara läsare ägare eller deltagare. I vissa fall kan vara det tomma om du vill ange att du kan ha åtkomst till prenumerationen, men inte till hanteringsgruppen hör till. Informationen i andra kolumner varierar beroende på din roll eftersom den är nyckeln i avgör vilka data som du kan se och åtgärder som du kan utföra när det gäller tilldela initiativ/policy (ägare), redigerar dem eller genom att visa kompatibiliteten. |
| **Totalt antal virtuella datorer** | Antalet virtuella datorer under detta omfång. Det är en summa av virtuella datorer kapslat under prenumerationer och/eller underordnad hanteringsgrupp för en hanteringsgrupp. |
| **Tilldelningen täckning** | Procent av virtuella datorer som omfattas av initiativ/policy. |
| **Tilldelningsstatus** | Under den här kolumnen, hittar du information om statusen för din princip / initiativ tilldelning. |
| **Kompatibla virtuella datorer** | Antalet virtuella datorer som är kompatibla under princip/initiativ. För initiativet **aktivera Azure Monitor för virtuella datorer** detta är antalet virtuella datorer som har både Log Analytics-agenten och beroendeagenten. I vissa fall kan vara det tomma på grund av ingen tilldelning eller inga virtuella datorer eller inte tillräckliga behörigheter. Informationen under Kompatibilitetstillstånd. |
| **Efterlevnad** | Övergripande kompatibilitet talet är summan av olika resurser som är kompatibla dividerat med summan av alla distinkta resurser. |
| **Kompatibilitetstillstånd** | Information om kompatibilitetstillstånd för din princip / initiativ tilldelning.|

När du tilldelar initiativ/policy kan omfattningen som valts i tilldelningen omfång i listan eller en delmängd av den. Du kan till exempel har skapat en tilldelning för en prenumeration (principomfånget) och inte en hanteringsgrupp (täckning omfattning). I det här fallet, värdet för **tilldelning täckning** visar att det finns virtuella datorer i principen (eller initiativ) omfång dividerat med virtuella datorer i täckning omfång. I ett annat fall kan kanske du har exkluderat vissa virtuella datorer, resursgrupper eller en prenumeration från principomfånget. Om värdet är tomt, indikerar det att principen/initiativet finns inte eller har inte behörighet (information finns i tilldelningsstatus).

## <a name="enable-using-azure-policy"></a>Aktivera med hjälp av Azure Policy

Aktivera Azure Monitor för virtuella datorer med hjälp av Azure Policy i din klient:

- Tilldela initiativ till ett omfång: hanteringsgruppen, prenumeration eller resursgrupp
- Granska och åtgärda kompatibilitetsresultat

Mer information om hur du tilldelar Azure Policy finns [översikten över Azure Policy](../../governance/policy/overview.md#policy-assignment) och granska de [översikt över hanteringsgrupper](../../governance/management-groups/index.md) innan du fortsätter.

### <a name="policies-for-azure-vms"></a>Principer för virtuella Azure-datorer

Principdefinitioner för en Azure virtuell dator visas i följande tabell:

|Namn |Beskrivning |Type |
|-----|------------|-----|
|[Förhandsversion]: Aktivera Azure Monitor för virtuella datorer |Aktivera Azure Monitor för virtuella datorer (VM) i definitionsområdet (hanteringsgruppen, prenumeration eller resursgrupp). Tar Log Analytics-arbetsyta som en parameter. |Initiativ |
|[Förhandsversion]: Granska beroende Agentdistribution – VM Image (OS) inte finns i listan |Rapporterar virtuella datorer som icke-kompatibel om VM-avbildning (OS) har inte definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Granska Agentdistribution för Log Analytics – VM Image (OS) inte finns i listan |Rapporterar virtuella datorer som icke-kompatibel om VM-avbildning (OS) har inte definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Distribuera Beroendeagenten för virtuella Linux-datorer |Distribuera Beroendeagenten för virtuella Linux-datorer om VM-avbildning (OS) har definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Distribuera Beroendeagenten för virtuella Windows-datorer |Distribuera beroende Agent för Windows virtuella datorer om VM-avbildning (OS) har definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Distribuera Log Analytics-agenten för Linux-datorer |Distribuera Log Analytics-agenten för Linux-datorer om VM-avbildning (OS) har definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Distribuera Log Analytics-agenten för Windows-datorer |Distribuera Log Analytics-agenten för Windows virtuella datorer om VM-avbildning (OS) har definierats i listan och agenten är inte installerad. |Princip |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Principer för Azure VM-skalningsuppsättningar

Principdefinitioner för en Azure VM scale Sets finns i följande tabell:

|Namn |Beskrivning |Type |
|-----|------------|-----|
|[Förhandsversion]: Aktivera Azure Monitor för VM Scale Sets (VMSS) |Aktivera Azure Monitor för VM-skalningsuppsättningar i definitionsområdet (hantering av grupp, prenumeration eller resursgrupp). Tar Log Analytics-arbetsyta som parameter. Obs: om din scale set upgradePolicy är inställd på manuell, måste du tillämpa tillägget för alla virtuella datorer i uppsättningen genom att anropa uppgraderingen på dem. Detta kan vara az vmss update-instances i CLI. |Initiativ |
|[Förhandsversion]: Granska beroende Agentdistribution i VMSS – VM Image (OS) inte finns i listan |Rapporter VM-skalningsuppsättning som icke-kompatibel om VM-avbildning (OS) har inte definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Granska Log Analytics-Agentdistribution i VMSS – VM Image (OS) inte finns i listan |Rapporter VM-skalningsuppsättning som icke-kompatibel om VM-avbildning (OS) har inte definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Distribuera Beroendeagenten för Linux VM Scale Sets (VMSS) |Distribuera Beroendeagenten för Linux VM-skalningsuppsättningar om VM-avbildning (OS) har definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Distribuera Beroendeagenten för Windows VM Scale Sets (VMSS) |Distribuera beroende Agent för Windows VM scale sets för om VM-avbildning (OS) har definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Distribuera Log Analytics-agenten för Linux VM Scale Sets (VMSS) |Distribuera Log Analytics-agenten för Linux VM-skalningsuppsättningar om VM-avbildning (OS) har definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Distribuera Log Analytics-agenten för Windows VM Scale Sets (VMSS) |Distribuera Log Analytics-agenten för Windows VM scale sets för om VM-avbildning (OS) har definierats i listan och agenten är inte installerad. |Princip |

Fristående princip (ingår inte i initiativet) som beskrivs här:

|Namn |Beskrivning |Type |
|-----|------------|-----|
|[Förhandsversion]: Granska Log Analytics-arbetsyta för VM - matchningsfel för rapport |Rapporter för virtuella datorer som inkompatibla om de inte loggar till Log Analytics-arbetsytan som angetts i principen/initiativ tilldelningen. |Princip |

### <a name="assign-the-azure-monitor-initiative"></a>Tilldela Azure Monitor-initiativ
Utför följande steg för att skapa principtilldelningen från Azure Monitor för virtuella datorer Principtäckning sida. Information om hur du utför de här stegen finns i [skapa en principtilldelning från Azure portal](../../governance/policy/assign-policy-portal.md).

När du tilldelar initiativ/policy kan omfattningen som valts i tilldelningen omfattningen som anges här eller en delmängd av den. Du kan exempelvis ha skapat tilldelning för prenumerationen (principomfånget) och inte hanteringsgruppen (täckning omfattning) där fallet täckning % visar att det finns virtuella datorer i principen (eller initiativ) omfång dividerat med virtuella datorer i täckning omfång. I vissa fall kan kanske du har inte några virtuella datorer eller RGs eller prenumeration principomfånget.  Om det är tom, så indikerar det antingen principen / initiativ finns inte eller du har inte behörighet (info i tilldelningsstatus)  

1. Logga in på [Azure Portal](https://portal.azure.com).

2. I Azure-portalen väljer du **övervakaren**. 

3. Välj **virtuella datorer (förhandsversion)** i den **Insights** avsnittet.
 
4. Välj den **börjar** och på sidan sedan **hantera Principtäckning**.

5. Välj en hanteringsgruppen eller prenumerationen från tabellen och välj **omfång** genom att klicka på ellipsen (...).      I vårt exempel begränsar en omfattning principtilldelning till en gruppering av virtuella datorer för tvång.

6. På den **Azure Policy-tilldelning** sida som den är ifyllt med initiativet **aktivera Azure Monitor för virtuella datorer**. 
    Den **tilldelningsnamn** fylls automatiskt med initiativ namn, men du kan ändra den. Du kan också lägga till en valfri beskrivning. Den **tilldelats av** fylls automatiskt baserat på vem som är inloggad och värdet är valfritt.

7. (Valfritt) Om du vill ta bort en eller flera resurser från området, Välj **undantag**.

8. I den **Log Analytics-arbetsyta** nedrullningsbara lista för regionen som stöds, Välj en arbetsyta.

   > [!NOTE]
   > Om arbetsytan som är utanför omfattningen för tilldelningen, bevilja *Log Analytics Contributor* behörigheter till principtilldelningen huvudnamn-ID. Om du inte gör det, kan du se ett distributionsfel som: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` Om du vill bevilja åtkomst, granska [manuellt konfigurera den hanterade identitet](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  Den **hanterade identiteter** kryssrutan är markerad, eftersom initiativet tilldelas innehåller en princip med de *deployIfNotExists* effekt.
    
9. I den **hantera identitet plats** listrutan väljer du rätt region.

10. Välj **Tilldela**.

När du har skapat tilldelningen uppdateras i Azure Monitor för virtuella datorer Principtäckning sidan tilldelning täckning, tilldelningsstatus, efterlevnad virtuella datorer och kompatibilitetsstatusen för att återspegla ändringarna. 

Matrisen följande mappar varje möjligt kompatibilitetsstatus för initiativet.  

| Kompatibilitetstillstånd | Beskrivning | 
|------------------|-------------|
| **Kompatibel** | Alla virtuella datorer i området har Log Analytics och beroende agenter som distribuerats till.|
| **Ej kompatibel** | Inte alla virtuella datorer i området har Log Analytics och beroendeagenter distribueras till dem och kan kräva åtgärder.|
| **Inte startad** | En ny tilldelning har lagts till. |
| **Lås** | Du har inte tillräcklig behörighet till hanteringsgruppen. <sup>1</sup> | 
| **Tom** | Saknar en policy. | 

<sup>1</sup> om du inte har åtkomst till hanteringsgruppen kan be en ägare för att ge åtkomst eller visa och hantera tilldelningar via underordnade hanteringsgrupper eller prenumerationer. 

I följande tabell visas varje möjligt tilldelningsstatus för initiativet.

| Tilldelningsstatus | Beskrivning | 
|------------------|-------------|
| **Lyckades** | Alla virtuella datorer i området har Log Analytics och beroende agenter som distribuerats till.|
| **Varning** | Prenumerationen är inte under en hanteringsgrupp.|
| **Inte startad** | En ny tilldelning har lagts till. |
| **Lås** | Du har inte tillräcklig behörighet till hanteringsgruppen. <sup>1</sup> | 
| **Tom** | Inga virtuella datorer finns eller tilldelad inte princip. | 
| **Åtgärd** | Tilldela principer eller redigera tilldelning | 

<sup>1</sup> om du inte har åtkomst till hanteringsgruppen kan be en ägare för att ge åtkomst eller visa och hantera tilldelningar via underordnade hanteringsgrupper eller prenumerationer. 

## <a name="review-and-remediate-the-compliance-results"></a>Granska och åtgärda kompatibilitetsresultaten

I följande exempel är för en Azure virtuell dator, men gäller även för VM-skalningsuppsättningar. Du kan lära dig hur du granskar kompatibilitetsresultat genom att läsa [identifiera inkompatibilitet resultat](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). På Azure Monitor för virtuella datorer Principtäckning sidan, välja en hanteringsgruppen eller prenumerationen från tabellen och välj **visa efterlevnad** genom att klicka på ellipsen (...).   

![För virtuella Azure-datorer](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

Baserat på resultatet av de principer som ingår i initiativet, rapporteras virtuella datorer som icke-kompatibel i följande scenarier:

* Log Analytics eller beroendeagenten distribueras inte.  
    Det här scenariot är vanligt för ett omfång med befintliga virtuella datorer. Om du vill lösa det skickar distribuera nödvändiga agenter efter [skapa reparation aktiviteter](../../governance/policy/how-to/remediate-resources.md) på en princip för icke-kompatibla.  
    - [Förhandsversion]: Deploy Dependency Agent for Linux VMs
    - [Förhandsversion]: Deploy Dependency Agent for Windows VMs
    - [Förhandsversion]: Deploy Log Analytics Agent for Linux VMs
    - [Förhandsversion]: Deploy Log Analytics Agent for Windows VMs

* VM-avbildning (OS) är inte identifieras i principdefinitionen.  
    Villkor för distributionen omfattar endast virtuella datorer som distribueras från välkända Azure VM-avbildningar. I dokumentationen om du vill se om VM-operativsystem stöds. Om det inte stöds, duplicera i princip för programdistribution och uppdatera eller ändra det så att avbildningen som är kompatibla.  
    - [Förhandsversion]: Granska beroende Agentdistribution – VM Image (OS) inte finns i listan
    - [Förhandsversion]: Granska Agentdistribution för Log Analytics – VM Image (OS) inte finns i listan

* Virtuella datorer logga inte in på den angivna Log Analytics-arbetsytan.  
    Det är möjligt att vissa virtuella datorer i området initiativ loggar in på en Log Analytics-arbetsyta än den som anges i principtilldelningen. Den här principen är ett verktyg för att identifiera vilka virtuella datorer rapporterar till en icke-kompatibla arbetsyta.  
    - [Förhandsversion]: Audit Log Analytics Workspace for VM - Report Mismatch

## <a name="edit-initiative-assignment"></a>Redigera initiativtilldelning

När som helst när du har tilldelat ett initiativ till en hanteringsgrupp eller en prenumeration kan du redigera den genom att ändra följande egenskaper:

- Namn på tilldelning
- Beskrivning
- Tilldelats av
- Log Analytics-arbetsyta
- Undantag

## <a name="next-steps"></a>Nästa steg

Nu när övervakning har aktiverats för dina virtuella datorer, är den här informationen tillgänglig för analys med Azure Monitor för virtuella datorer. Läs hur du använder funktionen hälsotillstånd i [visa Azure Monitor för virtuella datorer Health](vminsights-health.md). Identifierade programberoenden finns [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md). För att identifiera flaskhalsar och totala användningen med din prestanda för virtuella datorer, se [visa Azure VM prestanda](vminsights-performance.md), eller om du vill visa identifierade programberoenden, se [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md).