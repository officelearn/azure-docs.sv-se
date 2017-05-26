# Översikt
## [Vad är Site Recovery?](site-recovery-overview.md)
## [Hur fungerar Site Recovery?](site-recovery-components.md)
## [Hur fungerar Hyper-V-replikering till Azure?](site-recovery-hyper-v-azure-architecture.md)
## [Vilka arbetsbelastningar kan du skydda?](site-recovery-workload.md)
## [Site Recovery-stödmatris](site-recovery-support-matrix-to-azure.md)
## [Vanliga frågor och svar](site-recovery-faq.md)
## [Titta på en presentation](https://azure.microsoft.com/resources/videos/index/?services=site-recovery)

# Kom igång
## [Replikera VMware-VM:ar till Azure](site-recovery-vmware-to-azure.md)
## [Replikera fysiska servrar till Azure](site-recovery-physical-servers-to-azure.md)
## [Replikera virtuella Hyper-V-datorer till Azure (med VMM)](site-recovery-vmm-to-azure.md)
## [Replikera Hyper-V-VM:ar till Azure](site-recovery-hyper-v-site-to-azure.md)
## [Replikera virtuella Hyper-V-datorer till en sekundär plats (med VMM)](site-recovery-vmm-to-vmm.md)
## [Replikera virtuella VMware-datorer och fysiska servrar till en sekundär plats](site-recovery-vmware-to-vmware.md)
## [Replikera virtuella VMware-datorer till Azure i en distribution för flera innehavare (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)

# Gör så här för att
## Planera
### [Förutsättningar för Azure-replikering](site-recovery-prereq.md)
### [Planera nätverksinfrastruktur](site-recovery-network-design.md)
### [Planera nätverksmappning](site-recovery-network-mapping.md)
### [Planera kapacitet och skala VMware-replikering till Azure](site-recovery-plan-capacity-vmware.md)
### [Distributionsplaneraren för VMware-replikering till Azure](site-recovery-deployment-planner.md)
### [Capacity Planner för Hyper-V-replikering](site-recovery-capacity-planner.md)
### [Kontrollera VM-replikering med rollbaserad åtkomst](site-recovery-role-based-linked-access-control.md)

## Konfigurera
### [Konfigurera källmiljön](site-recovery-set-up-vmware-to-azure.md)
### [Konfigurera målmiljön](site-recovery-prepare-target-vmware-to-azure.md)
### [Konfigurera replikeringsinställningar](site-recovery-setup-replication-settings-vmware.md)
### [Distribuera mobilitetstjänsten för VMware-replikering](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [Distribuera mobilitetstjänsten med System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
#### [Distribuera mobilitetstjänsten med Azure Automation DSC](site-recovery-automate-mobility-service-install.md)
### [Aktivera replikering](site-recovery-replicate-vmware-to-azure.md)
## Redundans och återställning
### [Redundansväxla skyddade datorer](site-recovery-failover.md)
### [Konfigurera återställningsplaner](site-recovery-create-recovery-plans.md)
#### [Lägga till Azure-runbookflöden i återställningsplaner](site-recovery-runbook-automation.md)
### [Kör ett redundanstest](site-recovery-test-failover-to-azure.md)
### [Skydda datorer igen efter redundans](site-recovery-how-to-reprotect.md)
### [Återställ vid fel från Azure](site-recovery-failback-azure-to-vmware.md)

## Migrera
### [Migrera till Azure](site-recovery-migrate-to-azure.md)
### [Migrera mellan Azure-regioner](site-recovery-migrate-azure-to-azure.md)
### [Migrera AWS Windows-instanser till Azure](site-recovery-migrate-aws-to-azure.md)
## Arbetsbelastningar
### [Active Directory och DNS](site-recovery-active-directory.md)
### [SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-sharepoint.md)
### [Dynamics AX](site-recovery-dynamicsax.md)
### [RDS](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [IIS-baserade webbprogram](site-recovery-iis.md)
### [Citrix XenApp och XenDesktop](site-recovery-citrix-xenapp-and-xendesktop.md)
### [Övriga arbetsbelastningar](site-recovery-workload.md#workload-summary)
## Automatisera replikering
### [Automatisera Hyper-V-replikering till Azure (utan VMM)](site-recovery-deploy-with-powershell-resource-manager.md)
### [Automatisera Hyper-V-replikering till Azure (med VMM)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [Automatisera Hyper-V-replikering till en sekundär plats (med VMM)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## Hantera
### [Redigera replikeringsinställningar](site-recovery-setup-replication-settings-vmware.md#edit-replication-policy.md)
### [Hantera processervrar i Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md)
### [Hantera konfigurationsserver](site-recovery-vmware-to-azure-manage-configuration-server.md)
### [Hantera utskalade processervrar](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)
### [Hantera vCenter-servrar](site-recovery-vmware-to-azure-manage-vCenter.md)
### [Ta bort servrar och inaktivera skydd](site-recovery-manage-registration-and-protection.md)
## [Övervaka och felsök](site-recovery-monitoring-and-troubleshooting.md)

# Referens
## [PowerShell](/powershell/module/azurerm.siterecovery)
## [PowerShell – Klassisk](/powershell/module/azure/?view=azuresmps-3.7.0)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# Relaterat
## [Azure Automation](/azure/automation/)

# Resurser
## [Utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [Forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [Blogg](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [Prissättning](https://azure.microsoft.com/pricing/details/site-recovery/)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=site-recovery)
