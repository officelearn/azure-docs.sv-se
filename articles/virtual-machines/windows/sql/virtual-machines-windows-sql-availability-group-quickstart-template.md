---
title: Konfigurera tillgänglighetsgrupp (Azure-snabbstartsmall)
description: Använd Azure-snabbstartsmallar för att skapa Windows redundanskluster, ansluta virtuella VIRTUELLA SQL Server-datorer till klustret, skapa lyssnaren och konfigurera den interna belastningsutjämnaren i Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: edf810dfc975eebaf261eac7b89106c9e29c759c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022383"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-an-azure-vm"></a>Använda Snabbstartsmallar för Azure för att konfigurera en tillgänglighetsgrupp för SQL Server på en Virtuell Azure-dator
I den här artikeln beskrivs hur du använder Snabbstartsmallarna för Azure för att delvis automatisera distributionen av en alltid på tillgänglighetsgruppkonfiguration för virtuella SQL Server-datorer i Azure. Två Snabbstartsmallar för Azure används i den här processen: 

   | Mall | Beskrivning |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Skapar Windows redundanskluster och ansluter virtuella SQL Server-datorer till det. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Skapar tillgänglighetsgrupplyssnaren och konfigurerar den interna belastningsutjämnaren. Den här mallen kan bara användas om Windows redundanskluster skapades med mallen **101-sql-vm-ag-setup.** |
   | &nbsp; | &nbsp; |

Andra delar av konfigurationen för tillgänglighetsgruppen måste göras manuellt, till exempel att skapa tillgänglighetsgruppen och skapa den interna belastningsutjämnaren. Den här artikeln innehåller en sekvens av automatiska och manuella steg.
 

## <a name="prerequisites"></a>Krav 
Om du vill automatisera inställningarna för en alltid på tillgänglighetsgrupp med hjälp av snabbstartsmallar måste du ha följande förutsättningar: 
- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En resursgrupp med en domänkontrollant. 
- En eller flera domänanslutna [virtuella datorer i Azure som kör SQL Server 2016 (eller senare) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) som finns i samma tillgänglighetsuppsättning eller tillgänglighetszon och som har [registrerats hos SQL VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md).  
- Två tillgängliga (används inte av någon entitet) IP-adresser: en för den interna belastningsutjämnaren och en för tillgänglighetsgruppavlyssnaren inom samma undernät som tillgänglighetsgruppen. Om en befintlig belastningsutjämnare används behöver du bara en tillgänglig IP-adress.  

## <a name="permissions"></a>Behörigheter
Följande behörigheter är nödvändiga för att konfigurera gruppen Alltid på tillgänglighet med hjälp av Snabbstartsmallar för Azure: 

- Ett befintligt domänanvändarkonto som har **behörigheten Skapa datorobjekt** i domänen.  Ett domänadministratörskonto har till exempel vanligtvis account@domain.comtillräcklig behörighet (till exempel: ). _Det här kontot bör också ingå i den lokala administratörsgruppen på varje virtuell dator för att skapa klustret._
- Domänanvändarkontot som styr SQL Server-tjänsten. 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>Steg 1: Skapa redundansklustret och anslut virtuella virtuella SQL Server-datorer till klustret med hjälp av en snabbstartsmall 
När dina virtuella SQL Server-datorer har registrerats hos SQL VM-resursprovidern kan du ansluta till virtuella SQL Server-datorer med *SqlVirtualMachineGroups*. Den här resursen definierar metadata för Windows redundanskluster. Metadata innehåller version, utgåva, fullständigt kvalificerat domännamn, Active Directory-konton för att hantera både klustret och SQL Server-tjänsten och lagringskontot som molnvittne. 

Lägga till virtuella SQL Server-datorer i *sqlVirtualMachineGroups* resursgrupp bootstraps windows failover cluster service för att skapa klustret och sedan ansluter dessa SQL Server virtuella datorer till det klustret. Det här steget är automatiserat med snabbstartsmallen **101-sql-vm-ag-setup.** Du kan implementera den med hjälp av följande steg:

1. Gå till snabbstartsmallen [**101-sql-vm-ag-setup.**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) Välj sedan **Distribuera till Azure** för att öppna snabbstartsmallen i Azure-portalen.
1. Fyll i de obligatoriska fälten för att konfigurera metadata för Windows redundanskluster. Du kan lämna de valfria fälten tomma.

   I följande tabell visas de värden som krävs för mallen: 

   | **Field** | Värde |
   | --- | --- |
   | **Prenumeration** |  Prenumerationen där dina virtuella SQL Server-datorer finns. |
   |**Resursgrupp** | Resursgruppen där virtuella SQL Server-datorer finns. | 
   |**Klusternamn för redundans** | Namnet som du vill använda för det nya Redundansklustret i Windows. |
   | **Befintlig vm-lista** | De virtuella SQL Server-datorerna som du vill delta i tillgänglighetsgruppen och vara en del av det nya klustret. Avgränsa dessa värden med ett kommatecken och ett blanksteg (till *exempel: SQLVM1, SQLVM2*). |
   | **SQL Server-version** | SQL Server-versionen av virtuella SQL Server-datorer. Markera den i listrutan. För närvarande stöds endast SQL Server 2016- och SQL Server 2017-avbildningar. |
   | **Befintligt fullständigt kvalificerat domännamn** | Den befintliga FQDN för domänen där dina virtuella SQL Server-datorer finns. |
   | **Befintligt domänkonto** | Ett befintligt domänanvändarkonto som har **behörigheten Skapa datorobjekt** i domänen när [CNO](/windows-server/failover-clustering/prestage-cluster-adds) skapas under malldistributionen. Ett domänadministratörskonto har till exempel vanligtvis account@domain.comtillräcklig behörighet (till exempel: ). *Det här kontot bör också ingå i den lokala administratörsgruppen på varje virtuell dator för att skapa klustret.*| 
   | **Lösenord för domänkonto** | Lösenordet för det tidigare nämnda domänanvändarkontot. | 
   | **Befintligt Sql-tjänstkonto** | Domänanvändarkontot som styr [SQL Server-tjänsten](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) under distribution account@domain.comav tillgänglighetsgrupper (till exempel: ). |
   | **Sql-tjänstlösenord** | Lösenordet som används av domänanvändarkontot som styr SQL Server-tjänsten. |
   | **Namn på molnvittne** | Ett nytt Azure-lagringskonto som kommer att skapas och användas för molnvittnet. Du kan ändra det här namnet. |
   | **\_artefakter Plats** | Det här fältet är inställt som standard och bör inte ändras. |
   | **\_artefakter Plats Sas Token** | Det här fältet lämnas avsiktligt tomt. |
   | &nbsp; | &nbsp; |

1. Om du godkänner villkoren väljer du kryssrutan **Jag godkänner de villkor som anges ovan.** Välj sedan **Inköp** för att slutföra distributionen av snabbstartsmallen. 
1. Om du vill övervaka distributionen väljer du distributionen från ikonen **Meddelanden** i den övre navigeringsbanderollen eller går till **Resursgrupp** i Azure-portalen. Välj **Distributioner** under **Inställningar**och välj distributionen **microsoft.template.** 

>[!NOTE]
> Autentiseringsuppgifter som anges under malldistribution lagras endast för distributionens längd. När distributionen är klar tas dessa lösenord bort. Du blir ombedd att ange dem igen om du lägger till fler virtuella SQL Server-datorer i klustret. 


## <a name="step-2-manually-create-the-availability-group"></a>Steg 2: Skapa tillgänglighetsgruppen manuellt 
Skapa tillgänglighetsgruppen manuellt som vanligt genom att använda [SQL Server Management Studio,](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio) [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)eller [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> Skapa *inte* en lyssnare just nu, eftersom snabbstartsmallen **101-sql-vm-aglistener-setup** gör det automatiskt i steg 4. 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>Steg 3: Skapa den interna belastningsutjämnren manuellt
Gruppen Alltid på tillgänglighet kräver en intern instans av Azure Load Balancer. Den interna belastningsutjämnaren tillhandahåller en "flytande" IP-adress för tillgänglighetsgruppens lyssnare som möjliggör snabbare redundans och återanslutning. Om virtuella SQL Server-datorer i en tillgänglighetsgrupp ingår i samma tillgänglighetsuppsättning kan du använda en grundläggande belastningsutjämnare. Annars måste du använda en standardbelastningsutjämnare. 

> [!IMPORTANT]
> Den interna belastningsutjämnaren bör finnas i samma virtuella nätverk som SQL Server VM-instanserna. 

Du behöver bara skapa den interna belastningsutjämnaren. I steg 4 hanterar snabbstartsmallen **101-sql-vm-aglistener-setup** resten av konfigurationen (till exempel serverdelspoolen, hälsoavsökningen och belastningsutjämningsreglerna). 

1. Öppna resursgruppen som innehåller virtuella SQL Server-datorer i Azure-portalen. 
2. Välj **Lägg till**i resursgruppen .
3. Sök efter **belastningsutjämnare**. I sökresultaten väljer du **Belastningsutjämnare**, som publiceras av **Microsoft**.
4. På **bladet Belastningsutjämnare** väljer du **Skapa**.
5. Konfigurera belastningsutjämnaren i dialogrutan **Skapa belastningsutjämnare** på följande sätt:

   | Inställning | Värde |
   | --- | --- |
   | **Namn** |Ange ett textnamn som representerar belastningsutjämnaren. Ange till exempel **sqlLB**. |
   | **Typ** |**Internt**: De flesta implementeringar använder en intern belastningsutjämnare, vilket gör att program inom samma virtuella nätverk kan ansluta till tillgänglighetsgruppen.  </br> **Extern**: Tillåter program att ansluta till tillgänglighetsgruppen via en offentlig internetanslutning. |
   | **Virtuellt nätverk** | Välj det virtuella nätverk som SQL Server-instanserna finns i. |
   | **Undernät** | Markera det undernät som SQL Server-instanserna finns i. |
   | **TILLDELNING AV IP-adress** |**Statisk** |
   | **Privat IP-adress** | Ange en tillgänglig IP-adress från undernätet. |
   | **Prenumeration** |Om du har flera prenumerationer kan det här fältet visas. Välj den prenumeration som du vill associera med den här resursen. Det är normalt samma prenumeration som alla resurser för tillgänglighetsgruppen. |
   | **Resursgrupp** |Markera den resursgrupp som SQL Server-instanserna finns i. |
   | **Location** |Välj den Azure-plats som SQL Server-instanserna finns i. |
   | &nbsp; | &nbsp; |

6. Välj **Skapa**. 


>[!IMPORTANT]
> Den offentliga IP-resursen för varje VIRTUELL SQL Server bör ha en Standard SKU för att vara kompatibel med standardbelastningsutjämnaren. Om du vill ta reda på SKU för den virtuella datorns offentliga IP-resurs går du till **Resursgrupp,** väljer din **offentliga IP-adressresurs** för VIRTUELL SQL Server och letar reda på värdet under **SKU** i **fönstret Översikt.** 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>Steg 4: Skapa tillgänglighetsgrupplyssnare och konfigurera den interna belastningsutjämnaren med hjälp av snabbstartsmallen

Skapa tillgänglighetsgrupplyssnaren och konfigurera den interna belastningsutjämnaren automatiskt med hjälp av snabbstartsmallen **101-sql-vm-aglistener-setup.** Mallen innehåller microsoft.sqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener-resursen. Snabbstartsmallen **101-sql-vm-aglistener-setup,** via SQL VM-resursprovidern, gör följande åtgärder:

- Skapar en ny frontend IP-resurs (baserat på IP-adressvärdet som anges under distributionen) för lyssnaren. 
- Konfigurerar nätverksinställningarna för klustret och den interna belastningsutjämnaren. 
- Konfigurerar serverda poolen för den interna belastningsutjämnaren, hälsoavsökningen och belastningsutjämningsreglerna.
- Skapar tillgänglighetsgrupplyssnaren med den angivna IP-adressen och namnet.
 
>[!NOTE]
> Du kan bara använda **101-sql-vm-aglistener-setup** om Windows redundansklustret skapades med mallen **101-sql-vm-ag-setup.**
   
   
Så här konfigurerar du den interna belastningsutjämnaren och skapar tillgänglighetsgrupplyssnaren:
1. Gå till snabbstartsmallen [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) och välj **Distribuera till Azure** för att starta snabbstartsmallen i Azure-portalen.
1. Fyll i de obligatoriska fälten för att konfigurera den interna belastningsutjämnaren och skapa tillgänglighetsgrupplyssnaren. Du kan lämna de valfria fälten tomma. 

   I följande tabell visas de värden som krävs för mallen: 

   | **Field** | Värde |
   | --- | --- |
   |**Resursgrupp** | Resursgruppen där din virtuella SQL Server-datorer och tillgänglighetsgrupp finns. | 
   |**Befintligt redundansklusternamn** | Namnet på det kluster som virtuella SQL Server-datorer är anslutna till. |
   | **Befintlig sql-tillgänglighetsgrupp**| Namnet på den tillgänglighetsgrupp som virtuella SQL Server-datorer är en del av. |
   | **Befintlig vm-lista** | Namnen på virtuella SQL Server-datorer som ingår i den tidigare nämnda tillgänglighetsgruppen. Avgränsa namnen med ett kommatecken och ett blanksteg (till *exempel: SQLVM1, SQLVM2*). |
   | **Lyssnaren** | Det DNS-namn som du vill tilldela lyssnaren. Som standard anger den här mallen namnet "aglistener", men du kan ändra det. Namnet får inte överstiga 15 tecken. |
   | **Lyssnarport** | Porten som du vill att lyssnaren ska använda. Vanligtvis bör den här porten vara standard för 1433. Det här är det portnummer som mallen anger. Men om standardporten har ändrats bör lyssnarporten använda det värdet i stället. | 
   | **Lyssnare IP** | DEN IP-adress som du vill att lyssnaren ska använda. Den här adressen skapas under malldistributionen, så ange en som inte redan används.  |
   | **Befintligt undernät** | Namnet på det interna undernätet för virtuella SQL Server-datorer (till exempel: *standard*). Du kan bestämma det här värdet genom att gå till **Resursgrupp,** välja det virtuella nätverket, välja **undernät** i **fönstret Inställningar** och kopiera värdet under **Namn**. |
   | **Befintlig intern belastningsutjämnare** | Namnet på den interna belastningsutjämnaren som du skapade i steg 3. |
   | **Avsökningsport** | Den avsökningsport som du vill att den interna belastningsutjämnaren ska använda. Mallen använder 59999 som standard, men du kan ändra det här värdet. |
   | &nbsp; | &nbsp; |

1. Om du godkänner villkoren väljer du kryssrutan **Jag godkänner de villkor som anges ovan.** Välj **Inköp** för att slutföra distributionen av snabbstartsmallen. 
1. Om du vill övervaka distributionen väljer du distributionen från ikonen **Meddelanden** i den övre navigeringsbanderollen eller går till **Resursgrupp** i Azure-portalen. Välj **Distributioner** under **Inställningar**och välj distributionen **microsoft.template.** 

>[!NOTE]
>Om distributionen misslyckas halvvägs måste du [manuellt ta bort den nyskapade lyssnaren](#remove-the-availability-group-listener) med PowerShell innan du distribuerar snabbstartsmallen **101-sql-vm-aglistener-setup.** 

## <a name="remove-the-availability-group-listener"></a>Ta bort tillgänglighetsgrupplyssnaren
Om du senare behöver ta bort tillgänglighetsgruppavlyssnaren som mallen konfigurerade måste du gå igenom SQL VM-resursprovidern. Eftersom lyssnaren är registrerad via SQL VM-resursprovidern är det inte tillräckligt att bara ta bort den via SQL Server Management Studio. 

Den bästa metoden är att ta bort den via SQL VM-resursprovidern med hjälp av följande kodavsnitt i PowerShell. Om du gör det tas metadata för tillgänglighetsgruppen lyssnare från SQL VM-resursprovidern. Lyssnaren tas också bort fysiskt från tillgänglighetsgruppen. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Vanliga fel
I det här avsnittet beskrivs några kända problem och deras möjliga lösning. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>Tillgänglighetsgruppavlyssnare\<för tillgänglighetsgruppen " AG-Name>" finns redan
Den valda tillgänglighetsgruppen som används i snabbstartsmallen för Azure för tillgänglighetsgruppavlyssnaren innehåller redan en lyssnare. Antingen är det fysiskt inom tillgänglighetsgruppen eller så finns dess metadata kvar i SQL VM-resursprovidern. Ta bort lyssnaren med [PowerShell](#remove-the-availability-group-listener) innan du distribuerar snabbstartsmallen **101-sql-vm-aglistener-setup.** 

### <a name="connection-only-works-from-primary-replica"></a>Anslutningen fungerar bara från primär replik
Det här beteendet kommer troligen från en misslyckad malldistribution av **101-sql-vm-aglistener-setup** som har lämnat konfigurationen av den interna belastningsutjämningen i ett inkonsekvent tillstånd. Kontrollera att serverda poolen visar tillgänglighetsuppsättningen och att det finns regler för hälsoavsökningen och för belastningsutjämningsreglerna. Om något saknas är konfigurationen av den interna belastningsutjämnaren ett inkonsekvent tillstånd. 

Lös problemet genom att ta bort lyssnaren med [PowerShell,](#remove-the-availability-group-listener)ta bort den interna belastningsutjämnaren via Azure-portalen och börja om i steg 3. 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest - Endast SQL virtuell dator lista kan uppdateras
Det här felet kan uppstå när du distribuerar mallen **101-sql-vm-aglistener-setup** om lyssnaren togs bort via SQL Server Management Studio (SSMS), men inte togs bort från SQL VM-resursprovidern. Om du tar bort lyssnaren via SSMS tas inte metadata för lyssnaren bort från SQL VM-resursprovidern. Lyssnaren måste tas bort från resursprovidern via [PowerShell](#remove-the-availability-group-listener). 

### <a name="domain-account-does-not-exist"></a>Domänkontot finns inte
Det här felet kan ha två orsaker. Antingen finns inte det angivna domänkontot eller så saknar det [UPN-data (User Principal Name).](/windows/desktop/ad/naming-properties#userprincipalname) Mallen **101-sql-vm-ag-setup** förväntar sig ett domänkonto i *user@domain.com*UPN-formuläret (det vill säga ), men vissa domänkonton kan saknas. Detta inträffar vanligtvis när en lokal användare har migrerats till det första domänadministratörskontot när servern befordrades till en domänkontrollant eller när en användare skapades via PowerShell. 

Kontrollera att kontot finns. Om den gör det, kanske du stöter på den andra situationen. Gör så här för att lösa det:

1. Öppna fönstret **Active Directory – användare och datorer** i active directory i **Serverhanteraren**på domänkontrollanten **Tools** . 
2. Gå till kontot genom att välja **Användare** i den vänstra rutan.
3. Högerklicka på kontot och välj **Egenskaper**.
4. Välj fliken **Konto.** Om rutan **Användarens inloggningsnamn** är tom är detta orsaken till felet. 

    ![Tomt användarkonto anger att UPN saknas](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. Fyll i rutan **Användarens inloggningsnamn** för att matcha användarens namn och välj rätt domän i listrutan. 
6. Välj **Använd** om du vill spara ändringarna och stäng dialogrutan genom att välja **OK**. 

När du har gjort dessa ändringar kan du försöka distribuera snabbstartsmallen för Azure igen. 



## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över virtuella SQL Server-datorer](virtual-machines-windows-sql-server-iaas-overview.md)
* [Vanliga frågor och svar om virtuella SQL Server-datorer](virtual-machines-windows-sql-server-iaas-faq.md)
* [Prisvägledning för virtuella SQL Server-datorer](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Viktig information för virtuella SQL Server-datorer](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Byta licensieringsmodeller för en VIRTUELL SQL Server](virtual-machines-windows-sql-ahb.md)



