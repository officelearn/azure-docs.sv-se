# Översikt

## [Vad är Site Recovery?](site-recovery-overview.md)

## Hur fungerar Site Recovery?

### [Arkitektur för Azure till Azure](site-recovery-azure-to-azure-architecture.md)

### [Arkitektur för Hyper-V till Azure](site-recovery-architecture-hyper-v-to-azure.md)

### [Arkitektur för replikering till en sekundär plats](site-recovery-architecture-to-secondary-site.md)

## [Vilka arbetsbelastningar kan du skydda?](site-recovery-workload.md)

## Tabell över konfigurationer och komponenter som stöds för Site Recovery

### [Stöd för Azure till Azure](site-recovery-support-matrix-azure-to-azure.md)

### [Stöd för lokalt till Azure](site-recovery-support-matrix-to-azure.md)

### [Stöd för lokalt till sekundär plats](site-recovery-support-matrix-to-sec-site.md)

## [Vanliga frågor och svar](site-recovery-faq.md)

## [Titta på en presentation](https://azure.microsoft.com/resources/videos/index/?services=site-recovery)


# Kom igång

## [Replikera virtuella Azure-datorer (förhandsversion)](site-recovery-azure-to-azure.md)

## [Replikera VMware-VM:ar till Azure](vmware-walkthrough-overview.md)

### [Steg 1: Granska arkitekturen](vmware-walkthrough-architecture.md)

### [Steg 2: Granska krav och begränsningar](vmware-walkthrough-prerequisites.md)

### [Steg 3: Planera kapacitet](vmware-walkthrough-capacity.md)

### [Steg 4: Planera nätverk](vmware-walkthrough-network.md)

### [Steg 5: Förbereda Azure](vmware-walkthrough-prepare-azure.md)

### [Steg 6: Förbereda VMware](vmware-walkthrough-prepare-vmware.md)

### [Steg 7: Skapa ett valv](vmware-walkthrough-create-vault.md)

### [Steg 8: Ställa in källa och mål](vmware-walkthrough-source-target.md)

### [Steg 9: Skapa en replikeringsprincip](vmware-walkthrough-replication.md)

### [Steg 10: Installera mobilitetstjänsten](vmware-walkthrough-install-mobility.md)

### [Steg 11: Aktivera replikering](vmware-walkthrough-enable-replication.md)

### [Steg 12: Köra ett redundanstest](vmware-walkthrough-test-failover.md)

## [Replikera Hyper-V-VM:ar till Azure](hyper-v-site-walkthrough-overview.md)

### [Steg 1: Granska arkitekturen](hyper-v-site-walkthrough-architecture.md)

### [Steg 2: Granska krav och begränsningar](hyper-v-site-walkthrough-prerequisites.md)

### [Steg 3: Planera kapacitet](hyper-v-site-walkthrough-capacity.md)

### [Steg 4: Planera nätverk](hyper-v-site-walkthrough-network.md)

### [Steg 5: Förbereda Azure](hyper-v-site-walkthrough-prepare-azure.md)

### [Steg 6: Förbereda Hyper-V-värdar](hyper-v-site-walkthrough-prepare-hyper-v.md)

### [Steg 7: Skapa ett valv](hyper-v-site-walkthrough-create-vault.md)

### [Steg 8: Ställa in källa och mål](hyper-v-site-walkthrough-source-target.md)

### [Steg 9: Skapa en replikeringsprincip](hyper-v-site-walkthrough-replication.md)

### [Steg 10: Aktivera replikering](hyper-v-site-walkthrough-enable-replication.md)

### [Steg 11: Köra ett redundanstest](hyper-v-site-walkthrough-test-failover.md)

## [Replikera virtuella Hyper-V-datorer till Azure (med VMM)](site-recovery-vmm-to-azure.md)

## [Replikera fysiska servrar till Azure](physical-walkthrough-overview.md)

### [Steg 1: Granska arkitekturen](physical-walkthrough-architecture.md)

### [Steg 2: Granska krav och begränsningar](physical-walkthrough-prerequisites.md)

### [Steg 3: Planera kapacitet](physical-walkthrough-capacity.md)

### [Steg 4: Planera nätverk](physical-walkthrough-network.md)

### [Steg 5: Förbereda Azure](physical-walkthrough-prepare-azure.md)

### [Steg 6: Skapa ett valv](physical-walkthrough-create-vault.md)

### [Steg 7: Ställa in källa och mål](physical-walkthrough-source-target.md)

### [Steg 8: Skapa en replikeringsprincip](physical-walkthrough-replication.md)

### [Steg 9: Installera mobilitetstjänsten](physical-walkthrough-install-mobility.md)

### [Steg 10: Aktivera replikering](physical-walkthrough-enable-replication.md)

### [Steg 11: Köra ett redundanstest](physical-walkthrough-test-failover.md)

## [Replikera virtuella Hyper-V-datorer till en sekundär plats (med VMM)](site-recovery-vmm-to-vmm.md)

## [Replikera virtuella VMware-datorer och fysiska servrar till en sekundär plats](site-recovery-vmware-to-vmware.md)

## [Replikera virtuella VMware-datorer till Azure i en distribution för flera innehavare (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)


# Gör så här för att

## Planera

### [Förutsättningar för Azure-replikering](site-recovery-azure-to-azure-prereq.md)

### Planera nätverk

#### [Planera nätverk för Azure till Azure-replikering (förhandsversion)](site-recovery-azure-to-azure-networking-guidance.md)

#### [Planera nätverk för replikeringen av lokala datorer](site-recovery-network-design.md)

#### [Planera nätverksmappning för replikering av virtuella Azure-datorer](site-recovery-network-mapping-azure-to-azure.md)

#### [Planera nätverksmappning för replikering av virtuella Hyper-V-datorer](site-recovery-network-mapping.md)

### Planera kapacitet och skalbarhet

#### [Planera kapacitet för VMware-replikering till Azure](site-recovery-plan-capacity-vmware.md)

#### [Distributionsplaneraren för VMware-replikering till Azure](site-recovery-deployment-planner.md)

#### [Capacity Planner för Hyper-V-replikering](site-recovery-capacity-planner.md)

### [Planera rollbaserad åtkomst för replikering av virtuella datorer](site-recovery-role-based-linked-access-control.md)

## Konfigurera

### Konfigurera källmiljön

#### [Källmiljö för VMware till Azure](site-recovery-set-up-vmware-to-azure.md)

#### [Källmiljö för fysisk till Azure](site-recovery-set-up-physical-to-azure.md)

### Konfigurera målmiljön

#### [Målmiljö för VMware till Azure](site-recovery-prepare-target-vmware-to-azure.md)

#### [Målmiljö för fysisk till Azure](site-recovery-prepare-target-physical-to-azure.md)

### [Konfigurera replikeringsinställningar](site-recovery-setup-replication-settings-vmware.md)

### [Distribuera mobilitetstjänsten för VMware-replikering](site-recovery-vmware-to-azure-install-mob-svc.md)

#### [Distribuera mobilitetstjänsten med System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)

#### [Distribuera mobilitetstjänsten med Azure Automation DSC](site-recovery-automate-mobility-service-install.md)

### Aktivera replikering

#### [Aktivera Azure till Azure-replikering](site-recovery-replicate-azure-to-azure.md)

#### [Aktivera VMware till Azure-replikering](site-recovery-replicate-vmware-to-azure.md)

## Redundans och återställning

### [Konfigurera återställningsplaner](site-recovery-create-recovery-plans.md)

#### [Lägga till Azure-runbookflöden i återställningsplaner](site-recovery-runbook-automation.md)

### Köra ett redundanstest

#### [Kör ett redundanstest till Azure](site-recovery-test-failover-to-azure.md)

#### [Köra ett redundanstest mellan VMM-moln](site-recovery-test-failover-vmm-to-vmm.md)

### [Redundansväxla skyddade datorer](site-recovery-failover.md)

### Skydda datorer igen efter en redundansväxling

#### [Skydda datorer igen från en sekundär Azure-region till en primär](site-recovery-how-to-reprotect-azure-to-azure.md)

#### [Skydda datorer igen från Azure till lokalt](site-recovery-how-to-reprotect.md)

### Redundansväxla från Azure

#### [Redundansväxla från Azure till VMware](site-recovery-failback-azure-to-vmware.md)

#### [Redundansväxla från Azure till Hyper-V](site-recovery-failback-from-azure-to-hyper-v.md)

## Migrera

### [Migrera till Azure](site-recovery-migrate-to-azure.md)

### [Migrera mellan Azure-regioner](site-recovery-migrate-azure-to-azure.md)

### [Migrera AWS Windows-instanser till Azure](site-recovery-migrate-aws-to-azure.md)

### [Replikera migrerade datorer till en annan Azure-region](site-recovery-azure-to-azure-after-migration.md)

## Arbetsbelastningar

### [Active Directory och DNS](site-recovery-active-directory.md)

### [Replikera SQL Server](site-recovery-sql.md)

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

### [Hantera processervrar i Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md)

### [Hantera konfigurationsserver](site-recovery-vmware-to-azure-manage-configuration-server.md)

### [Hantera utskalade processervrar](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)

### [Hantera vCenter-servrar](site-recovery-vmware-to-azure-manage-vCenter.md)

### [Ta bort servrar och inaktivera skydd](site-recovery-manage-registration-and-protection.md)

### [Ta bort Recovery Services-valv](delete-vault.md)


## Övervaka och felsök

### [Problem med Azure till Azure-replikering](site-recovery-azure-to-azure-troubleshoot-errors.md)

### [Replikeringsproblem med lokalt till Azure](site-recovery-vmware-to-azure-protection-troubleshoot.md)

### [Samla in loggar och felsöka lokala problem](site-recovery-monitoring-and-troubleshooting.md)


# Referens

## [PowerShell](/powershell/module/azurerm.siterecovery)

## [PowerShell – Klassisk](/powershell/module/azure/?view=azuresmps-3.7.0)

## [REST](https://msdn.microsoft.com/en-us/library/mt750497)


# Relaterat

## [Azure Automation](/azure/automation/)


# Resurser

## [Azure-översikt](https://azure.microsoft.com/roadmap/)

## [Blogg](http://azure.microsoft.com/blog/tag/azure-site-recovery/)

## [Forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)

## [Utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)

## [Prissättning](https://azure.microsoft.com/pricing/details/site-recovery/)

## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=site-recovery)

