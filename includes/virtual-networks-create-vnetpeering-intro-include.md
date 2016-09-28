VNet-peering är en mekanism för att ansluta två virtuella nätverk i samma region via Azures stamnätverk. När peerkopplingen är utförd, visas de två virtuella nätverken som ett enda virtuellt nätverk för alla anslutningar. Läs [Översikt över VNet-peering](../articles/virtual-network/virtual-network-peering-overview.md) om du inte är bekant med VNet-peering.

VNet-peering är i offentlig förhandsvisning. För att kunna använda funktionen måste du registrera dig med nedanstående kommandon:

    Register-AzureRmProviderFeature -FeatureName AllowVnetPeering -ProviderNamespace Microsoft.Network

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
 

<!--HONumber=Sep16_HO3-->


