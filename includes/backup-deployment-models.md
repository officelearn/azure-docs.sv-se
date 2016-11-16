Azure Backup-tjänsten har två typer av valv – Backup-valvet och Recovery Services-valvet. Först kom Backup-valvet. Recovery Services-valvet tillkom senare som stöd för utökade Resource Manager-distributioner. Microsoft rekommenderar att använda Resource Manager-distributioner, såvida du inte måste ha en klassisk distribution.

| **Distribution** | **Portal** | **Valv** |
| --- | --- | --- |
| Klassisk |[Klassisk](https://manage.windowsazure.com) |Säkerhetskopiering |
| Resource Manager |[Azure](https://portal.azure.com) |Recovery Services |

> [!NOTE]
> Backup-valvet kan inte skydda Resource Manager-distribuerade lösningar. Du kan dock använda ett Recovery Services-valv för att skydda servrar och virtuella datorer som distribuerats klassiskt.  
> 
> 



<!--HONumber=Nov16_HO2-->


