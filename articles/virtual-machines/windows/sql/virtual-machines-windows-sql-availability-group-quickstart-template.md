---
title: Skapa WSFC, lyssnaren och och konfigurera ILB för en Always On-tillgänglighetsgrupp på en SQL Server-dator med Azure-Snabbstartsmall
description: Använda Azure Quickstart-mallar för att förenkla processen för att skapa Tillgänglighetsgrupper för SQL Server-datorer i Azure med hjälp av en mall för att skapa klustret, ansluta till SQL-datorer i klustret, skapa lyssnaren och konfigurera den interna Belastningsutjämnaren.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 9be8717bc9b1d15a59486edf206dd0657a711c06
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360331"
---
# <a name="create-wsfc-listener-and-configure-ilb-for-an-always-on-availability-group-on-a-sql-server-vm-with-azure-quickstart-template"></a>Skapa WSFC, lyssnaren och och konfigurera ILB för en Always On-tillgänglighetsgrupp på en SQL Server-dator med Azure-Snabbstartsmall
Den här artikeln beskriver hur du använder Azure-Snabbstartsmallar att delvis automatisera distributionen av en Always On tillgänglighetsgruppens konfiguration för SQL Server-datorer i Azure. Det finns två Azure-Snabbstartsmallar som används i den här processen. 

   | Mall | Beskrivning |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Skapar Windows-redundanskluster och ansluter till SQL Server-datorer till den. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Skapar tillgänglighetsgruppens lyssnare och konfigurerar den interna belastningsutjämnaren. |
   | &nbsp; | &nbsp; |

Andra delar av tillgänglighetsgruppens konfiguration måste göras manuellt, till exempel skapa tillgänglighetsgruppen och skapa den interna belastningsutjämnaren. Den här artikeln innehåller automatisk och manuell frågornas ordningsföljd.
 

## <a name="prerequisites"></a>Förutsättningar 
För att automatisera installationen av en Always On-tillgänglighetsgrupp med quickstart-mallar, måste du redan har följande krav: 
- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En resursgrupp med en [domänkontrollant](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-forest). 
- En eller flera domänanslutna [virtuella datorer i Azure som kör SQLServer 2016 (eller högre) Enterprise edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) i samma uppsättning eller tillgänglighet tillgänglighetszon som har [registrerad hos resursprovidern SQL VM](#register-existing-sql-vm-with-new-resource-provider).  

## <a name="register-existing-sql-vm-with-new-resource-provider"></a>Registrera den befintliga SQL VM med ny resursprovider
Eftersom dessa tillgänglighetsgruppen som Azure-Snabbstartsmallar förlitar sig på resursprovidern SQL VM (Microsoft.SqlVirtualMachine), måste befintliga SQL Server-datorer registreras hos SQL VM-resursprovidern. Hoppa över det här steget om du har skapat din SQL Server-VM efter December 2018, som alla SQL Server-datorer som skapas efter det här datumet registreras automatiskt. Det här avsnittet innehåller steg för att registrera med providern med hjälp av Azure portal, men du kan också använda [PowerShell](virtual-machines-windows-sql-ahb.md#powershell). 

  >[!IMPORTANT]
  > Om du släpper din SQL Server-VM-resurs ska du gå tillbaka till inställningen hårdkodad licens för avbildningen. 

1. Öppna Azure portal och gå till **alla tjänster**. 
1. Gå till **prenumerationer** och välj prenumerationen av intresse.  
1. I den **prenumerationer** bladet går du till **Resursprovidern**. 
1. Typ `sql` i filtret för att få fram de SQL-relaterade resursprovidrar. 
1. Välj antingen *registrera*, *Omregistrera*, eller *avregistrera* för den **Microsoft.SqlVirtualMachine** providern beroende på din önskad åtgärd. 

  ![Ändra providern](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

## <a name="step-1---create-the-wsfc-and-join-sql-server-vms-to-the-cluster-using-quickstart-template"></a>Steg 1 – Skapa WSFC och ansluta till SQL Server-datorer till klustret med snabbstartsmall 
När SQL Server-datorer har registrerats med den nya resurs-providern för SQL VM, du kan ansluta till SQL Server-datorer i *SqlVirtualMachineGroup*. Den här resursen definierar metadata för Windows-redundanskluster, inklusive versionen, utgåva, fullständigt kvalificerat domännamn, AD-konton att hantera klustret och Storage-konto som Molnvittnet. Att lägga till SQL Server-VM till den *SqlVirtualMachineGroup* startar den Windows-tjänsten för redundanskluster och ansluter till SQL Server-datorer i klustret. Det här steget är automatiserade med den **101-sql-vm-ag-setup** snabbstartsmall och kan implementeras med följande steg:

1. Navigera till den [ **101-sql-vm-ag-setup** ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) snabbstartsmall och välj **distribuera till Azure** att starta snabbstartsmall i Azure-portalen.
1. Fyll i de obligatoriska fälten för att konfigurera Windows-redundanskluster metadata. Valfria fält kan vara tomt.

    I följande tabell visas värden som krävs för mallen: 

   | **Fält** | Värde |
   | --- | --- |
   | **Prenumeration** |  Den prenumeration där din SQL Server-datorer finns. |
   |**Resursgrupp** | Den resursgrupp där din SQL Server-datorer finns. | 
   |**Namn på failover-kluster** | Önskat namn för ditt nya redundanskluster i Windows. |
   | **Befintlig Vm-lista** | Den SQL Server-datorer du vill delta i tillgänglighetsgruppen och därför inte ingå i den här nya klustret. Separera värdena med ett kommatecken och ett blanksteg (t.ex.: SQLVM1, SQLVM2). |
   | **SQL Server Version** | Välj den SQL Server-versionen av SQL Server-datorer i listrutan. För närvarande bara SQL 2016 och SQL 2017-avbildningar stöds. |
   | **Befintligt fullständigt kvalificerade domännamn** | Befintliga FQDN för domänen där din SQL Server-datorer finns. |
   | **Befintligt domänkonto** | Ett befintligt domänkonto som har sysadmin-åtkomst till SQL Server. | 
   | **Lösenordet för domänkontot** | Lösenordet för det tidigare nämnda domänkontot. | 
   | **Befintliga Sql-tjänstkontot** | Domänanvändarkonto som används för att kontrollera SQL Server-tjänsten. Den här informationen kan hittas med hjälp av den [ **SQL Server Configuration Manager**](https://docs.microsoft.com/sql/relational-databases/sql-server-configuration-manager?view=sql-server-2017). |
   | **Lösenord för SQL-tjänsten** | Lösenordet som används av domänanvändarkonto som styr SQL Server-tjänsten. |
   | &nbsp; | &nbsp; |

1. Om du samtycker till villkoren markerar du kryssrutan bredvid **jag godkänner villkoren som anges ovan** och välj **köp** att slutföra malldistributionen Snabbstart. 
1. För att övervaka din distribution, väljer du antingen distributionen från den **meddelanden** klockikonen i övre navigeringsfältet-banderollen eller navigera till din **resursgrupp** i Azure-portalen väljer du  **Distributioner** i den **inställningar** och välj ”Microsoft.Template”-distributionen. 

## <a name="step-2---manually-create-the-availability-group"></a>Steg 2 – Skapa tillgänglighetsgruppen manuellt 
Skapa tillgänglighetsgruppen manuellt som vanligt, med hjälp av antingen [PowerShell](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell?view=sql-server-2017), [SQL Server Management Studio](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio?view=sql-server-2017) eller [Transact-SQL](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql?view=sql-server-2017). 

  >[!IMPORTANT]
  > Gör **inte** skapa en lyssnare just nu eftersom det sker automatiskt av den **101-sql-vm-aglistener-setup** snabbstartsmall i steg 4. 

## <a name="step-3---manually-create-the-internal-load-balancer-ilb"></a>Steg 3 – manuellt skapa den interna belastningsutjämnaren (ILB)
Always On (AG) tillgänglighetsgruppslyssnaren kräver en intern Azure Load Balancer (ILB). Den interna Belastningsutjämnaren ger en ”flytande” IP-adress för AG-lyssnare som möjliggör snabbare redundans och återanslutning. Om SQL Server-datorer i en tillgänglighetsgrupp är en del av samma tillgänglighetsuppsättning, kan du använda en grundläggande belastningsutjämnaren. Annars kan behöva du använda en Standard Load Balancer.  **Den interna Belastningsutjämnaren måste vara i samma virtuella nätverk som SQL Server-VM-instanser.** Den interna Belastningsutjämnaren behöver bara skapas, resten av konfigurationen (till exempel serverdelspool, hälsoavsökning och belastningsutjämning regler) hanteras av den **101-sql-vm-aglistener-setup** snabbstartsmall i steg 4. 

1. Öppna resursgruppen som innehåller SQL Server-datorer i Azure-portalen. 
2. I resursgruppen, klickar du på **Lägg till**.
3. Sök efter **belastningsutjämnare** och markera i sökresultaten **Load Balancer**, som publiceras av **Microsoft**.
4. På den **belastningsutjämnaren** bladet klickar du på **skapa**.
5. I den **skapa belastningsutjämnare** dialogrutan Konfigurera belastningsutjämnaren på följande sätt:

   | Inställning | Värde |
   | --- | --- |
   | **Namn** |Ett namn som representerar belastningsutjämnaren. Till exempel **sqlLB**. |
   | **Typ** |**Interna**: De flesta implementeringar av använda en intern belastningsutjämnare, vilket gör att program i samma virtuella nätverk att ansluta till tillgänglighetsgruppen.  </br> **Externa**: Gör att program kan ansluta till tillgänglighetsgrupp via en offentlig Internetanslutning. |
   | **Virtuellt nätverk** |Välj det virtuella nätverket som SQL Server-instanserna. |
   | **Undernät** |Välj det undernät som SQL Server-instanser är i. |
   | **IP-adresstilldelning** |**Statisk** |
   | **Privat IP-adress** |Ange en tillgänglig IP-adress från undernätet. Använd den här IP-adressen när du skapar en lyssnare på klustret.|
   | **Prenumeration** |Om du har flera prenumerationer, visas det här fältet. Välj den prenumeration som du vill associera med den här resursen. Det är vanligtvis samma prenumeration som alla resurser för tillgänglighetsgruppen. |
   | **Resursgrupp** |Välj den resursgrupp som SQL Server-instanserna. |
   | **Plats** |Välj den Azure-plats som SQL Server-instanserna. |
   | &nbsp; | &nbsp; |

6. Välj **Skapa**. 


  >[!NOTE]
  > Den offentliga IP-adressresursen för varje SQL Server VM ska ha en standard-SKU att vara kompatibel med Standard Load Balancer. För att fastställa SKU för den Virtuella datorns offentliga IP-resurs, navigera till din **resursgrupp**väljer din **offentliga IP-adressen** resurs för den virtuella SQL Server-datorn, och leta upp värdet under **SKU**  av den **översikt** fönstret. 

## <a name="step-4---create-the-ag-listener-and-configure-the-ilb-with-the-quickstart-template"></a>Steg 4 – skapa AG-lyssnare och konfigurera den interna Belastningsutjämnaren med snabbstartsmallen

Skapa tillgänglighetsgruppens lyssnare och konfigurera den interna belastningsutjämnaren (ILB) automatiskt med den **101-sql-vm-aglistener-setup** snabbstartsmall som det etablerar Microsoft.SqlVirtualMachine/Sql virtuella Datorresurs grupper/tillgänglighetsgruppens lyssnare. Den **101-sql-vm-aglistener-setup** snabbstartsmall via SQL VM-resursprovidern har följande åtgärder:

 - Konfigurerar nätverksinställningar för klustret och ILB. 
 - Konfigurerar ILB-serverdelspoolen, hälsoavsökning och belastningsutjämning regler.
 - Skapar AG-lyssnare med den angivna IP-adressen och namnet.

Om du vill konfigurera den interna Belastningsutjämnaren och skapa AG-lyssnare, gör du följande:
1. Navigera till den [ **101-sql-vm-aglistener-setup** ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) snabbstartsmall och välj **distribuera till Azure** att starta snabbstartsmall i Azure-portalen.
1. Fyll i fälten som krävs för att konfigurera den interna Belastningsutjämnaren och skapa tillgänglighetsgruppens lyssnare. Valfria fält kan vara tomt. 

    I följande tabell visas värden som krävs för mallen: 

   | **Fält** | Värde |
   | --- | --- |
   |**Resursgrupp** | Den resursgrupp där din SQL Server-datorer och tillgänglighetsgruppen finns. | 
   |**Befintligt redundanskluster namn** | Namnet på klustret som SQL Server-datorer är anslutna till. |
   | **Befintliga Sql-tillgänglighetsgrupp**| Namnet på tillgänglighetsgruppen som SQL Server-datorer är en del av. |
   | **Befintlig Vm-lista** | Namn på SQL Server-datorer som ingår i de tidigare nämnda tillgänglighetsgruppen. Namnen ska avgränsas med kommatecken eller ett blanksteg (t.ex.: SQLVM1, SQLVM2). |
   | **Befintligt fullständigt kvalificerade domännamn** | Befintliga FQDN för domänen där din SQL Server-datorer finns. |
   | **Lyssnare** | DNS-namnet som du vill tilldela till lyssnaren. Den här mallen innehåller namnet aglistener som standard, men kan ändras. |
   | **Lyssningsport** | Den port som du vill att du använder. Normalt den här porten ska vara standardporten 1433, och därför måste det här är det portnummer som anges av den här mallen. Men om din standardporten har ändrats bör sedan lyssningsport använda värdet i stället. | 
   | **Befintligt virtuellt nätverk** | Namnet på det virtuella nätverket där din SQL Server-datorer och ILB finns. |
   | **Befintligt undernät** | Den *namn* på det interna undernätet för SQL Server-datorer (ex: standard). Det här värdet kan fastställas genom att gå till din **resursgrupp**, välja din **vNet**, välja **undernät** under den **inställningar**fönstret och kopiera värdet under **namn**. |
   | **Befintlig intern belastningsutjämnare** | Namnet på den interna Belastningsutjämnaren som du skapade i steg3. |
   | **Avsökningsport** | Avsökningsporten som du vill att den interna Belastningsutjämnaren för att använda. Mallen använder 59999 som standard men du kan ändra det här värdet. |
   | &nbsp; | &nbsp; |

1. Om du samtycker till villkoren markerar du kryssrutan bredvid **jag godkänner villkoren som anges ovan** och välj **köp** att slutföra malldistributionen Snabbstart. 
1. För att övervaka din distribution, väljer du antingen distributionen från den **meddelanden** klockikonen i övre navigeringsfältet-banderollen eller navigera till din **resursgrupp** i Azure-portalen väljer du  **Distributioner** i den **inställningar** och välj ”Microsoft.Template”-distributionen. 

  >[!NOTE]
  >Om distributionen misslyckas halvvägs genom, måste du manuellt [ta bort den nyligen skapade lyssnaren](#remove-availability-group-listener) med hjälp av PowerShell innan du omdistribuerar den **101-sql-vm-aglistener-setup** snabbstartsmall. 

## <a name="remove-availability-group-listener"></a>Ta bort tillgänglighetsgruppens lyssnare
Om du senare behöver ta bort tillgänglighetsgruppens lyssnare konfigurerats i mallen, måste du gå igenom SQL VM-resursprovidern. Eftersom lyssnaren har registrerats via SQL VM-resursprovidern kan är bara tas bort via SQL Server Management Studio otillräcklig. Den ska faktiskt tas bort via SQL VM-resursprovidern med hjälp av PowerShell. Detta tar bort AG-lyssnare metadata från SQL VM-resursprovidern och tar bort fysiskt lyssnaren från tillgänglighetsgruppen. 

Följande kodavsnitt tar bort SQL tillgänglighetsgruppens lyssnare från både den SQL-resursprovidern och från tillgänglighetsgruppen: 

```PowerShell
# Remove the AG listener
# example: Remove-AzureRmResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzureRmResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="known-issues-and-errors"></a>Kända problem och fel
Det här avsnittet beskrivs några kända problem och deras möjlig lösning. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>Tillgänglighetsgruppslyssnaren för tillgänglighetsgruppen '\<AG-namn >' finns redan
Valda availability-gruppen som används i Azure-Snabbstartsmall för AG-lyssnare redan innehåller en lyssnare fysiskt i tillgänglighetsgruppen, eller som metadata i SQL VM-resursprovidern.  Ta bort lyssnaren med [PowerShell](#remove-availability-group-listener) innan du omdistribuerar den **101-sql-vm-aglistener-setup** snabbstartsmall. 

### <a name="connection-only-works-from-primary-replica"></a>Anslutningen fungerar bara från primära repliken
Det här beteendet är sannolikt efter en misslyckad **101-sql-vm-aglistener-setup** malldistributionen lämnar ILB-konfigurationen i ett inkonsekvent tillstånd. Kontrollera att serverdelspoolen visar tillgänglighetsuppsättningen och att det finns regler för hälsoavsökningen och för regler för belastningsutjämning. Om något saknas, är ett inkonsekvent tillstånd med ILB-konfigurationen. 

Lös problemet genom att ta bort lyssnaren med [PowerShell](#remove-availability-group-listener), ta bort den interna belastningsutjämnaren via Azure portal och börja om igen [steg3](#step-3---manually-create-the-internal-load-balanced-ilb). 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest - endast SQL VM-lista kan uppdateras
Det här felet kan uppstå när du distribuerar den **101-sql-vm-aglistener-setup** mall om lyssnaren togs bort via SQL Server Management Studio (SSMS), men har inte tagits bort från SQL VM-resursprovidern. Tar bort lyssnaren via SSMS tar inte bort metadata för lyssnaren från resursprovidern SQL VM; lyssnaren måste tas bort från resursprovidern med hjälp av [PowerShell](#remove-availability-group-listener). 


## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQLServer-dator](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQLServer VM vanliga frågor och svar](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server-VM priser vägledning](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Viktig information om SQL Server-VM](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Växla licensieringsmodellerna för en SQL Server VM](virtual-machines-windows-sql-ahb.md)



