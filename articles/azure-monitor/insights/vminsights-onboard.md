---
title: Publicera Azure Monitor för virtuella datorer (förhandsversion) | Microsoft Docs
description: Den här artikeln beskrivs hur du publicerar och konfigurera Azure Monitor för virtuella datorer så att du kan börja med att förstå hur det distribuerade programmet fungerar och vilka hälsoproblem har identifierats.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 7b6752196d3512ab3ede4f5e339d681948e732e8
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853769"
---
# <a name="how-to-onboard-the-azure-monitor-for-vms-preview"></a>Hur att publicera Azure övervakar för virtuella datorer (förhandsversion)
Den här artikeln beskriver hur du ställer in Azure Monitor för virtuella datorer för att övervaka hälsotillståndet för operativsystemet för dina Azure-datorer och skalningsuppsättningar för virtuella datorer och virtuella datorer i din miljö, inklusive identifiering och mappning av programberoenden som kan vara baserad på dem.  

Aktivera Azure Monitor för virtuella datorer görs på något av följande metoder och information om hur du använder varje metod finns senare i artikeln.  

* En enda Azure-dator genom att välja **Insights (förhandsversion)** direkt från den virtuella datorn.
* Flera virtuella Azure-datorer med hjälp av Azure Policy för att se till att befintliga och nya virtuella datorer som utvärderas har de nödvändiga beroendena installeras och konfigureras korrekt.  Icke-kompatibla virtuella datorer rapporteras så att du kan avgöra baserat på vad inte är kompatibel, hur du vill åtgärda.  
* Flera virtuella Azure-datorer eller VM scale sets för över en viss prenumeration eller resursgrupp med hjälp av PowerShell.

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar bör du kontrollera att du förstår följande enligt beskrivningen i underavsnitt nedan.

### <a name="log-analytics"></a>Log Analytics 

Log Analytics-arbetsytan i följande regioner är för närvarande:

  - Västra centrala USA  
  - USA, östra  
  - Europa, västra  
  - Sydostasien<sup>1</sup>  

<sup>1</sup> den här regionen stöder för närvarande inte funktionen Health i Azure Monitor för virtuella datorer   

>[!NOTE]
>Azure-datorer kan vara implementerad från alla regioner och är inte begränsad till regionerna som stöds för Log Analytics-arbetsytan.
>

Om du inte har en arbetsyta kan du skapa den via [Azure CLI](../../log-analytics/log-analytics-quick-create-workspace-cli.md), via [PowerShell](../../log-analytics/log-analytics-quick-create-workspace-posh.md)i den [Azure-portalen](../../log-analytics/log-analytics-quick-create-workspace.md), eller med [Azure Resource Manager](../../log-analytics/log-analytics-template-workspace-configuration.md).  Om du aktiverar övervakning för en virtuell Azure-dator från Azure portal, har du möjlighet att skapa en arbetsyta under den här processen.  

Att aktivera lösningen för den i stor skala scenariot först måste konfigurera följande i din Log Analytics-arbetsyta:

* Installera den **ServiceMap** och **InfrastructureInsights** lösningar. Detta kan endast utföras med hjälp av en Azure Resource Manager-mall i den här artikeln.   
* Konfigurera Log Analytics-arbetsytan för att samla in prestandaräknare.

Konfigurera din arbetsyta för den skala scenariot överblick över [installationsprogrammet Log Analytics-arbetsyta för den vid skala distributionen](#setup-log-analytics-workspace).

### <a name="supported-operating-systems"></a>Operativsystem som stöds

I följande tabell visas de Windows- och Linux-operativsystem som stöds med Azure Monitor för virtuella datorer.  En fullständig lista med information om Linux-operativsystem högre och den lägre versionen och kernel-versioner som stöds finns senare i det här avsnittet.

|OS-version |Prestanda |Kartor |Hälsa |  
|-----------|------------|-----|-------|  
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |  
|Windows Server 2012 R2 | X | X | |  
|Windows Server 2012 | X | X | |  
|Windows Server 2008 R2 | X | X| |  
|RHEL 7, 6| X | X| X |  
|Ubuntu 18.04, 16.04, 14.04 | X | X | X |  
|Cent OS Linux 7, 6 | X | X | X |  
|SLES 12 | X | X | X |  
|Oracle Linux 7 | X<sup>1</sup> | | X |  
|Oracle Linux 6 | X | X | X |  
|Debian 9.4, 8 | X<sup>1</sup> | | X | 

<sup>1</sup> the prestanda funktion i Azure Monitor för virtuella datorer är endast tillgänglig från Azure Monitor, det är inte tillgängligt när du har åtkomst till den från den vänstra rutan i Azure VM direkt.  

>[!NOTE]
>Följande information gäller för att ge stöd för Linux-operativsystem:  
> - Endast standardversioner och SMP Linux-kernelversioner stöds.  
> - Avvikande kernelversioner, som exempelvis PAE och Xen, stöds inte för någon Linux-distribution. Till exempel stöds ett system med release-sträng med ”2.6.16.21-0.8-xen” inte.  
> - Anpassade kernelversioner inklusive omkompileringar av standardkernelversioner, stöds inte.  
> - CentOSPlus-kernel stöds inte.  


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| OS-version | Kernel-version |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| OS-version | Kernel-version |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| OS-version | Kernel-version |
|:--|:--|
| Ubuntu 18.04 | Kernel 4.15. * |
| Ubuntu 16.04.3 | Kernel 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 6 med Unbreakable Enterprise Kernel
| OS-version | Kernel-version
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 5 med Unbreakable Enterprise Kernel

| OS-version | Kernel-version
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS-version | Kernel-version
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |

### <a name="microsoft-dependency-agent"></a>Microsoft Dependency agent
Azure Monitor för virtuella datorer kartan hämtar data från Microsoft Dependency agenten. Beroendeagenten är beroende av Log Analytics-agenten för anslutningen till Log Analytics och därför är ett system måste ha Log Analytics-agenten installeras och konfigureras med beroendeagenten. När du aktiverar Azure Monitor för virtuella datorer för en virtuell Azure-dator eller när du använder metoderna för den vid skala distributionen Azure VM beroende agent-tillägget används för att installera agenten som en del av den publiceringsupplevelsen. Med en hybridmiljö beroendeagenten kan hämtas och installeras manuellt eller med hjälp av en automatisk distributionsmetod för de virtuella datorerna finns utanför Azure.  

I följande tabell beskrivs de anslutna källor som har stöd för funktionen kartan i en hybridmiljö.

| Ansluten källa | Stöds | Beskrivning |
|:--|:--|:--|
| Windows-agenter | Ja | Förutom den [Log Analytics-agenten för Windows](../../log-analytics/log-analytics-agent-overview.md), Windows-agenter kräver Microsoft Dependency agenten. Se [Operativsystem som stöds](#supported-operating-systems) för en fullständig lista med operativsystemversioner. |
| Linux-agenter | Ja | Förutom den [Log Analytics-agenten för Linux](../../log-analytics/log-analytics-agent-overview.md), kräver Microsoft Dependency agenten för Linux-agenter. Se [Operativsystem som stöds](#supported-operating-systems) för en fullständig lista med operativsystemversioner. |
| System Center Operations Manager-hanteringsgrupp | Nej | |  

Beroendeagenten kan hämtas från följande plats.

| Fil | OS | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.1 | 55030ABF553693D8B5112569FB2F97D7C54B66E9990014FC8CC43EFB70DE56C6 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.1 | 43C75EF0D34471A0CBCE5E396FFEEF4329C9B5517266108FA5D6131A353D29FE |

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll
Följande åtkomst måste beviljas åtkomst till dina användare för att aktivera och få åtkomst till funktionerna i Azure Monitor för virtuella datorer.  
  
- Du måste läggas till som en medlem i rollen som deltagare Log Analytics för att aktivera lösningen.  

- Om du vill visa prestanda, hälsotillstånd och mappa data, måste du lägga till som en medlem i rollen Läsare övervakning för den virtuella Azure-datorn och Log Analytics-arbetsytan som konfigurerats med Azure Monitor för virtuella datorer.   

Läs mer om hur du styr åtkomst till en Log Analytics-arbetsyta, [hantera arbetsytor](../../log-analytics/log-analytics-manage-access.md).

## <a name="enable-from-the-azure-portal"></a>Aktivera från Azure portal
Om du vill aktivera övervakning av Azure-VM i Azure-portalen, gör du följande:

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 
2. I Azure-portalen väljer du **virtuella datorer**. 
3. Välj en virtuell dator i listan. 
4. På sidan virtuell dator i den **övervakning** väljer **Insights (förhandsversion)**.
5. På den **Insights (förhandsversion)** väljer **Prova nu**.

    ![Aktivera Azure Monitor för virtuella datorer för en virtuell dator](./media/vminsights-onboard/enable-vminsights-vm-portal-01.png)

5. På den **Azure Monitor insikter Onboarding** om du har en befintlig Log Analytics-arbetsyta i samma prenumeration, markerar du den i den nedrullningsbara listan.  Listan förväljer standardarbetsytan och plats som den virtuella datorn har distribuerats till i prenumerationen. 

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervakningsdata från den virtuella datorn följer du anvisningarna i [skapa en Log Analytics-arbetsyta](../../log-analytics/log-analytics-quick-create-workspace.md) i någon av regionerna som stöds ovan.   

När du har aktiverat övervakning, kan det ta ungefär 10 minuter innan du kan visa hälsomått för den virtuella datorn. 

![Aktivera Azure Monitor för virtuella datorer övervakning distributionsbearbetning](./media/vminsights-onboard/onboard-vminsights-vm-portal-status.png)


## <a name="on-boarding-at-scale"></a>Registreringen i stor skala
I det här avsnittet anvisningar om hur du utför den på skala distributionen av Azure Monitor för virtuella datorer som använder antingen Azure Policy eller med Azure PowerShell.  

Sammanfattas anges de steg som du behöver utföra för att förkonfigurera Log Analytics-arbetsytan innan du kan fortsätta med onboarding dina virtuella datorer.

1. Skapa en ny arbetsyta om det inte redan finns som kan användas för att stödja Azure Monitor för virtuella datorer. Granska [hantera arbetsytor](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json) innan du skapar en ny arbetsyta för att förstå överväganden kostnader, hantering och kompatibilitet innan du fortsätter.       
2. Aktivera prestandaräknare på arbetsytan för samlingen på Linux- och Windows-datorer.
3. Installera och aktivera den **ServiceMap** och **InfrastructureInsights** lösningen i din arbetsyta.  

### <a name="setup-log-analytics-workspace"></a>Konfigurera Log Analytics-arbetsyta
Om du inte har en Log Analytics-arbetsyta kan du granska de tillgängliga metoderna som föreslås den [krav](#log-analytics) avsnitt för att skapa en.  

#### <a name="enable-performance-counters"></a>Aktivera prestandaräknare
Om Log Analytics-arbetsytan som refereras av lösningen inte är konfigurerat för att samla in prestandaräknare som krävs av lösningen redan kan måste de vara aktiverat. Detta kan åstadkommas manuellt enligt [här](../../log-analytics/log-analytics-data-sources-performance-counters.md), eller genom att hämta och köra ett PowerShell-skript som är tillgängliga från [Azure Powershell-galleriet](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).
 
#### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Installera ServiceMap och InfrastructureInsights lösningar
Den här metoden innehåller en JSON-mall som anger konfigurationen för att aktivera lösningskomponenter till Log Analytics-arbetsytan.  

Om du inte är bekant med begreppet att distribuera resurser med hjälp av en mall, se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md) 

Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. För att identifiera din version, kör `az --version`. Om du behöver installera eller uppgradera Azure CLI kan du läsa [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

1. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {

    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "WorkspaceName": {
            "type": "string"
        },
        "WorkspaceLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-15-preview",
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('WorkspaceName')]",
            "location": "[parameters('WorkspaceLocation')]",
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },

                    "plan": {
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                        "promotionCode": ""
                    }
                },
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },
                    "plan": {
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                        "promotionCode": ""
                    }
                }
            ]
        }
    ]
    ```

2. Spara filen som **installsolutionsforvminsights.json** till en lokal mapp.
3. Redigera värdena för **WorkspaceName**, **ResourceGroupName**, och **WorkspaceLocation**.  Värdet för **WorkspaceName** är fullständiga resurs-ID för Log Analytics-arbetsytan, som innehåller namnet på arbetsytan och värdet för **WorkspaceLocation** är den region som arbetsytan är definierad i.
4. Du är redo att distribuera den här mallen med hjälp av följande PowerShell-kommando:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

    ```powershell
    provisioningState       : Succeeded
    ```

### <a name="enable-using-azure-policy"></a>Aktivera med hjälp av Azure Policy
Aktivera Azure Monitor för virtuella datorer i stor skala som säkerställer konsekvent efterlevnad och automatisk aktivering för nya virtuella datorer som etablerats [Azure Policy](../../azure-policy/azure-policy-introduction.md) rekommenderas. Dessa principer:

* Distribuera Log Analytics-agenten och beroendeagenten 
* Rapport om kompatibilitetsresultat 
* Åtgärda problemet för icke-kompatibla virtuella datorer

Aktivera Azure Monitor för virtuella datorer via Grupprincip till din klient kräver: 

- Tilldela initiativ till ett omfång – hanteringsgruppen, prenumeration eller resursgrupp 
- Granska och reparation av kompatibilitetsresultat  

Mer information om Azure Policy-tilldelning, se [översikten över Azure Policy](../../governance/policy/overview.md#policy-assignment) och granska de [översikt över hanteringsgrupper](../../governance/management-groups/index.md) innan du fortsätter.  

I följande tabell visas de principdefinitioner som tillhandahålls.  

|Namn |Beskrivning |Typ |  
|-----|------------|-----|  
|[Förhandsversion]: aktivera Azure Monitor för virtuella datorer |Aktivera Azure Monitor för virtuella datorer (VM) i definitionsområdet (hanteringsgruppen, prenumeration eller resursgrupp). Tar Log Analytics-arbetsyta som parameter. |Initiativ |  
|[Förhandsversion]: granska beroende Agentdistribution – VM Image (OS) inte finns i listan |Rapporterar virtuella datorer som icke-kompatibla om den virtuella datoravbildningen (operativsystemet) inte finns i listan och agenten inte är installerad. |Princip |  
|[Förhandsversion]: Agentdistribution för granskning Log Analytics – VM Image (OS) inte finns i listan |Rapporterar virtuella datorer som icke-kompatibla om den virtuella datoravbildningen (operativsystemet) inte finns i listan och agenten inte är installerad. |Princip |  
|[Förhandsversion]: distribuera Beroendeagenten för virtuella Linux-datorer |Distribuera Dependency Agent för virtuella Linux-datorer om den virtuella datoravbildningen (operativsystem) finns i listan och agenten inte är installerad. |Princip |  
|[Förhandsversion]: distribuera Beroendeagenten för virtuella Windows-datorer |Distribuera Dependency Agent för virtuella Windows-datorer om den virtuella datoravbildningen (operativsystem) finns i listan och agenten inte är installerad. |Princip |  
|[Förhandsversion]: distribuerar Log Analytics-agenten för Linux-datorer |Distribuera Log Analytics Agent för virtuella Linux-datorer om den virtuella datoravbildningen (operativsystem) finns i listan och agenten inte är installerad. |Princip |  
|[Förhandsversion]: distribuerar Log Analytics-agenten för Windows-datorer |Distribuera Log Analytics Agent för virtuella Windows-datorer om den virtuella datoravbildningen (operativsystem) finns i listan och agenten inte är installerad. |Princip |  

Fristående princip (ingår inte i initiativet) 

|Namn |Beskrivning |Typ |  
|-----|------------|-----|  
|[Förhandsversion]: granska Log Analytics-arbetsyta för virtuell dator – rapportera matchningsfel |Rapporter för virtuella datorer som inkompatibla om de inte loggar till arbetsytan LA som angetts i principen/initiativ tilldelningen. |Princip |

#### <a name="assign-azure-monitor-initiative"></a>Tilldela Azure Monitor initiativ
Du kan bara skapa principtilldelningen från Azure-portalen med den här första versionen. Information om hur du utför de här stegen finns i [skapa en principtilldelning från Azure portal](../../governance/policy/assign-policy-portal.md). 

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**. 
2. Välj **Tilldelningar** till vänster på sidan Azure Policy. En tilldelning är en princip som tilldelats ett specifikt område.
3. Välj **tilldela initiativ** högst upp på den **princip – tilldelningar** sidan.
4. På den **tilldela initiativ** väljer den **omfång** genom att klicka på ellipsen och välj antingen en hanteringsgrupp eller prenumeration och eventuellt en resursgrupp. Ett omfång begränsar principtilldelning i vårt fall till en gruppering av virtuella datorer för tvång. Klicka på **Välj** längst ned på den **omfång** sidan för att spara dina ändringar.
5. **Undantag** kan du utelämna en eller flera resurser från scope, vilket är valfritt. 
6. Välj den **initiativdefinition** ellipsen för att öppna listan med tillgängliga definitioner och välja  **[förhandsversion] aktivera Azure Monitor för virtuella datorer** i listan och klicka sedan på **Välj**.
7. Den **tilldelningsnamn** är automatiskt ifylld med initiativ namn du valt, men du kan ändra den. Du kan också lägga till en valfri **Beskrivning**. **Tilldelad av** fylls i automatiskt baserat på vem som har loggat in och det här fältet är valfritt.
8. Välj en **Log Analytics-arbetsyta** i listrutan som är tillgänglig i regionen som stöds.

    >[!NOTE]
    >Om arbetsytan som är utanför omfånget för tilldelningen, måste du ge **Log Analytics Contributor** behörigheter till principtilldelningen huvudnamn-ID. Om du inte gör det kan du se ett distributionsfel som: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ... ` granska [manuellt konfigurera den hanterade identitet](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity) att bevilja åtkomst.
    >

9. Observera den **hanterade identiteter** alternativet är markerat. Detta kontrolleras när initiativet tilldelas innehåller en princip med effekten deployIfNotExists. Från den **hantera identitet plats** listrutan väljer du rätt region.  
10. Klicka på **Tilldela**.

#### <a name="review-and-remediate-the-compliance-results"></a>Granska och åtgärda kompatibilitetsresultaten 

Du kan lära dig hur du granskar kompatibilitetsresultat genom att läsa [identifiera inkompatibilitet resultat](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Välj **efterlevnad** till vänster på sidan och leta upp den  **[förhandsversion] aktivera Azure Monitor för virtuella datorer** initiativ som inte är kompatibla per tilldelningen som du skapade.

![För virtuella Azure-datorer](./media/vminsights-onboard/policy-view-compliance-01.png)

Baserat på resultatet av de principer som ingår i initiativet, rapporteras virtuella datorer som icke-kompatibel i följande scenarier:  
  
1. Log Analytics eller Beroendeagenten har inte distribuerats.  
   Det här är vanligt för ett omfång med befintliga virtuella datorer. Förhindras, [skapa reparation uppgifter](../../governance/policy/how-to/remediate-resources.md) på en icke-kompatibla princip för att distribuera nödvändiga agenter.    
 
    - [Förhandsversion]: Deploy Dependency Agent for Linux VMs   
    - [Förhandsversion]: Deploy Dependency Agent for Windows VMs  
    - [Förhandsversion]: Deploy Log Analytics Agent for Linux VMs  
    - [Förhandsversion]: Deploy Log Analytics Agent for Windows VMs  

2. VM-avbildning (OS) är inte i listan över identifierade i principdefinitionen.  
   Villkor för distribution av princip bara innehåller virtuella datorer som distribueras från välkända Azure VM-avbildningar. I dokumentationen om VM-operativsystem som stöds eller inte. Om inte måste du duplicera princip för programdistribution och uppdatera/ändra det så att avbildningen som är kompatibla. 
  
    - [Förhandsversion]: granska beroende Agentdistribution – VM Image (OS) inte finns i listan  
    - [Förhandsversion]: Agentdistribution för granskning Log Analytics – VM Image (OS) inte finns i listan

3. Virtuella datorer loggar inte på den angivna LA-arbetsytan.  
Det är möjligt att vissa virtuella datorer i området initiativ loggar till en LA arbetsyta skiljer sig från den en gång anges i rolltilldelningen. Den här principen är ett verktyg för att identifiera vilka virtuella datorer rapporterar till en icke-kompatibla arbetsyta.  
 
    - [Förhandsversion]: Audit Log Analytics Workspace for VM - Report Mismatch  

### <a name="enable-with-powershell"></a>Aktivera med PowerShell
Om du vill aktivera Azure Monitor för virtuella datorer för flera virtuella datorer eller VM-skalningsuppsättningar, kan du använda ett angivet PowerShell-skript – [installera VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0) tillgängliga från Azure PowerShell-galleriet för att slutföra den här uppgiften.  Det här skriptet ska gå igenom varje virtuella dator och VM-skalningsuppsättning i prenumerationen i den begränsade resursgruppen som anges av *ResourceGroup*, eller till en enda virtuell dator eller virtuell dator skala som anges av *Namn*.  Skriptet verifierar för varje virtuell dator eller VM-skalningsuppsättning om VM-tillägget har installerats, och om inte försöker installera det på nytt.  I annat fall fortsätter den att installera Log Analytics och beroende Agent VM-tillägg.   

Det här skriptet kräver Azure PowerShell-Modulversion 5.7.0-installationsprogram eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

Om du vill ha hjälp med att skriptet kan du köra `Get-Help` att hämta en lista över argument information och exempel på användning.   

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and Dependency Agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VM's and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VM's/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VM's are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong to

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VM's to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VM's/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Suported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VM's in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to ReInstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to ReInstall (move to a new workspace)
```

I följande exempel visas med hjälp av PowerShell-kommandon i mappen att aktivera Azure Monitor för virtuella datorer och förstå utdata som förväntas:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VM's or VM ScaleSets matching criteria specified

VM's or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency Agent extensions on above 2 VM's or VM Scale Sets.
VM's in a non-running state will be skipped.
Extension will not be re-installed if already installed. Use -ReInstall if desired, for example to update workspace

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-hybrid-environment"></a>Aktivera för hybridmiljö
Det här avsnittet beskrivs hur du publicera virtuella datorer eller fysiska datorer som värd ditt datacenter eller andra molnmiljö för övervakning av Azure Monitor för virtuella datorer.  

I Azure Monitor för virtuella datorer kartan beroendeagenten överföra inte några data själva och det kräver inte ändringar i brandväggar eller portar. Kartdata överförs alltid genom Log Analytics-agenten i Azure Monitor-tjänsten, antingen direkt eller via den [OMS-gatewayen](../../log-analytics/log-analytics-oms-gateway.md) om din IT-säkerhetsprinciper inte tillåter att datorer i nätverket att ansluta till Internet.

Granska kraven och distributionsmetoder för den [Log Analytics Linux och Windows-agenten](../../log-analytics/log-analytics-agent-overview.md).  

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Sammanfattande steg:

1. Installera Log Analytics-agenten för Windows eller Linux
2. Hämta och installera Azure Monitor för virtuella datorer kartan beroendeagenten för [Windows](https://aka.ms/dependencyagentwindows) eller [Linux](https://aka.ms/dependencyagentlinux).
3. Aktivera insamling av prestandaräknare
4. Publicera Azure Monitor för virtuella datorer

### <a name="install-the-dependency-agent-on-windows"></a>Installera beroendeagenten på Windows 
Beroendeagenten kan installeras manuellt på Windows-datorer genom att köra `InstallDependencyAgent-Windows.exe`. Om du kör den här körbara filen utan några alternativ, startar en installationsguide som du kan följa för att installera interaktivt.  

>[!NOTE]
>Administratörsbehörighet krävs för att installera eller avinstallera agenten.

Följande tabell visar de specifika parametrarna som stöds av installationsprogrammet för agenten från kommandoraden.  

| Parameter | Beskrivning |
|:--|:--|
| /? | Returnerar en lista över kommandoradsalternativ. |
| / S | Utföra en tyst installation utan interaktion från användaren. |

Till exempel för att köra installationsprogrammet med det `/?` parametern, Fyll `InstallDependencyAgent-Windows.exe /?`

Filer för Windows beroendeagenten installeras i `C:\Program Files\Microsoft Dependency Agent` som standard.  Om det inte går att starta när installationen är klar beroendeagenten, loggarna för Detaljerad felinformation. Loggkatalogen är `%Programfiles%\Microsoft Dependency Agent\logs`. 

### <a name="install-the-dependency-agent-on-linux"></a>Installera beroendeagenten på Linux
Beroende-agenten är installerad på Linux-servrar från `InstallDependencyAgent-Linux64.bin`, ett kommandoskript med en självextraherande binärfilen. Du kan köra filen med hjälp av `sh` eller Lägg till körbehörighet till själva filen.

>[!NOTE]
> Du måste ha rotbehörighet för att kunna installera eller konfigurera agenten.
> 

| Parameter | Beskrivning |
|:--|:--|
| -hjälp | Hämta en lista med kommandoradsalternativ. |
| -s | Utför en tyst installation utan någon användarprompter. |
| – Kontrollera | Kontrollera behörigheter och operativsystemet, men installera inte agenten. |

Till exempel för att köra installationsprogrammet med det `-help` parametern, Fyll `InstallDependencyAgent-Linux64.bin -help`.

Installera beroendeagenten för Linux som rot genom att köra följande kommando `sh InstallDependencyAgent-Linux64.bin`
    
Om det inte går att starta beroendeagenten, kontrollera loggarna för Detaljerad felinformation. På Linux-agenter till loggkatalogen är `/var/opt/microsoft/dependency-agent/log`.

Filer för beroendeagenten placeras i följande kataloger:

| Filer | Plats |
|:--|:--|
| Kärnfiler | /opt/microsoft/dependency-agent |
| Loggfiler | /var/opt/microsoft/dependency-agent/log |
| Konfigurationsfiler | /etc/opt/microsoft/dependency-agent/config |
| Körbara tjänstfiler | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binära lagringsfiler | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Aktivera prestandaräknare
Om Log Analytics-arbetsytan som refereras av lösningen inte är konfigurerat för att samla in prestandaräknare som krävs av lösningen redan kan måste de vara aktiverat. Detta kan åstadkommas manuellt enligt [här](../../log-analytics/log-analytics-data-sources-performance-counters.md), eller genom att hämta och köra ett PowerShell-skript som är tillgängliga från [Azure Powershell-galleriet](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).
 
### <a name="onboard-azure-monitor-for-vms"></a>Publicera Azure Monitor för virtuella datorer
Den här metoden innehåller en JSON-mall som anger konfigurationen för att aktivera lösningskomponenter till Log Analytics-arbetsytan.  

Om du inte är bekant med begreppet att distribuera resurser med hjälp av en mall, se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md) 

Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. För att identifiera din version, kör `az --version`. Om du behöver installera eller uppgradera Azure CLI kan du läsa [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Skapa och köra en mall

1. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {

    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "WorkspaceName": {
            "type": "string"
        },
        "WorkspaceLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-15-preview",
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('WorkspaceName')]",
            "location": "[parameters('WorkspaceLocation')]",
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },

                    "plan": {
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                        "promotionCode": ""
                    }
                },
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },
                    "plan": {
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                        "promotionCode": ""
                    }
                }
            ]
        }
    ]
    ```

2. Spara filen som **installsolutionsforvminsights.json** till en lokal mapp.
3. Redigera värdena för **WorkspaceName**, **ResourceGroupName**, och **WorkspaceLocation**.  Värdet för **WorkspaceName** är fullständiga resurs-ID för Log Analytics-arbetsytan, som innehåller namnet på arbetsytan och värdet för **WorkspaceLocation** är den region som arbetsytan är definierad i.
4. Du är redo att distribuera den här mallen med hjälp av följande PowerShell-kommando:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

    ```powershell
    provisioningState       : Succeeded
    ```
När du har aktiverat övervakning, kan det ta ungefär 10 minuter innan du kan visa hälsotillstånd och mått för hybrid-dator. 

## <a name="performance-counters-enabled"></a>Prestandaräknare som aktiverats
Azure Monitor för virtuella datorer konfigureras en Log Analytics-arbetsyta för att samla in prestandaräknare som används av lösningen.  I följande tabell visas de objekt och räknare som konfigurerats av lösningen som samlas in var 60: e sekund.

### <a name="windows-performance-counters"></a>Windows-prestandaräknare

|Objektnamn |Namn på räknare |  
|------------|-------------|  
|Logisk disk |Ledigt utrymme i procent |  
|Logisk disk |Genomsn. S/diskläsning |  
|Logisk disk |Genomsn. S/disköverföring |  
|Logisk disk |Genomsn. S/diskskrivning |  
|Logisk disk |Disk byte/sek |  
|Logisk disk |Disk – lästa byte/sek |  
|Logisk disk |Diskläsningar/sek |  
|Logisk disk |Disköverföringar/sek |  
|Logisk disk |Disk – skrivna byte/sek |  
|Logisk disk |Diskskrivningar/sek |  
|Logisk disk |Ledigt utrymme i MB |  
|Minne |Tillgängliga megabyte |  
|Nätverkskort |Mottagna byte/sek |  
|Nätverkskort |Skickade byte/sek |  
|Processor |Tid i procent för processor |  

### <a name="linux-performance-counters"></a>Prestandaräknare för Linux

|Objektnamn |Namn på räknare |  
|------------|-------------|  
|Logisk Disk |Använt utrymme i procent |  
|Logisk Disk |Disk – lästa byte/sek |  
|Logisk Disk |Diskläsningar/sek |  
|Logisk Disk |Disköverföringar/sek |  
|Logisk Disk |Disk – skrivna byte/sek |  
|Logisk Disk |Diskskrivningar/sek |  
|Logisk Disk |Ledigt utrymme i MB |  
|Logisk Disk |Logisk Disk byte/sek |  
|Minne |Tillgängligt minne i megabyte |  
|Nätverk |Totalt antal byte mottaget |  
|Nätverk |Totalt antal överförda byte |  
|Processor |Tid i procent för processor |  

## <a name="diagnostic-and-usage-data"></a>Diagnostik- och användningsdata
Microsoft samlar automatiskt in användnings- och data via din användning av Azure Monitor-tjänsten. Microsoft använder dessa data för att tillhandahålla och förbättra kvaliteten, säkerheten och integriteten för tjänsten. Data från funktionen kartan innehåller information om konfigurationen av din programvara, till exempel operativsystem och version, IP-adress, DNS-namn och namn på arbetsstation för att tillhandahålla korrekta och effektiva funktioner för felsökning. Microsoft samlar inte in namn, adresser eller annan kontaktinformation.

Mer information om insamling och användning finns i den [sekretesspolicyn för Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]
## <a name="next-steps"></a>Nästa steg

Den här informationen är tillgänglig för analys med Azure Monitor för virtuella datorer med övervakning aktiverad för den virtuella datorn.  Läs hur du använder funktionen hälsotillstånd i [visa Azure Monitor för virtuella datorer Health](vminsights-health.md), eller om du vill visa identifierade programberoenden, se [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md).  