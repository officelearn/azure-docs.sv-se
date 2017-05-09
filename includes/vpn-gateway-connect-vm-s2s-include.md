Du kan ansluta till en virtuell dator som är distribuerad till ditt VNet genom att skapa en anslutning till fjärrskrivbord till den virtuella datorn. Det bästa sättet att först kontrollera att du kan ansluta till den virtuella datorn är att ansluta via dess privata IP-adress snarare än datornamnet. På så sätt kan du testa om du kan ansluta, men inte om namnmatchningen är korrekt konfigurerad.

1. **Leta upp den privata IP-adressen.** Du kan hitta privata IP-adressen för en virtuell dator genom att antingen granska egenskaperna för den virtuella datorn i Azure Portal eller med hjälp av PowerShell.

  - Azure Portal – Leta upp din första virtuella dator på Azure Portal. Visa egenskaperna för den virtuella datorn. Den privata IP-adressen är angiven.

  - PowerShell – Använd exemplet för att visa en lista över virtuella datorer och privata IP-adresser från dina resursgrupper. Du behöver inte ändra exemplet innan du använder det.

    ```powershell
    $vms = get-azurermvm
    $nics = get-azurermnetworkinterface | where VirtualMachine -NE $null

    foreach($nic in $nics)
    {
      $vm = $vms | where-object -Property Id -EQ $nic.VirtualMachine.id
      $prv = $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAddress
      $alloc = $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($vm.Name): $prv,$alloc"
    }
    ```

2. **Anslut till den virtuella datorn.** Kontrollera att du är ansluten till ditt VNet med VPN-anslutning. Öppna fjärrskrivbordsanslutningen genom att skriva "RDP" eller "Remote Desktop Connection" (fjärrskrivbordsanslutning) i sökrutan i aktivitetsfältet och välj sedan fjärrskrivbordsanslutning. Du kan även öppna anslutning till fjärrskrivbord med hjälp av kommandot `mstsc` i PowerShell. I fjärrskrivbordsanslutningen anger du den virtuella datorns privata IP-adress. Du kan klicka på "Visa alternativ" för att justera inställningar och sedan ansluta.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Felsöka en RDP-anslutning till en virtuell dator

Om du har problem med att ansluta till en virtuell dator via VPN-anslutningen finns det några saker som du kan kontrollera.

- Kontrollera att VPN-anslutningen har genomförts.
- Kontrollera att du ansluter till den virtuella datorns privata IP-adress.
- Om du kan ansluta till den virtuella datorn med hjälp av den privata IP-adressen, men inte med namnet på datorn, kontrollerar du att du har konfigurerat DNS korrekt.
- Mer information finns i [Felsöka fjärrskrivbordsanslutningar till en virtuell dator](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).