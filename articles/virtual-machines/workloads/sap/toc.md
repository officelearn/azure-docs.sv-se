# Översikt
## [Kom igång](get-started.md)
## [Certifieringar](sap-certifications.md)
# SAP HANA på Azure (stora instanser)
## Översikt
### [Vad är SAP HANA på Azure (stora instanser)?](hana-overview-architecture.md)
### [Förstå villkoren](hana-know-terms.md)
### [Certifiering](hana-certification.md)
### [Tillgängliga SKU:er för HLI](hana-available-skus.md)
### [Storleksändring](hana-sizing.md)
### [Registreringskrav](hana-onboarding-requirements.md)
### [SAP HANA-datanivåer och -tilläggsnoder](hana-data-tiering-extension-nodes.md)
### [Driftmodell och ansvarsområden](hana-operations-model.md)
## Arkitektur
### [Allmän arkitektur](hana-architecture.md)
### [Nätverksarkitektur](hana-network-architecture.md)
### [Lagringsarkitektur](hana-storage-architecture.md)
### [HLI-scenarier som stöds](hana-supported-scenario.md)
## Infrastruktur och anslutning
### [HLI-distribution](hana-overview-infrastructure-connectivity.md)
### [Ansluta virtuella Azure-datorer till HANA – stora instanser](hana-connect-azure-vm-large-instances.md)
### [Ansluta ett virtuellt nätverk till HANA – stora instanser ExpressRoute](hana-connect-vnet-express-route.md)
### [Ytterligare nätverkskrav](hana-additional-network-requirements.md)
## Installera SAP HANA
### [Verifiera konfigurationen](hana-installation.md)
### [Exempel på HANA-installation](hana-example-installation.md)
## Hög tillgänglighet och haveriberedskap
### [Alternativ och överväganden](hana-overview-high-availability-disaster-recovery.md)
### [Säkerhetskopiering och återställning](hana-backup-restore.md)
### [Principer och förberedelse](hana-concept-preparation.md)
### [Redundans för haveriberedskap](hana-failover-procedure.md)
## Felsök och övervaka
### [Övervaka HLI](troubleshooting-monitoring.md)
### [Övervaka och felsöka från HANA-sida](hana-monitor-troubleshoot.md)
## Gör så här för att
### [Konfiguration för hög tillgänglighet med STONITH](ha-setup-with-stonith.md)
### [Säkerhetskopiering av operativsystem för Type II-SKU:er](os-backup-type-ii-skus.md)
### [Uppgradering av operativsystem för HANA på stora instanser](os-upgrade-hana-large-instance.md)
### [Konfigurera SMT-server för SUSE Linux](hana-setup-smt.md)
# SAP HANA på Azure Virtual Machines
## [Enkel instans av SAP HANA-installation](hana-get-started.md)
## [Distributionsguide för S/4 HANA eller BW/4 HANA SAP CAL](cal-s4h.md)
## [Konfigurationer och åtgärder för SAP HANA i Azure-infrastrukturer](hana-vm-operations.md)
## SAP HANA-tillgänglighet i Azure Virtual Machines
### [Tillgänglighetsöversikt för SAP HANA på Azure](sap-hana-availability-overview.md)
### [Tillgänglighet för SAP HANA på Azure inom en Azure-region](sap-hana-availability-one-region.md)
### [Tillgänglighet för SAP HANA på Azure i flera Azure-regioner](sap-hana-availability-across-regions.md)
### [Konfigurera SAP HANA System Replication på SLES](sap-hana-high-availability.md)
### [Konfigurera SAP HANA System Replication på RHEL](sap-hana-high-availability-rhel.md)
### [Troubleshoot SAP HANA scale-out and Pacemaker on SLES](hana-vm-troubleshoot-scale-out-ha-on-sles.md) (Felsöka SAP HANA-utskalning och Pacemaker på SLES)
## [Översikt över SAP HANA-säkerhetskopiering](sap-hana-backup-guide.md)
## [Säkerhetskopiering av SAP HANA-filnivå](sap-hana-backup-file-level.md)
## [Säkerhetskopior av ögonblicksbilder i SAP HANA-lagring](sap-hana-backup-storage-snapshots.md)
# SAP NetWeaver och Business One på Azure Virtual Machines
## [Planera för SAP-arbetsbelastning och checklista för distribution](sap-deployment-checklist.md)
## [Planera och implementera SAP NetWeaver på Azure](planning-guide.md)
## [Distributionsguide för SAP NetWeaver](deployment-guide.md)
## DBMS-distributionsguider för SAP-arbetsbelastning
### [DBMS-distribution för SAP-arbetsbelastning på vanliga Azure Virtual Machines](dbms_guide_general.md)
### [DBMS-distribution för SAP-arbetsbelastning för SQL Server på Azure Virtual Machines](dbms_guide_sqlserver.md)
### [DBMS-distribution för SAP-arbetsbelastning för Oracle på Azure Virtual Machines](dbms_guide_oracle.md)
### [DBMS-distribution för SAP-arbetsbelastning för IBM DB2 på Azure Virtual Machines](dbms_guide_ibm.md)
### [DBMS-distribution för SAP-arbetsbelastning för SAP ASE på Azure Virtual Machines](dbms_guide_sapase.md)
### [Distribution av SAP MaxDB, liveCache och innehållsserver på Azure](dbms_guide_maxdb.md)
### SAP HANA-tillgänglighet i Azure Virtual Machines
#### [Tillgänglighetsöversikt för SAP HANA på Azure](sap-hana-availability-overview.md)
#### [Tillgänglighet för SAP HANA på Azure inom en Azure-region](sap-hana-availability-one-region.md)
#### [Tillgänglighet för SAP HANA på Azure i flera Azure-regioner](sap-hana-availability-across-regions.md)
## [SAP Business One på Azure Virtual Machines](business-one-azure.md)
## [Distributionsguide för SAP IDES i Windows/SQL Server SAP CAL](cal-ides-erp6-erp7-sp3-sql.md)
## [SAP LaMa-anslutning för Azure](lama-installation.md)
## Hög tillgänglighet på Windows och Linux
### [Översikt](sap-high-availability-guide-start.md)
### Arkitektur med hög tillgänglighet
#### [Arkitektur med hög tillgänglighet och scenarier](sap-high-availability-architecture-scenarios.md)
#### [Arkitektur med högre tillgänglighet och scenarier](sap-higher-availability-architecture-scenarios.md)
#### [Hög tillgänglighet på Windows med delad disk för (A)SCS-instans](sap-high-availability-guide-wsfc-shared-disk.md)
#### [Hög tillgänglighet på Windows med SOFS-filresurs för (A)SCS-instans](sap-high-availability-guide-wsfc-file-share.md)
#### [Hög tillgänglighet på SUSE Linux för (A)SCS-instans](high-availability-guide-suse.md)
#### [Hög tillgänglighet på Red Hat Enterprise Linux för (A)SCS-instans](high-availability-guide-rhel.md)
### Förberedelse av Azure-infrastruktur
#### [Windows med delad disk för (A)SCS-instans](sap-high-availability-infrastructure-wsfc-shared-disk.md)
#### [Windows med SOFS-filresurs för (A)SCS-instans](sap-high-availability-infrastructure-wsfc-file-share.md)
#### [Hög tillgänglighet för NFS på virtuella Azure-datorer eller SLES](high-availability-guide-suse-nfs.md)
#### [GlusterFS på virtuella Azure-datorer på Red Hat Enterprise Linux för SAP NetWeaver](high-availability-guide-rhel-glusterfs.md)
#### [Pacemaker på SLES](high-availability-guide-suse-pacemaker.md)
#### [Pacemaker på RHEL](high-availability-guide-rhel-pacemaker.md)
### SAP-installation
#### [Windows med delad disk för (A)SCS-instans](sap-high-availability-installation-wsfc-shared-disk.md)
#### [Windows med SOFS-filresurs för (A)SCS-instans](sap-high-availability-installation-wsfc-file-share.md)
#### [SUSE Linux med NFS för (A)SCS-instans](high-availability-guide-suse.md)
#### [Hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux](high-availability-guide-rhel.md)
### SAP – flera säkerhetsidentifierare
#### [Windows med delad disk för (A)SCS-instans](sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
#### [Windows med SOFS-filresurs för (A)SCS-instans](sap-ascs-ha-multi-sid-wsfc-file-share.md)
##  [Haveriberedskap för Azure Site Recovery för SAP](../../../site-recovery/site-recovery-workload.md#protect-sap)
# AAD SAP-identitetsintegrering och enkel inloggning
## [Integrering med SAP-moln](../../../active-directory/saas-apps/sap-customer-cloud-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [AAD-integrering med identitetsautentisering på SAP-molnplattform](../../../active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Konfigurera enkel inloggning med SAP-molnplattform](../../../active-directory/saas-apps/sap-hana-cloud-platform-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [AAD-integrering med SAP NetWeaver](../../../active-directory/saas-apps/sap-netweaver-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [AAD-integrering med SAP Business ByDesign](../../../active-directory/saas-apps/sapbusinessbydesign-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [AAD-integrering med SAP HANA DBMS](../../../active-directory/saas-apps/saphana-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
##[SAP Fiori Launchpad SAML, enkel inloggning med Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad)
# Integrering av Azure-tjänster till SAP
## [Använda SAP HANA i Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
## [DirectQuery och SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
## [Använda SAP BW-anslutningsappen i Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector)
## [Azure Data Factory erbjuder dataintegrering för SAP HANA och Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)
# Resurser
## [Azure-översikt](https://azure.microsoft.com/roadmap/)

