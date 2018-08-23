---
title: Azure SQL Database Managed instanskonfiguration av virtuellt nätverk | Microsoft Docs
description: Det här avsnittet beskriver konfigurationsalternativ för ett virtuellt nätverk (VNet) med en Azure SQL Database Managed Instance.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: f634167f24c221e702696174ea86a212c535695b
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42062103"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Konfigurera ett virtuellt nätverk för Azure SQL Database Managed Instance

Azure SQL Database Managed Instance (förhandsversion) måste distribueras i en Azure [virtuellt nätverk (VNet)](../virtual-network/virtual-networks-overview.md). Den här distributionen gör det möjligt för följande scenarier: 
- Ansluta till en hanterad instans direkt från ett lokalt nätverk 
- Ansluta en hanterad instans till länkad server eller en annan lokala datalager 
- Ansluta en hanterad instans till Azure-resurser  

## <a name="plan"></a>Planera

Planera hur du distribuerar en hanterad instans i virtuellt nätverk med dina svar på följande frågor: 
- Planerar du att distribuera en eller flera hanterade instanser? 

  Antalet instanser som hanteras av anger den minsta storleken på undernätet för att allokera för dina hanterade instanser. Mer information finns i [avgör storleken på undernätet för hanterad instans](#determine-the-size-of-subnet-for-managed-instances). 
- Behöver du distribuera din hanterade instans i ett befintligt virtuellt nätverk eller du skapar ett nytt nätverk? 

   Om du planerar att använda ett befintligt virtuellt nätverk måste du ändra den nätverkskonfigurationen för att hantera din hanterade instans. Mer information finns i [ändra befintligt virtuellt nätverk för hanterad instans](#modify-an-existing-virtual-network-for-managed-instances). 

   Om du tänker skapa nytt virtuellt nätverk finns i [Skapa nytt virtuellt nätverk för hanterad instans](#create-a-new-virtual-network-for-managed-instances).

## <a name="requirements"></a>Krav

För att skapa en hanterad instans måste du anger ett undernät i det virtuella nätverket som uppfyller följande krav:
- **Vara tom**: undernätet får inte innehålla andra molntjänst som är associerade med den och det får inte vara Gateway-undernätet. Du kan inte skapa Managed Instance i undernät som innehåller resurser än hanterad instans eller lägga till andra resurser i undernätet senare.
- **Inga Nätverkssäkerhetsgrupper är**: undernätet får inte ha en Nätverkssäkerhetsgrupp som är kopplade till den.
- **Har specifika routningstabellen**: undernätet måste ha en användare väg tabell (UDR) med 0.0.0.0/0 nästa hopp till Internet som den enda vägen tilldelade till den. Mer information finns i [skapar nödvändiga routningstabell och associerar den](#create-the-required-route-table-and-associate-it)
3. **Valfri anpassad DNS**: om anpassad DNS anges i det virtuella nätverket, Azures rekursiva matchare IP-adress (till exempel 168.63.129.16) måste läggas till i listan. Mer information finns i [konfigurera anpassad DNS](sql-database-managed-instance-custom-dns.md).
4. **Ingen tjänstslutpunkt**: undernätet får inte ha en tjänstslutpunkt (lagring eller Sql) som är associerade med den. Se till att tjänstslutpunkter alternativet inaktiveras när du skapar virtuella nätverk.
5. **Tillräckligt med IP-adresser**: undernätet måste ha minst 16 IP-adresser. Mer information finns i [avgör storleken på undernätet för hanterade instanser](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> Du kan inte distribuera nya Managed Instance om målundernätet inte är kompatibel med alla föregående krav. Målets virtuella nätverket och undernätet måste hållas i enlighet med kraven för hanterad instans (före och efter distributionen), eftersom varje överträdelse kan medföra att instansen att ange felaktigt tillstånd och blir otillgänglig. Återställa från att tillstånd måste du skapa ny instans i ett virtuellt nätverk med efterlevnadsprinciperna för nätverk, återskapa nivå instansdata och Återställ dina databaser. Det här medför betydande driftavbrott för dina program.

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Avgör storleken på undernätet för hanterade instanser

När du skapar en hanterad instans, tilldelar Azure ett antal virtuella datorer beroende på storleken för nivån som du väljer under etableringen. Eftersom de virtuella datorerna är kopplade till ditt undernät, kräver de IP-adresser. För att säkerställa hög tillgänglighet under normal drift och underhåll, kan Azure allokera fler virtuella datorer. Därför kan är antalet begärda IP-adresser i ett undernät större än antalet instanser som hanteras i det undernätet. 

Enligt design, en hanterad instans måste ha minst 16 IP-adresser i ett undernät och använder upp till 256 IP-adresser. Du kan därmed använda nätmasker /28 /24 när du definierar dina IP-adressintervall för undernätet. 

Om du planerar att distribuera flera hanterade instanser i undernätet och behöver för att optimera på undernätets storlek, använder du parametrarna för att bilda en beräkning: 

- Azure använder fem IP-adresser i undernätet för sin egen behov 
- Varje instans generell användning måste två adresser 
- Varje affärskritisk instans behöver fyra adresserna

**Exempel**: du planerar att ha tre generella och två företag kritiska hanterade instanser. Att innebär att du behöver 5 + 3 * 2 + 2 * 4 = 19 IP-adresser. När IP-intervall är definierade i potensen 2, måste IP-adressintervall 32 (2 ^ 5) IP-adresser. Du måste därför reservera undernätet med nätmask på/27. 

## <a name="create-a-new-virtual-network-for-managed-instance-using-azure-resource-manager-deployment"></a>Skapa ett nytt virtuellt nätverk för hanterad instans med Azure Resource Manager-distribution

Det enklaste sättet att skapa och konfigurera ett virtuellt nätverk är att använda Azure Resource Manager-mall för distribution.

1. Logga in på Azure Portal.

2. Använd **distribuera till Azure** knappen för att distribuera virtuella nätverk i Azure-molnet:

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  Den här knappen öppnas ett formulär som du kan använda för att konfigurera nätverket där du kan distribuera Managed Instance.

  > [!Note]
  > Azure Resource Manager-mallen distribuerar virtuellt nätverk med två undernät. Ett undernät som kallas **ManagedInstances** är reserverad för hanterade instanser och har förkonfigurerade routningstabellen, medan andra undernätet kallas **standard** används för andra resurser som ska få åtkomst till hanterad Instans (till exempel Azure virtuella datorer). Du kan ta bort **standard** undernät om du inte behöver den.

3. Konfigurera nätverksmiljön. Du kan konfigurera parametrar för din nätverksmiljö på följande format:

![Konfigurera azure-nätverk](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

Du kan ändra namnen på VNet och undernät och justera IP-adressintervall som är kopplade till dina nätverksresurser. När du trycker på ”Köp”-knappen, kommer det här formuläret Skapa och konfigurera din miljö. Om du inte behöver två undernät kan du ta bort standardvärdet. 

## <a name="create-a-new-virtual-network-for-managed-instances-using-portal"></a>Skapa ett nytt virtuellt nätverk för hanterade instanser med hjälp av portalen

Skapa ett Azure-nätverk är en förutsättning för att skapa en hanterad instans. Du kan använda Azure-portalen [PowerShell](../virtual-network/quick-create-powershell.md), eller [Azure CLI](../virtual-network/quick-create-cli.md). Följande avsnitt visar stegen med Azure portal. De information som beskrivs här gäller för var och en av dessa metoder.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Leta upp och klicka på **Virtual Network**, kontrollera den **Resource Manager** som är markerad för distributionsläge och klicka sedan på **Skapa**.

   ![virtual network create](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Fyll i formuläret för virtuellt nätverk med den begärda informationen, på ett sätt som på följande skärmbild:

   ![virtual network create form](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Klicka på **Skapa**.

   Adressutrymmen och undernät har angetts i CIDR-notation. 

   > [!IMPORTANT]
   > Standardvärdena skapa undernät som tar alla VNet-adressutrymmet. Om du väljer det här alternativet kan du inte skapa andra resurser i det virtuella nätverket än Managed Instance. 

   Den rekommenderade metoden är följande: 
   - Beräkna undernätets storlek genom att följa [avgör storleken på undernätet för hanterad instans](#determine-the-size-of-subnet-for-managed-instances) avsnittet  
   - Utvärdera behoven för resten av virtuellt nätverk 
   - Fyll i VNet och undernät adressintervall därefter 

   Kontrollera att alternativet tjänstslutpunkter förblir **inaktiverad**. 

   ![virtual network create form](./media/sql-database-managed-instance-tutorial/service-endpoint-disabled.png)

### <a name="create-the-required-route-table-and-associate-it"></a>Skapa nödvändiga routningstabell och koppla den

1. Logga in på Azure Portal  
2. Leta upp och klicka på **Routningstabell** och klicka sedan på **Skapa** på sidan Routningstabell.

   ![route table create form](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

3. Skapa en 0.0.0.0/0 nästa hopp till Internet-väg på ett sätt som följande skärmbilder:

   ![route table add](./media/sql-database-managed-instance-tutorial/route-table-add.png)

   ![route](./media/sql-database-managed-instance-tutorial/route.png)

4. Koppla den här vägen till undernätet för den hanterade instansen på ett sätt som följande skärmbilder:

    ![subnet](./media/sql-database-managed-instance-tutorial/subnet.png)

    ![set route table](./media/sql-database-managed-instance-tutorial/set-route-table.png)

    ![set route table-save](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)


När du har skapat ditt virtuella nätverk är du redo att skapa din hanterade instans.  

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Ändra ett befintligt virtuellt nätverk för hanterade instanser 

Frågor och svar i det här avsnittet visar hur du lägger till en hanterad instans till befintligt virtuellt nätverk. 

**Använder du klassisk eller Resource Manager-distributionsmodellen för det befintliga virtuella nätverket?** 

Du kan bara skapa en hanterad instans i Resource Manager-nätverk. 

**Vill du skapa ett nytt undernät för SQL-hanterad instans eller Använd befintlig?**

Om du vill skapa ny: 

- Beräkna undernätets storlek genom att följa riktlinjerna i den [avgör storleken på undernätet för hanterade instanser](#determine-the-size-of-subnet-for-managed-instances) avsnittet.
- Följ stegen i [lägga till, ändra eller ta bort ett virtuellt nätverksundernät](../virtual-network/virtual-network-manage-subnet.md). 
- Skapa en routingtabell som innehåller en enda post **0.0.0.0/0**, enligt nästa hopp-Internet och koppla den till undernätet för den hanterade instansen.  

Om du vill skapa en hanterad instans i ett befintligt undernät: 
- Kontrollera om undernätet är tom - det inte går att skapa en hanterad instans i ett undernät som innehåller andra resurser, inklusive Gateway-undernätet 
- Beräkna undernätets storlek genom att följa riktlinjerna i den [avgör storleken på undernätet för hanterade instanser](#determine-the-size-of-subnet-for-managed-instances) och kontrollera att den är lämplig storlek. 
- Kontrollera att tjänstslutpunkter inte är aktiverade på undernätet.
- Se till att det finns inga nätverkssäkerhetsgrupper som är kopplad till undernätet 

**Har du anpassad DNS-server konfigurerad?** 

Om Ja, gå [konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md). 

- Skapa nödvändiga routningstabellen och associera den: se [skapar nödvändiga routningstabell och associerar den](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [vad är en hanterad instans](sql-database-managed-instance.md)
- Se en självstudie som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställa en databas från en säkerhetskopia av databasen, [skapar en Azure SQL Database Managed Instance](sql-database-managed-instance-create-tutorial-portal.md).
- DNS-problem, se [konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md)
