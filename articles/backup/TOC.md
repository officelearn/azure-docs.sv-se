
# Översikt
## [Vad är Azure Backup?](backup-introduction-to-azure-backup.md)

# Kom igång
## [Säkerhetskopiera virtuella Azure-datorer](backup-azure-vms-first-look-arm.md)
## [Säkerhetskopiera Windows Server- eller Windows-datorer](backup-try-azure-backup-in-10-mins.md)
## [Säkerhetskopiera VMware-servrar](backup-azure-backup-server-vmware.md)

# Gör så här för att

## Azure Backup Server
### [Skyddsöversikt för Azure Backup Server](backup-mabs-protection-matrix.md)
### Installera eller uppgradera
#### [Förbereda Azure Backup Server-arbetsbelastningar i Azure Portal](backup-azure-microsoft-azure-backup.md)
#### [Förbereda Azure Backup Server-arbetsbelastningar i den klassiska portalen](backup-azure-microsoft-azure-backup-classic.md)
#### [Lägga till lagringsutrymme på Azure Backup Server](backup-mabs-add-storage.md)
#### [Uppgradera Azure Backup Server till v.2](backup-mabs-upgrade-to-v2.md)
#### [Obevakad installation av Azure Backup Server](backup-mabs-unattended-install.md)
### Skydda arbetsbelastningar
#### [Säkerhetskopiera en VMware-server med hjälp av Azure Backup Server](backup-azure-backup-server-vmware.md)
#### [Säkerhetskopiera Exchange med hjälp av Azure Backup Server](backup-azure-exchange-mabs.md)
#### [Säkerhetskopiera en SharePoint-grupp med hjälp av Azure Backup Server](backup-azure-backup-sharepoint-mabs.md)
#### [Säkerhetskopiera SQL med hjälp av Azure Backup Server](backup-azure-sql-mabs.md)
#### [Skydda systemtillstånd och Bare Metal Recovery](backup-mabs-system-state-and-bmr.md)
### [Återställa data från Azure Backup Server](backup-azure-alternate-dpm-server.md)

## Virtuella Azure-datorer
### Förbereda den virtuella datorn
#### [Förbereda resurshanterarens distribuerade virtuella datorer](backup-azure-arm-vms-prepare.md)
#### [Programkonsekventa säkerhetskopior av virtuella Linux-datorer](backup-azure-linux-app-consistent.md)
#### [Förbereda virtuella Azure-datorer](backup-azure-vms-prepare.md)
### Planera din miljö
#### [Planera säkerhetskopiering av VM-infrastrukturen](backup-azure-vms-introduction.md)
### Säkerhetskopiera virtuella datorer
#### [Säkerhetskopiera virtuella Azure-datorer till ett Recovery Services-valv](backup-azure-arm-vms.md)
#### [Säkerhetskopiera krypterade virtuella datorer](backup-azure-vms-encryption.md)
#### [Säkerhetskopiera virtuella Azure-datorer](backup-azure-vms.md)
### Hantera och övervaka virtuella datorer
#### [Hantera virtuella Azure-säkerhetskopieringar i Azure Portal](backup-azure-manage-vms.md)
#### [Övervaka varningar om virtuella Azure-säkerhetskopieringar i Azure Portal](backup-azure-monitor-vms.md)
#### [Hantera och övervaka virtuella Azure-säkerhetskopior i den klassiska portalen](backup-azure-manage-vms-classic.md)
### Återställa data från virtuella datorer
#### [Återställa filer från säkerhetskopierade virtuella Azure-datorer](backup-azure-restore-files-from-vm.md)
#### [Återställa Resource Manager-distribuerade virtuella datorer i Azure Portal](backup-azure-arm-restore-vms.md)
#### [Återställa krypterade virtuella datorer](backup-azure-vms-encryption.md)
#### [Återställa virtuella datorer i Azure](backup-azure-restore-vms.md)
#### [Återställa nyckel och hemlighet för Key Vault för krypterade virtuella datorer](backup-azure-restore-key-secret.md)

## Konfigurera Azure Backup-rapporter
### [Konfigurera Azure Backup-rapporter](backup-azure-configure-reports.md)
### [Datamodell för Azure Backup-rapporter](backup-azure-reports-data-model.md)
### [Log Analytics-datamodell för Azure Backup](backup-azure-log-analytics-data-model.md)

## Data Protection Manager
### [Förbereda DPM-arbetsbelastningar i Azure Portal](backup-azure-dpm-introduction.md)
### [Förbereda DPM-arbetsbelastningar i den klassiska portalen](backup-azure-dpm-introduction-classic.md)
### [Använd System Center DPM för att säkerhetskopiera Exchange-server](backup-azure-backup-exchange-server.md)
### [Återställa data till en annan DPM-server](backup-azure-alternate-dpm-server.md)
### [Använda DPM för att säkerhetskopiera SQL Server-arbetsbelastningar](backup-azure-backup-sql.md)
### [Använda DPM för att säkerhetskopiera en SharePoint-servergrupp](backup-azure-backup-sharepoint.md)

## Använd PowerShell
### [Virtuella Azure-datorer i Azure Portal](backup-azure-vms-automation.md)
### [Virtuella Azure-datorer i den klassiska portalen](backup-azure-vms-classic-automation.md)
### [DPM i Azure Portal](backup-dpm-automation.md)
### [DPM i den klassiska portalen](backup-dpm-automation-classic.md)
### [Windows Server i Azure Portal](backup-client-automation.md)
### [Windows Server i den klassiska portalen](backup-client-automation-classic.md)

## Azure SQL Database
### [Konfigurera långsiktig kvarhållning av säkerhetskopior](../sql-database/sql-database-configure-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Visa säkerhetskopior i ett Recovery Services-valv](../sql-database/sql-database-view-backups-in-vault.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Återställa från långsiktig kvarhållning av säkerhetskopior](../sql-database/sql-database-restore-from-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Ta bort långsiktiga Azure SQL-säkerhetskopior](../sql-database/sql-database-long-term-retention-delete.md?toc=%2fazure%2fbackup%2ftoc.json)

## Windows Server
### [Säkerhetskopiera Windows Server-filer och -mappar](backup-configure-vault.md)
### [Säkerhetskopiera Windows Server System-tillstånd](backup-azure-system-state.md)
### [Återställa filer från Azure till Windows Server](backup-azure-restore-windows-server.md)
### [Återställa Windows Server System-tillstånd](backup-azure-restore-system-state.md)
### [Övervaka och hantera Recovery Services-valv](backup-azure-manage-windows-server.md)
### Säkerhetskopiera och återställa med den klassiska portalen
#### [Windows Server med den klassiska distributionsmodellen](backup-configure-vault-classic.md)
#### [Hantera säkerhetskopieringsvalv och servrar med hjälp av den klassiska distributionsmodellen](backup-azure-manage-windows-server-classic.md)
#### [Återställ filer till en Windows-server med hjälp av den klassiska distributionsmodellen](backup-azure-restore-windows-server-classic.md)

## Recovery Services-valv
### [Översikt över Recovery Services-valv](backup-azure-recovery-services-vault-overview.md)
### [Uppgradera ett säkerhetskopieringsvalv till Recovery Services-valvet](backup-azure-upgrade-backup-to-recovery-services.md)
### [Ta bort ett Recovery Services-valv](backup-azure-delete-vault.md)

## Felsöka
### [Problem med virtuella Azure-säkerhetskopieringar i Azure Portal](backup-azure-vms-troubleshoot.md)
### [Problem med virtuella Azure-säkerhetskopieringar i den klassiska portalen](backup-azure-vms-troubleshoot-classic.md)
### [Fel vid säkerhetskopiering av virtuell Azure-dator: Det gick inte att kommunicera med VM-agenten för ögonblicksbildstatus – Tidsgränsen uppnåddes för underåtgärden för ögonblicksbilds-VM](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
### [Långsam säkerhetskopiering av filer och mappar i Azure Backup](backup-azure-troubleshoot-slow-backup-performance-issue.md)
### [Felsöka Azure Backup Server](backup-azure-mabs-troubleshoot.md)

# Koncept

## VANLIGA FRÅGOR OCH SVAR
### [Vanliga frågor och svar om Recovery Services-valv](backup-azure-backup-faq.md)
### [Vanliga frågor och svar om säkerhetskopiering av virtuella Azure-datorer](backup-azure-vm-backup-faq.md)
### [Vanliga frågor och svar om säkerhetskopiering av filer och mappar med Azure Backup-agenten](backup-azure-file-folder-backup-faq.md)

## [Rollbaserad åtkomstkontroll](backup-rbac-rs-vault.md)
## [Säkerhet för hybridsäkerhetskopieringar](backup-azure-security-feature.md)
## [Konfigurera säkerhetskopiering offline](backup-azure-backup-import-export.md)
## [Ersätt ditt bandbibliotek](backup-azure-backup-cloud-as-tape.md)


# Referens
## [PowerShell](/powershell/module/azurerm.recoveryservices.backup)
## [.NET](/dotnet/api/microsoft.azure.management.recoveryservices.backup)

# Resurser
## [Azure-översikt](https://azure.microsoft.com/roadmap/)
## [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazureonlinebackup)
## [Prissättning](https://azure.microsoft.com/pricing/details/backup/)
## [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=backup)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=backup)
