- **Principbaserad VPN-typ:** Principbaserade VPN:er kallades innan för statiska routnings-gatewayer i den klassiska distributionsmodellen. Principbaserade VPN:er krypterar och dirigerar paket via IPSec-tunnlar baserat på IPsec-principer som konfigureras med kombinationer av adressprefix mellan ditt lokala nätverk och Azure VNet. Principen (eller trafikväljaren) definieras vanligtvis som en åtkomstlista i VPN-enhetens konfiguration. Värdet för en principbaserad VPN-typ är *PolicyBased*.

- **Ruttbaserad VPN-typ:** Ruttbaserade VPN:er kallades tidigare för dynamiska routnings-gatewayer i den klassiska distributionsmodellen. Ruttbaserade VPN:er använder "rutter" i IP-vidarebefordrings- eller routningstabeller för att dirigera paket till sina motsvarande tunnelgränssnitt. Tunnelgränssnitten krypterar eller dekrypterar sedan paketen in och ut tunnlarna. Principen (eller trafikväljaren) för ruttbaserade VPN:er konfigureras som alla-till-alla (eller jokertecken). Värdet för en ruttbaserad VPN-typ är *RouteBased*.


<!--HONumber=Jun16_HO2-->


