---
title: Aktivera Azure Monitor för virtuella datorer med hjälp av Azure Policy
description: I den här artikeln beskrivs hur du aktiverar Azure Monitor för virtuella datorer för flera virtuella Azure-datorer eller skalningsuppsättningar för virtuella datorer med hjälp av Azure Policy.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 73c18d45136eea90ad29dc1bd40c4539dddc0ee6
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767250"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>Aktivera Azure Monitor för virtuella datorer med hjälp av Azure Policy

I den här artikeln beskrivs hur du aktiverar Azure Monitor för virtuella datorer för virtuella Azure-datorer eller skalningsuppsättningar för virtuella datorer med hjälp av Azure Policy. I slutet av den här processen har du konfigurerat aktivering av log analytics- och beroendeagenter och identifierade virtuella datorer som inte är kompatibla.

Om du vill identifiera, hantera och aktivera Azure Monitor för virtuella datorer för alla dina virtuella Azure-datorer eller skalningsuppsättningar för virtuella datorer kan du använda antingen Azure Policy eller Azure PowerShell. Azure Policy är den metod vi rekommenderar eftersom du kan hantera principdefinitioner för att effektivt styra dina prenumerationer för att säkerställa konsekvent efterlevnad och automatisk aktivering av nyetablerade virtuella datorer. Dessa principdefinitioner:

* Distribuera Log Analytics-agenten och beroendeagenten.
* Rapport om efterlevnadsresultat.
* Åtgärda för icke-kompatibla virtuella datorer.

Om du är intresserad av att utföra dessa uppgifter med Azure PowerShell eller en Azure Resource Manager-mall läser du [Aktivera Azure Monitor för virtuella datorer med Azure PowerShell- eller Azure Resource Manager-mallar](vminsights-enable-at-scale-powershell.md).

## <a name="prerequisites"></a>Krav
Innan du använder princip för att registrera dina virtuella Azure-datorer och skalningsuppsättningar för virtuella datorer till Azure Monitoring för virtuella datorer måste du aktivera VMInsights-lösningen på arbetsytan som du använder för att lagra dina övervakningsdata. Den här uppgiften kan slutföras från sidan **Kom igång** i Azure Monitor på fliken **Övriga introduktionsalternativ.**  Välj **Konfigurera en arbetsyta**, som uppmanar dig att välja den arbetsyta som ska konfigureras.

![Konfigurera arbetsyta](media/vminsights-enable-at-scale-policy/configure-workspace.png)

Du kan också konfigurera arbetsytan genom att välja **Aktivera med princip** och sedan välja knappen Konfigurera verktygsfältet i **arbetsytan.**  Detta installerar VMInsights-lösningen på den valda arbetsytan, vilket gör det möjligt för arbetsytan att lagra övervakningsdata som skickas av de virtuella datorer och skalningsuppsättningar för virtuella datorer som du aktiverar med princip. 

![Aktivera med hjälp av principen](media/vminsights-enable-at-scale-policy/enable-using-policy.png)

## <a name="manage-policy-coverage-feature-overview"></a>Hantera funktionsöversikt för principtäckning

Azure Monitor för VMs Policy Coverage förenklar identifiering, hantering och aktivering i skala initiativet **Aktivera Azure Monitor för virtuella datorer,** som innehåller de principdefinitioner som nämndes tidigare. Om du vill komma åt den här funktionen väljer du **Andra introduktionsalternativ** på fliken **Kom igång** i Azure Monitor för virtuella datorer. Välj **Hantera principtäckning** för att öppna sidan **Azure Monitor för virtuella datorers principtäckning.**

![Fliken Komma igång med Azure Monitor från virtuella datorer](./media/vminsights-enable-at-scale-policy/get-started-page.png)

Härifrån kan du kontrollera och hantera täckning för initiativet i dina hanteringsgrupper och prenumerationer. Du kan förstå hur många virtuella datorer som finns i var och en av hanteringsgrupperna och prenumerationerna och deras efterlevnadsstatus.

![Sidan Hantera princip i Azure Monitor för virtuella datorer](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Den här informationen är användbar för att hjälpa dig att planera och köra ditt styrningsscenario för Azure Monitor för virtuella datorer från en central plats. Azure Policy ger en efterlevnadsvy när en princip eller ett initiativ tilldelas ett scope, men med den här nya sidan kan du ta reda på var principen eller initiativet inte har tilldelats och tilldela det på plats. Alla åtgärder som tilldela, visa och redigera omdirigering till Azure Policy direkt. **Sidan Azure Monitor för virtuella datorers principtäckning** är en utökad och integrerad upplevelse för endast initiativet Aktivera Azure Monitor för virtuella **datorer**.

Från den här sidan kan du också konfigurera din Log Analytics-arbetsyta för Azure Monitor för virtuella datorer, som:

- Installerar servicekartlösningen.
- Aktiverar prestandaräknarna för operativsystemet som används av prestandadiagram, arbetsböcker och dina anpassade loggfrågor och varningar.

![Azure Monitor för virtuella datorer konfigurerar arbetsyta](media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Det här alternativet är inte relaterat till några principåtgärder. Den är tillgänglig för att ge ett enkelt sätt att uppfylla de [förutsättningar](vminsights-enable-overview.md) som krävs för att aktivera Azure Monitor för virtuella datorer.  

### <a name="what-information-is-available-on-this-page"></a>Vilken information finns på den här sidan?

I följande tabell visas en översikt över den information som visas på sidan för principtäckning och hur den ska tolkas.

| Funktion | Beskrivning | 
|----------|-------------| 
| **Omfång** | Hanteringsgrupp och prenumerationer som du har eller ärvt åtkomst till med möjlighet att öka detaljnivån i hanteringsgrupphierarkin.|
| **Roll** | Din roll till omfattningen, som kan vara läsare, ägare eller deltagare. I vissa fall kan det verka tomt för att indikera att du kan ha åtkomst till prenumerationen men inte till den hanteringsgrupp den tillhör. Informationen i andra kolumner varierar beroende på din roll. Rollen är avgörande för att avgöra vilka data du kan se och vilka åtgärder du kan utföra när det gäller att tilldela principer eller initiativ (ägare), redigera dem eller visa efterlevnad. |
| **Totalt antal virtuella datorer** | Antal virtuella datorer under det omfånget. För en hanteringsgrupp är det en summa virtuella datorer som är kapslade under prenumerationerna eller den underordnade hanteringsgruppen. |
| **Tilldelning Täckning** | Procent av virtuella datorer som omfattas av principen eller initiativet. |
| **Tilldelningsstatus** | Information om status för din policy eller initiativtilldelning. |
| **Kompatibla virtuella datorer** | Antal virtuella datorer som är kompatibla enligt policyn eller initiativet. För initiativet **Aktivera Azure Monitor för virtuella datorer**är detta antalet virtuella datorer som har både Log Analytics-agent och Dependency Agent. I vissa fall kan det visas tomt på grund av ingen tilldelning, inga virtuella datorer eller inte tillräckligt med behörigheter. Information lämnas under **kompatibilitetstillstånd.** |
| **Efterlevnad** | Det övergripande efterlevnadsnumret är summan av distinkta resurser som är kompatibla dividerat med summan av alla distinkta resurser. |
| **Kompatibilitetstillstånd** | Information om efterlevnadstillståndet för din policy eller initiativtilldelning.|

När du tilldelar principen eller initiativet kan det scope som valts i tilldelningen vara det scope som anges eller en delmängd av den. Du kan till exempel ha skapat en tilldelning för en prenumeration (principomfattning) och inte en hanteringsgrupp (täckningsomfång). I det här fallet anger värdet för **tilldelningstäckning** de virtuella datorerna i princip- eller initiativomfattningen dividerat med de virtuella datorerna i täckningsomfånget. I ett annat fall kan du ha uteslutit vissa virtuella datorer, resursgrupper eller en prenumeration från principomfattning. Om värdet är tomt betyder det att antingen principen eller initiativet inte finns eller att du inte har behörighet. Information finns under **Tilldelningsstatus**.

## <a name="enable-by-using-azure-policy"></a>Aktivera med hjälp av Azure Policy

Så här aktiverar du Azure Monitor för virtuella datorer genom att använda Azure Policy i din klientorganisation:

- Tilldela initiativet till ett scope: hanteringsgrupp, prenumeration eller resursgrupp.
- Granska och åtgärda efterlevnadsresultat.

Mer information om hur du tilldelar Azure Policy finns i [Översikt över Azure-princip](../../governance/policy/overview.md#assignments) och granska [översikten över hanteringsgrupper](../../governance/management-groups/overview.md) innan du fortsätter.

### <a name="policies-for-azure-vms"></a>Principer för virtuella Azure-datorer

Principdefinitionerna för en Virtuell Azure-dator visas i följande tabell.

|Namn |Beskrivning |Typ |
|-----|------------|-----|
|Aktivera Azure Monitor för virtuella datorer |Aktivera Azure Monitor för de virtuella datorerna i det angivna scopet (hanteringsgrupp, prenumeration eller resursgrupp). Tar Log Analytics-arbetsytan som en parameter. |Initiativ |
|Distribution av granskningsberoendeagent – VM-avbildning (OS) opererad |Rapporterar virtuella datorer som inkompatibla om vm-avbildningen (OS) inte definieras i listan och agenten inte är installerad. |Princip |
|Distribution av Granskningslogganalysagent – VM-avbildning (OS) opererad |Rapporterar virtuella datorer som inkompatibla om vm-avbildningen (OS) inte definieras i listan och agenten inte är installerad. |Princip |
|Distribuera beroendeagent för virtuella Linux-datorer |Distribuera beroendeagent för virtuella Linux-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |
|Distribuera beroendeagent för virtuella Windows-datorer |Distribuera beroendeagent för Virtuella Datorer i Windows om VM-avbildningen (OS) har definierats i listan och agenten inte är installerad. |Princip |
|Distribuera Log Analytics-agent för virtuella Linux-datorer |Distribuera Log Analytics-agent för virtuella Linux-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |
|Distribuera Log Analytics-agent för virtuella Windows-datorer |Distribuera Log Analytics-agent för virtuella Windows-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Principer för Azure-skalningsuppsättningar för virtuella datorer

Principdefinitionerna för en Azure-skalauppsättning för virtuella datorer visas i följande tabell.

|Namn |Beskrivning |Typ |
|-----|------------|-----|
|Aktivera Azure Monitor för skalningsuppsättningar för virtuella datorer |Aktivera Azure Monitor för skalningsuppsättningar för den virtuella datorn i det angivna scopet (hanteringsgrupp, prenumeration eller resursgrupp). Tar Log Analytics-arbetsytan som en parameter. Om uppgraderingsprincipen för skalningsuppsättningen är inställd på Manuell använder du tillägget på alla virtuella datorer i uppsättningen genom att anropa uppgradering på dem. I CLI, detta `az vmss update-instances`är . |Initiativ |
|Distribution av granskningsberoendeagent i skalningsuppsättningar för virtuella datorer – VM-avbildning (OS) olistad |Rapporter virtuell dator skala som inkompatibla om vm-avbildningen (OS) inte definieras i listan och agenten inte är installerad. |Princip |
|Granskningslogganalysagentdistribution i skalningsuppsättningar för virtuella datorer – VM-avbildning (OS) olistad |Rapporter virtuell dator skala som inkompatibla om vm-avbildningen (OS) inte definieras i listan och agenten inte är installerad. |Princip |
|Distribuera beroendeagent för Linux-skalningsuppsättningar för virtuella datorer |Distribuera beroendeagent för Linux-skalningsuppsättningar för virtuella datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |
|Distribuera beroendeagent för Windows-skalningsuppsättningar för virtuella datorer |Distribuera beroendeagent för Windows virtuella datorskalauppsättningar om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |
|Distribuera Log Analytics-agent för Linux-skalningsuppsättningar för virtuella datorer |Distribuera Log Analytics-agent för Linux-skalningsuppsättningar för virtuella datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |
|Distribuera Log Analytics-agent för Windows skalningsuppsättningar för virtuella datorer |Distribuera Log Analytics-agent för Windows-skalningsuppsättningar för virtuella datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |

Fristående politik (ingår inte i initiativet) beskrivs här:

|Namn |Beskrivning |Typ |
|-----|------------|-----|
|Analysarbetsyta för granskningslogg för virtuell dator – Rapportmatchning |Rapportera virtuella datorer som inkompatibla om de inte loggar in på den logganalysarbetsyta som anges i princip- eller initiativtilldelningen. |Princip |

### <a name="assign-the-azure-monitor-initiative"></a>Tilldela Azure Monitor-initiativet

Så här skapar du principtilldelningen från sidan **Azure Monitor för virtuella datorers principtäckning.** Information om hur du slutför de här stegen finns i [Skapa en principtilldelning från Azure-portalen](../../governance/policy/assign-policy-portal.md).

När du tilldelar principen eller initiativet kan det scope som valts i tilldelningen vara det scope som visas här eller en delmängd av den. Du kan till exempel ha skapat en tilldelning för prenumerationen (principomfattning) och inte hanteringsgruppen (täckningsomfång). I det här fallet skulle täckningsprocenten ange de virtuella datorerna i princip- eller initiativomfattningen dividerat med de virtuella datorerna i täckningsomfattningsområdet. I ett annat fall kan du ha uteslutit vissa virtuella datorer, resursgrupper eller en prenumeration från principomfattningen. Om den är tom betyder det att antingen principen eller initiativet inte finns eller att du inte har behörighet. Information finns under **Tilldelningsstatus**.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Övervaka**i Azure-portalen . 

3. Välj **Virtuella datorer** i avsnittet **Insikter.**
 
4. Välj fliken **Kom igång.** Välj **Hantera principtäckning**på sidan .

5. Välj antingen en hanteringsgrupp eller en prenumeration i tabellen. Välj **Scope** genom att välja ellipsen (...). I exemplet begränsar ett scope principtilldelningen till en gruppering av virtuella datorer för tvingande.

6. På **tilldelningssidan för Azure-princip** fylls den i ifylld med initiativet **Aktivera Azure Monitor för virtuella datorer**. 
    Rutan **Tilldelningsnamn** fylls i automatiskt med initiativnamnet, men du kan ändra det. Du kan också lägga till en valfri beskrivning. Rutan **Tilldelad efter** fylls i automatiskt baserat på vem som är inloggad. Det här värdet är valfritt.

7. (Valfritt) Om du vill ta bort en eller flera resurser från scopet väljer du **Uteslutningar**.

8. Välj en arbetsyta i listrutan **Logganalysarbetsyta** för den region som stöds.

   > [!NOTE]
   > Om arbetsytan ligger utanför tilldelningens omfattning beviljar du behörighet för *Log Analytics Contributor* till principtilldelningens huvud-ID. Om du inte gör det kan du se `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` ett distributionsfel som Att bevilja åtkomst, granska [hur du konfigurerar den hanterade identiteten manuellt](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  Kryssrutan **Hanterad identitet** är markerad eftersom det tilldelade initiativet innehåller en princip med effekten *deployIfNotExists.*
    
9. Välj lämplig region i listrutan **Hantera identitetsplats.**

10. Välj **Tilldela**.

När du har skapat tilldelningen uppdaterar **sidan Azure Monitor for VMs Policy Coverage** **Tilldelningstäckning,** **Tilldelningsstatus,** **Kompatibla virtuella datorer**och **efterlevnadstillstånd** för att återspegla ändringarna. 

Följande matris mappar varje möjlig efterlevnadstillstånd för initiativet.  

| Kompatibilitetstillstånd | Beskrivning | 
|------------------|-------------|
| **Kompatibel** | Alla virtuella datorer i scopet har log analytics- och beroendeagenter distribuerade till dem.|
| **Ej kompatibel** | Alla virtuella datorer i omfånget har inte log analytics- och beroendeagenter distribuerade till dem och kan kräva reparation.|
| **Inte startat** | Ett nytt uppdrag har lagts till. |
| **Låsa** | Du har inte tillräckliga privilegier till hanteringsgruppen. <sup>1.</sup> | 
| **Tom** | Ingen princip har tilldelats. | 

<sup>1</sup> Om du inte har tillgång till hanteringsgruppen ber du en ägare att ge åtkomst. Du kan också visa efterlevnad och hantera tilldelningar via underordnade hanteringsgrupper eller prenumerationer. 

I följande tabell mappas varje möjlig tilldelningsstatus för initiativet.

| Tilldelningsstatus | Beskrivning | 
|------------------|-------------|
| **Framgång** | Alla virtuella datorer i scopet har log analytics- och beroendeagenter distribuerade till dem.|
| **Varning** | Prenumerationen är inte under en ledningsgrupp.|
| **Inte startat** | Ett nytt uppdrag har lagts till. |
| **Låsa** | Du har inte tillräckliga privilegier till hanteringsgruppen. <sup>1.</sup> | 
| **Tom** | Det finns inga virtuella datorer eller så har ingen princip tilldelats. | 
| **Åtgärd** | Tilldela en princip eller redigera en tilldelning. | 

<sup>1</sup> Om du inte har tillgång till hanteringsgruppen ber du en ägare att ge åtkomst. Du kan också visa efterlevnad och hantera tilldelningar via underordnade hanteringsgrupper eller prenumerationer.

## <a name="review-and-remediate-the-compliance-results"></a>Granska och åtgärda efterlevnadsresultaten

Följande exempel är för en Virtuell Azure-dator, men det gäller även för skalningsuppsättningar för virtuella datorer. Mer information om hur du granskar efterlevnadsresultat finns i [Identifiera resultat som inte följer reglerna](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). På sidan **Azure Monitor för virtuella datorers principtäckning** väljer du antingen en hanteringsgrupp eller en prenumeration i tabellen. Välj **Visa efterlevnad** genom att välja ellipsen (...).   

![Principefterlevnad för virtuella Azure-datorer](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

Baserat på resultaten av de principer som ingår i initiativet rapporteras virtuella datorer som inkompatibla i följande scenarier:

* Log Analytics-agent eller beroendeagent har inte distribuerats.  
    Det här scenariot är typiskt för ett scope med befintliga virtuella datorer. För att minska det, distribuera de nödvändiga agenterna genom [att skapa reparationsuppgifter](../../governance/policy/how-to/remediate-resources.md) i en icke-kompatibel princip.  
    - Distribuera beroendeagent för virtuella Linux-datorer
    - Distribuera beroendeagent för virtuella Windows-datorer
    - Distribuera Log Analytics-agent för virtuella Linux-datorer
    - Distribuera Log Analytics-agent för virtuella Windows-datorer

* VM-avbildning (OS) identifieras inte i principdefinitionen.  
    Villkoren för distributionsprincipen omfattar endast virtuella datorer som distribueras från välkända Azure VM-avbildningar. Läs dokumentationen för att se om vm-operativsystemet stöds. Om det inte stöds duplicerar du distributionsprincipen och uppdaterar eller ändrar den så att avbildningen blir kompatibel.  
    - Distribution av granskningsberoendeagent – VM-avbildning (OS) opererad
    - Distribution av Granskningslogganalysagent – VM-avbildning (OS) opererad

* Virtuella datorer loggar inte in på den angivna Log Analytics-arbetsytan.  
    Det är möjligt att vissa virtuella datorer i initiativescopet loggar in på en log analytics-arbetsyta än den som anges i principtilldelningen. Den här principen är ett verktyg för att identifiera vilka virtuella datorer som rapporterar till en icke-kompatibel arbetsyta.  
    - Analysarbetsyta för granskningslogg för virtuell dator – Rapportmatchning

## <a name="edit-an-initiative-assignment"></a>Redigera en initiativuppgift

När som helst efter att du har tilldelat ett initiativ till en hanteringsgrupp eller prenumeration kan du redigera det för att ändra följande egenskaper:

- Tilldelningsnamn
- Beskrivning
- Tilldelad av
- Log Analytics-arbetsyta
- Undantag

## <a name="next-steps"></a>Nästa steg

Nu när övervakning är aktiverat för dina virtuella datorer är den här informationen tillgänglig för analys med Azure Monitor för virtuella datorer. 

- Information om hur du visar identifierade programberoenden finns i [Visa Azure Monitor för virtuella datorer Karta](vminsights-maps.md). 

- Information om hur du identifierar flaskhalsar och övergripande användning med den virtuella datorns prestanda finns i [Visa Azure VM-prestanda](vminsights-performance.md). 
