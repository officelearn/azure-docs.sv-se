---
title: Använd Azure snabb starts mallar för att konfigurera en tillgänglighets grupp som alltid är tillgänglig för SQL Server på en virtuell Azure-dator
description: Använd Azures snabb starts mallar för att skapa Windows-redundansklustret, Anslut SQL Server virtuella datorer till klustret, skapa lyssnaren och konfigurera den interna belastningsutjämnaren i Azure.
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
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f95d3487adecb17e0f4b79e81a08e16bafe4594f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855260"
---
# <a name="use-azure-quickstart-templates-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Använd Azure snabb starts mallar för att konfigurera en tillgänglighets grupp som alltid är tillgänglig för SQL Server på en virtuell Azure-dator
Den här artikeln beskriver hur du använder Azures snabb starts mallar för att delvis automatisera distributionen av en Always on-tillgänglighets grupps konfiguration för SQL Server virtuella datorer i Azure. Två Azure snabb starts mallar används i den här processen: 

   | Mall | Beskrivning |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Skapar Windows-redundansklustret och ansluter SQL Server virtuella datorer till den. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Skapar tillgänglighets gruppens lyssnare och konfigurerar den interna belastningsutjämnaren. Den här mallen kan bara användas om Windows-redundansklustret skapades med mallen **101-SQL-VM-AG-setup** . |
   | &nbsp; | &nbsp; |

Andra delar av tillgänglighets grupps konfigurationen måste utföras manuellt, till exempel skapa tillgänglighets gruppen och skapa den interna belastningsutjämnaren. Den här artikeln innehåller en sekvens med automatiserade och manuella steg.
 

## <a name="prerequisites"></a>Förutsättningar 
Om du vill automatisera installationen av en tillgänglighets grupp som alltid är tillgänglig med hjälp av snabb starts mallar måste du ha följande krav: 
- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En resurs grupp med en domänkontrollant. 
- En eller flera domänanslutna [virtuella datorer i Azure som kör SQL Server 2016 (eller senare) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) som finns i samma tillgänglighets uppsättning eller tillgänglighets zon och som har [registrerats med den virtuella SQL](virtual-machines-windows-sql-register-with-resource-provider.md)-providern för virtuella datorer.  
- Två tillgängliga (används inte av någon entitet) IP-adresser: en för den interna belastningsutjämnaren och en för tillgänglighets gruppens lyssnare i samma undernät som tillgänglighets gruppen. Om en befintlig belastningsutjämnare används behöver du bara en tillgänglig IP-adress.  

## <a name="permissions"></a>Behörigheter
Följande behörigheter är nödvändiga för att konfigurera tillgänglighets gruppen Always on med hjälp av Azure snabb starts mallar: 

- Ett befintligt domän användar konto som har behörighet att **skapa dator objekt** i domänen.  Till exempel har ett domän administratörs konto vanligt vis tillräcklig behörighet (till exempel account@domain.com:). _Detta konto bör också vara en del av den lokala administratörs gruppen på varje virtuell dator för att skapa klustret._
- Domän användar kontot som styr SQL Servers tjänsten. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>Steg 1: Skapa klustret för växling vid fel och Anslut SQL Server virtuella datorer till klustret med hjälp av en snabb starts mall 
När dina SQL Server virtuella datorer har registrerats med resurs leverantören för SQL-VM kan du ansluta dina SQL Server virtuella datorer till *SqlVirtualMachineGroups*. Den här resursen definierar metadata för Windows-redundansklustret. Metadata innehåller version, utgåva, fullständigt kvalificerat domän namn Active Directory konton för att hantera både klustret och tjänsten SQL Server och lagrings kontot som moln vittne. 

Om du lägger till SQL Server virtuella datorer i resurs gruppen *SqlVirtualMachineGroups* startar kluster tjänsten Windows-redundans för att skapa klustret och ansluter sedan till de SQL Server virtuella datorerna till klustret. Det här steget är automatiserat med snabb starts mal len **101-SQL-VM-AG-setup** . Du kan implementera det med hjälp av följande steg:

1. Gå till snabb starts mal len [**101-SQL-VM-AG-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) . Välj sedan **distribuera till Azure** för att öppna snabb starts mal len i Azure Portal.
1. Fyll i de obligatoriska fälten om du vill konfigurera metadata för Windows-redundansklustret. Du kan lämna de valfria fälten tomma.

   Följande tabell visar de nödvändiga värdena för mallen: 

   | **Fält** | Value |
   | --- | --- |
   | **Prenumeration** |  Den prenumeration där SQL Server virtuella datorer finns. |
   |**Resursgrupp** | Resurs gruppen där dina SQL Server virtuella datorer finns. | 
   |**Kluster namn för växling vid fel** | Det namn som du vill använda för det nya Windows-redundansklustret. |
   | **Befintlig VM-lista** | De SQL Server virtuella datorer som du vill delta i tillgänglighets gruppen och vara en del av det nya klustret. Avgränsa dessa värden med kommatecken och blank steg (till exempel: *SQLVM1, SQLVM2*). |
   | **SQL Server version** | SQL Server versionen av dina SQL Server virtuella datorer. Välj den i list rutan. För närvarande stöds endast SQL Server 2016 och SQL Server 2017-avbildningar. |
   | **Befintligt fullständigt kvalificerat domän namn** | Det befintliga fullständiga domän namnet för den domän där SQL Server virtuella datorerna finns. |
   | **Befintligt domän konto** | Ett befintligt domän användar konto som har behörighet att **skapa dator objekt** i domänen när [CNO: t](/windows-server/failover-clustering/prestage-cluster-adds) skapas när mallen distribueras. Till exempel har ett domän administratörs konto vanligt vis tillräcklig behörighet (till exempel account@domain.com:). *Detta konto bör också vara en del av den lokala administratörs gruppen på varje virtuell dator för att skapa klustret.*| 
   | **Lösen ord för domän konto** | Lösen ordet för det tidigare nämnda domän användar kontot. | 
   | **Befintligt SQL-tjänstkonto** | Domän användar kontot som styr [SQL Server tjänsten](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) under distribution av tillgänglighets grupper (till exempel: account@domain.com). |
   | **SQL-tjänstens lösen ord** | Lösen ordet som används av det domän användar konto som styr SQL Servers tjänsten. |
   | **Namn på moln vittne** | Ett nytt Azure Storage-konto som ska skapas och användas för moln vittnet. Du kan ändra det här namnet. |
   | **\_artefakt plats** | Det här fältet anges som standard och ska inte ändras. |
   | **\_SAS-token för artefakt plats** | Det här fältet lämnas avsiktligt tomt. |
   | &nbsp; | &nbsp; |

1. Om du godkänner villkoren markerar du kryss rutan **Jag accepterar villkoren som anges ovan** . Välj sedan **köp** för att slutföra distributionen av snabb starts mal len. 
1. Om du vill övervaka din distribution väljer du antingen distributionen i klock ikonen för **meddelanden** i den övre navigerings banderollen eller går till **resurs grupp** i Azure Portal. Välj **distributioner** under **Inställningar**och välj distributionen av **Microsoft. template** . 

>[!NOTE]
> De autentiseringsuppgifter som angavs vid mal distributionen lagras bara för distributionens längd. När distributionen är klar tas lösen orden bort. Du uppmanas att ange dem igen om du lägger till fler SQL Server virtuella datorer i klustret. 


## <a name="step-2-manually-create-the-availability-group"></a>Steg 2: Skapa tillgänglighets gruppen manuellt 
Skapa tillgänglighets gruppen manuellt som vanligt, genom att använda [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)eller [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> Skapa *inte* en lyssnare just nu, eftersom snabb starts mal len för **101-SQL-VM-aglistener** gör det automatiskt i steg 4. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>Steg 3: Skapa den interna belastningsutjämnaren manuellt
Lyssnaren för Always on-tillgänglighetsgrupper kräver en intern instans av Azure Load Balancer. Den interna belastningsutjämnaren innehåller en "flytande" IP-adress för tillgänglighets gruppens lyssnare som möjliggör snabbare redundans och åter anslutning. Om SQL Server virtuella datorer i en tillgänglighets grupp ingår i samma tillgänglighets uppsättning kan du använda en grundläggande belastningsutjämnare. Annars måste du använda en standard belastningsutjämnare. 

> [!IMPORTANT]
> Den interna belastningsutjämnaren bör finnas i samma virtuella nätverk som SQL Server VM-instanserna. 

Du behöver bara skapa den interna belastningsutjämnaren. I steg 4 hanterar snabb starts mal len **101-SQL-VM-aglistener-setup** resten av konfigurationen (till exempel backend-poolen, hälso avsökningen och regler för belastnings utjämning). 

1. Öppna den resurs grupp som innehåller de SQL Server virtuella datorerna i Azure Portal. 
2. I resurs gruppen väljer du **Lägg till**.
3. Sök efter **belastningsutjämnare**. I Sök resultaten väljer du **Load Balancer**, som publiceras av **Microsoft**.
4. På bladet **Load Balancer** väljer du **skapa**.
5. I dialog rutan **skapa belastnings utjämning** konfigurerar du belastningsutjämnaren enligt följande:

   | Inställning | Value |
   | --- | --- |
   | **Namn** |Ange ett text namn som representerar belastningsutjämnaren. Skriv till exempel **sqlLB**. |
   | **Typ** |**Internt**: De flesta implementeringar använder en intern belastningsutjämnare som gör det möjligt för program i samma virtuella nätverk att ansluta till tillgänglighets gruppen.  </br> **Externt**: Tillåter att program ansluter till tillgänglighets gruppen via en offentlig Internet anslutning. |
   | **Virtuellt nätverk** | Välj det virtuella nätverk som SQL Servers instanserna finns i. |
   | **Undernät** | Välj det undernät som de SQL Server instanserna finns i. |
   | **Tilldelning av IP-adress** |**Oföränderlig** |
   | **Privat IP-adress** | Ange en tillgänglig IP-adress från under nätet. |
   | **Prenumeration** |Om du har flera prenumerationer kan det här fältet visas. Välj den prenumeration som du vill koppla till den här resursen. Det är normalt samma prenumeration som alla resurser för tillgänglighets gruppen. |
   | **Resursgrupp** |Välj den resurs grupp som SQL Server instanserna finns i. |
   | **Location** |Välj den Azure-plats som SQL Server instanserna finns i. |
   | &nbsp; | &nbsp; |

6. Välj **Skapa**. 


>[!IMPORTANT]
> Den offentliga IP-resursen för varje SQL Server VM måste ha en standard-SKU för att vara kompatibel med standard belastnings utjämningen. Ta reda på SKU: n för den virtuella datorns offentliga IP-resurs genom att gå till **resurs grupp**, välja din **offentliga IP** -adressresurs för SQL Server VM och leta upp värdet under **SKU** i fönstret **Översikt** . 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>Steg 4: Skapa tillgänglighets gruppens lyssnare och konfigurera den interna belastningsutjämnaren med hjälp av snabb starts mal len

Skapa tillgänglighets gruppens lyssnare och konfigurera den interna belastningsutjämnaren automatiskt med hjälp av snabb starts mal len **101-SQL-VM-aglistener-setup** . Mallen etablerar resursen Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener. Snabb starts mal len **101-SQL-VM-aglistener-setup** , via SQL VM-adressresursen, gör följande:

- Skapar en ny IP-resurs för klient delen (baserat på det IP-värde som angavs under distributionen) för lyssnaren. 
- Konfigurerar nätverks inställningarna för klustret och den interna belastningsutjämnaren. 
- Konfigurerar backend-poolen för den interna belastningsutjämnaren, hälso avsökningen och reglerna för belastnings utjämning.
- Skapar tillgänglighets gruppens lyssnare med den tilldelade IP-adressen och namnet.
 
>[!NOTE]
> Du kan använda **101-SQL-VM-aglistener-setup** endast om Windows-redundansklustret skapades med mallen **101-SQL-VM-AG-setup** .
   
   
Gör så här för att konfigurera den interna belastningsutjämnaren och skapa tillgänglighets gruppens lyssnare:
1. Gå till snabb starts mal len [101-SQL-VM-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) och välj **distribuera till Azure** för att starta snabb starts mal len i Azure Portal.
1. Fyll i de obligatoriska fälten för att konfigurera den interna belastningsutjämnaren och skapa tillgänglighets gruppens lyssnare. Du kan lämna de valfria fälten tomma. 

   Följande tabell visar de nödvändiga värdena för mallen: 

   | **Fält** | Värde |
   | --- | --- |
   |**Resursgrupp** | Resurs gruppen där SQL Server VM och tillgänglighets grupp finns. | 
   |**Befintligt kluster namn för växling vid fel** | Namnet på det kluster som dina SQL Server virtuella datorer är anslutna till. |
   | **Befintlig SQL-tillgänglighets grupp**| Namnet på den tillgänglighets grupp som dina SQL Server virtuella datorer ingår i. |
   | **Befintlig VM-lista** | Namnen på de SQL Server virtuella datorer som ingår i den tidigare nämnda tillgänglighets gruppen. Avgränsa namnen med kommatecken och blank steg (till exempel: *SQLVM1, SQLVM2*). |
   | **Lyssnare** | Det DNS-namn som du vill tilldela lyssnaren. Som standard anger den här mallen namnet "aglistener", men du kan ändra den. Namnet får inte överskrida 15 tecken. |
   | **Lyssnar port** | Den port som du vill att lyssnaren ska använda. Normalt ska den här porten vara standardvärdet 1433. Detta är port numret som mallen anger. Men om standard porten har ändrats bör lyssnar porten använda det värdet i stället. | 
   | **Lyssnare-IP** | Den IP-adress som du vill att lyssnaren ska använda. Den här adressen skapas när mallen distribueras, så ange en som inte redan används.  |
   | **Befintligt undernät** | Namnet på det interna under nätet för SQL Server virtuella datorer (till exempel: *standard*). Du kan fastställa det här värdet genom att gå till **resurs grupp**, välja ditt virtuella nätverk, välja **undernät** i fönstret **Inställningar** och kopiera värdet under **namn**. |
   | **Befintliga interna Load Balancer** | Namnet på den interna belastningsutjämnare som du skapade i steg 3. |
   | **Avsöknings port** | Avsöknings porten som du vill att den interna belastningsutjämnaren ska använda. Mallen använder 59999 som standard, men du kan ändra det här värdet. |
   | &nbsp; | &nbsp; |

1. Om du godkänner villkoren markerar du kryss rutan **Jag accepterar villkoren som anges ovan** . Välj **köp** för att slutföra distributionen av snabb starts mal len. 
1. Om du vill övervaka din distribution väljer du antingen distributionen i klock ikonen för **meddelanden** i den övre navigerings banderollen eller går till **resurs grupp** i Azure Portal. Välj **distributioner** under **Inställningar**och välj distributionen av **Microsoft. template** . 

>[!NOTE]
>Om distributionen Miss lyckas halvvägs måste du manuellt [ta bort den nyligen skapade lyssnaren](#remove-the-availability-group-listener) med hjälp av PowerShell innan du distribuerar snabb starts mal len **101-SQL-VM-aglistener-setup** . 

## <a name="remove-the-availability-group-listener"></a>Ta bort tillgänglighets gruppens lyssnare
Om du senare behöver ta bort tillgänglighets gruppens lyssnare som mallen har kon figurer ATS för måste du gå igenom SQL VM-providern. Eftersom lyssnaren har registrerats via SQL VM Resource Provider, tar du bara bort den via SQL Server Management Studio är otillräcklig. 

Den bästa metoden är att ta bort den via en SQL VM-adressresurs genom att använda följande kodfragment i PowerShell. Om du gör det tas tillgänglighets gruppens lyssnar metadata bort från providern för SQL VM-resursen. Det tar också bort en lyssnare från tillgänglighets gruppen fysiskt. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Vanliga fel
I det här avsnittet beskrivs några kända problem och eventuell lösning. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>Tillgänglighets gruppens lyssnare för tillgänglighets gruppen '\<AG-Name > ' finns redan
Den valda tillgänglighets gruppen som används i Azure snabb starts mal len för tillgänglighets grupps lyssnaren innehåller redan en lyssnare. Antingen är den fysiskt i tillgänglighets gruppen, eller så finns dess metadata kvar i den virtuella SQL-adressresursen. Ta bort lyssnaren med hjälp av [PowerShell](#remove-the-availability-group-listener) innan du distribuerar snabb starts mal len **101-SQL-VM-aglistener-setup** . 

### <a name="connection-only-works-from-primary-replica"></a>Anslutningen fungerar bara från den primära repliken
Det här beteendet är förmodligen en misslyckad distribution av **101-SQL-VM-aglistener** -mallar som har lämnat konfigurationen av den interna belastningsutjämnaren i ett inkonsekvent tillstånd. Kontrol lera att Server delens pool listar tillgänglighets uppsättningen och att det finns regler för hälso avsökningen och reglerna för belastnings utjämning. Om något saknas är konfigurationen av den interna belastnings utjämningen ett inkonsekvent tillstånd. 

Lös problemet genom att ta bort lyssnaren med hjälp av [PowerShell](#remove-the-availability-group-listener), ta bort den interna belastningsutjämnaren via Azure Portal och börja om från steg 3. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest-det går inte att uppdatera listan över virtuella SQL-datorer
Det här felet kan inträffa när du distribuerar mallen **101-SQL-VM-aglistener-setup** om lyssnaren har tagits bort via SQL Server Management Studio (SSMS), men inte togs bort från providern för SQL VM-resursen. Om du tar bort lyssnaren via SSMS tas inte metadata för lyssnaren bort från providern för SQL VM-resursen. Lyssnaren måste tas bort från resurs leverantören via [PowerShell](#remove-the-availability-group-listener). 

### <a name="domain-account-does-not-exist"></a>Domän kontot finns inte
Det här felet kan ha två orsaker. Antingen finns inte det angivna domän kontot eller så saknar det [UPN-data (User Principal Name)](/windows/desktop/ad/naming-properties#userprincipalname) . Mallen **101-SQL-VM-AG-setup** förväntar sig ett domän konto i UPN-formuläret (det *user@domain.com* vill säga), men vissa domän konton kanske saknar det. Detta inträffar vanligt vis när en lokal användare har migrerats till det första domän administratörs kontot när servern befordrades till en domänkontrollant, eller när en användare skapades via PowerShell. 

Kontrol lera att kontot finns. Om det gör det kan du köra den andra situationen. Gör så här för att lösa problemet:

1. På domänkontrollanten öppnar du fönstret **Active Directory användare och datorer** från alternativet **verktyg** i **Serverhanteraren**. 
2. Gå till kontot genom att välja **användare** i det vänstra fönstret.
3. Högerklicka på kontot och välj **Egenskaper**.
4. Välj fliken **konto** . Om rutan **användarens inloggnings namn** är tom är detta orsaken till felet. 

    ![Ett tomt användar konto indikerar att UPN saknas](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. Fyll i rutan **användarens inloggnings namn** för att matcha namnet på användaren och välj rätt domän i list rutan. 
6. Välj **Verkställ** för att spara ändringarna och Stäng dialog rutan genom att välja **OK**. 

När du har gjort dessa ändringar kan du försöka Distribuera Azure snabb starts mal len en gång till. 



## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server virtuella datorer](virtual-machines-windows-sql-server-iaas-overview.md)
* [Vanliga frågor och svar om SQL Server virtuella datorer](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pris vägledning för SQL Server virtuella datorer](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Viktig information för SQL Server virtuella datorer](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Växla licensierings modeller för en SQL Server VM](virtual-machines-windows-sql-ahb.md)



