# Översikt
## [Vad är Site Recovery?](site-recovery-overview.md)
## [Hur fungerar Site Recovery?](site-recovery-components.md)
## [Vilka arbetsbelastningar kan du skydda?](site-recovery-workload.md)
## [Site Recovery-stödmatris](site-recovery-support-matrix.md)
## [Vanliga frågor och svar](site-recovery-faq.md)
## [Titta på en presentation](https://www.youtube.com/watch?v=eOOwMQPBKfM)

# Kom igång
## [Replikera virtuella VMWare-datorer till Azure](site-recovery-vmware-to-azure.md)
## [Replikera virtuella VMware-datorer till Azure i en distribution för flera innehavare (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)
## [Replikera virtuella Hyper-V-datorer till Azure (med VMM)](site-recovery-vmm-to-azure.md)
## [Replikera Hyper-V-VM:ar till Azure](site-recovery-hyper-v-site-to-azure.md)
## [Replikera virtuella VMware-datorer och fysiska servrar till en sekundär plats](site-recovery-vmware-to-vmware.md)
## [Replikera virtuella Hyper-V-datorer till en sekundär plats (med VMM)](site-recovery-vmm-to-vmm.md)

# Gör så här för att
## Planera
### [Förhandskrav för distribution](site-recovery-prereq.md)
### [Saker att tänka på gällande nätverksinfrastruktur](site-recovery-network-design.md)
### [Använda Site Recovery Capacity Planner](site-recovery-capacity-planner.md)
### [Planera kapacitet och skala VMware-replikering till Azure](site-recovery-plan-capacity-vmware.md)
## Konfigurera
### [Konfigurera källmiljön för replikering](site-recovery-set-up-vmware-to-azure.md)
### [Konfigurera replikeringsinställningar](site-recovery-setup-replication-settings-vmware.md)
### [Distribuera mobilitetstjänsten för VMware-replikering](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [Distribuera mobilitetstjänsten med System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
#### [Distribuera mobilitetstjänsten med Azure Automation DSC](site-recovery-automate-mobility-service-install.md)
### [Ta bort servrar och inaktivera skydd](site-recovery-manage-registration-and-protection.md)
## Migrera
### [Migrera till Azure](site-recovery-migrate-to-azure.md)
### [Migrera mellan Azure-regioner](site-recovery-migrate-azure-to-azure.md)
### [Migrera AWS Windows-instanser till Azure](site-recovery-migrate-aws-to-azure.md)
## Arbetsbelastningar
### [Active Directory och DNS](site-recovery-active-directory.md)
### [SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-workload.md#protect-sharepoint)
### [Dynamics AX](site-recovery-workload.md#protect-dynamics-ax)
### [RDS](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [Övriga arbetsbelastningar](site-recovery-workload.md#workload-summary)
## Automatisera replikering
### [Automatisera Hyper-V-replikering till Azure (utan VMM)](site-recovery-deploy-with-powershell-resource-manager.md)
### [Automatisera Hyper-V-replikering till Azure (med VMM)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [Automatisera Hyper-V-replikering till en sekundär plats (med VMM)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## Redundans och återställning efter fel
### [Redundans i Site Recovery](site-recovery-failover.md)
### [Konfigurera återställningsplaner](site-recovery-create-recovery-plans.md)
#### [Lägga till Azure-runbookflöden i återställningsplaner](site-recovery-runbook-automation.md)
### [Köra ett redundanstest från VMware till Azure](site-recovery-test-failover-to-azure.md)
### [Köra ett redundanstest mellan två VMM-platser](site-recovery-test-failover-vmm-to-vmm.md)
### [Återställ virtuella VMware-datorer och fysiska servrar](site-recovery-failback-azure-to-vmware.md)
## [Övervaka och felsök](site-recovery-monitoring-and-troubleshooting.md)

# Referens
## [PowerShell](/powershell/resourcemanager/azurerm.siterecovery/v3.2.0/azurerm.siterecovery)
## [PowerShell – Klassisk](/powershell/servicemanagement/azure.siterecovery/v3.1.0/azure.siterecovery)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# Relaterat
## [Azure Automation](/azure/automation/)

# Resurser
## [Utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [Forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [Blogg](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [Prissättning](https://azure.microsoft.com/pricing/details/site-recovery/)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=site-recovery)


<!--HONumber=Feb17_HO2-->


