# Översikt
## [Om virtuella datorer](../../virtual-machines-windows-about.md)
## [Diskar och VHD:er](../about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
## [virtuella nätverk](../../../virtual-network/virtual-networks-overview.md)
## [Vanliga frågor och svar](faq.md)
## [Jämför virtuella datorer, webbplatser och molntjänster](../../../app-service-web/choose-web-site-cloud-service-vm.md)
## [Behållare](../../virtual-machines-windows-containers.md)

# Kom igång
## [Skapa en virtuell dator med portalen](tutorial.md)
## [Logga in på en virtuell dator](connect-logon.md)
## [Installera Azure PowerShell](/powershell/azure/overview)
## [Installera Azure CLI](../../../cli-install-nodejs.md)

# Gör så här för att

## Använda Storage
### [Anslut en datadisk](attach-disk.md)
### [Koppla ifrån en datadisk](detach-disk.md)
### [Använda D: som en datadisk](../../virtual-machines-windows-change-drive-letter.md)

## Nätverk
### [Så här ställer du in slutpunkter](setup-endpoints.md)
### [Ansluta virtuella datorer med virtuella nätverk eller molntjänster](connect-vms.md)
### [Ansluta klassiska virtuella nätverk till virtuella nätverk för Resource Manager](../../../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
### [Skapa en belastningsutjämnare](../../../load-balancer/load-balancer-get-started-internet-classic-portal.md)
### [Hantera NSG:er med hjälp av Azure PowerShell](../../../virtual-network/virtual-networks-create-nsg-classic-ps.md)

## Distribuera
### [Skapa en anpassad virtuell dator](createportal.md)
### [Skapa och förkonfigurera en virtuell dator med Azure PowerShell](create-powershell.md)
### [Avbilda en virtuell Windows-dator](capture-image.md)
### [Skapa och överföra en virtuell hårddisk med hjälp av PowerShell](createupload-vhd.md)
### [Automatisera distribution av virtuella Azure-datorer med Chef](../../virtual-machines-windows-chef-automation.md)
### [Skapa och hantera virtuella datorer i Visual Studio](manage-visual-studio.md)
### [Skapa en virtuell dator för en webbapp med Visual Studio](web-app-visual-studio.md)
### [Kör en beräkningsintensiv uppgift i Java](java-run-compute-intensive-task.md)
### [En Hello World-webbapp i Django](python-django-web-app.md)

## Konfigurera
### [Återställ ett lösenord eller fjärrskrivbordstjänsten](../../virtual-machines-windows-reset-rdp.md)
### [Installera och konfigurera Symantec Endpoint Protection](install-symantec.md)
### [Installera och konfigurera säkerhet som en tjänst i Trend Micro Deep](install-trend.md)
### [Konfigurera en tillgänglighetsuppsättning](configure-availability.md)
### [Ändra storlek på en Windows VM som skapats i den klassiska distributionsmodellen](resize-vm.md)
### [Underhåll](planned-maintenance-schedule.md)

## Hantera
### [Migrera från klassisk till Resource Manager](../../virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
### [Hantera virtuella datorer med hjälp av Azure PowerShell](manage-psh.md)
### [Om VM-agenten och tillägg](agents-and-extensions.md)
### [Hantera VM-tillägg](manage-extensions.md)
### [Anpassat skripttillägg för virtuella datorer](extensions-customscript.md)
### [Infoga anpassade data i en virtuell Azure-dator](inject-custom-data.md)

## Planera
### [Om avbildningar](about-images.md)
### [Storlekar för virtuella datorer](../../virtual-machines-windows-sizes.md)
### [Planerat underhåll av virtuella Azure-datorer](../../virtual-machines-windows-planned-maintenance.md)
### [Vägledning för implementering av Azure-infrastrukturtjänster](../../virtual-machines-windows-infrastructure-subscription-accounts-guidelines.md)

## Hantera arbetsbelastningar
### [Databehandling med höga prestanda (HPC)](../../virtual-machines-windows-hpcpack-cluster-options.md)
#### [Skala resurser automatiskt](hpcpack-cluster-node-autogrowshrink.md)
#### [Hantera beräkningsnoder](hpcpack-cluster-node-manage.md)
#### [Skapa ett kluster](hpcpack-cluster-powershell-script.md)
#### [Konfigurera ett kluster för att köra MPI-program](hpcpack-rdma-cluster.md)
#### [Kör Excel och SOA-arbetsbelastningar](../../virtual-machines-windows-excel-cluster-hpcpack.md)
#### [Skapa huvudnoden med en Marketplace-avbildning](../../virtual-machines-windows-hpcpack-cluster-headnode.md)
#### [Skicka jobb från en lokal plats till Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md)
### [MongoDB](install-mongodb.md)
### [MySQL](mysql-2008r2.md)
### [Oracle](../../workloads/oracle/oracle-considerations.md)
### [SAP](sap-get-started.md)
### [SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
### [tomcat](java-run-tomcat-app-server.md)

## Felsöka
### [Anslutningar till fjärrskrivbord](../../virtual-machines-windows-troubleshoot-rdp-connection.md)
####[Detaljerade åtgärder för felsökning av problem med anslutning till fjärrskrivbord](../../virtual-machines-windows-detailed-troubleshoot-rdp.md)
### [Åtkomst till ett program](../../virtual-machines-windows-troubleshoot-app-connection.md)
### [Problem med att skapa en ny virtuell dator med den klassiska distributionen](troubleshoot-deployment-new-vm.md)
### [Problem med att starta om eller ändra storlek på en befintlig virtuell dator med den klassiska distributionen](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
### [Återställ RDP-lösenord](reset-rdp.md)
### [Ansluta en virtuell hårddisk till felsöknings-VM](troubleshoot-recovery-disks-portal.md)

# Referens
## [PowerShell](/powershell/azure/overview)
## [Azure CLI](/cli/azure/vm)
## [Java](/java/api)
## [NET](/dotnet/api/microsoft.azure.management.compute)
## [Skapa Resource Manager-mallar](../../../resource-group-authoring-templates.md)
## [Community-mallar](https://azure.microsoft.com/documentation/templates)
## [Compute REST](/rest/api/compute)
## [Network REST](/rest/api)
## [Storage REST](/rest/api/storageservices)

# Resurser
## [Azure-översikt](https://azure.microsoft.com/roadmap/?category=compute)
## [Prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)
## [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
## [Regional tillgänglighet](https://azure.microsoft.com/regions/services/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-machine)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=virtual-machines)
