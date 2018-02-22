

![Virtuella datorer i en fristående molntjänst](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

Om du placerar dina virtuella datorer i ett virtuellt nätverk, kan du bestämma hur många molntjänster som du vill använda för att läsa in belastningsutjämning och tillgänglighet. Du kan dessutom sortera de virtuella datorerna på undernät på samma sätt som ditt lokala nätverk och ansluta det virtuella nätverket till ditt lokala nätverk. Här är ett exempel:

![Virtuella datorer i ett virtuellt nätverk](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

Virtuella nätverk är det rekommenderade sättet att ansluta virtuella datorer i Azure. Det bästa sättet är att konfigurera varje nivå av ditt program i en separat molntjänst. Du kan dock behöva kombinera vissa virtuella datorer från olika program nivåer i samma molntjänst förblir inom högst 200 molntjänster per prenumeration. Det här och andra begränsningar finns [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../articles/azure-subscription-service-limits.md).

## <a name="connect-vms-in-a-virtual-network"></a>Ansluta virtuella datorer i ett virtuellt nätverk
Att ansluta virtuella datorer i ett virtuellt nätverk:

1. Skapa virtuella nätverk i den [Azure-portalen](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md) och ange 'klassisk distribution ”.
2. Skapa en uppsättning molntjänster för distributionen för att avspegla din design för tillgänglighetsuppsättningar och belastningsutjämning. I Azure-portalen klickar du på **skapar du en resurs > Compute > molntjänst** för varje tjänst i molnet.

  När du fyller i molnet tjänstinformation Välj samma _resursgruppen_ användas med det virtuella nätverket.

3. Klicka för att skapa varje ny virtuell dator **skapar du en resurs > Compute**, Välj lämplig VM-avbildning från den **aktuella appar**.

  I Virtuellt **grunderna** bladet Välj samma _resursgruppen_ användas med det virtuella nätverket.

  ![Grunderna i VM-bladet när du använder ett virtuellt nätverk](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_VN.png)

4. När du fyller i den virtuella datorn **inställningar**, Välj rätt _Molntjänsten_ eller _virtuellt nätverk_ för den virtuella datorn.

  Azure markerar det andra objektet baserat på ditt val.

  ![VM inställningsbladet när du använder ett virtuellt nätverk](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_VN.png)


## <a name="connect-vms-in-a-standalone-cloud-service"></a>Ansluta virtuella datorer i en fristående molntjänst
Att ansluta virtuella datorer i en fristående molnbaserad tjänst:

1. Skapa molntjänst i den [Azure-portalen](http://portal.azure.com). Klicka på **New > Compute > molntjänst**. Eller, du kan skapa Molntjänsten för din distribution när du skapar din första virtuella datorn.
2. När du skapar virtuella datorer väljer du samma resursgrupp som används med Molntjänsten.

  ![Lägga till en virtuell dator i en befintlig molntjänst](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_SA.png)

3.  Välj namnet på Molntjänsten som skapats i det första steget när du fyller i VM-information.

  ![Att välja en tjänst i molnet för en virtuell dator](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_SA.png)
