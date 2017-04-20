
# Översikt
## [Vad är Azure Backup?](backup-introduction-to-azure-backup.md)

# Kom igång
## [Säkerhetskopiera filer och mappar](backup-try-azure-backup-in-10-mins.md)
## [Säkerhetskopiera virtuella Azure-datorer](backup-azure-vms-first-look.md)
## [Om virtuella Azure-datorer](backup-azure-vms-first-look-arm.md)

# Gör så här för att
## Använd PowerShell
### [Virtuella Azure-datorer i Azure Portal](backup-azure-vms-automation.md)
### [Virtuella Azure-datorer i den klassiska portalen](backup-azure-vms-classic-automation.md)
### [DPM i Azure Portal](backup-dpm-automation.md)
### [DPM i den klassiska portalen](backup-dpm-automation-classic.md)
### [Windows Server i Azure Portal](backup-client-automation.md)
### [Windows Server i den klassiska portalen](backup-client-automation-classic.md)

## Azure Backup Server
### [Förbereda Azure Backup Server-arbetsbelastningar i Azure Portal](backup-azure-microsoft-azure-backup.md)
### [Förbereda Azure Backup Server-arbetsbelastningar i den klassiska portalen](backup-azure-microsoft-azure-backup-classic.md)
### [Säkerhetskopiera en VMware-server med hjälp av Azure Backup Server](backup-azure-backup-server-vmware.md)
### [Säkerhetskopiera Exchange med hjälp av Azure Backup Server](backup-azure-exchange-mabs.md)
### [Säkerhetskopiera en SharePoint-grupp med hjälp av Azure Backup Server](backup-azure-backup-sharepoint-mabs.md)
### [Säkerhetskopiera SQL med hjälp av Azure Backup Server](backup-azure-sql-mabs.md)

## Data Protection Manager
### [Förbereda DPM-arbetsbelastningar i Azure Portal](backup-azure-dpm-introduction.md)
### [Förbereda DPM-arbetsbelastningar i den klassiska portalen](backup-azure-dpm-introduction-classic.md)
### [Använd System Center DPM för att säkerhetskopiera Exchange-server](backup-azure-backup-exchange-server.md)
### [Återställa data i säkerhetskopieringsvalvet till en annan DPM-server](backup-azure-alternate-dpm-server.md)
### [Använda DPM för att säkerhetskopiera SQL Server-arbetsbelastningar](backup-azure-backup-sql.md)
### [Använda DPM för att säkerhetskopiera en SharePoint-servergrupp](backup-azure-backup-sharepoint.md)

## Virtuella Azure-datorer
### [Förbereda virtuella Azure-datorer](backup-azure-vms-prepare.md)
### [Förbereda resurshanterarens distribuerade virtuella datorer](backup-azure-arm-vms-prepare.md)
### [Planera säkerhetskopiering av VM-infrastrukturen](backup-azure-vms-introduction.md)
### [Säkerhetskopiera virtuella Azure-datorer till säkerhetskopieringsvalvet](backup-azure-vms.md)
### [Säkerhetskopiera virtuella Azure-datorer till ett Recovery Services-valv](backup-azure-arm-vms.md)
### [Säkerhetskopiera och återställa krypterade virtuella datorer](backup-azure-vms-encryption.md)
### [Hantera och övervaka virtuella Azure-säkerhetskopior i den klassiska portalen](backup-azure-manage-vms-classic.md)
### [Hantera virtuella Azure-säkerhetskopieringar i Azure Portal](backup-azure-manage-vms.md)
### [Övervaka varningar om virtuella Azure-säkerhetskopieringar i Azure Portal](backup-azure-monitor-vms.md)
### [Återställa filer från säkerhetskopierade virtuella Azure-datorer](backup-azure-restore-files-from-vm.md)
### [Återställa virtuella datorer i Azure](backup-azure-restore-vms.md)
### [Återställa Resource Manager-distribuerade virtuella datorer i Azure Portal](backup-azure-arm-restore-vms.md)
### [Återställa nyckel och hemlighet för Key Vault för krypterade virtuella datorer med Azure Backup](backup-azure-restore-key-secret.md)

## Azure SQL Database
### [Konfigurera långsiktig kvarhållning av säkerhetskopior](../sql-database/sql-database-configure-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Visa säkerhetskopior i ett Recovery Services-valv](../sql-database/sql-database-view-backups-in-vault.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Återställa från långsiktig kvarhållning av säkerhetskopior](../sql-database/sql-database-restore-from-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Ta bort långsiktiga Azure SQL-säkerhetskopior](../sql-database/sql-database-long-term-retention-delete.md?toc=%2fazure%2fbackup%2ftoc.json)

## Windows-filer och -mappar
### [Windows Server med den klassiska distributionsmodellen](backup-configure-vault-classic.md)
### [Windows Server med Resource Manager-distributionsmodellen](backup-configure-vault.md)
### [Hantera säkerhetskopieringsvalv och servrar med hjälp av den klassiska distributionsmodellen](backup-azure-manage-windows-server-classic.md)
### [Övervaka och hantera Recovery Services-valv](backup-azure-manage-windows-server.md)
### [Återställ filer till en Windows S med hjälp av Resource Manager-distributionsmodellen](backup-azure-restore-windows-server.md)
### [Återställ filer till en Windows-server med hjälp av den klassiska distributionsmodellen](backup-azure-restore-windows-server-classic.md)

## [Vanliga frågor och svar](backup-azure-backup-faq.md)

## Felsöka
### [Problem med virtuella Azure-säkerhetskopieringar i Azure Portal](backup-azure-vms-troubleshoot.md)
### [Problem med virtuella Azure-säkerhetskopieringar i den klassiska portalen](backup-azure-vms-troubleshoot-classic.md)
### [Azure Backup Server](backup-azure-mabs-troubleshoot.md)
### [Fel vid säkerhetskopiering av virtuell Azure-dator: Det gick inte att kommunicera med VM-agenten för ögonblicksbildstatus – Tidsgränsen uppnåddes för underåtgärden för ögonblicksbilds-VM](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
### [Långsam säkerhetskopiering av filer och mappar i Azure Backup](backup-azure-troubleshoot-slow-backup-performance-issue.md)

# Koncept
## [Rollbaserad åtkomstkontroll](backup-rbac-rs-vault.md)
## [Säkerhet för hybridsäkerhetskopieringar](backup-azure-security-feature.md)
## [Radera ett Azure Backup-valv](backup-azure-delete-vault.md)
## [Konfigurera säkerhetskopiering offline](backup-azure-backup-import-export.md)
## [Ersätt ditt bandbibliotek](backup-azure-backup-cloud-as-tape.md)
## [Programkonsekventa säkerhetskopior av virtuella Linux-datorer](backup-azure-linux-app-consistent.md)

# Referens
## [PowerShell](/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/azurerm.recoveryservices.backup)
## [.NET](/dotnet/api/microsoft.azure.management.recoveryservices.backup)

# Resurser
## [Prissättning](https://azure.microsoft.com/pricing/details/backup/)
## [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazureonlinebackup)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=backup)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=backup)
