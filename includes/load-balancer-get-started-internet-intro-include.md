En Azure belastningsutjämnare är en Layer 4-belastningsutjämnare (TCP, UDP). Belastningsutjämnaren ger hög tillgänglighet genom att distribuera inkommande trafik mellan felfria tjänstinstanser i molntjänster eller virtuella datorer i en belastningsutjämningsuppsättning. Azure belastningsutjämnare kan även presentera dessa tjänster på flera portar, flera IP-adresser eller både och.

Du kan konfigurera en belastningsutjämnare för att:

* Belastningsutjämna inkommande Internettrafik till virtuella datorer (VM). Vi refererar till en belastningsutjämnare i det här scenariot som en [Internetuppkopplad belastningsutjämnare](../articles/load-balancer/load-balancer-internet-overview.md).
* Belastningsutjämna trafik mellan virtuella datorer i ett virtuellt nätverk (VNet), mellan virtuella datorer i molntjänster eller mellan lokala datorer och virtuella datorer i ett virtuellt nätverk mellan olika platser. Vi refererar till en belastningsutjämnare i det här scenariot som en [Intern belastningsutjämnare (ILB)](../articles/load-balancer/load-balancer-internal-overview.md).
* Vidarebefordra extern trafik till en viss VM-instans.


<!--HONumber=Nov16_HO2-->


