## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>Så här skapar du ett virtuellt nätverk med en konfigurationsfil för nätverk från PowerShell
Azure använder en XML-fil för att definiera alla virtuella nätverk som är tillgängliga för en prenumeration. Du kan hämta den här filen, redigera det för att ändra eller ta bort befintliga virtuella nätverk och skapa nya virtuella nätverk. I den här kursen lär du dig hur du hämtar den här filen, som kallas configuration (eller netcfg) nätverksfil, och redigera det för att skapa ett nytt virtuellt nätverk. Mer information om nätverket konfigurationsfilen finns på [virtuella Azure-nätverket Konfigurationsschemat](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Utför följande steg för att skapa ett virtuellt nätverk med en netcfg-fil med hjälp av PowerShell:

1. Om du aldrig har använt Azure PowerShell kan du slutföra stegen i den [installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs) artikel, och sedan logga in på Azure och välja din prenumeration.
2. Azure PowerShell-konsolen använda den **Get-AzureVnetConfig** för att hämta konfigurationsfilen nätverk till en katalog på datorn genom att köra följande kommando: 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   Förväntad utdata:
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. Öppna filen som du sparade i steg 2 med XML- eller textredigerare och leta efter den  **<VirtualNetworkSites>**  element. Om du har några nätverk som redan har skapats visas varje nätverk som sin egen  **<VirtualNetworkSite>**  element.
4. För att skapa det virtuella nätverket som beskrivs i det här scenariot, lägger du till följande XML precis under den  **<VirtualNetworkSites>**  element:

   ```xml
        <VirtualNetworkSite name="TestVNet" Location="East US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>
   ```
   
5. Spara konfigurationsfilen nätverk.
6. Azure PowerShell-konsolen använda den **Set AzureVnetConfig** för att ladda upp konfigurationsfilen nätverk genom att köra följande kommando: 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   Returnerade utdata:
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   Om **OperationStatus** är inte *lyckades* returnerade utdata och kontrollera xml-filen för fel och fullständig steg 6 igen.

7. Azure PowerShell-konsolen använda den **Get-AzureVnetSite** för att kontrollera att det nya nätverket har lagts till genom att köra följande kommando: 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   Returnerade (förkortas) utdata innehåller följande text:
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```
