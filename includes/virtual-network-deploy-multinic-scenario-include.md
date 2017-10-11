## <a name="scenario"></a>Scenario
Det här dokumentet beskriver via en distribution som använder flera nätverkskort i virtuella datorer i ett specifikt scenario. I det här scenariot har du en två skikt IaaS arbetsbelastning i Azure. Varje nivå har distribuerats i undernätet i ett virtuellt nätverk (VNet). Frontend-nivå består av flera webbservrar, grupperade i en belastningsutjämnare för hög tillgänglighet. Backend-nivå består av flera databasservrar. Dessa databasservrar kommer att distribueras med två nätverkskort, ett för databasåtkomst för hantering. Scenariot omfattar även Nätverkssäkerhetsgrupper (NSG: er) att styra vilken trafik tillåts att varje undernät och NIC i distributionen. Bilden nedan visar den grundläggande arkitekturen i det här scenariot.  

![MultiNIC scenario](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

