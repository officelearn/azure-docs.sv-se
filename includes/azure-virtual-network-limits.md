<a name="virtual-networking-limits-classic"></a>Följande begränsningar gäller endast för nätverksresurser som hanteras via den klassiska distributionsmodellen per prenumeration.

| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Virtuella nätverk |50 |100 |
| Lokala nätverksplatser |20 |kontakta supporten |
| DNS-servrar per virtuellt nätverk |20 |100 |
| Privata IP-adresser per virtuellt nätverk |4096 |4096 |
| Samtidiga TCP-anslutningar för en virtuell dator eller rollinstans |500 000 |500 000 |
| Nätverkssäkerhetsgrupper (NSG) |100 |200 |
| NSG-regler per NSG |200 |400 |
| Användardefinierade vägtabeller |100 |200 |
| Användardefinierade vägar vägtabell |100 |400 |
| Offentliga IP-adresser (dynamiska) |5 |kontakta supporten |
| Reserverade offentliga IP-adresser |20 |kontakta supporten |
| Offentlig VIP per distribution |5 |kontakta supporten |
| Privat VIP (ILB) per distribution |1 |1 |
| Åtkomstkontrollistor (ACL:er) för slutpunkt |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Nätverksgränser – Azure Resource Manager
Följande begränsningar gäller endast för nätverksresurser som hanteras via Azure Resource Manager per region per prenumeration.

| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Virtuella nätverk |50 |500 |
| Undernät per virtuellt nätverk |1,000 |kontakta supporten |
| DNS-servrar per virtuellt nätverk |9 |25 |
| Privata IP-adresser per virtuellt nätverk |4096 |8192 |
| Privata IP-adresser per nätverksgränssnitt |50 |kontakta supporten |
| Samtidiga TCP-anslutningar för en virtuell dator eller rollinstans |500 000 |500 000 |
| Nätverksgränssnitt (NIC) |300 |10000 |
| Nätverkssäkerhetsgrupper (NSG) |100 |400 |
| NSG-regler per NSG |200 |500 |
| Användardefinierade vägtabeller |100 |200 |
| Användardefinierade vägar vägtabell |100 |400 |
| Offentliga IP-adresser (dynamiska) |60 |kontakta supporten |
| Offentliga IP-adresser (statiska) |20 |kontakta supporten |
| Belastningsutjämnare (interna och internetkopplade) |100 |kontakta supporten |
| Regler för belastningsutjämnare per belastningsutjämnare |150 |150 |
| IP för offentlig klient per belastningsutjämnare |10 |30 |
| IP för privat klient per belastningsutjämnare |10 |kontakta supporten |
| VNets-peering-sessioner per virtuellt nätverk |10 |50 |
| Punkt-till-plats-rotcertifikat per VPN-gateway |20 |20 |
| Sekundära IP-konfigurationer per virtuellt nätverk |1000 |kontakta supporten |

[Kontakta supporten](../articles/azure-supportability/resource-manager-core-quotas-request.md ) om du måste öka gränserna från standardvärdet.

