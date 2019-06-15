---
title: Aktivera Azure Monitor för virtuella datorer med hjälp av Azure Policy | Microsoft Docs
description: Den här artikeln beskrivs hur du aktiverar Azure Monitor för virtuella datorer för flera virtuella Azure-datorer eller VM-skalningsuppsättningar med hjälp av Azure Policy.
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
ms.openlocfilehash: fda79a7ea361a6b44798d18b79ffd763055087a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122622"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>Aktivera Azure Monitor för virtuella datorer (förhandsversion) med hjälp av Azure Policy

Den här artikeln förklarar hur du aktiverar Azure Monitor för virtuella datorer (förhandsversion) för Azure-datorer eller VM-skalningsuppsättningar med hjälp av Azure Policy. I slutet av den här processen kommer har konfigurerats att aktivera Log Analytics och beroendeagenter och identifiera virtuella datorer som inte är kompatibla.

Om du vill identifiera, hantera och aktivera Azure Monitor för virtuella datorer för alla dina Azure-datorer eller VM-skalningsuppsättningar kan använda du Azure Policy eller Azure PowerShell. Azure Policy är den metod som rekommenderas eftersom du kan hantera principdefinitioner för att effektivt reglera dina prenumerationer för att säkerställa konsekvent efterlevnad och automatisk aktivering av nyligen etablerade virtuella datorer. Dessa definitioner:

* Distribuera Log Analytics-agenten och beroendeagenten.
* Rapport om kompatibilitetsresultat.
* Åtgärda problemet för icke-kompatibla virtuella datorer.

Om du är intresserad av att utföra dessa uppgifter med Azure PowerShell eller en Azure Resource Manager-mall, se [aktivera Azure Monitor för virtuella datorer (förhandsversion) med hjälp av Azure PowerShell eller Azure Resource Manager-mallar](vminsights-enable-at-scale-powershell.md).

## <a name="manage-policy-coverage-feature-overview"></a>Hantera Principtäckning funktionsöversikt

Erfarenhet av Azure Policy för att hantera och distribuera principdefinitioner för Azure Monitor för virtuella datorer gjordes ursprungligen, uteslutande av Azure Policy. Hantera Principtäckning-funktionen gör det enklare att identifiera, hantera och aktivera i stor skala den **aktivera Azure Monitor för virtuella datorer** initiativet, som innehåller de principdefinitioner som vi nämnde tidigare. Du kan komma åt den här nya funktionen från den **börjar** fliken i Azure Monitor för virtuella datorer. Välj **hantera Principtäckning** att öppna den **Azure Monitor för virtuella datorer Principtäckning** sidan.

![Azure Monitor från virtuella datorer komma igång-fliken](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

Härifrån kan du kontrollera och hantera täckning för initiativet över dina hanteringsgrupper och prenumerationer. Du kan förstå hur många virtuella datorer finns på båda hanteringsgrupperna och prenumerationer och deras efterlevnadsstatus.

![Azure Monitor för virtuella datorer Hantera princip](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Den här informationen är användbar för att hjälpa dig att planera och utföra styrningsåtgärder scenariot för Azure Monitor för virtuella datorer från en central plats. Medan Azure Policy ger en Kompatibilitetsvyn när en princip eller ett initiativ har tilldelats ett scope, med den nya sidan kan du identifiera där principen eller initiativ inte är tilldelad och tilldela den på plats. Alla åtgärder som tilldelar, visa och redigera omdirigering till Azure Policy direkt. Den **Azure Monitor för virtuella datorer Principtäckning** är en utökad och integrerad upplevelse för endast initiativet **aktivera Azure Monitor för virtuella datorer**.

Den här sidan kan du kan också konfigurera Log Analytics-arbetsytan för Azure Monitor för virtuella datorer, som:

- Installerar lösningar installerar Service Map och Infrastruktursinsikter.
- Gör det möjligt för operativsystemet prestandaräknare som används av prestandadiagram, arbetsböcker, och den anpassade loggfrågor och aviseringar.

![Konfigurera arbetsyta för Azure Monitor för virtuella datorer](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Det här alternativet är inte relaterat till alla åtgärder. Det går att tillhandahålla ett enkelt sätt att uppfylla den [krav](vminsights-enable-overview.md) krävs för att aktivera Azure Monitor för virtuella datorer.  

### <a name="what-information-is-available-on-this-page"></a>Vilken information är tillgänglig på den här sidan?
Följande tabell innehåller en detaljerad analys av informationen som visas på sidan täckning och hur du tolkar det.

| Funktion | Beskrivning | 
|----------|-------------| 
| **Omfång** | Hanteringsgrupp och prenumerationer som du har eller ärvs åtkomst till med möjligheten att bläddra nedåt i hierarkin för gruppen.|
| **Roll** | Din roll till scope, vilket kan vara läsare, ägare eller deltagare. I vissa fall visas den tomt om du vill ange att du kan ha åtkomst till prenumerationen, men inte till hanteringsgruppen hör till. Informationen i andra kolumner varierar beroende på din roll. Rollen är nyckeln i avgör vilka data som du kan se och åtgärder som du kan utföra när det gäller tilldela principer eller initiativ (ägare), redigerar dem eller genom att visa kompatibiliteten. |
| **Totalt antal virtuella datorer** | Antalet virtuella datorer under detta omfång. Det är en summa av virtuella datorer kapslat under prenumerationer eller underordnad hanteringsgrupp för en hanteringsgrupp. |
| **Tilldelningen täckning** | Procent av virtuella datorer som omfattas av principen eller initiativ. |
| **Tilldelningsstatus** | Information om statusen för din princip eller ett initiativ tilldelningen. |
| **Kompatibla virtuella datorer** | Antalet virtuella datorer som är kompatibla under den princip eller ett initiativ. För initiativet **aktivera Azure Monitor för virtuella datorer**, detta är antalet virtuella datorer som har både Log Analytics-agenten och beroendeagenten. I vissa fall visas den tom på grund av ingen tilldelning, inga virtuella datorer eller inte tillräckliga behörigheter. Informationen tillhandahålls **Kompatibilitetstillstånd**. |
| **Efterlevnad** | Övergripande kompatibilitet talet är summan av olika resurser som är kompatibla dividerat med summan av alla distinkta resurser. |
| **Kompatibilitetstillstånd** | Information om hur kompatibilitetstillståndet för tilldelningen princip eller ett initiativ.|

När du tilldelar principen eller initiativ kan omfattningen som valts i tilldelningen omfång i listan eller en delmängd av den. Du kan exempelvis ha skapat en tilldelning för en prenumeration (principomfånget) och inte en hanteringsgrupp (täckning omfattning). I det här fallet, värdet för **tilldelning täckning** anger de virtuella datorerna i principen eller initiativ omfång dividerat med de virtuella datorerna i täckning omfång. I ett annat fall kan kanske du har exkluderat vissa virtuella datorer, resursgrupper eller en prenumeration från principomfånget. Om värdet är tomt, anger principen eller initiativ finns inte eller har inte behörighet. Informationen tillhandahålls **tilldelningsstatus**.

## <a name="enable-by-using-azure-policy"></a>Aktivera med hjälp av Azure Policy

Aktivera Azure Monitor för virtuella datorer med hjälp av Azure Policy i din klient:

- Tilldela initiativ till ett omfång: hanteringsgruppen, prenumeration eller resursgrupp.
- Granska och åtgärda kompatibilitetsresultat.

Mer information om hur du tilldelar Azure Policy finns [översikten över Azure Policy](../../governance/policy/overview.md#policy-assignment) och granska de [översikt över hanteringsgrupper](../../governance/management-groups/index.md) innan du fortsätter.

### <a name="policies-for-azure-vms"></a>Principer för virtuella Azure-datorer

Principdefinitioner för en Azure virtuell dator visas i följande tabell.

|Namn |Beskrivning |Typ |
|-----|------------|-----|
|[Förhandsversion]: Aktivera Azure Monitor för virtuella datorer |Aktivera Azure Monitor för de virtuella datorerna i det specificerade omfånget (hanteringsgruppen, prenumeration eller resursgrupp). Tar Log Analytics-arbetsyta som en parameter. |Initiativ |
|[Förhandsversion]: Granska beroende agentdistribution – VM-avbildning (OS) inte finns i listan |Rapporterar virtuella datorer som inkompatibel om VM-avbildning (OS) har inte definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Granska agentdistribution Log Analytics – VM-avbildning (OS) inte finns i listan |Rapporterar virtuella datorer som inkompatibel om VM-avbildning (OS) har inte definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Distribuera beroendeagenten för virtuella Linux-datorer |Distribuera beroendeagenten för virtuella Linux-datorer om VM-avbildning (OS) har definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Distribuera beroendeagenten för virtuella Windows-datorer |Distribuera beroendeagenten för Windows virtuella datorer om VM-avbildning (OS) har definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Distribuera Log Analytics-agenten för Linux-datorer |Distribuera Log Analytics-agenten för Linux-datorer om VM-avbildning (OS) har definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Distribuera Log Analytics-agenten för Windows-datorer |Distribuera Log Analytics-agenten för Windows virtuella datorer om VM-avbildning (OS) har definierats i listan och agenten är inte installerad. |Princip |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Principer för Azure VM-skalningsuppsättningar

Principdefinitioner för en skalningsuppsättning för virtuell Azure-dator visas i följande tabell.

|Namn |Beskrivning |Typ |
|-----|------------|-----|
|[Förhandsversion]: Aktivera Azure Monitor för VM-skalningsuppsättningar |Aktivera Azure Monitor för VM-skalningsuppsättningar i definitionsområdet (hanteringsgruppen, prenumeration eller resursgrupp). Tar Log Analytics-arbetsyta som en parameter. Obs! Om din skala set uppgraderingsprincipen är inställd på manuell, tillämpa tillägget för alla virtuella datorer i uppsättningen genom att anropa uppgraderingen på dem. I CLI är detta az vmss update-instances. |Initiativ |
|[Förhandsversion]: Granska beroende agentdistribution i VM scale sets – VM-avbildning (OS) inte finns i listan |Rapporterar VM-skalningsuppsättning som inkompatibel om VM-avbildning (OS) har inte definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Granska Log Analytics agentdistribution i VM scale sets – VM-avbildning (OS) inte finns i listan |Rapporterar VM-skalningsuppsättning som inkompatibel om VM-avbildning (OS) har inte definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Distribuera beroendeagenten för Linux VM scale sets |Distribuera beroendeagenten för Linux VM-skalningsuppsättningar om VM-avbildning (OS) har definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Distribuera beroendeagenten för Windows VM scale sets |Distribuera beroendeagenten för Windows VM scale sets för om VM-avbildning (OS) har definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Distribuera Log Analytics-agenten för Linux VM-skalningsuppsättningar |Distribuera Log Analytics-agenten för Linux VM-skalningsuppsättningar om VM-avbildning (OS) har definierats i listan och agenten är inte installerad. |Princip |
|[Förhandsversion]: Distribuera Log Analytics-agenten för Windows VM scale sets |Distribuera Log Analytics-agenten för Windows VM scale sets för om VM-avbildning (OS) har definierats i listan och agenten är inte installerad. |Princip |

Fristående princip (ingår inte i initiativet) som beskrivs här:

|Namn |Beskrivning |Typ |
|-----|------------|-----|
|[Förhandsversion]: Granska Log Analytics-arbetsyta för en virtuell dator – matchningsfel för rapport |Rapporter för virtuella datorer som inkompatibla om de inte loggar till Log Analytics-arbetsytan som anges i princip eller ett initiativ tilldelningen. |Princip |

### <a name="assign-the-azure-monitor-initiative"></a>Tilldela Azure Monitor-initiativ
Att skapa principtilldelning från den **Azure Monitor för virtuella datorer Principtäckning** utför de här stegen. Information om hur du utför de här stegen finns i [skapa en principtilldelning från Azure portal](../../governance/policy/assign-policy-portal.md).

När du tilldelar principen eller initiativ kan omfattningen som valts i tilldelningen omfattningen som anges här eller en delmängd av den. Du kan exempelvis ha skapat en tilldelning för prenumerationen (principomfånget) och inte hanteringsgruppen (täckning omfattning). I det här fallet visar täckning procentandelen att de virtuella datorerna i principen eller initiativ omfång dividerat med de virtuella datorerna i området täckning. I ett annat fall kan kanske du har exkluderat vissa virtuella datorer eller resursgrupper eller en prenumeration från principomfånget. Om den är tom, anger principen eller initiativ finns inte eller saknar du behörighet. Informationen tillhandahålls **tilldelningsstatus**.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. I Azure-portalen väljer du **övervakaren**. 

3. Välj **virtuella datorer (förhandsversion)** i den **Insights** avsnittet.
 
4. Välj den **börjar** fliken. På sidan Välj **hantera Principtäckning**.

5. Välj en hanteringsgrupp eller en prenumeration från tabellen. Välj **omfång** genom att välja ellipsen (...). I det här exemplet begränsar en omfattning principtilldelning till en gruppering av virtuella datorer för tvång.

6. På den **Azure Policy-tilldelning** kan den har fyllts i i förväg med initiativet **aktivera Azure Monitor för virtuella datorer**. 
    Den **tilldelningsnamn** fylls automatiskt med initiativ namn, men du kan ändra den. Du kan också lägga till en valfri beskrivning. Den **tilldelats av** fylls automatiskt baserat på vem som är inloggad. Det här värdet är valfritt.

7. (Valfritt) Om du vill ta bort en eller flera resurser från området, Välj **undantag**.

8. I den **Log Analytics-arbetsyta** nedrullningsbara lista för regionen som stöds, Välj en arbetsyta.

   > [!NOTE]
   > Om arbetsytan som är utanför omfattningen för tilldelningen, bevilja *Log Analytics Contributor* behörigheter till principtilldelningen huvudnamn-ID. Om du inte gör det, kan du se ett distributionsfel som `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` för att bevilja åtkomst, granska [manuellt konfigurera den hanterade identitet](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  Den **hanterade identiteter** kryssrutan är markerad eftersom initiativet tilldelas innehåller en princip med de *deployIfNotExists* effekt.
    
9. I den **hantera identitet plats** listrutan väljer du rätt region.

10. Välj **Tilldela**.

När du har skapat tilldelningen den **Azure Monitor för virtuella datorer Principtäckning** sidan uppdateringar **tilldelning täckning**, **tilldelningsstatus**, **kompatibla Virtuella datorer**, och **Kompatibilitetstillstånd** att återspegla ändringarna. 

Matrisen följande mappar varje möjligt kompatibilitetsstatus för initiativet.  

| Kompatibilitetstillstånd | Beskrivning | 
|------------------|-------------|
| **Kompatibel** | Alla virtuella datorer i området har Log Analytics och beroende agenter som distribuerats till.|
| **Ej kompatibel** | Inte alla virtuella datorer i området har Log Analytics och beroendeagenter distribueras till dem och kan kräva åtgärder.|
| **Inte startad** | En ny tilldelning har lagts till. |
| **Lås** | Du har inte tillräcklig behörighet till hanteringsgruppen. <sup>1</sup> | 
| **Tom** | Ingen princip har tilldelats. | 

<sup>1</sup> om du inte har åtkomst till hanteringsgruppen kan be en ägare för att ge åtkomst. Kan också visa och hantera tilldelningar via underordnade hanteringsgrupper eller prenumerationer. 

I följande tabell visas varje möjligt tilldelningsstatus för initiativet.

| Tilldelningsstatus | Beskrivning | 
|------------------|-------------|
| **Lyckades** | Alla virtuella datorer i området har Log Analytics och beroende agenter som distribuerats till.|
| **Varning** | Prenumerationen är inte under en hanteringsgrupp.|
| **Inte startad** | En ny tilldelning har lagts till. |
| **Lås** | Du har inte tillräcklig behörighet till hanteringsgruppen. <sup>1</sup> | 
| **Tom** | Inga virtuella datorer finns eller en princip inte är tilldelad. | 
| **Åtgärd** | Tilldela en princip eller redigera en tilldelning. | 

<sup>1</sup> om du inte har åtkomst till hanteringsgruppen kan be en ägare för att ge åtkomst. Kan också visa och hantera tilldelningar via underordnade hanteringsgrupper eller prenumerationer.

## <a name="review-and-remediate-the-compliance-results"></a>Granska och åtgärda kompatibilitetsresultaten

I följande exempel är för en Azure virtuell dator, men det gäller även för VM-skalningsuppsättningar. Läs hur du granskar kompatibilitetsresultat i [identifiera inkompatibilitet resultat](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). På den **Azure Monitor för virtuella datorer Principtäckning** väljer du en hanteringsgrupp eller en prenumeration från tabellen. Välj **visa efterlevnad** genom att välja ellipsen (...).   

![För virtuella Azure-datorer](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

Baserat på resultatet av de principer som ingår i initiativet, rapporteras virtuella datorer som icke-kompatibel i följande scenarier:

* Log Analytics-agenten eller beroendeagenten distribueras inte.  
    Det här scenariot är vanligt för ett omfång med befintliga virtuella datorer. Om du vill lösa det skickar distribuera nödvändiga agenter efter [skapa reparation aktiviteter](../../governance/policy/how-to/remediate-resources.md) en inkompatibel princip.  
    - [Förhandsversion]: Deploy Dependency agent for Linux VMs
    - [Förhandsversion]: Deploy Dependency agent for Windows VMs
    - [Förhandsversion]: Deploy Log Analytics agent for Linux VMs
    - [Förhandsversion]: Deploy Log Analytics agent for Windows VMs

* VM-avbildning (OS) är inte identifieras i principdefinitionen.  
    Villkor för distributionen omfattar endast virtuella datorer som distribueras från välkända Azure VM-avbildningar. I dokumentationen om du vill se om VM-operativsystem stöds. Om det inte stöds, duplicera i princip för programdistribution och uppdatera eller ändra det så att avbildningen som är kompatibla.  
    - [Förhandsversion]: Granska beroende agentdistribution – VM-avbildning (OS) inte finns i listan
    - [Förhandsversion]: Granska agentdistribution Log Analytics – VM-avbildning (OS) inte finns i listan

* Virtuella datorer logga inte in på den angivna Log Analytics-arbetsytan.  
    Det är möjligt att vissa virtuella datorer i området initiativ loggar in på en Log Analytics-arbetsyta än den som anges i principtilldelningen. Den här principen är ett verktyg för att identifiera vilka virtuella datorer rapporterar till en icke-kompatibla arbetsyta.  
    - [Förhandsversion]: Audit Log Analytics workspace for VM – Report mismatch

## <a name="edit-an-initiative-assignment"></a>Redigera principtilldelning

När som helst när du har tilldelat ett initiativ till en hanteringsgrupp eller -prenumeration kan redigera du den om du vill ändra följande egenskaper:

- Tilldelningsnamn
- Beskrivning
- Tilldelad av
- Log Analytics-arbetsyta
- Undantag

## <a name="next-steps"></a>Nästa steg

Nu när övervakning har aktiverats för dina virtuella datorer, är den här informationen tillgänglig för analys med Azure Monitor för virtuella datorer. 

- Läs hur du använder funktionen hälsotillstånd i [visa Azure Monitor för virtuella datorer health](vminsights-health.md). 
- Identifierade programberoenden finns [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md). 
- För att identifiera flaskhalsar och totala användningen med den Virtuella datorns prestanda, se [visa Azure VM prestanda](vminsights-performance.md). 
- Identifierade programberoenden finns [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md).
