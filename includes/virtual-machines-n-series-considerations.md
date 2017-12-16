## <a name="deployment-considerations"></a>Distributionsöverväganden

* Tillgängligheten för virtuella datorer N-serien finns [produkter som är tillgängliga efter region](https://azure.microsoft.com/en-us/regions/services/).

* N-serien virtuella datorer kan bara distribueras i Resource Manager-distributionsmodellen.

* När du skapar en N-serien virtuell dator med hjälp av Azure portal på den **grunderna** bladet väljer en **VM disktyp** av **Hårddisk**. Välj en tillgänglig N-serien storlek på att den **storlek** bladet, klickar du på **visa alla**.

* NC och NV virtuella datorer stöder inte Virtuella diskar som backas upp av Azure Premium-lagring.

* Om du vill distribuera flera virtuella datorer N-serien du en prenumeration med användningsbaserad betalning eller andra köpalternativ. Om du använder ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) kan du bara använda ett begränsat antal Azure Compute-kärnor.

* Du kan behöva öka kvoten kärnor (per region) i din Azure-prenumeration och öka separat kvoten för NC NCv2, ND eller NV kärnor. Att begära en ökad kvot [öppna en supportbegäran online customer](../articles/azure-supportability/how-to-create-azure-support-request.md) utan kostnad. Standardgränser kan variera beroende på din prenumerationskategori.

* En VM-avbildning som du kan distribuera på N-serien virtuella datorer är det [vetenskap virtuell dator i Azure Data](../articles/machine-learning/data-science-virtual-machine/overview.md). Datavetenskap Virtual Machine förinstallerar och konfigurerar många populära datavetenskap och djup learning verktyg. Det kan också förinstalleras NVIDIA Tesla GPU drivrutiner.





