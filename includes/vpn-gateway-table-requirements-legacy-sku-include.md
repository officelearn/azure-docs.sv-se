I följande tabell visas kraven för PolicyBased och RouteBased VPN-gatewayer. Tabellen gäller både för Resource Manager- och den klassiska distributionsmodellen. Den klassiska modellen PolicyBased VPN-gatewayer är detsamma som statiska gatewayer och ruttbaserade gatewayer är detsamma som dynamiska gatewayer.

|  | **PolicyBased grundläggande VPN-Gateway** | **RouteBased grundläggande VPN-Gateway** | **RouteBased Standard VPN-Gateway** | **RouteBased hög prestanda VPN-Gateway** |
| --- | --- | --- | --- | --- |
| **Plats-till-plats-anslutning (S2S)** |PolicyBased VPN-konfiguration |RouteBased VPN-konfiguration |RouteBased VPN-konfiguration |RouteBased VPN-konfiguration |
| **Punkt-till-plats-anslutning (P2S**) |Stöds inte |Stöds (Kan samexistera med S2S) |Stöds (Kan samexistera med S2S) |Stöds (Kan samexistera med S2S) |
| **Autentiseringsmetod** |I förväg delad nyckel |I förväg delad nyckel för S2S-anslutning, certifikat för P2S-anslutning |I förväg delad nyckel för S2S-anslutning, certifikat för P2S-anslutning |I förväg delad nyckel för S2S-anslutning, certifikat för P2S-anslutning |
| **Maximalt antal S2S-anslutningar** |1 |10 |10 |30 |
| **Maximalt antal P2S-anslutningar** |Stöds inte |128 |128 |128 |
| **Stöd för aktiv routning (BGP)** |Stöds inte |Stöds inte |Stöds |Stöds |

