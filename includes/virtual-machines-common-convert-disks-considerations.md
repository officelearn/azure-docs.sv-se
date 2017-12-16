
* För omvandlingen krävs att den virtuella datorn startas om, så du bör schemalägga migreringen av dina virtuella datorer under en redan inställd underhållsperiod. 

* Omvandlingen kan inte ångras. 

* Testa omvandlingen först. Migrera först en virtuell testdator innan du gör en migrering i produktion.

* Under omvandlingen frigör du den virtuella datorn. Den virtuella datorn får en ny ip-adress när den startas efter omvandlingen. Om det behövs kan du [tilldela datorn en statisk ip-adress](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md).

* De ursprungliga virtuella hårddiskarna och lagringskontot som användes av den virtuella datorn före omvandlingen tas inte bort. De kan medföra ytterligare kostnader. För att undvika att debiteras för dem kan du ta bort de ursprungliga virtuella hårddiskblobbarna när du har kontrollerat att omvandlingen har slutförts.

* Granska den lägsta versionen av den Virtuella Azure-datoragenten krävs för att stödja konverteringen. Mer information om hur du kontrollerar och uppdatera din agentversion finns [minst Versionsstöd för VM-agenter i Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)