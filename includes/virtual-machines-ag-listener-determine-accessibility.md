Det är viktigt att det finns två sätt att konfigurera en tillgänglighetsgruppslyssnare i Azure. Hur skiljer sig i typ av Azure belastningsutjämnare som du använder när du skapar lyssnaren. I följande tabell beskrivs skillnaderna:

| Typer av belastningsutjämnare | Implementering | Använd när: |
| --- | --- | --- |
| **Externa** |Använder den *offentliga virtuella IP-adressen* av Molntjänsten som är värd för virtuella datorer (VM). |Du behöver åtkomst till lyssnaren från utanför det virtuella nätverket, inklusive från Internet. |
| **Internt** |Använder en *intern belastningsutjämnare* med en privat adress för lyssnaren. |Du kan öppna lyssnaren endast från inom samma virtuella nätverk. Den här access innehåller plats-till-plats VPN för hybridscenarion. |

> [!IMPORTANT]
> För en lyssnare som använder Molntjänsten offentliga VIP (extern belastningsutjämnare), så länge som klienten lyssnaren och databaser som finns i samma Azure-region, kommer du inte betalar utgång avgifter. Annars alla data som returneras via lyssnaren anses utgång och den debiteras med normal dataöverföring priser. 
> 
> 

En ILB kan konfigureras bara på virtuella nätverk med ett regionalt omfång. Befintliga virtuella nätverk som har konfigurerats för en tillhörighetsgrupp kan inte använda en ILB. Mer information finns i [översikt över intern belastningsutjämnare](../articles/load-balancer/load-balancer-internal-overview.md).

