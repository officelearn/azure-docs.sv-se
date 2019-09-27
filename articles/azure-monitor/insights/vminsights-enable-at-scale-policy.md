---
title: Aktivera Azure Monitor for VMs med Azure Policy | Microsoft Docs
description: I den här artikeln beskrivs hur du aktiverar Azure Monitor for VMs för flera virtuella Azure-datorer eller skalnings uppsättningar för virtuella datorer med hjälp av Azure Policy.
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
ms.openlocfilehash: cbb471d337bd386b6c5f2c7a960565ef29855c9c
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338229"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>Aktivera Azure Monitor for VMs (för hands version) med Azure Policy

I den här artikeln beskrivs hur du aktiverar Azure Monitor for VMs (för hands version) för virtuella Azure-datorer eller skalnings uppsättningar för virtuella datorer med hjälp av Azure Policy. I slutet av den här processen har du konfigurerat att aktivera Log Analytics-och beroende agenter och identifierade virtuella datorer som inte är kompatibla.

Om du vill identifiera, hantera och aktivera Azure Monitor for VMs för alla dina virtuella Azure-datorer eller skalnings uppsättningar för virtuella datorer kan du använda antingen Azure Policy eller Azure PowerShell. Azure Policy är metoden vi rekommenderar eftersom du kan hantera princip definitioner för att effektivt styra dina prenumerationer för att säkerställa konsekvent kompatibilitet och automatisk aktivering av nyligen etablerade virtuella datorer. Följande princip definitioner:

* Distribuera Log Analytics-agenten och beroendeagenten.
* Rapport om kompatibilitetsresultat.
* Åtgärd för icke-kompatibla virtuella datorer.

Om du är intresse rad av att utföra dessa uppgifter med Azure PowerShell eller en Azure Resource Manager mall, se [aktivera Azure Monitor for VMS (för hands version) med hjälp av Azure PowerShell eller Azure Resource Manager mallar](vminsights-enable-at-scale-powershell.md).

## <a name="manage-policy-coverage-feature-overview"></a>Översikt över hantering av princip täckning

Från början genomfördes upplevelsen med Azure Policy för att hantera och distribuera princip definitionerna för Azure Monitor for VMs uteslutande från Azure Policy. Funktionen Hantera princip täckning gör det enklare och enklare att upptäcka, hantera och aktivera vid skalning av **aktiverings Azure Monitor for VMS** initiativet, som innehåller de princip definitioner som nämnts ovan. Du kan komma åt den här nya funktionen från fliken **Kom igång** i Azure Monitor for VMS. Välj **Hantera princip täckning** för att öppna sidan **Azure Monitor for VMS princip täckning** .

![Fliken för att komma igång med Azure Monitor från virtuella datorer](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

Härifrån kan du kontrol lera och hantera täckning för initiativet i dina hanterings grupper och prenumerationer. Du kan förstå hur många virtuella datorer som finns i varje hanterings grupp och prenumerationer och deras kompatibilitetsstatus.

![Sidan Azure Monitor for VMs hantera princip](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Den här informationen är användbar för att hjälpa dig att planera och köra styrnings scenariot för Azure Monitor for VMs från en central plats. Medan Azure Policy ger en efterlevnadsprincip när en princip eller ett initiativ tilldelas ett omfång, kan du med den här nya sidan upptäcka var principen eller initiativet inte är tilldelad och tilldelar den på plats. Alla åtgärder som tilldela, Visa och redigera omdirigering till Azure Policy direkt. Sidan **Azure Monitor for VMS policy disponering** är en utökat och integrerad upplevelse för endast initiativet **Aktivera Azure Monitor for VMS**.

På den här sidan kan du också konfigurera din Log Analytics arbets yta för Azure Monitor for VMs, som:

- Installerar Tjänstkarta-och Infrastructure Insights-lösningar.
- Aktiverar prestanda räknare för operativ systemet som används av prestanda diagram, arbets böcker och dina anpassade logg frågor och aviseringar.

![Azure Monitor for VMs konfigurera arbets yta](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Det här alternativet är inte relaterat till några princip åtgärder. Det är tillgängligt för att tillhandahålla ett enkelt sätt att uppfylla de [krav](vminsights-enable-overview.md) som krävs för att aktivera Azure Monitor for VMS.  

### <a name="what-information-is-available-on-this-page"></a>Vilken information finns på den här sidan?
Följande tabell innehåller en uppdelning av den information som visas på sidan policy disponering och hur du tolkar den.

| Funktion | Beskrivning | 
|----------|-------------| 
| **Omfång** | Hanterings grupp och prenumerationer som du har eller ärvt åtkomst till med möjlighet att gå nedåt i hanterings gruppens hierarki.|
| **Roll** | Din roll till omfånget, som kan vara läsare, ägare eller deltagare. I vissa fall kan det vara tomt för att indikera att du kan ha åtkomst till prenumerationen men inte till den hanterings grupp som den tillhör. Informationen i andra kolumner varierar beroende på din roll. Rollen är en nyckel för att avgöra vilka data du kan se och vilka åtgärder du kan utföra när du tilldelar principer eller initiativ (ägare), redigerar dem eller visar efterlevnad. |
| **Totalt antal virtuella datorer** | Antal virtuella datorer under den omfattningen. För en hanterings grupp är det summan av de virtuella datorer som är kapslade under prenumerationerna eller den underordnade hanterings gruppen. |
| **Tilldelnings täckning** | Procent av de virtuella datorer som omfattas av principen eller initiativet. |
| **Tilldelnings status** | Information om status för din princip eller initiativ tilldelning. |
| **Kompatibla virtuella datorer** | Antal virtuella datorer som är kompatibla med principen eller initiativet. För att initiativet ska vara **aktiverat Azure Monitor for VMS**det här antalet virtuella datorer som har både Log Analytics agent och beroende agent. I vissa fall kan det vara tomt på grund av ingen tilldelning, inga virtuella datorer eller otillräcklig behörighet. Information anges under **kompatibilitetstillstånd**. |
| **Efterlevnad** | Det övergripande kompatibilitets numret är summan av distinkta resurser som är kompatibla med summan av alla distinkta resurser. |
| **Kompatibilitetstillstånd** | Information om kompatibilitetstillstånd för principen eller initiativ tilldelningen.|

När du tilldelar principen eller initiativet kan omfånget som valts i tilldelningen vara omfånget eller en delmängd av den. Du kanske till exempel har skapat en tilldelning för en prenumeration (princip omfattning) och inte en hanterings grupp (täcknings område). I det här fallet indikerar värdet för **tilldelnings täckning** de virtuella datorerna i principen eller initiativ omfånget dividerat med de virtuella datorerna i täcknings omfånget. I ett annat fall kan du ha uteslutit vissa virtuella datorer, resurs grupper eller en prenumeration från princip omfattningen. Om värdet är tomt anger det att antingen principen eller initiativet inte finns eller eftersom du inte har behörighet. Information finns under **tilldelnings status**.

## <a name="enable-by-using-azure-policy"></a>Aktivera med hjälp av Azure Policy

Aktivera Azure Monitor för virtuella datorer med hjälp av Azure Policy i din klient:

- Tilldela initiativet till ett omfång: hanterings grupp, prenumeration eller resurs grupp.
- Granska och åtgärda resultatet av efterlevnaden.

Mer information om hur du tilldelar Azure Policy finns [översikten över Azure Policy](../../governance/policy/overview.md#policy-assignment) och granska de [översikt över hanteringsgrupper](../../governance/management-groups/overview.md) innan du fortsätter.

### <a name="policies-for-azure-vms"></a>Principer för virtuella Azure-datorer

Princip definitionerna för en virtuell Azure-dator visas i följande tabell.

|Name |Beskrivning |type |
|-----|------------|-----|
|\[För\]hands version: Aktivera Azure Monitor för virtuella datorer |Aktivera Azure Monitor för de virtuella datorerna i det angivna omfånget (hanterings grupp, prenumeration eller resurs grupp). Tar Log Analytics-arbetsyta som en parameter. |Initiativ |
|\[För\]hands version: Granska distribution av beroende agent – VM-avbildning (OS) har inte listats |Rapporterar virtuella datorer som icke-kompatibla om VM-avbildningen (OS) inte är definierad i listan och agenten inte är installerad. |Princip |
|\[För\]hands version: Granska Log Analytics agent distribution – VM-avbildningen (OS) har inte listats |Rapporterar virtuella datorer som icke-kompatibla om VM-avbildningen (OS) inte är definierad i listan och agenten inte är installerad. |Princip |
|\[För\]hands version: Distribuera beroende agent för virtuella Linux-datorer |Distribuera beroende agent för virtuella Linux-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |
|\[För\]hands version: Distribuera beroende agent för virtuella Windows-datorer |Distribuera beroende agent för virtuella Windows-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |
|\[För\]hands version: Distribuera Log Analytics agent för virtuella Linux-datorer |Distribuera Log Analytics agent för virtuella Linux-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |
|\[För\]hands version: Distribuera Log Analytics agent för virtuella Windows-datorer |Distribuera Log Analytics agent för virtuella Windows-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Principer för skalnings uppsättningar för virtuella Azure-datorer

Princip definitionerna för en skalnings uppsättning för en virtuell Azure-dator visas i följande tabell.

|Name |Beskrivning |type |
|-----|------------|-----|
|\[För\]hands version: Aktivera Azure Monitor för skalnings uppsättningar för virtuella datorer |Aktivera Azure Monitor för skalnings uppsättningar för virtuella datorer i det angivna omfånget (hanterings grupp, prenumeration eller resurs grupp). Tar Log Analytics-arbetsyta som en parameter. Obs! Om din uppgraderings princip för skalnings uppsättningen är inställd på manuell, ska du använda tillägget på alla virtuella datorer i uppsättningen genom att anropa uppgraderingen på dem. I CLI är detta AZ VMSS Update-instances. |Initiativ |
|\[För\]hands version: Granska beroende agent distribution i virtuell dator skalnings uppsättningar – VM-avbildning (OS) har inte listats |Rapporterar skalnings uppsättning för virtuella datorer som icke-kompatibel om VM-avbildningen (OS) inte är definierad i listan och agenten inte är installerad. |Princip |
|\[För\]hands version: Granska Log Analytics agent distribution i Virtual Machine Scale Sets – VM-avbildning (OS) har inte listats |Rapporterar skalnings uppsättning för virtuella datorer som icke-kompatibel om VM-avbildningen (OS) inte är definierad i listan och agenten inte är installerad. |Princip |
|\[För\]hands version: Distribuera beroende agent för skalnings uppsättningar för virtuella Linux-datorer |Distribuera beroende agent för skalnings uppsättningar för virtuella Linux-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |
|\[För\]hands version: Distribuera beroende agent för skalnings uppsättningar för virtuella Windows-datorer |Distribuera beroende agent för skalnings uppsättningar för virtuella Windows-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |
|\[För\]hands version: Distribuera Log Analytics agent för skalnings uppsättningar för virtuella Linux-datorer |Distribuera Log Analytics agent för skalnings uppsättningar för virtuella Linux-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |
|\[För\]hands version: Distribuera Log Analytics agent för skalnings uppsättningar för virtuella Windows-datorer |Distribuera Log Analytics agent för skalnings uppsättningar för virtuella Windows-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |

Fristående princip (ingår inte i initiativet) som beskrivs här:

|Namn |Beskrivning |type |
|-----|------------|-----|
|\[För\]hands version: Granska Log Analytics arbets yta för VM – rapport matchnings fel |Rapportera virtuella datorer som icke-kompatibla om de inte loggar in på den Log Analytics arbets ytan som anges i principen eller på initiativ tilldelningen. |Princip |

### <a name="assign-the-azure-monitor-initiative"></a>Tilldela Azure Monitor-initiativ
Följ dessa steg om du vill skapa princip tilldelningen från sidan **Azure Monitor for VMS princip täckning** . Information om hur du utför de här stegen finns i [skapa en principtilldelning från Azure portal](../../governance/policy/assign-policy-portal.md).

När du tilldelar principen eller initiativet kan omfånget som valts i tilldelningen vara det omfång som listas här eller en delmängd av den. Du kanske till exempel har skapat en tilldelning för prenumerationen (princip omfattning) och inte hanterings gruppen (täcknings område). I det här fallet anger täcknings procenten för de virtuella datorerna i principen eller initiativ omfånget delat av de virtuella datorerna i täcknings omfånget. I ett annat fall kan du ha uteslutit vissa virtuella datorer eller resurs grupper eller en prenumeration från princip omfånget. Om det är tomt anger det att antingen principen eller initiativet inte finns eller eftersom du inte har behörighet. Information finns under **tilldelnings status**.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. I Azure-portalen väljer du **övervakaren**. 

3. Välj **Virtual Machines (för hands version)** i avsnittet **insikter** .
 
4. Välj fliken **Kom igång** . På sidan väljer du **Hantera princip täckning**.

5. Välj antingen en hanterings grupp eller en prenumeration från tabellen. Välj **omfång** genom att välja ellipsen (...). I exemplet begränsar ett område princip tilldelningen till en gruppering av virtuella datorer för tvångs skydd.

6. På sidan **Azure policy tilldelning** är den i förväg ifylld med initiativet **Aktivera Azure Monitor for VMS**. 
    Rutan **tilldelnings namn** fylls i automatiskt med initiativ namnet, men du kan ändra det. Du kan också lägga till en valfri beskrivning. Rutan **tilldelad av** fylls i automatiskt baserat på vem som är inloggad. Det här värdet är valfritt.

7. (Valfritt) Om du vill ta bort en eller flera resurser från området, Välj **undantag**.

8. I den **Log Analytics-arbetsyta** nedrullningsbara lista för regionen som stöds, Välj en arbetsyta.

   > [!NOTE]
   > Om arbetsytan som är utanför omfattningen för tilldelningen, bevilja *Log Analytics Contributor* behörigheter till principtilldelningen huvudnamn-ID. Om du inte gör det kan du se ett distributions fel som `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` om du vill bevilja åtkomst, se [hur du konfigurerar den hanterade identiteten manuellt](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  Kryss rutan **hanterad identitet** är markerad eftersom initiativet som tilldelas innehåller en princip med *deployIfNotExists* -påverkan.
    
9. I den **hantera identitet plats** listrutan väljer du rätt region.

10. Välj **Tilldela**.

När du har skapat tilldelningen uppdaterar sidan **Azure Monitor for VMS policy disponering** **tilldelnings täckning**, **tilldelnings status**, **kompatibla virtuella datorer**och **kompatibilitetstillstånd** för att avspegla ändringarna. 

Följande matris mappar varje möjligt kompatibilitetstillstånd för initiativet.  

| Kompatibilitetstillstånd | Beskrivning | 
|------------------|-------------|
| **Kompabilitet** | Alla virtuella datorer i omfånget har de Log Analytics och beroende agenter som distribuerats till dem.|
| **Inte kompatibel** | Alla virtuella datorer i omfånget har inte de Log Analytics-och beroende agenter som distribuerats till dem och kan kräva reparation.|
| **Inte startat** | En ny tilldelning har lagts till. |
| **Skrivlås** | Du har inte tillräcklig behörighet för hanterings gruppen. <sup>1</sup> | 
| **Tom** | Ingen princip har tilldelats. | 

<sup>1</sup> om du inte har åtkomst till hanterings gruppen ber du en ägare att ge åtkomst. Du kan också visa efterlevnad och hantera tilldelningar via underordnade hanterings grupper eller prenumerationer. 

I följande tabell mappas varje möjlig tilldelnings status för initiativet.

| Tilldelnings status | Beskrivning | 
|------------------|-------------|
| **Resultatet** | Alla virtuella datorer i omfånget har de Log Analytics och beroende agenter som distribuerats till dem.|
| **Honom** | Prenumerationen ingår inte i en hanterings grupp.|
| **Inte startat** | En ny tilldelning har lagts till. |
| **Skrivlås** | Du har inte tillräcklig behörighet för hanterings gruppen. <sup>1</sup> | 
| **Tom** | Det finns inga virtuella datorer eller så har ingen princip tilldelats. | 
| **Åtgärd** | Tilldela en princip eller redigera en tilldelning. | 

<sup>1</sup> om du inte har åtkomst till hanterings gruppen ber du en ägare att ge åtkomst. Du kan också visa efterlevnad och hantera tilldelningar via underordnade hanterings grupper eller prenumerationer.

## <a name="review-and-remediate-the-compliance-results"></a>Granska och åtgärda kompatibilitetsresultaten

Följande exempel gäller för en virtuell Azure-dator, men den gäller även för skalnings uppsättningar för virtuella datorer. Information om hur du granskar kompatibilitetsstatus finns i [identifiera inkompatibla resultat](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). På sidan **Azure Monitor for VMS princip täckning** väljer du antingen en hanterings grupp eller en prenumeration från tabellen. Välj **Visa kompatibilitet** genom att välja ellipsen (...).   

![För virtuella Azure-datorer](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

Utifrån resultaten av de principer som ingår i initiativet rapporteras de virtuella datorerna som inkompatibla i följande scenarier:

* Log Analytics agent eller beroende agent har inte distribuerats.  
    Det här scenariot är vanligt för ett omfång med befintliga virtuella datorer. Du kan minimera det genom att distribuera de nödvändiga agenterna genom att [skapa reparations uppgifter](../../governance/policy/how-to/remediate-resources.md) på en icke-kompatibel princip.  
    - \[För\]hands version: Distribuera beroende agent för virtuella Linux-datorer
    - \[För\]hands version: Distribuera beroende agent för virtuella Windows-datorer
    - \[För\]hands version: Distribuera Log Analytics agent för virtuella Linux-datorer
    - \[För\]hands version: Distribuera Log Analytics agent för virtuella Windows-datorer

* VM-avbildningen (OS) är inte identifierad i princip definitionen.  
    Villkor för distributionen omfattar endast virtuella datorer som distribueras från välkända Azure VM-avbildningar. I dokumentationen om du vill se om VM-operativsystem stöds. Om det inte stöds, duplicera i princip för programdistribution och uppdatera eller ändra det så att avbildningen som är kompatibla.  
    - \[För\]hands version: Granska distribution av beroende agent – VM-avbildning (OS) har inte listats
    - \[För\]hands version: Granska Log Analytics agent distribution – VM-avbildningen (OS) har inte listats

* Virtuella datorer logga inte in på den angivna Log Analytics-arbetsytan.  
    Det är möjligt att vissa virtuella datorer i området initiativ loggar in på en Log Analytics-arbetsyta än den som anges i principtilldelningen. Den här principen är ett verktyg för att identifiera vilka virtuella datorer som rapporterar till en icke-kompatibel arbets yta.  
    - \[För\]hands version: Granska Log Analytics arbets yta för VM – rapport matchnings fel

## <a name="edit-an-initiative-assignment"></a>Redigera en initiativ tilldelning

När som helst efter att du har tilldelat ett initiativ till en hanterings grupp eller prenumeration kan du redigera den för att ändra följande egenskaper:

- Namn på tilldelning
- Beskrivning
- Tilldelad av
- Log Analytics-arbetsyta
- Undantag

## <a name="next-steps"></a>Nästa steg

Nu när övervakning är aktiverat för dina virtuella datorer är den här informationen tillgänglig för analys med Azure Monitor for VMs. 

- Information om hur du använder hälso funktionen finns i [visa Azure Monitor for VMS hälsa](vminsights-health.md). 
- Identifierade programberoenden finns [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md). 
- Information om hur du identifierar Flask halsar och övergripande användning med den virtuella datorns prestanda finns i [Visa prestanda för virtuella Azure-datorer](vminsights-performance.md). 
- Identifierade programberoenden finns [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md).
