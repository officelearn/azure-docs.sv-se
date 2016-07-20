Tabellen nedan listar kraven för principbaserade och ruttbaserade VPN-gatewayer. Tabellen gäller både för Resource Manager- och den klassiska distributionsmodellen. För den klassiska modellen, är principbaserade VPN-gatewayer detsamma som statiska gatewayer och ruttbaserade gatewayer är detsamma som dynamiska gatewayer.


|   | **Principbaserad grundläggande VPN-gateway** | **Ruttbaserad grundläggande VPN-gateway** | **Ruttbaserad standard VPN-gateway**   | **Ruttbaserad högpresterande VPN-Gateway** |
|---|---------------------------------------|---------------------------------------|----------------------------|----------------------------------|
|    **Plats-till-plats-anslutning   (S2S)**  | Principbaserad VPN-konfiguration        | Ruttbaserad VPN-konfiguration  | Ruttbaserad VPN-konfiguration     | Ruttbaserad VPN-konfiguration    |
| **Punkt-till-plats-anslutning (P2S**)      | Stöds inte   | Stöds (Kan samexistera med S2S)  | Stöds (Kan samexistera med S2S)  | Stöds (Kan samexistera med S2S) |
| **Autentiseringsmetod**                 |    I förväg delad nyckel  | I förväg delad nyckel för S2S-anslutning, certifikat för P2S-anslutning | I förväg delad nyckel för S2S-anslutning, certifikat för P2S-anslutning | I förväg delad nyckel för S2S-anslutning, certifikat för P2S-anslutning |
| **Maximalt antal S2S-anslutningar**       | 1                              | 10                                                                    | 10                                | 30                               |
| **Maximalt antal P2S-anslutningar**       | Stöds inte                  | 128                                                                   | 128                               | 128                              |
|**Stöd för aktiv routning (BGP)**           | Stöds inte                  | Stöds inte                                                         | Stöds inte                     | Stöds inte                    |
 


<!--HONumber=Jun16_HO2-->


