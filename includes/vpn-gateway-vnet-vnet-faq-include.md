* De virtuella nätverken kan finnas i samma eller olika Azure-regioner (platser).
* En molntjänst eller en slutpunkt för belastningsutjämning KAN INTE omfatta flera virtuella nätverk, även om de är sammankopplade.
* Att ansluta flera virtuella Azure-nätverk tillsammans kräver inte några lokala VPN-gatewayer, såvida inte en anslutning mellan flera platser krävs.
* VNet-till-VNet stöder anslutning av virtuella nätverk. Det stöder INTE anslutning av virtuella datorer eller molntjänster i ett virtuellt nätverk.
* VNet-till-VNet kräver Azure VPN-gatewayer med VPN-typen RouteBased (tidigare kallad dynamisk routning). 
* En virtuell nätverksanslutning kan användas samtidigt med VPN för flera platser, med högst 10 (standard-gateways) eller 30 (gateways med hög kapacitet) VPN-tunnlar för en VPN-gateway i ett virtuellt nätverk, som antingen ansluter till andra virtuella nätverk eller till lokala platser.
* Adressutrymmen till virtuella nätverk och lokala nätverksplatser får inte överlappa varandra. Överlappande adressutrymmen kommer att innebära att VNet-till-VNet-anslutningarna inte skapas.
* Redundanta tunnlar mellan ett virtuellt nätverkspar stöds inte.
* Alla VPN-tunnlar i det virtuella nätverket delar på den tillgängliga bandbredden i Azures VPN-gateway och samma VPN-gateways upptids-SLA i Azure.
* VNet-till-VNet-trafik skickas via Microsoft Network, inte Internet.
* VNet-till-VNet-trafik i samma region är kostnadsfri i båda riktningarna. VNet-till-VNets utgående trafik i flera regioner debiteras med priset på utgående dataöverföring mellan virtuella nätverk, baserat på källregionerna. Se [sidan med prissättning](https://azure.microsoft.com/pricing/details/vpn-gateway/) för mer information.



<!--HONumber=Nov16_HO2-->


