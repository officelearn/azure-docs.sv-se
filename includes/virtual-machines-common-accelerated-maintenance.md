

## <a name="what-is-happening"></a>Vad händer?

En branschomfattande, maskinvarubaserad säkerhetsproblem har [lämnas på 3 januari](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html). Kunder säker är alltid vår högsta prioritet och vi tar med aktiva åtgärder för att säkerställa att ingen Azure kund exponeras för dessa problem.

Med offentligt avslöjande säkerhetsproblem, vi [snabbare planerat underhåll tidtagningen](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) och började automatiskt startar om de virtuella datorer som fortfarande behövs uppdateringen.
 
## <a name="how-can-i-see-which-of-my-vms-are-already-updated"></a>Hur kan jag se vilka av Mina virtuella datorer är redan uppdaterade? 

Du kan se status för din virtuella dator och om omstarten slutföras inom den i den [VM listan i Azure portal](https://aka.ms/T08tdc). Om uppdateringen har tillämpats, eller ”planerat” om uppdateringen krävs fortfarande visas dina virtuella datorer som antingen ”redan uppdaterad”. Om du vill se just din virtuella dator ”schemalagda” referera till din [Azure tjänstens hälsa](https://portal.azure.com/).

## <a name="can-i-find-out-exactly-when-my-vms-will-be-rebooted"></a>Kan jag ta reda exakt när Mina virtuella datorer kommer att startas om?

Det bästa sättet att få en avisering om omstarten är att konfigurera [schemalagda händelser](https://docs.microsoft.com/azure/virtual-machines/windows/scheduled-events). Detta ger ett 15 minuters meddelande för den virtuella datorn gå på grund av Underhåll – det gör ett API-anrop till den virtuella datorn och dessutom aktivitetsloggpost kan användas för att utlösaren Azure-Monitor för att skicka e-post, SMS eller webhooks. Det går inte att ange någon tidigare meddelande.

## <a name="can-i-manually-redeploy-now-to-perform-the-required-maintenance"></a>Kan jag manuellt distribuera nu för att utföra det nödvändiga underhållet? 

Vi kan inte garantera att omdistribuerade VM allokeras till en uppdaterad värd. Om möjligt görs Azure-strukturen försök att allokera virtuella datorer till värdar som redan har uppdaterats. Det är möjligt att distribuera en virtuell dator kan hamna på en icke-uppdaterade värd, i så fall kan du eventuellt få betala en andra omstart tvingas som en del av schemalagt underhåll. Därför rekommenderas manuell återdistribuerar inte som en lösning.

## <a name="how-long-will-the-reboot-take"></a>Hur lång tid tar startas om? 

De flesta omstarter tar cirka **30 minuter**.

## <a name="does-the-guest-os-need-to-be-updated"></a>Stöder gästen OS behöver uppdateras? 

Uppdateringen Azure-infrastrukturen adresserar säkerhetsrisk på nivån hypervisor och kräver inte en uppdatering för Windows eller Linux VM-avbildningar. Dock bör du fortsätta att tillämpa rekommenderade säkerhetsmetoder för VM-avbildningar som vanligt. Kontakta med leverantören av din operativsystem för uppdateringar och instruktioner, vid behov. För Windows Server VM kunder vägledning nu har publicerats och är tillgängligt.

## <a name="will-there-be-a-performance-impact-as-a-result-of-resolving-this-update"></a>Kommer det att påverka prestanda på grund av hur du löser den här uppdateringen?

Merparten av Azure-kunder sett inte en tydlig prestandapåverkan med den här uppdateringen. Vi har arbetat för att optimera processor- och diskresurser i/o-sökväg och kan se inte tydlig prestandapåverkan när korrigeringen har tillämpats. En liten uppsättning kunder kan drabbas av vissa nätverk inverkan på prestanda. Detta kan åtgärdas genom att använda Azure snabbare nätverk för [Windows](https://docs.microsoft.com/en-us/azure/virtual-network/create-vm-accelerated-networking-powershell) eller [Linux](https://docs.microsoft.com/en-us/azure/virtual-network/create-vm-accelerated-networking-cli), vilket är en kostnadsfri funktioner som är tillgängliga för alla Azure-kunder.

## <a name="i-follow-your-recommendations-for-high-availability-will-my-environment-remain-highly-available-during-the-reboot"></a>Jag följer rekommendationer för hög tillgänglighet, Min miljö förblir hög tillgänglighet under omstarten?

Ja, virtuella datorer som distribueras i en tillgänglighetsgrupp eller skalningsuppsättningar i virtuella datorer har uppdatering domäner (UD)-konstruktion. När du utför underhåll, Azure godkänner UD begränsningen och kommer inte att starta om virtuella datorer från olika UD (inom samma tillgänglighetsuppsättning). Mer information om hög tillgänglighet finns i [hantera tillgängligheten för Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) eller [hantera tillgängligheten för Linux virtuella datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

## <a name="i-have-architected-my-business-continuitydisaster-recovery-plan-using-region-pairs-will-reboots-to-my-vms-occur-in-region-pairs-at-the-same-time"></a>Jag har konstruerat min business continuity/plan för katastrofåterställning med region par. Omstarter för Mina virtuella datorer sker parvis region på samma gång?

Normalt sett är Azure planerade underhållshändelser distribuerat till parad regioner en åt gången för att minska risken för avbrott i båda regioner. Men på grund av den här säkerhetsuppdateringen brådskande hur vi distribuera uppdateringen till alla regioner samtidigt.

## <a name="what-about-paas-services-on-azure"></a>Nyheter om PaaS-tjänster i Azure?  

Azure-plattformstjänster inklusive webb & mobile, datatjänster IoT, serverlösa, etc. har löst problemet. Det finns ingen åtgärd krävs för kunder som använder dessa tjänster.

## <a name="next-steps"></a>Nästa steg

Läs mer i [skydda Azure-kunder från CPU säkerhetsproblem](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).