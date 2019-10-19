---
title: Aktivera Azure Monitor for VMs med Azure Policy | Microsoft Docs
description: I den här artikeln beskrivs hur du aktiverar Azure Monitor for VMs för flera virtuella Azure-datorer eller skalnings uppsättningar för virtuella datorer med hjälp av Azure Policy.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 068d320c85a1e10027c92b92c1b3c205a999bed7
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553825"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>Aktivera Azure Monitor for VMs (för hands version) med Azure Policy

I den här artikeln beskrivs hur du aktiverar Azure Monitor for VMs (för hands version) för virtuella Azure-datorer eller skalnings uppsättningar för virtuella datorer med hjälp av Azure Policy. I slutet av den här processen har du konfigurerat att aktivera Log Analytics-och beroende agenter och identifierade virtuella datorer som inte är kompatibla.

Om du vill identifiera, hantera och aktivera Azure Monitor for VMs för alla dina virtuella Azure-datorer eller skalnings uppsättningar för virtuella datorer kan du använda antingen Azure Policy eller Azure PowerShell. Azure Policy är metoden vi rekommenderar eftersom du kan hantera princip definitioner för att effektivt styra dina prenumerationer för att säkerställa konsekvent kompatibilitet och automatisk aktivering av nyligen etablerade virtuella datorer. Följande princip definitioner:

* Distribuera Log Analytics agenten och beroende agenten.
* Rapportera om överensstämmelse resultat.
* Åtgärd för icke-kompatibla virtuella datorer.

Om du är intresse rad av att utföra dessa uppgifter med Azure PowerShell eller en Azure Resource Manager mall, se [aktivera Azure Monitor for VMS (för hands version) med hjälp av Azure PowerShell eller Azure Resource Manager mallar](vminsights-enable-at-scale-powershell.md).

## <a name="manage-policy-coverage-feature-overview"></a>Översikt över hantering av princip täckning

Från början genomfördes upplevelsen med Azure Policy för att hantera och distribuera princip definitionerna för Azure Monitor for VMs uteslutande från Azure Policy. Funktionen Hantera princip täckning gör det enklare och enklare att upptäcka, hantera och aktivera vid skalning av **aktiverings Azure Monitor for VMS** initiativet, som innehåller de princip definitioner som nämnts ovan. Du kan komma åt den här nya funktionen från fliken **Kom igång** i Azure Monitor for VMS. Välj **Hantera princip täckning** för att öppna sidan **Azure Monitor for VMS princip täckning** .

![Fliken för att komma igång med Azure Monitor från virtuella datorer](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

Härifrån kan du kontrol lera och hantera täckning för initiativet i dina hanterings grupper och prenumerationer. Du kan förstå hur många virtuella datorer som finns i varje hanterings grupp och prenumerationer och deras kompatibilitetsstatus.

![Sidan Azure Monitor for VMs hantera princip](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Den här informationen är användbar för att hjälpa dig att planera och köra styrnings scenariot för Azure Monitor for VMs från en central plats. Medan Azure Policy ger en efterlevnadsprincip när en princip eller ett initiativ tilldelas ett omfång, kan du med den här nya sidan upptäcka var principen eller initiativet inte är tilldelad och tilldelar den på plats. Alla åtgärder som tilldela, Visa och redigera omdirigering till Azure Policy direkt. Sidan **Azure Monitor for VMS policy disponering** är en utökat och integrerad upplevelse för endast initiativet **Aktivera Azure Monitor for VMS**.

På den här sidan kan du också konfigurera din Log Analytics arbets yta för Azure Monitor for VMs, som:

- Installerar Tjänstkarta-lösningen.
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

Så här aktiverar du Azure Monitor for VMs med Azure Policy i din klient organisation:

- Tilldela initiativet till ett omfång: hanterings grupp, prenumeration eller resurs grupp.
- Granska och åtgärda resultatet av efterlevnaden.

Mer information om hur du tilldelar Azure Policy finns [Azure policy översikt](../../governance/policy/overview.md#policy-assignment) och granska [översikten över hanterings grupper](../../governance/management-groups/overview.md) innan du fortsätter.

### <a name="policies-for-azure-vms"></a>Principer för virtuella Azure-datorer

Princip definitionerna för en virtuell Azure-dator visas i följande tabell.

|Namn |Beskrivning |Typ |
|-----|------------|-----|
|\[Preview \]: Aktivera Azure Monitor for VMs |Aktivera Azure Monitor för de virtuella datorerna i det angivna omfånget (hanterings grupp, prenumeration eller resurs grupp). Tar Log Analytics-arbetsyta som en parameter. |Förmåga |
|\[Preview \]: granska distribution av beroende agent – VM-avbildningen (OS) har inte listats |Rapporterar virtuella datorer som icke-kompatibla om VM-avbildningen (OS) inte är definierad i listan och agenten inte är installerad. |Princip |
|\[Preview \]: granska Log Analytics agent distribution – VM-avbildningen (OS) har inte listats |Rapporterar virtuella datorer som icke-kompatibla om VM-avbildningen (OS) inte är definierad i listan och agenten inte är installerad. |Princip |
|\[Preview \]: Distribuera beroende agent för virtuella Linux-datorer |Distribuera beroende agent för virtuella Linux-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |
|\[Preview \]: Distribuera beroende agent för virtuella Windows-datorer |Distribuera beroende agent för virtuella Windows-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |
|\[Preview \]: Distribuera Log Analytics agent för virtuella Linux-datorer |Distribuera Log Analytics agent för virtuella Linux-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |
|\[Preview \]: Distribuera Log Analytics agent för virtuella Windows-datorer |Distribuera Log Analytics agent för virtuella Windows-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Principer för skalnings uppsättningar för virtuella Azure-datorer

Princip definitionerna för en skalnings uppsättning för en virtuell Azure-dator visas i följande tabell.

|Namn |Beskrivning |Typ |
|-----|------------|-----|
|\[Preview \]: Aktivera Azure Monitor för skalnings uppsättningar för virtuella datorer |Aktivera Azure Monitor för skalnings uppsättningar för virtuella datorer i det angivna omfånget (hanterings grupp, prenumeration eller resurs grupp). Tar Log Analytics-arbetsyta som en parameter. OBS! om uppgraderings principen för skalnings uppsättningen är inställd på manuell ska du använda tillägget på alla virtuella datorer i uppsättningen genom att anropa uppgraderingen på dem. I CLI är detta AZ VMSS Update-instances. |Förmåga |
|\[Preview \]: Granska beroende agent distribution i Virtual Machine Scale Sets – VM-avbildning (OS) har inte listats |Rapporterar skalnings uppsättning för virtuella datorer som icke-kompatibel om VM-avbildningen (OS) inte är definierad i listan och agenten inte är installerad. |Princip |
|\[Preview \]: granska Log Analytics agent distribution i Virtual Machine Scale Sets – VM-avbildning (OS) har inte listats |Rapporterar skalnings uppsättning för virtuella datorer som icke-kompatibel om VM-avbildningen (OS) inte är definierad i listan och agenten inte är installerad. |Princip |
|\[Preview \]: Distribuera beroende agent för skalnings uppsättningar för virtuella Linux-datorer |Distribuera beroende agent för skalnings uppsättningar för virtuella Linux-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |
|\[Preview \]: Distribuera beroende agent för skalnings uppsättningar för virtuella Windows-datorer |Distribuera beroende agent för skalnings uppsättningar för virtuella Windows-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |
|\[Preview \]: Distribuera Log Analytics agent för virtuella Linux-datorer med skalnings uppsättningar |Distribuera Log Analytics agent för skalnings uppsättningar för virtuella Linux-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |
|\[Preview \]: Distribuera Log Analytics agent för skalnings uppsättningar för virtuella Windows-datorer |Distribuera Log Analytics agent för skalnings uppsättningar för virtuella Windows-datorer om VM-avbildningen (OS) definieras i listan och agenten inte är installerad. |Princip |

Fristående princip (ingår inte i initiativet) beskrivs här:

|Namn |Beskrivning |Typ |
|-----|------------|-----|
|\[Preview \]: granska Log Analytics arbets yta för VM – rapportera fel |Rapportera virtuella datorer som icke-kompatibla om de inte loggar in på den Log Analytics arbets ytan som anges i principen eller på initiativ tilldelningen. |Princip |

### <a name="assign-the-azure-monitor-initiative"></a>Tilldela Azure Monitor initiativ

Följ dessa steg om du vill skapa princip tilldelningen från sidan **Azure Monitor for VMS princip täckning** . Information om hur du utför de här stegen finns i [skapa en princip tilldelning från Azure Portal](../../governance/policy/assign-policy-portal.md).

När du tilldelar principen eller initiativet kan omfånget som valts i tilldelningen vara det omfång som listas här eller en delmängd av den. Du kanske till exempel har skapat en tilldelning för prenumerationen (princip omfattning) och inte hanterings gruppen (täcknings område). I det här fallet anger täcknings procenten för de virtuella datorerna i principen eller initiativ omfånget delat av de virtuella datorerna i täcknings omfånget. I ett annat fall kan du ha uteslutit vissa virtuella datorer eller resurs grupper eller en prenumeration från princip omfånget. Om det är tomt anger det att antingen principen eller initiativet inte finns eller eftersom du inte har behörighet. Information finns under **tilldelnings status**.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. I Azure Portal väljer du **övervaka**. 

3. Välj **Virtual Machines (för hands version)** i avsnittet **insikter** .
 
4. Välj fliken **Kom igång** . På sidan väljer du **Hantera princip täckning**.

5. Välj antingen en hanterings grupp eller en prenumeration från tabellen. Välj **omfång** genom att välja ellipsen (...). I exemplet begränsar ett område princip tilldelningen till en gruppering av virtuella datorer för tvångs skydd.

6. På sidan **Azure policy tilldelning** är den i förväg ifylld med initiativet **Aktivera Azure Monitor for VMS**. 
    Rutan **tilldelnings namn** fylls i automatiskt med initiativ namnet, men du kan ändra det. Du kan också lägga till en valfri beskrivning. Rutan **tilldelad av** fylls i automatiskt baserat på vem som är inloggad. Det här värdet är valfritt.

7. Valfritt Om du vill ta bort en eller flera resurser från omfattningen väljer du **undantag**.

8. Välj en arbets yta i list rutan **Log Analytics arbets yta** för den region som stöds.

   > [!NOTE]
   > Om arbets ytan överskrider tilldelningens omfattning beviljar du *Log Analytics deltagar* behörighet till princip tilldelningens huvud-ID. Om du inte gör det kan det hända att du ser ett distributions fel som `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` för att bevilja åtkomst. Läs mer om [hur du konfigurerar den hanterade identiteten manuellt](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  Kryss rutan **hanterad identitet** är markerad eftersom initiativet som tilldelas innehåller en princip med *deployIfNotExists* -påverkan.
    
9. Välj lämplig region i list rutan **Hantera identitets plats** .

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

## <a name="review-and-remediate-the-compliance-results"></a>Granska och åtgärda resultatet av efterlevnaden

Följande exempel gäller för en virtuell Azure-dator, men den gäller även för skalnings uppsättningar för virtuella datorer. Information om hur du granskar kompatibilitetsstatus finns i [identifiera inkompatibla resultat](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). På sidan **Azure Monitor for VMS princip täckning** väljer du antingen en hanterings grupp eller en prenumeration från tabellen. Välj **Visa kompatibilitet** genom att välja ellipsen (...).   

![Efterlevnadsprincip för virtuella Azure-datorer](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

Utifrån resultaten av de principer som ingår i initiativet rapporteras de virtuella datorerna som inkompatibla i följande scenarier:

* Log Analytics agent eller beroende agent har inte distribuerats.  
    Det här scenariot är vanligt för ett omfång med befintliga virtuella datorer. Du kan minimera det genom att distribuera de nödvändiga agenterna genom att [skapa reparations uppgifter](../../governance/policy/how-to/remediate-resources.md) på en icke-kompatibel princip.  
    - \[Preview \]: Distribuera beroende agent för virtuella Linux-datorer
    - \[Preview \]: Distribuera beroende agent för virtuella Windows-datorer
    - \[Preview \]: Distribuera Log Analytics agent för virtuella Linux-datorer
    - \[Preview \]: Distribuera Log Analytics agent för virtuella Windows-datorer

* VM-avbildningen (OS) är inte identifierad i princip definitionen.  
    Villkoren för distributions principen inkluderar endast virtuella datorer som distribueras från välkända Azure VM-avbildningar. Läs dokumentationen för att se om det finns stöd för VM-operativsystem. Om den inte stöds duplicerar du distributions principen och uppdaterar eller ändrar den för att göra avbildningen kompatibel.  
    - \[Preview \]: granska distribution av beroende agent – VM-avbildningen (OS) har inte listats
    - \[Preview \]: granska Log Analytics agent distribution – VM-avbildningen (OS) har inte listats

* Virtuella datorer loggar inte in på den angivna Log Analytics-arbetsytan.  
    Det är möjligt att vissa virtuella datorer i initiativet initiativ loggar in på en Log Analytics annan arbets yta än den som anges i princip tilldelningen. Den här principen är ett verktyg för att identifiera vilka virtuella datorer som rapporterar till en icke-kompatibel arbets yta.  
    - \[Preview \]: granska Log Analytics arbets yta för VM – rapportera fel

## <a name="edit-an-initiative-assignment"></a>Redigera en initiativ tilldelning

När som helst efter att du har tilldelat ett initiativ till en hanterings grupp eller prenumeration kan du redigera den för att ändra följande egenskaper:

- Tilldelnings namn
- Beskrivning
- Tilldelad av
- Log Analytics arbets yta
- Undantag

## <a name="next-steps"></a>Nästa steg

Nu när övervakning är aktiverat för dina virtuella datorer är den här informationen tillgänglig för analys med Azure Monitor for VMs. 

- Information om hur du visar identifierade program beroenden finns i [visa Azure Monitor for VMS karta](vminsights-maps.md). 

- Information om hur du identifierar Flask halsar och övergripande användning med den virtuella datorns prestanda finns i [Visa prestanda för virtuella Azure-datorer](vminsights-performance.md). 
