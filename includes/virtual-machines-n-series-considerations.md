## <a name="deployment-considerations"></a>Distributionsöverväganden

* Tillgängligheten för virtuella datorer N-serien finns [produkter som är tillgängliga efter region](https://azure.microsoft.com/en-us/regions/services/).

* N-serien virtuella datorer kan bara distribueras i Resource Manager-distributionsmodellen.

* N-serien virtuella datorer skiljer sig i typ av Azure Storage som de har stöd för deras diskar. NC och NV virtuella datorer stöder bara Virtuella diskar som backas upp av Standard Disk lagring (HDD). NCv2 och ND NCv3 förhandsgranskning () virtuella datorer bara stöd för Virtuella diskar som backas upp av Premium Disk lagring (SSD).

* Om du vill distribuera flera virtuella datorer N-serien du en prenumeration med användningsbaserad betalning eller andra köpalternativ. Om du använder ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) kan du bara använda ett begränsat antal Azure Compute-kärnor.

* Du kan behöva öka kvoten kärnor (per region) i din Azure-prenumeration och öka separat kvoten för NC NCv2, ND eller NV kärnor. Att begära en ökad kvot [öppna en supportbegäran online customer](../articles/azure-supportability/how-to-create-azure-support-request.md) utan kostnad. Standardgränser kan variera beroende på din prenumerationskategori.

* En VM-avbildning som du kan distribuera på N-serien virtuella datorer är det [vetenskap virtuell dator i Azure Data](../articles/machine-learning/data-science-virtual-machine/overview.md). Datavetenskap Virtual Machine förinstallerar och konfigurerar många populära datavetenskap och djup learning verktyg. Det kan också förinstalleras NVIDIA Tesla GPU drivrutiner.





